Underlying Wire Protocol
===

<!-- TODO(ale): check the following -->

The Cthun messaging fabric requires an underlying wire protocol that enables
data communication among nodes. Such protocol must:

 - provide reliable delivery of messages
 - deliver messages in binary or text format
 - deliver messages that can be as large as permitted by the [message][1] syntax
 - establish a persistent connection between nodes, even if by means of
 keep-alive messages
 - provide secure connection by using SSL-based authentication; the SSL
 certificate infrastructure is used by the registration service

<!-- TODO(ale): wire level compression? -->
<!-- TODO(ale): anything about RBAC at this level? -->

There are no requirements regarding:
 - the transport mechanism
 - message segmentation
 - message ordering

[1]: message.md
