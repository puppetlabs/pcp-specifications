Message Delivery
===

In this section, we define the behavior of Cthun servers when delivering messages.

### Client to client messages

Consider the following example. client A and client B are connected to a server S.
A is identified by the URI _cth://client_a/controller_ and B by _cth://client_b/agent_.
A wishes to send a [message][3] via S to B.

```
    client A                    server S                    client B
       |                           |                           |
       |        1 message          |                           |
       |-------------------------->|                           |
       |                           | 2                         |
       |                           |        3 message          |
       |                           |-------------------------->|

```

**A** sends a message to **S**, specifying a single URI in the message envelope's
*targets* field, _cth://client_b/agent_ (1).

**S** receives the message from **A**, parses it and determines that it is valid.
**S** inspects the *targets* field in the message envelope
and prepares to deliver the message to each of the supplied URIs,
_cth://client_b/agent_ in this particular case (2).

**S** determines that the URI points to **B**. It then delivers the message to
**B** (3).

### Client to client messages using wildcards

Consider the following example. client A, client B and client C are connected to
a server S. A is identified by the URI _cth://client_a/controller_, B by
_cth://client_b/agent_ and C by _cth://client_c/agent_. A wishes to send a message
to all clients of type agent.

```
    client A                server S                 client B                 client C
       |                       |                       |                        |
       |        1 message      |                       |                        |
       |---------------------->|                       |                        |
       |                       | 2                     |                        |
       |                       |        3 message      |                        |
       |                       |---------------------->|                        |
       |                       |                                                |
       |                       |                     4 message                  |
       |                       |----------------------------------------------->|
       |                                                                        |

```

**A** sends a message to **S**, specifying a single URI in the message envelope's
*targets* field, _cth://*/agent_. (1)

**S** receives the message from **A**, parses it and determines that it is valid.
**S** inspects the *targets* field in the message envelope and determines that
the URI _cth://*/agent_ contains a wildcard. The wildcard is expanded to include
all clients that have specified their type as *agent*. (2)

**S** determines that the expanded URI points to both **B** and **C**. It then
delivers one copy of the message to both **B** and **C**. (3), (4)

### Client to server messages

Consider the following example. Client A is connected to a server S. A is identified
by the URI _cth://client_a/controller_. A wishes to perform an inventory query and
thus needs to send a message to S (refer to the [inventory][2] section).

```
    client A                    server S
       |                           |
       |        1 message          |
       |-------------------------->|
       |                           | 2
       |        3 message          |
       |<--------------------------|

```

**A** sends a message to **S**, specifying a single URI in the message envelope's
*targets* field, _cth:///server_ (1).

**S** receives the message from **A**, parses it and determines that it is valid.
**S** inspects the *targets* field in the message envelope and determines that
the message was directed at itself.
**S** performs a server specific task, determined by the value supplied in the
*message_type* field in the [message][3] envelope (2).
**S** constructs a resulting message and sends it to **A** using the URI supplied
in the original message's *sender* field, _cth://client_a/contoller_ (3).

### Server Operation

From the above examples, it is clear that Cthun servers must perform a number of
operations to deliver a message. In this section we describe the operation
requirements for the server.

- the message cannot be parsed (see [message][3])
- the message envelope does not match the envelope schema (see [message][3])

The server must discard a message in case where:

 - a message expired, as indicated in the *expires* envelope entry

The server will not take any action in the case where:

 - none of the recipients of the *targets* envelope entry is registered in the
 server; this include the case of wildcarded Cthun URIs that expand into nothing


When a server processes a client message, it may add a debug chunk with a JSON
`content` containing a *hops* entry that indicates the route the message has
taken so far in the message fabric. The *hops* entry is an array with the
following items:

| name | type | description
|------|------|------------
| server | string | Cthun URI of the server that performed the processing hop
| time | string | time entry in ISO8601 format indicating when the processing took place
| stage | string | type of processing (e.g. "accepted", "delivered")

When a server processes a client message and the *destination_report* flag is set
the server must respond to the client with a message containing the list of URIs
it will be sending the message to in the Data Chunk.

If we look at the example described in [Client to client messages](#client-to-client-messages),
the flow of messages when the *destination_report* flag has been set will look as follows.



```
    client A                    server S                    client B
       |                           |                           |
       |        1 message          |                           |
       |-------------------------->|                           |
       |                           | 2                         |
       |   3 destination report    |                           |
       |<--------------------------|                           |
       |                           |                           |
       |                           |        4 message          |
       |                           |-------------------------->|

```

The destination report is described by the following json-schema:

```
{
    "properties" : {
        "id" : { "type" : "string" },
        "targets" : { "type" : "array",
                      "items" : { "type" : "string",
                                  "pattern" : "^cth://[^/]*/[^/]+$" }}
    },
    "required" : ["id", "targets"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| id | string | ID of the message with the *destination_report* flag set
| targets | array | URIs of the recipient clients

*TODO(ale):* add inter-server routing specs, if necessary once we implement
      distribution

[1]: association.md
[2]: inventory.md
[3]: message.md
[4]: error_handling.md
