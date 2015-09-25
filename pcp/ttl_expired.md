TTL Expired
===

When a server processes an expired message, it must send a *ttl_expired* message
back to its sender and not attempt to deliver it. The message expiration is
indicated in the *expires* entry of the its envelope.

A message expiration may happen, for example, as a consequece of a series of
failed delivery attempts:

```
    client A                    server S                    client B
       |                           |                           |
       |        1 message          |                           |
       |-------------------------->| 2                         |
       |                           |        3 message          |
       |                           |-------------X             |
       |                           |        4 message          |
       |                           |---------------------X     |
       |                           |                           |
       |       5 ttl_expired       |                           |
       |<--------------------------|                           |
       |                           |                           |
```

Client A sends a message to client B through server S (1). S processes (2) and
tries to deliver it to B, but the message is dropped at some point in the
sending queue (3). S tries to resend the message, but it fails again (4). In the
meantime the message TTL did expire; as a consequence S sends a *ttl_expired*
message back to A (5).

ttl_expired messages must have the envelope *message_type* equal to
`http://puppetlabs.com/ttl_expired`.

The data content is described by the following json-schema:

```
{
    "properties" : {
        "id" : { "type" : "string" },
    },
    "required" : ["id"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| id | string | ID of the expired message

[1]: delivery.md