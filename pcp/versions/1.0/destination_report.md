Destination Report
===

Destination report messages containing the list of URIs of the receivers to
which a given message will be sent by a broker. The following figure explains
that exchange by showing the workflow of a message delivery.
Client A sends a message to client C through broker B; such message has the
*desitnation_report* flag set (1). As a consequence of that, after processing
the message (2), B replies to A with a destination report (3), before delivering
the message to client C (4).

```
    client A                    server B                    client C
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
                                  "pattern" : "^pcp://[^/]*/[^/]+$" }}
    },
    "required" : ["id", "targets"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| id | string | ID of the message with the *destination_report* flag set
| targets | array | URIs of the recipient clients
