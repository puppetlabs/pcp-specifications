Sessions
===

PCP clients must establish a session with a broker before they can use
its capabilities. A session is an authenticated connection between a client and
broker that the broker has associated with a [client URI][1].

A [client URI][1] is established based on the client's common name and the
requested *client_type*. A *client_type* is requested by appending it as a path
element to the underlying connection URI. If the resulting URI is not
authorized by the broker, the connection will be closed.

Example: given a broker designated at the URI `wss://broker.example.com/pcp`,
a session using the `controller` *client_type* would be established by
connecting to the URI `wss://broker.example.com/pcp/controller`. Given a client
authenticated as `client01`, the client session would then be established with
the associated [client URI][1] `pcp://client01/controller`.

If no *client_type* is appended to the path, the broker may select a default.

Should a session already be associated with the given [client URI][1], the
broker should disconnect the older connection, and consider the
newly-associated session to be authoritative for that URI.

A session ends when the connection is lost. Inventory subscriptions are not
maintained across sessions.

Broker Operation
---

Once a connection is established, the broker must extract the common name from
the client SSL certificate used to establish the connection and use it to
generate the [client URI][1] associated with that connection.

In case the underlying wire connection drops, the broker must guarantee that the
client will be disassociated, meaning that the [client URI][1] will no longer map to
its underlying connection (e.g. WebSocket connection).

The broker must keep track of the associated clients and must be
able to obtain the current status of their wire connections. That is necessary
in order to perform delivery and inventory operations.

Client Operation
---

Clients should append their desired *client_type* to the URI used to establish
the underlying wire connection.

In case a client wants a persistent session, it should monitor the
state of the wire layer connection and attempt to re-establish it if necessary.

[1]: uri.md
