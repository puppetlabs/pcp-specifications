Error Messages
===

When an invalid message has been sent to a server the server must respond with
an error message.

Error messages are described by the following json-schema:

```
{
    "properties" : {
        "id" : { "type" : "string" },
        "description" : { "type" : "string" }
    },
    "required" : ["description"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| id | string | ID of the received message that originated the error (optional)
| description | string | error description

An error message must have the envelope *message_type* entry equal to:

`http://puppetlabs.com/error_message`

[1]: association.md
