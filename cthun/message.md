Cthun Messages
===

Cthun messages consists of a `version` field followed by a sequence of chunks.
The format of a message is:

*DISCUSS(ale):* using "data" for the payload chunk may be confusing; we have two
    layers for which we may want to discriminate some stuff as "data": the
    overall message and a given chunk. Alternatives: "body", "payload"

```
    version | envelope chunk | [data chunk] | [debug chunk] | ... | [debug chunk]
```

*HERE(ale):* the versioning section in README.md should be consistent with
    the version field

*DISCUSS(ale):* we shouldn't have a use case where multiple data chunks are
    required in a message; besides, having  a single data_schema entry in the
    envelope imposes a constraint on the data chunk(s)

The `version` field is 1 byte large and indicates the version of the Cthun
specification used by the message.
All messages must have one and only one envelope chunk.
Data and debug chunks are optional; a message can have one data chunk at most
and any number of debug chunks.

Chunk Format
---

*DISCUSS(ale):* defining the chunk format; name for the chunk content:
    "content"? "body"?

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
order.

The `content` field has a variable length that must not be larger than 2.1 GB
since it must be represented by the 4 bytes of the `size` field. There are no
restrictions for the type of the stored value nor for its representation.

Envelope Chunk
---

*DISCUSS(ale):* do we need a UTF-8 constraint?

Every Cthun message must contain one and only one envelope chunk.

The `content` of an envelope is a JSON document that must have the following
entries:

*TODO(ale):* it would be nice to have the envelope JSON schema here

*DISCUSS(ale):* consider using message_type instead of data_schema and leave it
    up to the implementation to associate the message schema (apart from the
    login, that is defined here); mesasge_type would make the intent of this
    envelope field clearer

*TODO(ale):* check message id 128-bit UUID constraint

| name | type | description
|------|------|------------
| id | string | 128-bit unique identifier of the message
| data_schema | string | schema name that identifies the type of message and the schema used to validate its data chunk (if any)
| expires | string | time entry in ISO8601 format indicating when the message is no longer valid anymore
| endpoints | array | list of Cthun URIs (string items) indicating the recipients
| sender | string | Cthun URI indicating the endpoint node (server or client node) that sent the message

Data Chunk
---

*TODO(ale):* check this - as stated above, we can have one data chunk at most

Data chunks are optional, but a message must not contain more than one. The type
and and structure of the data chunk `content` is determined by the *data_schema*
entry of the envelope.

Debug Chunk
---

*DISCUSS(ale):* can we specify the debug data content format here? Perhaps, in a
    separate section?

A message may have zero or multiple debug chunks. Debug chunks are used by Cthun
server to add processing information. The only debug format specified by this
document regards the *hops* entry (refer to the [delivery][1] section).

[1]: delivery.md
