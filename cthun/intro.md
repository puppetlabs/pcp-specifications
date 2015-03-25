Introduction
===

<!-- TODO(ale): organize this as it was meant for publishing a paper:
    Messaging fabric - problem description ...
    What Cthun solves - solution description ...
    MCO and other messaging solutions; differences, improvements ...
    What is the Cthun communications layer ...
-->

Actors
---

<!-- TODO(ale): consider using broker instead of server -->

#### Server

The Cthun server is the entity that provides messaging fabric capabilities. It
offers the following services.

<!-- TODO(ale): should we use the registration term? -->

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

What is out of scope
---

 - higher layer specifications (C&C) and implementation (modules - see
 [CthunAgent][3] repo)

What will come
---

 - RBAC
 - api (see [CthunClient][4] repo)
 - distributed fabric / HA (multi server)
 - puppetdb integration, to improve the inventory service

[1]: registration.md
[2]: wire_protocol.md
[3]: https://github.com/puppetlabs/cthun-agent
[4]: https://github.com/puppetlabs/cthun-client
