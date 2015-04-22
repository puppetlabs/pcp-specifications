Inventory
===

The inventory service provides the list of nodes of a given type currently
registered in the messaging fabric.

Message Flow
---

A client node that wants an inventory list of a Cthun framework must 1) be
registered to it (refer to the [login][1] transaction) and 2) send an inventory
request message to a Cthun server.

In case the request is processed successfully, the server will reply with an
inventory response message.

Inventory Messages
---

Inventory request and response messages must have the envelope *data_schema*
entry respectively equal to `http://puppetlabs.com/inventory_request` and
`http://puppetlabs.com/inventory_response`.

*TODO(ale):* update this in case we decide to enable regex queries; it only
    describes wildcarded URIs queries, as supported so far

An inventory request message is decribed by the following json-schema:

```
{
    "properties" : {
        "query" : { "type" : "string",
                    "pattern" : "^cth://[^/]*/[^/]+$" },

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
                               "pattern" : "^cth://[^/]*/[^/]+$" }
        }
    },
    "required" : ["uris"],
    "additionalProperties" : false
}
```

Error Handling
---

*DISCUSS(ale):* must the server always reply to an inventory request?

In case of an error during the processing of the inventory list, the server must
reply with an error message as described in the [error handling][2] section.
The *message_type* entry of the JSON data `content` must be equal to the
inventory request one mentioned above.

Server Operation
---

The server must expand the wildcard and determine the list of URI's of all matching
clients.

[1]: registration.md
[2]: error_handling.md
