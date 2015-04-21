Terminology
===

### Cthun Framework

communications system that provides capabilities such as: message delivery
and inventory queries.

### Client

addressable entity that uses the Cthun communications protocol
described in this document

### Uniform Resource Indicator (URI)

String that identifies a client within a Cthun framework; URIs are used to address
nodes and are made up of three parts, a scheme name, a common name and a type. For
Example: cth://bob.com/controller

### Client common name

The name of a client determined by the common name in a client certificate

### Client type

A text label that identifies the role of a given node within a Cthun framework.
The "server" type is the reserved label for Cthun servers

### Server

entity that provides inter client communication

### Message

data sent between nodes; it includes the actual payload, the metadata that
enables the delivery, and debug information

### Message Delivery

the process of sending a message to a number of recipient nodes; also the
related service provided by the messaging fabric

### Transaction

message exchange that enables a given Cthun framework functionality; it may
occur at the messaging fabric layer (e.g. session associations) or at client
layer (e.g. request/response exchange among client nodes to enable a certain C&C
operation - see Cthun C&C specifications)

### Re-delivery

the act of delivering a message after a failed attempt, that should be repeated
until the message expires

### Session Association

service that processes association transactions and records the clients that have
sessions associated with the server, together with their wire connection states

### Inventory

service that provides the list of nodes registered to the messaging fabric

### Schema

JSON schema used to validate the format of message parts

### Chunk

a segment of a Cthun message that consists of descriptor, size, and content fields

### Envelope Chunk

chunk that stores the message metadata

### Data Chunk

chunk that stores the message payload

### Debug Chunk

chunk that stores debug information added by servers
