Message Delivery
===

In this section, we define the behavior of PCP broker when delivering messages.

### Client to client messages

Consider the following example. client A and client C are connected to a broker B.
A is identified by the URI _pcp://client_a/controller_ and C by _pcp://client_b/agent_.
A wishes to send a [message][3] via B to C.

```
    client A                    broker B                    client C
       |                           |                           |
       |        1 message          |                           |
       |-------------------------->|                           |
       |                           | 2                         |
       |                           |        3 message          |
       |                           |-------------------------->|

```

**A** sends a message to **B**, specifying a single URI in the message envelope's
*targets* field, _pcp://client_b/agent_ (1).

**B** receives the message from **A**, parses it and determines that it is valid.
**B** inspects the *targets* field in the message envelope
and prepares to deliver the message to each of the supplied URIs,
_pcp://client_b/agent_ in this particular case (2).

**B** determines that the URI points to **C**. It then delivers the message to
**C** (3).

### Client to client messages using wildcards

Consider the following example. Client A, client C and client D are connected to
a broker B. A is identified by the URI _pcp://client_a/controller_, C by
_pcp://client_b/agent_ and D by _pcp://client_c/agent_. A wishes to send a message
to all clients of type agent.

```
    client A                broker B                 client C                 client D
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

**A** sends a message to **B**, specifying a single URI in the message envelope's
*targets* field, _pcp://*/agent_. (1)

**B** receives the message from **A**, parses it and determines that it is valid.
**B** inspects the *targets* field in the message envelope and determines that
the URI _pcp://*/agent_ contains a wildcard. The wildcard is expanded to include
all clients that have specified their type as *agent*. (2)

**B** determines that the expanded URI points to both **C** and **D**. It then
delivers one copy of the message to both **C** and **D**. (3), (4)

### Client to broker messages

Consider the following example. Client A is connected to a broker B. A is identified
by the URI _pcp://client_a/controller_. A wishes to perform an inventory query and
thus needs to send a message to B (refer to the [inventory][2] section).

```
    client A                    broker B
       |                           |
       |        1 message          |
       |-------------------------->|
       |                           | 2
       |        3 message          |
       |<--------------------------|

```

**A** sends a message to **B**, specifying a single URI in the message envelope's
*targets* field, _pcp:///broker_ (1).

**B** receives the message from **A**, parses it and determines that it is valid.
**B** inspects the *targets* field in the message envelope and determines that
the message was directed at itself.
**B** performs a broker specific task, determined by the value supplied in the
*message_type* field in the [message][3] envelope (2).
**B** constructs a resulting message and sends it to **A** using the URI supplied
in the original message's *sender* field, _pcp://client_a/contoller_ (3).

### Broker Operation

From the above examples, it is clear that PCP Brokers must perform a number of
operations to deliver a message. In this section we describe the operation
requirements for the broker.

#### Destination report

When the broker processes a client message and the *destination_report* flag is
set, it must respond to the client with a [destination report message][5]
containing the list of URIs it will be sending the message to in the Data Chunk.

The *destination_report* flag is ignored in case of [inventory requests][2],
which are addressed directly to the broker.

#### Delivery Authorization

The broker must notify the sender with an [unauthorized][6] message in case
a sender's message fails authorization. The broker must not deliver the
message to any clients in such case, even if the message delivery to a subset
of the original targets is authorized.

#### Message Expiration

The broker must notify the sender of an expired message with a [TTL expired][7]
message.

#### Error handling

The broker must respond to a client with an [error message][4] in case:

- the message cannot be parsed (see [message][3])
- the message envelope does not match the envelope schema (see [message][3])

The broker will not take any action in the case where:

 - none of the recipients of the *targets* envelope entry is registered in the
 broker; this include the case of wildcarded PCP URIs that expand into nothing

#### Debug data

When a broker processes a client message, it may add a debug chunk with a JSON
`content` containing a *hops* entry that indicates the route the message has
taken so far in the message fabric. The *hops* entry is an array with the
following items:

| name | type | description
|------|------|------------
| server | string | PCP URI of the broker that performed the processing hop
| time | string | time entry in ISO8601 format indicating when the processing took place
| stage | string | type of processing (e.g. "accepted", "delivered")

[1]: association.md
[2]: inventory.md
[3]: message.md
[4]: error_handling.md
[5]: destination_report.md
[6]: unauthorized.md
[7]: ttl_expired.md
