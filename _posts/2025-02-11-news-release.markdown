---
layout: post
title: "Secure Electronic Voting: CHERIoT-Enhanced Smart Ballot Box on Sonata"
date:   2025-02-11 00:00:00 +0100
background: '/assets/images/cyber_orig.jpg'
---

<b> Introduction </b>

Ensuring security and reliability in electronic voting is paramount in modern democracies. The Smart Ballot Box (SBB) on Sonata showcases how formal models, state machine modelling, and compartmentalization enhance voting security. By leveraging CHERIoT infrastructure, the system provides structured error handling, ensuring fault tolerance and robust recovery mechanisms.

<b> Formal Models for Safe Error Recovery </b>

A key aspect of the SBB system is its reliance on formal models to define normal and error behaviour. This structured approach ensures:

*	Correct vote counting, maintaining system integrity under all circumstances.
*	Predictable fault responses, where errors are identified and managed without compromising voting operations.
*	State-driven recovery, ensuring stable system restoration via predefined transitions.

By employing formal methods, the SBB guarantees structured, predictable, and secure responses to faults, mitigating risks associated with system failures.

<b> Compartmentalization for Enhanced Security </b>

The SBB system employs a compartmentalized architecture to bolster both security and fault resilience. It is structured into:

*	Top-level compartments: Roller and SBB controller.
*	Library compartments: Barcode Reader and Barcode Processing.
*	User interaction compartments: Sonata LCD and Joystick.

<figure>
  <img src="/files/compartments.png" width="1000" class="center">
</figure>
 
Each compartment operates independently, communicating securely through controlled function calls. This design ensures:

*	Isolation of sensitive operations, such as barcode processing, reducing unauthorized access risks.
*	Fault containment, preventing errors from cascading across compartments.
*	Compartment-specific error handling, allowing targeted recovery mechanisms rather than system-wide failures.

<b> State Encoding for Advanced Error Handling </b>

The SBB integrates state encoding to refine its error-handling approach. By adopting a state machine model, UML-B state machine, it achieves:

*	Explicit state transitions, ensuring controlled system changes based on defined events.
*	Exception-based recovery, where faults are mapped to structured corrective actions.
*	Granular fault detection, enabling precise identification and resolution of errors.

For instance, in the event of an exception within a compartment, the state machine isolates the fault source and triggers the optimal recovery path, minimizing operational disruptions.

<figure>
  <img src="/files/stateMachine1.png" width="1000" class="center">
</figure>

<figure>
  <img src="/files/stateMachine2.png" width="1000" class="center">
</figure>

<b> CHERIoT Infrastructure: Strengthening Security and Recovery </b>

The CHERIoT framework plays a critical role in supporting structured and compartmentalized error management. Its features include:

*	Localized error handling, ensuring that faults are contained within specific compartments.
*	State-driven exception responses, enabling the system to adapt based on its current operational state.
*	Secure execution environments, leveraging CHERIoT's memory safety mechanisms to mitigate security risks.

Through CHERIoT integration, the SBB enhances its ability to detect, contain, and recover from faults efficiently while maintaining a secure and tamper-resistant voting process.

<b> Conclusion </b>

The Smart Ballot Box (SBB) on Sonata exemplifies how formal modelling, compartmentalization, and structured error handling can ensure a secure electronic voting system. By leveraging CHERIoT’s infrastructure, the system strengthens fault recovery, compartment selection, and state-based error management. This approach lays the foundation for future advancements in trustworthy and secure electronic voting technologies.


