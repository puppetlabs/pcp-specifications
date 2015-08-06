Acknowledgement
===

All messages must be acknowledged by their receivers. This is mandatory for both
client and server nodes and is accomplished by sending an acknowledge message
(ack) back to the original sender.
Note that acks are the only messages that must not be acknowledged.

In the figure below, node X sends a message to node Y (1). Y then acknowledges
it by sending an ack message back to X (2). Note that X and Y can be
indifferently clients or servers.

```
    node X                       node Y
       |                           |
       |        1 message          |
       |-------------------------->|
       |          2 ack            |
       |<--------------------------|
       |                           |

```

Ack messages must have the envelope *message_type* equal to
`http://puppetlabs.com/ack`.

The JSON schema of acknowledge messages contains only the envelope *id* of the
message being acknowledged; such value is stored in the *id* entry:

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
| id | string | ID of the message that is being acknowledged

Please refer to the [delivery section][1] for the server operation requirements
about redelivering messages after missed acks. Note that there are no operation
requirements for clients.

In the rest of the specs we will implicitly assume acks being part of all
message exchanges; acks will not be further specified nor illustrated.

[1]: delivery.md
