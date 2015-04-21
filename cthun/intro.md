Introduction
===

*TODO(ale):* write abstract
    Messaging fabric - problem description ...
    What Cthun solves - solution description ...
    MCO and other messaging solutions; differences, improvements ...
    What is the Cthun communications layer ...

Actors
---

#### Server

The Cthun server is the entity that provides inter client communication. It
has the following capabilities.

##### Message Delivery

Messages are delivered in a point to point manner. A client sends a message
to a server and specifies one or more recipients. The server will attempt to
deliver the message to its intended targets. If the message cannot be delivered
immediately,the server will attempt to deliver a message to its intended
recipients until the message has expired.

##### Inventory

This capability depends on an naive internal inventory service which, when
queried, will return a list of URI's of clients that have been registered with
the server.

(*Caveat*) The current implementation of the inventory service is purposefully
simplistic. When the requirements have been finalised the inventory service
may depend on puppetdb.

#### Clients

Clients are addressable entities, connected to a Cthun server which can send
and receive messages.

What is covered by Cthun specifications
---

The Cthun specification define the communications protocol used by the nodes
within a Cthun framework. It covers the message syntax, Cthun URI format, the
semantics of the services described above, and error handling.

*TODO(ale):* protocol specs versioning

What will come
---

 - RBAC
 - distributed fabric / HA (multi server)
 - puppetdb integration (improve the inventory service)
 - node classifier integration

[3]: https://github.com/puppetlabs/cthun-agent
[4]: https://github.com/puppetlabs/clj-cthun-client
