Request/Response transactions
===

In this section, we describe the request/response message exchange between
client endpoints.

### Blocking Action - Message Flow

The following figure shows an example of a *blocking* request/response
transaction, for a `puppet status` action.

```
    controller C                agent A
       |                           |
       |  `puppet status` request  |
     1 |-------------------------->|
       |                           | puppet status 2
       |                           |   |
       |                           |   |
       |                           |   |
       |          response         |   V 3
     4 |<--------------------------|
       |                           |
```

The controller **C** and the agent **A** are registered in the same PCP
framework. **C** sends a `puppet status` *blocking request* to **A** (1).
As soon as the request is received and validated, **A** starts the execution of
`puppet status` by relying on the logic of the puppet module (2).

Once the action terminates (3), **A** sends back to **B** a *blocking response*
containing the `puppet status` outcome (4).

Note that the transaction does not employ acknowledgment messages. And there
are no constraints regarding the controller operation; **C** can decide whether
or to not to implement a response timeout.

Also, note that **A** must reply with a:
 - *PCP protocol error message* in case the request message contains a bad data chunk (see [PCP Error Handling][1])
 - *RPC error message* if it fails to execute the requested action or in case of invalid results (see [RPC Error Message](#error-message))

### Non-blocking Action - Message Flow

This time, **C** wants to executes a `puppet run` action in **A**. Since such
action may take some time to complete, **C** decides to use a *non-blocking*
transaction, in order to receive an immediate confirmation of its execution
start.

```
    controller C                agent A
       |                           |
       |    `puppet run` request   |
     1 |-------------------------->|
       |    provisional response   | puppet run 2
     3 |<--------------------------|   |
       |                           |   |
       |                           |   |
       |                           |   |
       |                           |   |
       |                           |   |
       |                           |   |
       |    `puppet run` response  |   V 4
     5 |<--------------------------|
       |                           |
```

**C** sends to **A** a `puppet run` *non-blocking request* (1). After validating
the request message, **A** starts the requested action (2) and immediately sends
back to **C** a *provisional response* containing the action job ID (3).

The `puppet run` job terminates (4) and, since **C** requested to be notified
with the outcome of the job (*notify_outcome* was flagged in the
*non-blocking request* message), **A** sends back to **C** a
*non-blocking response* containing the job output and possible errors of the
executed action (5).

### Message types

The following list gives the *message_type* string for the Request Response
transaction messages:

| message | *message_type*
|---------|---------------
| blocking request | `http://puppetlabs.com/rpc_blocking_request`
| blocking response | `http://puppetlabs.com/rpc_blocking_response`
| non-blocking request | `http://puppetlabs.com/rpc_non_blocking_request`
| non-blocking response | `http://puppetlabs.com/rpc_non_blocking_response`
| provisional response | `http://puppetlabs.com/rpc_provisional_response`
| error | `http://puppetlabs.com/rpc_error_message`

### Message Format

In this section we describe the data content format of request/response
messages.

##### Blocking Request

```
{
    "properties" : {
        "transaction_id" : { "type" : "string" },
        "module" : { "type" : "string" },
        "action" : { "type" : "string" },
        "params" : { "type" : "object" }
    },
    "required" : ["transaction_id", module", "action"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| transaction_id | string | free format id of the request/response transaction
| module | string | name of the module that includes the requested action
| action | string | name of the requested action withing its submodule
| params | object | input parameters (optional)

##### Blocking Response

The output of the requested action (results on stdout, errors on stderr, and the
exit code) should be included in the *output* object.

Note that there's no requirement for the action's *stdout* type, as its format
is action-specific. As for the agent operation, the *stdout* JSON schema should
be enforced by the agent in order to guarantee the specific format expected by
the controller; in case the *stdout* entry is not validated against its schema,
the agent should instead reply with an [RPC error message](#rpc-error-message).

Other required entries are the *transaction_id*, that correlates the response
message with the original request, and the *action_metadata* object, that
provides more information about the action outcome.

```
{
    "properties" : {
        "transaction_id" : { "type" : "string" },
        "output" : {
            "type" : "object",
            "properties" : {
                "stdout" : { "description" : "results in an action-specific format" },
                "stderr" : { "type" : "string" },
                "exitcode" : { "type" : "number" }
            },
            "required" : ["stdout", "exitcode"],
            "additionalProperties" : false
        },
        "metadata" : {
            "type" : "object",
            "properties" : {
                "module" : { "type" : "string" },
                "action" : { "type" : "string" },
                "start" : { "type" : "string" },
                "end" : { "type" : "string" }
            },
            "required" : ["module", "action", "start", "end"],
            "additionalProperties" : false
        }
    },
    "required" : ["transaction_id", "output", "metadata"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| transaction_id | string | free format id of the request/response transaction
| output | object | contains the action's output
| metadata | object | contains information about the execution of the requested action

Description of *output* entries:

| name | type | description
|------|------|------------
| stdout | string | action results presented on stdout, in the action-specific JSON format (validated by the agent)
| stderr | string | any output on stderr produced by the action
| exitcode | number | action's exit code

Description of *action_metadata* entries:

| name | type | description
|------|------|------------
| module | string | the executed module
| action | string | the executed module's action
| start | string | timestamp in ISO8601 format indicating when the action started
| end | string | timestamp in ISO8601 format indicating when the action completed

##### Non-blocking Request

Same as the *blocking request*, apart from the *notify_outcome* entry:

```
{
    "properties" : {
        "transaction_id" : { "type" : "string" },
        "notify_outcome" : { "type" : "bool" },
        "module" : { "type" : "string" },
        "action" : { "type" : "string" },
        "params" : { "type" : "object" }
    },
    "required" : ["transaction_id", "notify_outcome", module", "action"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| transaction_id | string | free format id of the request/response transaction
| notify_outcome | bool | if true, the agent must send a non-blocking response containing the action output, once its execution completes
| module | string | name of the module that includes the requested action
| action | string | name of the requested action withing its submodule
| params | object | input parameters (optional)

##### Non-blocking Response

This type of message has the same structure as the *blocking response* one. The
only difference lies in the envelope's *message_type*.

The agent must send the *non-blocking* response at the end of the action
execution if the original request had the *notify_outcome* entry set to `true`.

##### Provisional Response

Such message indicates that requested action has successfully started.
The *transaction_id* should be used as a reference to the remote action.

```
{
    "properties" : {
        "transaction_id" : { "type" : "string" }
    },
    "required" : ["transaction_id"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| transaction_id | string | free format id of the request/response transaction

### RPC Error Message

An *RPC error message* indicates that the agent failed to process an action
requested previously (the *transaction_id* refers to such transaction) and
provides information about such failure.

The format is:

```
{
    "properties" : {
        "transaction_id" : { "type" : "string" },
        "id" : { "type" : "string" },
        "output" : {
            "type" : "object",
            "properties" : {
                "stdout" : { "type" : "string" },
                "stderr" : { "type" : "string" },
                "exitcode" : { "type" : "number" }
            },
            "required" : [],
            "additionalProperties" : false
        },
        "metadata" : {
            "type" : "object",
            "properties" : {
                "execution_error" : { "type" : "string" },
                "module" : { "type" : "string" },
                "action" : { "type" : "string" },
                "start" : { "type" : "string" },
                "end" : { "type" : "string" }
            },
            "required" : ["execution_error", "module", "action", "start"],
            "additionalProperties" : false
        }
    },
    "required" : ["transaction_id", "id", "metadata"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| transaction_id | string | free format id of the request/response transaction
| id | string | free format id of the received request that originated the error
| output | object | contains the action's output, if any
| metadata | object | contains information about the execution of the requested action

Description of *output* entries

| name | type | description
|------|------|------------
| stdout | string | any output on stdout produced by the action (the agent does not validate it against the expected results' JSON schema)
| stderr | string | any output on stderr produced by the action
| exitcode | number | action's exit code

The *metadata* entry has the same entries as the ones defined for the above
response messages, with the additional *execution_error*:

| name | type | description
|------|------|------------
| execution_error | string | any error reported by the agent during the execution of the action (mandatory)
| module | string | the requested module
| action | string | the requested module's action
| start | string | optional - timestamp in ISO8601 format indicating when the action started
| end | string | optional - timestamp in ISO8601 format indicating when the action completed

### Error Handling

The agent should reply with an *RPC error message* in case of:
 - unknown module
 - unknown action
 - failure when starting the action execution
 - failure during the action execution
 - action's results on stdout don't are not validated against the action's results JSON schema

The agent should reply with a *PCP error message* in case of:
 - the request does not contain any data content
 - the request contains invalid data content
Note that in such cases, it is not possible to retrieve a *transaction_id* and
create an *RPC error message*.

The controller error handling operation should handle messages at both the PXP and
PCP layers, by covering the above cases.

[1]: ../pcp/error_handling.md
