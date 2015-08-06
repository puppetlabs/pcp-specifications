Destination Report
===

Destination report messages containin the list of URIs of the receivers to
which a given message will be sent by a server. The following figure explains
that exchange by showing the workflow of a message delivery.
Client A sends a message to client B through server S; such message has the
*desitnation_report* flag set (1). As a consequence of that, after processing
the message (2), S replies to A with a destination report (3), before delivering
the message to client B (4).

```
    client A                    server S                    client B
       |                           |                           |
       |        1 message          |                           |
       |-------------------------->|                           |
       |                           | 2                         |
       |   3 destination report    |                           |
       |<--------------------------|                           |
       |                           |                           |
       |                           |        4 message          |
       |                           |-------------------------->|

```

Destination report messages must have the envelope *message_type*
equal to `http://puppetlabs.com/destination_report`.

The destination report is described by the following json-schema:

```
{
    "properties" : {
        "id" : { "type" : "string" },
        "targets" : { "type" : "array",
                      "items" : { "type" : "string",
                                  "pattern" : "^cth://[^/]*/[^/]+$" }}
    },
    "required" : ["id", "targets"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| id | string | ID of the message with the *destination_report* flag set
| targets | array | URIs of the recipient clients
