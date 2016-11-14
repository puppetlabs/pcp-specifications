Puppet Communications Protocol (PCP)
===

This document describes the PCP framework and its communications protocol.

Index
----

- [Introduction][10] - overview of the PCP framework
- [Terminology][11] - definitions used by the Puppet communications protocol

##### Syntax
- [Message][20] - JSON-based message format
- [URI][21] - PCP URI format

##### Semantics
- [Session][31] - initialise connections
- [Inventory][32] - inventory service and transaction
- [Message Delivery][33] - how the fabric processes and delivers client messages
- [Error Handling][34] - error handling
- [Delivery Authorization][35] - how the fabric reports message delivery authorization failures
- [Delivery Guarantees][36] - how the fabric guarantees the delivery of messages
- [Message Versioning][37] - how the fabric handles message versions

Implementations
----

The following projects follow the PCP specifications described here, using
WebSockets as the underlying wire protocol.

 - [pcp-broker][41] provides a PCP broker implementation in Clojure
 - [cpp-pcp-client][42] is a PCP client library written in C++
 - [pxp-agent][43] is a PXP agent based on cpp-pcp-client, that offers a Puppet module
 - [clj-pcp-client][44] is a PCP client library written in Clojure

[10]: intro.md
[11]: terminology.md
[20]: message.md
[21]: uri.md
[31]: session.md
[32]: inventory.md
[33]: delivery.md
[34]: error_handling.md
[35]: unauthorized.md
[36]: delivery_guarantees.md
[37]: versioning.md
[41]: https://github.com/puppetlabs/pcp-broker
[42]: https://github.com/puppetlabs/cpp-pcp-client
[43]: https://github.com/puppetlabs/pxp-agent
[44]: https://github.com/puppetlabs/clj-pcp-client
