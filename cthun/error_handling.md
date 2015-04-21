Error Messages
===

When an invalid message has been sent to a server or when [association][1] errors
occur the server must respond with an error message.

Error messages are described by the following json-schema:

```
{
    "properties" : {
        "id" : { "type" : "string" },
        "message_type" : { "type" : "string" },
        "description" : { "type" : "string" }
    },
    "required" : ["id", "message_type", "description"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| id | string | ID of the received message that originated the error
| data_schema | string | data_schema of the received message that originated the error
| description | string | error description

An error message must have the envelope *message_type* entry equal to:

`http://puppetlabs.com/errorschema`

[1]: association.md
