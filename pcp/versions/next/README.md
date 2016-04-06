Puppet Communications Protocol (PCP)
===

This document describes the PCP framework and its communications protocol.

Index
----

- [Introduction][10] - overview of the PCP framework
- [Terminology][11] - definitions used by the Puppet communications protocol

##### Syntax
- [Message][20] - chunk-based message format
- [URI][21] - PCP URI format

##### Semantics
- [Session Association][31] - initialise connections and associate sessions
- [Inventory][32] - inventory service and transaction
- [Message Delivery][33] - how the fabric processes and delivers client messages
- [Destination Report][34] - how the fabric reports the receivers of a message
- [Error Handling][35] - error handling
- [Delivery Authorization][36] - how the fabric reports message delivery authorization failures
- [Message Expiration][37] - how the fabric reports expired messages
- [Delivery Guarantees][38] - how the fabric guarantees the delivery of messages
- [Message Versioning][39] - how the fabric handles message versions

Implementations
----

The following projects follow the PCP specifications described here, using
WebSockets as the underlying wire protocol.

 - [pcp-broker][41] provides a PCP broker implementation in Clojure
 - [cpp-pcp-client][42] is a PCP client library written in C++
 - [pxp-agent][43] is a PXP agent based on cpp-pcp-client, that offers a Puppet module
 - [clj-pcp-common][44] is a library for processing PCP messages written in
 Clojure
 - [clj-pcp-client][45] is a PCP client ibrary written in Clojure

[10]: intro.md
[11]: terminology.md
[12]: wire_protocol.md
[20]: message.md
[21]: uri.md
[31]: association.md
[32]: inventory.md
[33]: delivery.md
[34]: destination_report.md
[35]: error_handling.md
[36]: unauthorized.md
[37]: ttl_expired.md
[38]: delivery_guarantees.md
[39]: versioning.md
[41]: https://github.com/puppetlabs/pcp-broker
[42]: https://github.com/puppetlabs/cpp-pcp-client
[43]: https://github.com/puppetlabs/pxp-agent
[44]: https://github.com/puppetlabs/clj-pcp-common
[45]: https://github.com/puppetlabs/clj-pcp-client
