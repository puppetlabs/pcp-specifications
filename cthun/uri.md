Cthun Uniform Resource Indicator
===

A Cthun URI is a text string used to identify an addressable node within a Cthun
framework.

The components of a Cthun URI are:

<!--TODO(ale): do we need any notion of hostname here? We're on top of a wire
    protocol. See the "Endpoints" section in Cthun doc-->

<!--TODO(ale): in the "Endpoints" section in Cthun doc we mention using the
    connection ID in the "endpoint format"; do we need anything like that?-->

- *Cthun scheme name* `cth`, followed by a colon and a double slash
- *Node identity* (e.g. "cthun_agent_RHEL_042")
- *Node type* (e.g. "agent")

<!--- TODO(ale): we are currently using cth://server to identify the server;
    we should make it uniform with the format below -->

The "server" label is reserved, as it indicates the node type of Cthun servers.

The Cthun URI format is:

`cth://<node_identity>/<node_type>`

It is possible to identify all nodes of a given type of a Cthun framework. That
is done by replacing the *node name* with the asterisk wildcard symbol:

`cth://*/<node_type>`
