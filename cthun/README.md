Cthun Communications Protocol
===

This document describes the Cthun framework and its communications protocol.

Index
----

- [Introduction][10] - overview of the Cthun framework
- [Terminology][11] - definitions used by the Cthun communications protocol

##### Syntax
- [Message][20] - chunk-based message format
- [URI][21] - Cthun URI format

##### Semantics
- [Session Association][31] - initialise connections and associate sessions
- [Inventory][32] - inventory service and transaction
- [Message Delivery][33] - how the fabric processes and delivers client messages
- [Destination Report][34] - how the fabric reports the receivers of a message
- [Error Handling][35] - error handling
- [Message Expiration][36] - how the fabric reports expired messages

Implementations
----

The following projects follow the Cthun specifications described here, using
WebSockets as the underlying wire protocol.

*TODO(ale):* rename repos

 - [cthun][41] provides a Cthun server implementation in Clojure
 - [cthun-client][42] is a Cthun client library written in C++
 - [cthun-agent][43] is an agent used to run puppet, based on CthunClient
 - [clj-cthun-message][44] is a library for processing Cthun messages written in
 Clojure
 - [clj-cthun-client][45] is a Cthun client Clojure library written in Clojure

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
[36]: ttl_expired.md
[41]: https://github.com/puppetlabs/cthun
[42]: https://github.com/puppetlabs/cthun-client
[43]: https://github.com/puppetlabs/cthun-agent
[44]: https://github.com/puppetlabs/clj-cthun-message
[45]: https://github.com/puppetlabs/clj-cthun-client
