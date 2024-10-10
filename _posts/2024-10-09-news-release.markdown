---
layout: post
title: "Developing safe exception recovery mechanisms for CHERI capability hardware using UML-B formal analysis"
date:   2024-10-09 00:00:00 +0100
background: '/assets/images/cyber_orig.jpg'
---
<p style="text-align:justify;">
Mechanisms for detecting exceptional erroneous behaviour are often generic since they are flag unusual use or activity in the underlying low level machinery. 
An example is the CHERI memory safe capability based approach which is implemented within general purpose electronic computing devices. 
In contrast, the  design of a suitable recovery response to the detected exception is usually application, or at least domain, specific.  
In some cases a  safe response might be to halt, but this could play into the hands of a malicious attacker by providing an easy vector to achieve denial of service attacks. 
In many cases it is not safe for a critical service to halt.
Therefore we believe that generic detection mechanisms like CHERI are only useful of they are complimented by tools and techniques for application  engineers to design and implement safe recovery strategies that allow the system to continue its service as much as possible.
 
We already use formal modelling tools to support the rigorous analysis of systems ensuring that they meet important (e.g. safety and security) properties.
In HDSEC we have adapted these formal analysis tools to show how they can be used to design and analyse exception recovery responses and verify that they recover the system to a condition that satisfies the important system properties.
 
We have also implemented  the modelled system in order to demonstrate the recovery responses  in a real system running on a CHERI Morello PC. 
The implementation is a demonstrator that also contains a simulation of the environment and the user interfaces. 
The code is seeded to allow a capability exception to be detected so that the recovery can be demonstrated.

We start by proving that the normal behaviour satisfies the important properties at key states of the system, then identify the transactions where variables are out of step according to those properties, then add exception handling where transactions do not complete and show that the recovery mechanisms return the system to a safe state where the properties hold once again. See below figure.

We basically wrap each invocation of a state-machine state in a sigsetjmp which acts as a kind of 'try' and if there is an exception (which could be any POSIX signal but we show SIGPROT, SIGSEGV & SIGALRM as examples) it exits to the 'catch'  which forces the  state-machine into a new state (which the handler should have set up to be the designated recovery state for that exception occurring in that source state).</p>

<figure>
  <img src="/files/stm_SBB_exceptions.png" width="1000" class="center">
</figure>

The exception handling is set up at intialisation using sigactions:

<pre style="background-color: #f4f4f4; padding: 10px; border: 1px solid #ddd; overflow-x: auto;">
<code style="font-family: Consolas, 'Courier New', monospace; color: #d63384;">
struct sigaction SBB_sa;

void SBB_setup_exception_handling(){
//allow interrupts other than the ones we handle
sigemptyset(&SBB_sa.sa_mask);
sigaddset(&SBB_sa.sa_mask, SIGPROT);
sigaddset(&SBB_sa.sa_mask, SIGALRM);
sigaddset(&SBB_sa.sa_mask, SIGSEGV);
//assign the exception handler routine
SBB_sa.sa_handler = (void *)SBB_Handler;
   //assign handler to SIGALRM and SIGPROT and SIGSEGV
   sigaction(SIGALRM, &SBB_sa, &SBB_SIGALRM_oldsa);
   sigaction(SIGPROT, &SBB_sa, &SBB_SIGPROT_oldsa);
   sigaction(SIGSEGV, &SBB_sa, &SBB_SIGSEGV_oldsa);
}
</code>
</pre>

where  SBB_Handler is the exception handler routine:

<pre style="background-color: #f4f4f4; padding: 10px; border: 1px solid #ddd; overflow-x: auto;">
<code style="font-family: Consolas, 'Courier New', monospace; color: #d63384;">
printf("\n>>SBB Handler - sigtype: %d while in state ", sigtype);
printCurrentState();
record_exception(info);
switch (sigtype) {

case SIGPROT: //capability  violation
                  ///code to  decide on the best recovery and set the next statemachine state accordingly
               ... sbb_control.state = ??
      case SIGSEGV: //segmentation violation
      ....
    }
 siglongjmp(SBB_abort_step, 1);
}

void SBB_statemachine(){
bool  changedState=false;
while (true){

if (sigsetjmp(SBB_abort_step,true) ==0) {   // TRY

if (sbb_control.state==SBB_Null){
printf("Something went wrong! SBB_state is Null\n");
}else{
alarm(getAlarm()); //set the timeout for the current state
changedState = false;
printCurrentState();
do {
ROLLER_step(); //progress the roller simulation
changedState = SBB_checkState(); //see if we can change the state
} while (!changedState); //repeat until a state change occurs
}

} //SBB_abort_step - exit point for handled exceptions

sbb_control.trigger = NULL_TRIGGER;
}

}
 </code>
</pre>

