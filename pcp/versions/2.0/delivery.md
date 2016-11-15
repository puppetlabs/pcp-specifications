Message Delivery
===

In this section, we define the behavior of PCP broker when delivering messages.

### Client to client messages

Consider the following example. Client A and client C are connected to a broker B.
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

**A** sends a message to **B**, specifying a URI in the message's
*target* field, _pcp://client_b/agent_ (1).

**B** receives the message from **A**, parses it and determines that it is valid.
**B** inspects the *target* field in the message and prepares to deliver
the message to the supplied URI, _pcp://client_b/agent_ in this particular case (2).

**B** determines that the URI points to **C**. It then delivers the message to
**C** (3).

### Client to broker messages

Consider the following example. Client A is connected to a broker B. A is identified
by the URI _pcp://client_a/controller_. A wishes to perform an inventory query and
thus needs to send a message to B (refer to the [inventory][1] section).

```
    client A                    broker B
       |                           |
       |        1 message          |
       |-------------------------->|
       |                           | 2
       |        3 message          |
       |<--------------------------|

```

**A** sends a message to **B**, omitting the *target* field (1).

**B** receives the message from **A**, parses it and determines that it is valid.
**B** inspects the *target* field in the message and determines that
the message was directed at itself.
**B** performs a broker specific task, determined by the value supplied in the
*message_type* field in the [message][3] (2).
**B** constructs a resulting message and sends it to **A** using the URI supplied
in the original message's *sender* field, _pcp://client_a/contoller_ (3).

### Broker Operation

From the above examples, it is clear that PCP Brokers must perform a number of
operations to deliver a message. In this section we describe the operation
requirements for the broker.

#### Delivery Authorization

The broker must notify the sender with an [unauthorized][4] message in case
a sender's message fails authorization. The broker must not deliver the
message in such case.

#### Error handling

The broker must respond to a client with an [error message][2] in case:

- the message cannot be parsed (see [message][3])
- the message does not match the message schema (see [message][3])
- the recipient of the *target* field is not registered in the broker

[1]: inventory.md
[2]: error_handling.md
[3]: message.md
[4]: unauthorized.md
