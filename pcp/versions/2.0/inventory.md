Inventory
===

The inventory service provides the list of nodes of a given type currently
registered in the messaging fabric.

Message Flow
---

A client node that wants an inventory list of a PCP broker must 1) be
connected to it (refer to [sessions][1]) and 2) send an inventory
request message to a PCP broker.

In case the request is processed successfully, the broker will reply with an
inventory response message.

Inventory Messages
---

Inventory request and response messages must have the *message_type*
entry respectively equal to `http://puppetlabs.com/inventory_request` and
`http://puppetlabs.com/inventory_response`.

The *data* entry of an inventory request message is an array of URIs,
described by the following json-schema:

```
{
    "type" : "array",
    "items" : { "type" : "string",
                "pattern" : "^pcp://[^/]*/[^/]+$" }
}
```

The *data* entry of an inventory response message is an array of URIs,
described by the same json-schema.

Wildcard URI's
---

When constructing URI's for inventory requests both the *Common name* and
*Client type* fields can be replaced by a wild card. When the broker processes
such a URI it must expand the wild card to include all clients.

A URI that expands to all types of clients that present the *Common name* _bob.com_
will look like:

`pcp://bob.com/*`

A URI that expands to all clients that define their type as _agent_ will look like:

`pcp://*/agent`

A URI that expands to all clients will look like:

`pcp://*/*`

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

[1]: session.md
[2]: error_handling.md
