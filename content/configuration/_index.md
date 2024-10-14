+++
title = "Configuration"
weight = 20
+++

Configuring interfaces for the Fusion HL7 application is done by creating a [toml](http://toml.io) 
file for each interface in the `config` subfolder of the installation directory.

![Configuration Folder](config-folder.png)

Each config TOML file contains a variety of options for configuring an interface.
Examples of each type are included in the install in the `config-examples` subfolder of
the installation.

For a listener, a typical configuration is to have an hl7 listener that receives messages
and posts them to a RabbitMQ exchange.  Here is the example configuration file for a
RabbitMQ HL7 listener.

```toml
[interface]
type = "rabbit_listener"
name = "Rabbit Listener"
host = "127.0.0.1"
port = 2019

[log]
path = "logs/"

[rabbit]
url = "amqp://app:D0LB3Y@dockermain:5632"
queue = "outbound-hl7-queue"
exchange = "outbound-hl7-exchange"
```

Each section of the configuration file is denoted by a header in brackets
(e.g. `[interface]`).  Below each heading are properties corresponding to the 
different configuration options related to that section.

> [!important] Not all Configuration Settings Apply to all Interfaces
Most configuration settings are *optional*, and do not necessarily apply to all interface
types.  When creating a new interface, it is recommended that you start with the corresponding
example file for the interface type in question.

## Interface Section

The interface section defines properties related to the HL7 interface itself.

| Property               | Description                                                |
| ---------------------- | ---------------------------------------------------------- |
| type                   | The type of the interface: (DirectoryListener, RabbitListener, LargeRabbitListener, MongoListener, DirectorySender, RabbitSender) |
| name                   | A friendly name for the interface, used in logging and service creation. |
| host                   | The hostname or IP address that the interface should listen on.  (This is typically `localhost`) |
| port                   | The TCP/IP port that the interface should listen on. |
| *header**              | The HL7 message header character code array |
| *footer**              | The HL7 message footer character code array|
| *strip_characters*     | An array of characters that should be removed from all incoming messages before they are stored |
| *encoding*             | The name of the encoding to use when sending/receiving hl7 messages.  A list of valid encoding can be found [here](https://encoding.spec.whatwg.org/#concept-encoding-get) |
| *id_field_spec*        | The hl7 path to the message Id (default "MSH-10") |
| *account_number_field* | The hl7 path to the account number (default "PID-18.1") |
| *required_fields*      | A vector of hl7 paths denoting fields that must be in the message.  If these are not present, the message will be rejected. |
 
## Log Section

The log section contains properties related to logging.

| Property      | Description                                                |
| ------------- | ---------------------------------------------------------- |
| path          | The path to store log files.                               |
| level         | The level of messages to include in logging (Trace, Debug, Info, Warn, Error) |

## *Ssl Section*

The (optional) SSL section contains properties related to SSL/TLS on the hl7 socket.

| Property             | Description                                                |
| -------------------- | ---------------------------------------------------------- |
| server_name          | The SSL hostname.  This should match the CNAME on the host certificate. |
| certificate.pemfile  | The PEM file for the hl7 application's side of TLS.  This should be a concatenated pair file containing both the public and private key. |
| certificate.root     | Path to the root certificate store. |
| certificate.password | The password for the certificate. |

## *Debugging Section*

The debugging section contains settings that can be enabled for storing copies of
messages for debugging purposes.

| Property        | Description                                                |
| --------------- | ---------------------------------------------------------- |
| path            | Path to store debug copies of messages |
| replace_endings | Should CR characters be replaced with LF in debug copies of messages? (true/false) |

## *Archive Section*

| Property      | Description                                                |
| ------------- | ---------------------------------------------------------- |
| path          | Path to archive messages after senders send them. |

## *Export Section*

| Property      | Description                                                |
| ------------- | ---------------------------------------------------------- |
| path          | Path to save hl7 messages to (used by DirectoryListener and LargeRabbitListener) |

## *Import Section*

| Property      | Description                                                |
| ------------- | ---------------------------------------------------------- |
| path          | Path to find hl7 files for sending (used by DirectorySender) |
| poll_ms       | Miliseconds between polling folder for new files. |
| extension     | Extension on files in Import.path that should be sent. |
| error_path    | Path to store files when failing to send them. |

## *Rabbit Section*

The rabbit section contains properties related to RabbitMQ and is only applicable
to RabbitMQ listeners and senders.

| Property                    | Description                                                |
| --------------------------- | ---------------------------------------------------------- |
| url                         | The `amqp://` prefixed url to the RabbitMQ server.         |
| exchange                    | The RabbitMQ exchange to send incoming HL7 messages to.    |
| queue                       | The RabbitMQ queue to poll for messages on an HL7 sender (not applicable to listeners.) |
| ack_log_exchange            | If this is set, ACKS received by senders will be copied to this rabbit exchange.  (Generally used when needing proof/receipts that a message was received by the other side during diagnostics.) |
| rabbit_certificate_host     | The hostname for the rabbit server.  This should match the CNAME on the rabbit certificate. |
| rabbit_certificate_path     | The path to the rabbit public certificate file. This file should only contain the rabbit public certificate. |
| rabbit_certificate_password | The password to the rabbit public certificate file. |


## *Mongo Section*

| Property      | Description                                                |
| ------------- | ---------------------------------------------------------- |
| url           | The url to the mongo server. |
| database      | The database name to store/retrieve messages for mongo listeners/senders. |
| collection    | The collection name to read/write messages to for mongo listeners/senders. |
| category      | The category field value to store for messages stored in this collection. |

## *Metrics Server*

This is a single top level string setting specifying the prometheus or other OTLP server
to log metrics to.
