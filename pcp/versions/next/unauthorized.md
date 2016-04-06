Unauthorized
===

When a broker declines to deliver a message because it fails authorization,
it must send an *unauthorized* message back to the original message sender.
The broker must not deliver the message to any clients in such case, even
if the message delivery to a subset of the original targets is authorized.

A message is only delivered by the broker if it passes authorization. In
the opposite case the situation is quite simple:

```
    client A                    broker B
       |                           |
       |         1 message         |
       |-------------------------->| 2
       |                           |
       |      3 unauthorized       |
       |<--------------------------|
       |                           |
       |                           |
```

Client A sends a message through the broker B (1). B receives the message,
checks its authorization, which fails (2). As a result B sends the
*unauthorized* message (3) to A.

*unauthorized* messages must have the envelope *message_type* equal to
`http://puppetlabs.com/unauthorized`.
The *in_reply_to* field of the unauthorized message envelope contains
the ID of the message which failed the authorization.

There is no data chunk in the message.
