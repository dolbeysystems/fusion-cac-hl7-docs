+++
title = "HL7 Format"
weight = 30
+++

**(An Example HL7 Message)**
```hl7
MSH|^~\&|DOLBEY|MHMC|CERNER|HNAM|20190815135411||MDM^T02|T0220190815135411|P|2.1||
PID|1|AAA|900565362||FLTEST^SUSAN^||19640411|F||||||||||80000437|||||||0|
TXA||CODER^QUERY||20190815135411||||20190617100100000|03289|03289||ID33|||ID33||P||||
OBX|1|ED|CODER.QUERY||^TEXT^ASCII^ASCII^||||||P|||||||||||||||
```

## Segments

At the top-level, an HL7 message is made up of several text segments separated by CR (carriage-return)
characters.  In short, this means that each segment is on its own line.  However, it is important
to note that the line separator is **always specifically a single CR character.**

> [!note]
Many Windows programs use two characters to denote a line break (CR, LF).  Many linux programs
use only an LF character.  Modern editors allow any combination of CR and LF to denote a line
break, but it is important to keep the CR only rule in mind if you are manually editing an HL7
message using a general purpose editor like Visual Studio Code or Notepad++.  Your editor must
be set to keep the CR only line breaks intact!

Each segment starts with the segment's name, followed by a field separator character (often
a pipe, but can vary - see MSH segment below.)

## The MSH Message Header Segment

The first segment in every hl7 message is the MSH segment.  The MSH sement includes global
header values for the message.  Of immediate interest, is the values in characters 4-8 on the
MSH line.  These characters (collectively referred to as "message delimiters") define the
separators used throughout the message to further split all further segment lines
into fields, components, subcomponents and to denote repetition:

### Message Delimiters

| MSH Character Position | Typical Value | Definition |
| ---------------------- | ------------- | ---------- |
| 4                      | \|            | Field separator |
| 5                      | ^             | Component separator |
| 6                      | ~             | Component repetition separator |
| 7                      | \             | Escape character (used as a prefix for putting any of these separator values into a message literally.) |
| 8                      | &             | Subcomponent separator |

## Fields, Components, SubComponents

All HL7 segments are broken into *field*, which are separated by a pipe `|` character
(or whatever the field delimiter specified in MSH 4 is.)

A field is often referred to by the segment it is in, and then the number of the field, where
field 1 is the field immediately following the segment name. (e.g. `PID-3` on the message
above would refer to the value `900565362`.)

Fields can further be separated into components using the component separator (typically `^`.)
When this is done, the component is typically referred to by the segment name, the field name
and then the subcomponent index starting at 1. (e.g. `TXA-2.2` on the message above would 
refer to the value `QUERY`.)

Components can contain a repeating array of values by separating the values with the 
repetition separator.

Finally individual component values (repeated or not can be separated into sub-components using the
subcomponent separator.)



## Segment Standards

The available segments are formally specified as part of the HL7 specification.  Any site-specific
or "custom" segments start with the letter "Z" followed by two characters.  (All segment names
are three characters long.)

Here is an incomplete list containing some common segments used with CAC interfaces:

| Segment Name | Description |
| ------------ | ----------- |
| MSH          | Message Header |
| PID          | Patient Information |
| PV1          | Patient Visit Information |
| OBX          | Observation Result (Typically contains lab results or a line of document text.) |
| OBR          | Observation Header (Headers in the document.) |
| DG1          | Diagnosis |
| TXA          | Transcribed Document Header |

The fields, components, etc. used with a segment are technically standardized, however this is
a loose standard and can not be reliably used when interpreting information from a system
and importing it into CAC.  

Reading and translating the hl7 message is not done by the Fusion HL7 application.  It is instead
handled downstream by the **Fusion CAC Script Engine.**  However, HL7 messages are still parsed
by the HL7 application for the purpose of uniquely identifying and routing the message.

## HL7 Message Parsing

For the most part, the HL7 application itself does not concern itself with values in these fields.
There are two fields, however that are read out of the message field, and used for routing and 
file naming when receiving messages from an outside source.

### Message ID

The message ID is used as a key for the message to name files or rabbit routing keys.  This field
is located in `MSH-10`.  

In cases where an interface deviates from the standard and stores the message ID elsewhere, 
the hl7 application can be configured to look for it in an alternate spot with the *id_field_spec*
property in the *interface* section
(see [Configuration]({{% ref "../configuration/#interface-section" %}})

### Account Number (optional)

The account number is present in most messages, and can be found in the `PID` segment. If found,
this field used by the HL7 application as a seconary ID and is by default located
in `PID-18.1`.

In cases where an interface deviates from the standard and stores the message ID elsewhere, 
the hl7 application can be configured to look for it in an alternate spot with the *id_field_spec*
property in the *interface* section
(see [Configuration]({{% ref "../configuration/#interface-section" %}})
