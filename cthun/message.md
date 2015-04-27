Cthun Messages
===

Cthun messages use a chunked approach and are defined as:

```
    version | envelope chunk | [data chunk] | [debug chunk] | ... | [debug chunk]
```

### Version

*HERE(ale):* the versioning section in README.md should be consistent with
the version field

The `version` field is 1 byte large and indicates the version of the Cthun
specification used by the message.


### Envelope Chunk

Every Cthun message must contain one and only one envelope chunk.

The [content](#chunk-format) of an envelope is a JSON document that must match the following
json-schema:

```
{
    "properties" : {
        "id" : { "type" : string" },
        "message_type" : { "type" : "string" },
        "expires" : {"type" : "string" },
        "targets" : { "type" : "array",
                      "items" : { "type" : "string",
                                  "pattern" : "^cth://[^/]*/[^/]+$" }},
        "sender" : { "type" : "string",
                     "pattern" : "^cth://[^/]*/[^/]+$" },
        "destination_report" : {"type" : "bool" }
    },
    "required" : ["id", "message_type", "expires", "targets", "sender"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| id | string | string representation of 128-bit unique identifier of the message
| message_type | string | schema name that identifies the type of message and the schema used to validate its data chunk (if any)
| expires | string | time entry in ISO8601 format indicating when the message is no longer valid anymore
| targets | array | list of Cthun URIs (string items) indicating the recipients
| sender | string | Cthun URI indicating the endpoint node (server or client node) that sent the message
| destination_report | bool | If set to true server must respond with a list of URI's that include all targets for a message |


### Data Chunk

Data chunks are optional, but a message must not contain more than one. The type
and and structure of the data chunk [content](#chunk-format) is determined by the *message_type*
entry of the envelope.

### Debug Chunk

A message may have zero or multiple debug chunks. Debug chunks are appended to the
back of the message by either clients or servers, and their [content](#chunk-format)
is described by the following json-schema.

```
{
    "type" : "object",
    "properties" : {
        "hops" : {
            "type" : "array",
            "items" : {
                "type": "object",
                "properties" : {
                    "server" : {
                        "type" : "string",
                        "pattern" : "^cth://[^/]*/[^/]+$"
                    },
                    "stage" : { "type" : "string" },
                    "time" : { "type" : "string" }
                },
                "required" : ["server", "time"],
                "additionalProperties" : false
            }
        }
    }
    "required": ["hops"],
    "additionalProperties" : false
}
```

### Chunk Format

Chunks have variable size and use the following format:

```
    descriptor | size | content
```

*TODO(ale):* define descriptor flags of the higher bits (compression, content
    type)

The `descriptor` field is 1 byte large and describes the chunk.
The high 4 bits are reserved. The lower 4 bits indicate the chunk type; the list
of admissible values and associated types is:

 - 0001: envelope
 - 0010: data
 - 0011: debug

The `size` field is 4 bytes large and provides the length in bytes of the chunk
`content`. The size value must be a signed integer represented in big-endian
order. This limits chunk content to a maximum size of 2.1 GB.

The `content` field is of variable length which is determined by the `size` field.
There are no restrictions for the type of the stored value nor for its representation.



[1]: delivery.md
