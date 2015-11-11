Session Association
===

PCP clients must associate their session with a broker before they can use
its capabilities. Session Association is the process of establishing a connection
between a client and broker and the broker associating a client URI with that connection.

This is done by completing a successful _associate_ transaction.

Association Message Flow
---

A client that wants to establish a session with a broker must 1) establish the
underlying wire layer connection (e.g. WebSocket) and 2) send a *associate request*
message to that PCP broker.

In case the request is processed successfully, the broker will reply with a
*associate response* message. The broker may ignore the request or reply with an
*error message* in case of node identity mismatch (see
[Broker Operation](#broker-operation)).

Should a session already be associated with the given client URI, the broker
should disconnect the older connection, and consider the newly-associated
session to be authoritative for that URI.

Associate Messages
---

*Associate request* and *response* messages must have the envelope *message_type*
entry respectively equal to `http://puppetlabs.com/associate_request` and
`http://puppetlabs.com/associate_response`. An Associate request message does not
require a Data Chunk.

Association response messages are described by the following JSON schema:

```json
{
    "properties" : {
        "id" : { "type" : "string" },
        "success" : { "type" : "bool" },
        "reason" : { "type" : "string" }
    },
    "required" : [ "id", "success" ],
    "additionalProperties" : false
}
```
These fields are described as:
          
| name | type | description
|------|------|------------
| id | string | message id of the related request
| success | boolean | whether the session association was successful
| reason | string | reason why a association failed (optional)

Error Message
---

Association error messages must conform with the format described in the
[error handling][2] section. The *message_type* entry of the JSON data `content`
must be equal to the respective association schema, mentioned above.

Broker Operation
---

Once an association request is received, the broker must extract the common name
from the client SSL certificate, the one used to establish the underlying connection.
The broker will then match the certificate ID against the node identity indicated in
the *sender* URI of the request envelope. In case of mismatch, the broker must
reply with an error message, as described in the above section.

In case the underlying wire connection drops, the broker must guarantee that the
client will be disassociated, meaning that the client URI will no longer map to
its underlying connection (e.g. WebSocket connection).

The broker must keep track of the associated clients and must be
able to obtain the current status of their wire connections. That is necessary
in order to perform delivery and inventory operations.

Client Operation
---

Clients must be able to process association responses and change their status
accordingly.

In case a client wants a persistent session, it should monitor the
state of the wire layer connection and attempt to re-establish it if necessary.

[1]: uri.md
[2]: error_handling.md
