Messaging Fabric Errors
===

<!--TODO(ale): can we specify an error message? Since error messages should be
    used by the server (mandatory errors: certificate name mismatch -> login
    failure), it would be nice to have a common format. Implementations could
    later extend it.
-->

The server must send an error message to the login requester client in case of
certificate identity mismatch (refer to the [registration][1] section).

<!-- TODO(ale): should we require any error message from clients? -->

Error Message Format
---

<!-- TODO(ale): check this - error format -->

An error message must have the envelope *data_schema* entry equal to:

`http://puppetlabs.com/errorschema`

The `content` of the data chunk must be a JSON document with the following
entries:

| name | type | description
|------|------|------------
| id | string | ID of the received message that originated the error
| data_schema | string | data_schema of the received message that originated the error
| description | string | error description

[1]: registration.md
