Delivery of Client Node Messages
===

<!-- TODO(ale): consider changing the name of this section -->

In this section, we define the behavior of Cthun servers when delivering client
node messages.

Let us consider the following example of a request-response transaction between
a pair of client nodes; the message flow is shown in the figure below:

```
    client A                    server S                    client B
       |                           |                           |
       |        1 request          |                           |
       |-------------------------->|                           |
       |                           | 2                         |
       |                           |        3 request          |
       |                           |-------------------------->|
       |                           |                           |
       |                           |                           | 4
       |                           |        5 response         |
       |                           |<--------------------------|
       |                         6 |                           |
       |        7 response         |                           |
       |<--------------------------|                           |
       |                           |                           |

```

Both **A** and **B** are client nodes registered in the server **S**.

**A** wants to sends a request message to **B** as part of a C&C transaction.
The C&C request *data_schema* has been previously defined and it is known to
both client endpoints. The the data `content` of the request message conforms
to the related schema definitions. Note that such transaction is not part of the
Cthun specifications; it just relies on the messaging fabric capabilities.

The request message sent by **A** includes only the Cthun URI of **B** in the
*endpoints* entry of the envelope. **A** forwards such message to **S** which
takes care of its delivery (1).

**S** parses the request message. Both the overall message and the `envelope`
chunk have a proper format. Also, the message is not expired, as indicated in
the envelope *expires* entry. Thus, the message is a valid one.

**S** inspects the *data_schema* entry of the `envelope`. It determines that the
message must be delivered to the client node **B** since it is not a login nor
an inventory request. Note that [registration][1] and [inventory][2] are the
only existent client-server transactions, both defined in this document.

**B** is currently registered to **S**, so **S** can proceed with the delivery
process; it adds a debug chunk with its *hops* entries (see the
[Server Operation](#server_operation) section below) to the request message
and forwards it directly to **B** (2). The wire layer connection is currently up
and the message is successfully delivered to **B** (3).

Once **B** receives the request message, it parses it and inspects the
*data_schema* entry. **B**'s client implementation is able to process the data
that uses the known C&C schema format. In this case, the process succeeds and a
response message is created, with a C&C response *data_schema* entry. The
message is addressed to **A** and forwarded to **S** (4).

The response message follows the same steps done by the request, but this time
the message goes back to **A**.

In the above example, it is clear that Cthun servers must perform a number of
operations to deliver a message. Below, we will formalize theier expected
behavior.

<a name="server_operation"/>
Server Operation
---

<!-- TODO(ale): check everything here -->

The server must discard a message in case of:

 - invalid format (see the [message][3] section)
 - invalid envelope `content` (see the [message][3] section)
 - expiration, as indicated in the *expires* envelope entry
 - none of the recipients of the *endpoints* envelope entry is registered in the
 message fabric; this include the case of wildcarded Cthun URIs with unknown
 node type

<!-- TODO(ale): check error cases -->

In none of the above cases the server should reply with an error message.

<!-- TODO(ale): mandatory or optional? -->

When a server processes a client message, it may add a debug chunk with a JSON
`content` containing a *hops* entry that indicates the route the message has
taken so far in the message fabric. The *hops* entry is an array with the
following items:

| name | type | description
|------|------|------------
| server | string | Cthun URI of the server that performed the processing hop
| time | string | time entry in ISO8601 format indicating when the processing took place
| stage | string | type of processing (e.g. "accepted", "delivered")

<!-- TODO(ale): add inter-server routing specs once we implement distribution
-->

[1]: registration.md
[2]: inventory.md
[3]: message.md
