PCP Uniform Resource Indicator
===

A PCP URI is a text string used to identify an addressable client within a PCP
framework.

The PCP URI format is:

`pcp://<common_name>/<client_type>`

The components of a PCP URI are:

- *PCP scheme name* `pcp`, followed by a colon and a double slash
- *Common name* The name presented in the client's SSL certificate (e.g. bob.com)
- *Client type* The type of the client (e.g. agent)

The "server" label is reserved type, as it indicates the node type of PCP brokers.

### Broker addressing

A client may address the broker it is currently connected to by using the short
hand:

`pcp:///server`
