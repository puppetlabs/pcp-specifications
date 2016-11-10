Terminology
===

### PCP Framework

communications system that provides capabilities such as: message delivery
and inventory queries.

### Client

addressable entity that uses the Puppet Communications Protocol
described in this document

### Uniform Resource Indicator (URI)

string that identifies a client within a PCP framework; URIs are used to address
nodes and are made up of three parts, a scheme name, a common name and a type. For
Example: pcp://bob.com/controller

### Client common name

the name of a client determined by the common name in a client certificate

### Client type

a text label that identifies the role of a given node within a PCP framework;
the "server" type is the reserved label for PCP brokers

### Broker

entity that provides inter client communication

### Message

data sent between nodes; it includes the actual payload and the metadata that
enables the delivery

### Message Delivery

the process of sending a message to the recipient node; also the related
service provided by the messaging fabric

### Transaction

message exchange that enables a given PCP framework functionality; it may
occur at the messaging fabric layer (e.g. inventory request/response) or at
client layer (e.g. request/response exchange among client nodes)

### Session

an active connection between broker and client

### Inventory

service that provides the list of nodes registered to the messaging fabric

### Schema

JSON schema used to validate the format of message parts
