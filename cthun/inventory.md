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
entry respectively equal to `http://puppetlabs.com/inventoryrequestschema` and
`http://puppetlabs.com/inventoryresponseschema`.

<!-- TODO(ale): update this in case we decide to enable regex queries; it only
    describes wildcarded URIs queries, as supported so far -->

For an inventory request, the data `content` must be a JSON document with a
single *query* entry that specifies the types of nodes that the requester is
interested in; *query* must be an array of strings, where each item is a
wildcarded Cthun URI.

For inventory responses, the data `content` must still be a JSON document with
an unique *endpoints* entry, an array of strings where each item is a Cthun URI;
*endpoints* is the inventory list requested by the query.

Error Handling
---

<!-- TODO(ale): the server must always reply to an inventory request? -->

In case of an error during the processing of the inventory list, the server must
reply with an error message as described in the [error handling][2] section.
The *data_schema* entry of the JSON data `content` must be equal to the
inventory request one mentioned above.

Server Operation
---

The server must determine the list of client nodes that are currently registered
in the messaging fabric, with type equal to the one specified in the request
*query* entry.

[1]: registration.md
[2]: error_handling.md
