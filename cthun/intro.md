Introduction
===

*TODO(ale):* write abstract
    Messaging fabric - problem description ...
    What Cthun solves - solution description ...
    MCO and other messaging solutions; differences, improvements ...
    What is the Cthun communications layer ...

Actors
---

*DISCISS(ale):* should we use "broker" instead of "server"?

#### Server

The Cthun server is the entity that provides messaging fabric capabilities. It
offers the following services.

*DISCUSS(ale):* should we use "registration"?

##### Registration

This service consists of:

 - processing [login][1] requests
 - verifying the identity of the requester node by relying on the authentication
 provided by the underlying [wire protocol][2]
 - registering the presence of the requester node within the framework

##### Message Delivery

Message delivery consists of validating Cthun messages and forwarding them to
registered recipient nodes.

Note that the Cthun messaging fabric does not provide publish/subscribe
capabilities.

In case an endpoint loses connectivity, the fabric will re-try to delivery a
given message until its expiration time.

##### Inventory

This service provides the list of nodes currently registered to the messaging
fabric. The list may be limited to the nodes of a given type.

#### Client Nodes

In the Cthun framework, Clients nodes are addressable entities that rely on the
services provided by the messaging fabric in order to implement functionalities
such as C&C ones.

What is covered by Cthun specifications
---

The Cthun specification define the communications protocol used by the nodes
within a Cthun framework. It covers the message syntax, Cthun URI format, the
semantics of the services described above, and error handling.

*TODO(ale):* protocol specs versioning

*TODO(ale):* client asynchronous API: connect, send, isConnected,
    registerMessageCallback, monitorConnection

What will come
---

 - RBAC
 - client API specifications (see [CthunClient][3] and [clj-cthun-client][4]
 repos)
 - distributed fabric / HA (multi server)
 - puppetdb integration (improve the inventory service)
 - node classifier integration
 - notify/subscribe & topics

[1]: registration.md
[2]: wire_protocol.md
[3]: https://github.com/puppetlabs/cthun-agent
[4]: https://github.com/puppetlabs/clj-cthun-client
