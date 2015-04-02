Terminology
===

<dl>
  <dt>Cthun Messaging Fabric</dt>
  <dd>communications system that provides functionalities such as: node presence registration (authentication, inventory) and message delivery (validation, queuing, routing, forwarding)</dd>

  <dt>Cthun Framework</dt>
  <dd>Cthun messaging fabric together with the set of client nodes that use it</dd>

  <dt>Command and Control (C&C)</dt>
  <dd>system that provides a number of services for enabling the remote execution of shell commands and scripts</dd>

  <dt>Node</dt>
  <dd>addressable entity (server or client node) that uses the Cthun communications protocol described in this document</dd>

  <dt>Node type</dt>
  <dd>text label that identifies the role of a given node within a Cthun framework (e.g. "agent" or "controller"); "server" is the reserved label for Cthun servers</dd>

  <dt>Server</dt>
  <dd>broker node that provides the messaging fabric capabilities</dd>

  <dt>Client Node</dt>
  <dd>node that uses the messaging fabric capabilities for communicating (with other client nodes) or requesting framework information (provided by a server node)</dd>

  <dt>Uniform Resource Indicator (URI)</dt>
  <dd>String that identifies a node within a Cthun framework; URIs are used to address nodes</dd>

  <dt>Message</dt>
  <dd>data sent between nodes; it includes the actual payload, the metadata that enables the delivery, and debug information</dd>

  <dt>Message Delivery</dt>
  <dd>the process of sending a message to a number of recipient nodes; also the related service provided by the messaging fabric</dd>

  <dt>Transaction</dt>
  <dd>message exchange that enables a given Cthun framework functionality; it may occur at the messaging fabric layer (e.g. login messages exchange among a client/server pair to perform the registration of that client node) or at client layer (e.g. request/response exchange among client nodes to enable a certain C&C operation - see Cthun C&C specifications)</dd>

  <dt>Re-delivery</dt>
  <dd>the act of delivering a message after a failed attempt, that should be repeated until the message expires</dd>

  <dt>Registration</dt>
  <dd>service that processes login transactions and records the nodes that are currently registered to the messaging fabric, together with their wire connection states</dd>

  <dt>Inventory</dt>
  <dd>service that provides the list of nodes registered to the messaging fabric</dd>

  <dt>Schema</dt>
  <dd>JSON schema used to validate the format of message parts</dd>

  <dt>Chunk</dt>
  <dd>a segment of a Cthun message that consists of descriptor, size, and content fields</dd>

  <dt>Envelope Chunk</dt>
  <dd>chunk that stores the message metadata</dd>

  <dt>Data Chunk</dt>
  <dd>chunk that stores the message payload</dd>

  <dt>Debug Chunk</dt>
  <dd>chunk that stores debug information added by servers</dd>
</dl>
