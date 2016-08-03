Session Association
===

PCP clients must associate their underlying wire layer connection with a broker
before they can use PCP capabilities. Session Association is the process of
establishing a connection between a client and broker and the broker associating
a client URI with that connection.

This is done by completing a successful _associate_ transaction.

Association Message Flow
---

A client that wants to establish a session with a broker must 1) establish the
underlying wire layer connection (e.g. WebSocket) and 2) send an *Associate Request*
message to that PCP broker. The broker will then 3) reply with an
*Associate Response*, indicating the outcome of the Session Association.

We describe how the broker must handle the Session Association in the
[Broker Operation](#broker-operation)) section below.

Associate Messages
---

*Associate Request* and *Response* messages must have the envelope *message_type*
entry respectively equal to `http://puppetlabs.com/associate_request` and
`http://puppetlabs.com/associate_response`. Associate Requests don't
have a Data Chunk, whereas Associate Responses are described by the following
JSON schema:

```json
{
    "properties" : {
        "id" : { "type" : "string" },
        "success" : { "type" : "bool" },
        "reason" : { "type" : "string" }
    },
    "required" : [ "id", "success" ],
    "additionalProperties" : false
}
```
These fields are described as:

| name | type | description
|------|------|------------
| id | string | message id of the related request
| success | boolean | whether the session association was successful
| reason | string | reason why a association failed (optional)


Broker Operation
---

#### During Session Association

If the broker receives a malformed PCP message during the Session Association
with a given client (i.e. the client's connection is not associated yet), it
must reply with an *Error Message*, as described in the [error handling][2]
section.

The broker must drop all PCP messages other than *Associate Requests* that are
sent by not associated clients.

The broker must authenticate *Associate Requests* as any other PCP message. That
is accomplished by extracting the common name from the client SSL certificate
(the one used to establish the underlying secure connection) and then matching
it against the node identity indicated in the *sender* URI of the request's
envelope.

The broker may also employ a mechanism for authorizing clients' association; if
so, such mechanism is entirely implementation-specific.

In case of authentication or authorization failure, the broker must reply with
an unsuccessful (*success* set to false) *Associate Response* and close the
underlying wire layer connection.

When processing an expired *Associate Request*, the broker must reply with a
*TTL expired*, as described in the relevant [section][3].

If the broker receives an *Associate Request* from a client that is already
associated on a different underlying wire layer connection, the broker must
supersede the old connection if the new request succeeds. In practice, if the
broker authenticates (and authorizes) the new *Associate Request*, it must reply
with a successful *Associate Response* and close the old connection.

The broker must ensure that the Session Association is completed before a given
time point after the underlying wire layer connection is established; such
timeout is implementation-specific.

#### After a successful Session Association

The broker must keep track of the associated clients and must be
able to obtain the current status of their wire connections. That is necessary
in order to perform delivery and inventory operations.

If an *Associate Request* is sent by a client that is already associated, the
broker must reply with a successful *Associate Response*; the client should
remain associated with the broker.

In case the underlying wire connection drops, the broker must guarantee that the
client will be disassociated, meaning that the client URI will no longer map to
its underlying connection (e.g. WebSocket connection).

Client Operation
---

Clients must be able to process *Association Responses*, *Error Messages*, and
*TTL Expired* messages in the context of the _associate_ transaction; clients
must change their status accordingly and retry the Session Association if
necessary (after a network error or timeout, for instance). A client must
consider its session with the broker as associated only after receiving a
successful *Associate Response* (with Data Content's *success* flagged).

During Session Association, a client should correlate received *Error Message*
and *TTL Expired* messages with the *Associate Request* it sent; that should be
done by matching the request's *ID* with the one indicated in the broker's
reply.

In case the broker replies with an *Error Message* after a broken
*Associate Request*, the reply may not include *id* of the broken request so
that it's not possible for the client to correlate such message with the
*Associate Request*; in that case, the client must retry the _associate_
transaction.

In case a client wants a persistent session, it should monitor the
state of the wire layer connection and attempt to re-associate if necessary.

[1]: uri.md
[2]: error_handling.md
[3]: ttl_expired.md
