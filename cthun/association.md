Session Association
===

Cthun clients must associate their session with a server before they can use
its capabilities. Session Association is the process of establishing a connection
between a client and server and the server associating a client URI with that connection.

This is done by completing a successful _associate_ transaction.

Association Message Flow
---

A client that wants to establish a session with a server must 1) establish the
underlying wire layer connection (e.g. WebSocket) and 2) send a *associate request*
message to a Cthun server.

In case the request is processed successfully, the server will reply with a
*associate response* message. The server may ignore the request or reply with an
*error message* in case of node identity mismatch (see
[Server Operation](#server_operation)).

Associate Messages
---

Associate request and response messages must have the envelope *message_type* entry
respectively equal to `http://puppetlabs.com/associate_request` and
`http://puppetlabs.com/associate_response`. An Associate request message does not
require a Data Chunk.

*DISCUSS(ale):* decide how to use error messages; decide if the success entry
    in a association response is redundant (a response would be a success notification
    whereas errors would signal a failure)

Association response messages are described by the following json-schema:

```
{
    "properties" : {
        "request_id" : { "type" : "string" },
        "success" : { "type" : "bool" },
        "message" : { "type" : "string" }
    },
    "required" : [request_id", "success"],
    "additionalProperties" : false
}
```
These fields are described as:

 - a *request_id* string entry set to the message id of the related request
 - a *success* boolean entry that indicates session association was successful
 - an optional *message* field may include the reason why a association failed

<a name="error_handling"/>
Error Handling
---

Association error messages must conform with the format described in the
[error handling][2] section. The *message_type* entry of the JSON data `content`
must be equal to the respective association schema, mentioned above.

<a name="server_operation"/>
Server Operation
---

Once an association request is received, the server must extract the common name
from the client SSL certificate, the one used to establish the underlying connection. The
server will then match the certificate ID against the node identity indicated in
the *sender* URI of the request envelope. In case of mismatch, the server must
reply with an error message, as described in the above section.

In case the underlying wire connection drops, the server must guarantee that the
client will be disassociated, meaning that the client URI will no longer map to
its underlying websocket connection.

The server must keep track of the associated clients and must be
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
