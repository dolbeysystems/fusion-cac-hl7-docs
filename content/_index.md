+++
archetype = "chapter"
title = "Hl7 Listener/Sender"
weight = 7
+++

The Fusion HL7 application provides the ability to set up HL7 listener and sender ports for interfacing
with hospital systems.

Listeners are the primary method that CAC uses to receive accounts, documents, charges, medications 
and other chart data from upstream hospital systems.

Senders are the primary method that CAC uses to send final coding results to the hospital's downstream 
billing system.

HL7 is a standard data format and network protocol for communicating healthcare information between systems.
While the standard does loosely define the location and format of data in different fields, different systems
often deviate from that standard or contain additional details that must be accounted for.  The HL7 
listner receives and stores messages "as is" and they are processed and imported later by interface scripts 
within the Script Engine.

