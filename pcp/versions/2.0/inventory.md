Inventory
===

The inventory service provides the list of nodes of a given type currently
registered in the messaging fabric.

Message Flow
---

A client node that wants an inventory list of a PCP broker must 1) be
connected to it (refer to [sessions][1]) and 2) send an *inventory request*
message to a PCP broker.

If the request is processed successfully, the broker will reply with an
*inventory response* message. Additionally, if the inventory request includes a
`subscribe` flag set to `true`, the broker will send the client an
*inventory update* message when the broker's inventory changes.


Inventory Messages
---

PCP specifies three types of inventory messages: inventory requests,
inventory responses, and inventory updates.

For inventory requests:
* The *message_type* field must equal `http://puppetlabs.com/inventory_request`
* The *data* field must conform to the following json-schema:
```
{
  "properties" : {
    "query" : { "type" : "array",
      "items" : { "type" : "string",
                  "pattern" : "^pcp://[^/]*/[^/]+$" }
    },
    "subscribe": { "type" : "boolean" }
  },
  "required" : ["query"],
  "additionalProperties" : false
}
```
* When provided, the *subscribe* flag must be set to `true` or `false`.

For inventory responses:
* The *message_type* field must equal `http://puppetlabs.com/inventory_response`
* The *in_reply_to* field must contain the ID of the inventory request.
* The *data* field must conform to the following json-schema:

```
{
  "properties": {
    "uris" : { "type" : "array",
               "items" : { "type" : "string",
                           "pattern" : "^pcp://[^/]*/[^/]+$" }
    },
    "version": { "type": "integer"}
  },
  "required" : ["uris", "version"],
  "additionalProperties" : false
}
```

In reference to the schema above, the `version` field is an interger equal to
the number of changes (client connections and disconnections) that have
occurred on the inventory since broker startup.

For inventory updates:
* The *message_type* field must equal `http://puppetlabs.com/inventory_update`
* The *data* field must be a JSON map conforming to the following json-schema:
  ```
{
  "properties": {
    "version": { "type": "integer"},
    "changes": { "type": "array",
                 "items": {
                  "properties": {
                    "client": { "type": "string",
                                "pattern": "^pcp://[^/]*/[^/]+$" },
                    "change": { "type": "integer",
                                "description": "A representation of a connection or disconnection. 1 for connection, -1 for disconnection"}
                  }
                }
    }
  }
  "required": ["changes", "version"],
  "additionalProperties": false
}
```

In the schema above, the `version` field is as described in the "inventory
responses" section. The `changes` field is specified as an array so that
inventory updates may batch multiple changes to reduce network chatter.
Batching is optional and the batching stragegy is up to the implementation. The
version reflected in an inventory update message must equal the inventory
version *prior* to the accompanying list of changes.


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
The *message_type* field of the JSON data `content` must be equal to
`http://puppetlabs.com/inventory_request`.

Server Operation
---

* The broker must expand the wildcard and determine the list of URI's of all matching
clients.

* If the `subscribe` flag is set to `false`, no inventory subscription will be
  initiated and any current subscription will be terminated.

[1]: session.md
[2]: error_handling.md
