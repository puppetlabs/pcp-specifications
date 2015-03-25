Cthun Specifications
===

The Cthun messaging fabric provides a lightweight communication layer for
Command and Control (C&C) by enabling request-respnse transactions among
addressable nodes.

This repository describes the communications protocol used by it.

Implementations
----

The following projects implement the Cthun specifications described here:
<!--TODO(ale): rename repos: cthun-server? pegasus? cpp vs clojure?-->

 - [Cthun][1] provides a clojure-based Cthun server
 - [CthunClient][2] is a WebSocket-based C++ client library to manage Cthun
 connections
 - [CthunAgent][3] is a C&C agent, based on [CthunClient][2]
 - [Pegasus][4] is a C&C tool that can be used as a controller node; it also
 relies on [CthunClient][2]

<!--TODO(ale): versioning-->

Index
----

- [Introduction][10] - overview of the Cthun framework
- [Terminology][11] - definitions used in this document
- [Wire Protocol][12] - requirements for the underlying wire protocol

##### Syntax
- [Message][20] - description of the chunk-based message format
- [URI][21] - Cthun URI format

##### Semantics
- [Registration][30] - registration service and login transaction
- [Inventory][31] - inventory service and transaction
- [Delivery][32] - how the fabric processes and delivers messages sent by client
nodes
- [Error Handling][33] - error handling

<!--TODO(ale): client asynchronous API (connect, send, isConnected,
               registerMessageCallback, monitorConnection)-->

<!--TODO(ale): our C2 specs (request/response, schemas)-->

[1]: https://github.com/puppetlabs/cthun
[2]: https://github.com/puppetlabs/cthun-client
[3]: https://github.com/puppetlabs/cthun-agent
[4]: https://github.com/puppetlabs/pegasus
[10]: cthun/intro.md
[11]: cthun/terminology.md
[12]: cthun/wire_protocol.md
[20]: cthun/message.md
[21]: cthun/uri.md
[30]: cthun/registration.md
[31]: cthun/inventory.md
[32]: cthun/delivery.md
[33]: cthun/error_handling.md
