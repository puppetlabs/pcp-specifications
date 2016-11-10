Introduction
===

Actors
---

#### Broker

The PCP Broker is the entity that enables inter-client communication. It
provides the following capabilities.

##### Message Delivery

Messages are delivered in a point to point manner. A client sends a message
to a broker and specifies a recipient. The broker will attempt to
deliver the message to its intended target. If the message cannot be delivered
immediately, the broker will drop the message and inform the sender with an
error message.

##### Inventory

When the broker receives an [inventory][1] request, it will return the list of
[PCP URIs][2] of clients that currently have a [session][3] with the broker.

(*Caveat*) The current implementation of the inventory service is purposefully
simplistic. When the requirements have been finalised, the inventory service
may depend on puppetdb.

#### Clients

Clients are addressable entities connected to a PCP Broker which can send
and receive messages.

What is covered by PCP specifications
---

The PCP specification define the communications protocol used by the nodes
within a PCP framework. It covers the message syntax, PCP URI format, the
semantics of the services described above, and error handling.

What will come
---

 - RBAC
 - distributed message fabric / HA (multi broker)
 - puppetdb integration (improve the inventory service)
 - node classifier integration

[1]: inventory.md
[2]: uri.md
[3]: association.md
