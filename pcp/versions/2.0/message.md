PCP Messages
===

PCP messages are a JSON object.

The content of an envelope is a JSON document that must match the following
JSON schema:

```
{
    "properties" : {
        "id" : { "type" : string" },
        "message_type" : { "type" : "string" },
        "target" : { "type" : "string",
                     "pattern" : "^pcp://[^/]*/[^/]+$" },
        "sender" : { "type" : "string",
                     "pattern" : "^pcp://[^/]*/[^/]+$" },
        "in_reply_to" : { "type" : "string" }
        "data" : {}
    },
    "required" : ["id", "message_type"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| id | string | string representation of 128-bit unique identifier of the message
| message_type | string | schema name that identifies the type of message and the schema used to validate its data field (if any)
| target | string | PCP URI indicating the recipient
| sender | string | PCP URI indicating the endpoint node (broker or client node) that sent the message
| in_reply_to | string | the ID of the received message to which this is a reply |
| data | any | the data field, containing an arbitrary json type determined by the *message_type* (most likely string, array, or object)
