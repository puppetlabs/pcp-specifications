Inventory
===

The inventory service provides the list of nodes of a given type currently
registered in the messaging fabric.

Message Flow
---

A client node that wants an inventory list of a PCP broker must 1) establish
a session with that broker (refer to the [association][1] section) and 2) send
an inventory request message to it.

In case the request is processed successfully, the broker will reply with an
inventory response message.

Inventory Messages
---

Inventory request and response messages must have the envelope *data_schema*
entry respectively equal to `http://puppetlabs.com/inventory_request` and
`http://puppetlabs.com/inventory_response`.

An inventory request message is described by the following json-schema:

```
{
    "properties" : {
        "query" : { "type" : "array",
                    "items" : { "type" : "string",
                                "pattern" : "^pcp://[^/]*/[^/]+$" }
        }
    },
    "required" : ["query"],
    "additionalProperties" : false
}
```

An inventory response message is decribed by the following json-schema:

```
{
    "properties" : {
        "uris" : { "type" : "array",
                   "items" : { "type" : "string",
                               "pattern" : "^pcp://[^/]*/[^/]+$" }
        }
    },
    "required" : ["uris"],
    "additionalProperties" : false
}
```

Error Handling
---

In case of an error during the processing of the inventory list, the broker must
reply with an error message as described in the [error handling][2] section.
The *message_type* entry of the JSON data `content` must be equal to the
inventory request one mentioned above.

Server Operation
---

The broker must expand the wildcard and determine the list of URI's of all matching
clients.

[1]: association.md
[2]: error_handling.md
