PCP Uniform Resource Indicator
===

A PCP URI is a text string used to identify an addressable client within a PCP
framework.

The PCP URI format is:

`cth://<common_name>/<client_type>`

The components of a PCP URI are:

- *PCP scheme name* `cth`, followed by a colon and a double slash
- *Common name* The name presented in the client's SSL certificate (e.g. bob.com)
- *Client type* The type of the client (e.g. agent)

The "server" label is reserved type, as it indicates the node type of PCP brokers.

### Wildcard URI's

When constructing URI's both the *Common name* and *Client type* fields can be
replaced by a wild card. When an entity processes such a URI it must expand the
wild card to include all clients.

A URI that expands to all types of clients that present the *Common name* _bob.com_
will look like:

`cth://bob.com/*`

A URI that expands to all clients that define their type as _agent_ will look like:

`cth://*/agent`

A URI that expands to all clients will look like:

`cth://*/*`

### Server addressing

A client may address the server it is currently correct to by using the short
hand:

`cth:///server`

