Introduction
===

Actors
---

#### Server

The PCP Broker is the entity that provides inter client communication. It
has the following capabilities.

##### Message Delivery

Messages are delivered in a point to point manner. A client sends a message
to a server and specifies one or more recipients. The server will attempt to
deliver the message to its intended targets. If the message cannot be delivered
immediately,the server will attempt to deliver a message to its intended
recipients until the message has expired.

##### Inventory

When the broker receives an [inventory][1] request, it will return the list of
[PCP URIs][2] of clients that currently have a [session][3] associated with the
broker.

(*Caveat*) The current implementation of the inventory service is purposefully
simplistic. When the requirements have been finalised the inventory service
may depend on puppetdb.

#### Clients

Clients are addressable entities, connected to a PCP Broker which can send
and receive messages.

What is covered by PCP specifications
---

The PCP specification define the communications protocol used by the nodes
within a PCP framework. It covers the message syntax, PCP URI format, the
semantics of the services described above, and error handling.

What will come
---

 - RBAC
 - distributed fabric / HA (multi server)
 - puppetdb integration (improve the inventory service)
 - node classifier integration

[1]: inventory.md
[2]: uri.md
[3]: association.md
