Registration
===

Cthun client nodes must be registered before using the fabric capabilities. This
is done by completing a successful login transaction with a Cthun server.

In this section we illustrate the login transaction and the registration
operation performed by a server.

Login Message Flow
---

A client that wants to register to the messaging fabric must 1) establish the
underlying wire layer connection (e.g. WebSocket) and 2) send a *login request*
message to a Cthun server.

In case the request is processed successfully, the server will reply with a
*login response* message. The server may ignore the request or reply with an
*error message* in case of node identity mismatch (see
[Server Operation](#server_operation)).

<!-- TODO(ale): should the server always send back an error message even in case
    of a failure other than id mismatch? -->
<!-- TODO(ale): list all possible failure cases for which the server must
    reply -->

In case the node identity indicated in the *sender* URI of the request does not
match with the ID stored in the client certificate used to establish the wire
layer connection, the server will reply with an error message (see
[Error Handling](#error_handling)).

Login Messages
---

<!-- TODO(ale): discriminating request/response to be consistent with inventory;
    update server/client code accordingly -->

Login request and response messages must have the envelope *data_schema* entry
respectively equal to `http://puppetlabs.com/loginrequestschema` and
`http://puppetlabs.com/loginresponseschema`.

<!-- TODO(ale): specify the type of node is redundant since it's already in the
    sender URI -->

For a login request, the data `content` must be a JSON document with a single
*type* string entry that specifies the type of node (e.g. "controller"). Note
that, as stated in the [URI][1] section, the client node type must not be equal
to "server".

<!-- TODO(ale): check the following -->
<!-- TODO(ale): check the following; decide how to use error messages; decide if
    the success entry is redundant -->

For responses, the data `content` must still be a JSON document with:
 - the same *type* entry of the related request
 - a *request_id* string entry set to the message id of the related request
 - a *success* boolean entry that indicates the login success

<a name="error_handling"/>
Error Handling
---

Login error messages must conform with the format described in the
[error handling][2] section. The *data_schema* entry of the JSON data `content`
must be equal to the login one, mentioned above.

<!-- TODO(ale): should the server always reply to a login request? With a
    response, in case of success, or with an error message otherwise? -->

<a name="server_operation"/>
Server Operation
---

<!-- TODO(ale): establish the login request timer duration -->

Once the underlying wire connection with a given client node is established, the
server must start a login timer. Then, if the server does not receive a login
request from such client node before a timeout event triggers, the underlying
connection must be closed.

Once a login request is received, the server must extract the ID from the client
SSL certificate, the one used to established the underlying connection. The
server will then match the certificate ID against the node identity indicated in
the *sender* URI of the request envelope. In case of mismatch, the server must
reply with an error message, as described in the above section. Please refer to
the [wire protocol][3] section for authentication requirements for the wire
protocol.

<!-- TODO(ale): check this - is it good to persist the login status after an
    underlying disconnection? If so, can we mandate a timer here? Or a min
    time interval in which keep the login status? 3 minutes? In cthun-client,
    Connector::monitorConnection() has a CONNECTION_CHECK interval of 15 s -->
<!-- TODO(ale): in case we decide to have login persistence, do we need to
    specify the server behaviour in case a message must be delivered to a logged
    in but disconnected client? Is it already covered by the deliver / redeliver
    mechanism? -->

In case the underlying wire connection drops, the server must guarantee that the
registered status of the client is kept for at least 1 minute in order to allow
the client node to fully reconnect to the fabric by simply re-establishing the
underlying connection.

The server must keep track of the nodes registered to the fabric and must be
able to obtain the current status of their wire connections. That is necessary
in order to perform delivery and inventory operations.

Client Operation
---

Client nodes must be able to process login responses and change their status
accordingly.

In case a client node wants a persistent Cthun connection, it should monitor the
state of the wire layer connection and attempt to re-establish it if necessary.

<!-- TODO(ale): should we suggest / mandate a login response timer for the
    client? The server may not respond at all... -->

[1]: uri.md
[2]: error_handling.md
[3]: wire_protocol.md
