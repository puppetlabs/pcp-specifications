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
 - *RPC error message* if it fails to execute the requested action (see [RPC Error Message](#error-message))

### Non-blocking Action - Message Flow

This time, **C** wants to executes a `puppet run` action in **A**. Since such
action may take some time to complete, **C** decides to use a *non-blocking*
transaction in order to receive an immediate confirmation of its execution
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
executed action (5) (the final non-blocking response is not is scope for Ankeny).

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

The results of the requested action should be included in the *results* object;
its schema is action-specific. *transaction_id* is the other required entry.

```
{
    "properties" : {
        "transaction_id" : { "type" : "string" },
        "results" : { "type" : "object" }
    },
    "required" : ["transaction_id", "results"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| transaction_id | string | free format id of the request/response transaction
| results | object | action results in action-specific format

##### Non-blocking Request

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
| notify_outcome | bool | if true, the agent must send a response containing the action outcome, once its execution completes (not is scope for Ankeny)
| module | string | name of the module that includes the requested action
| action | string | name of the requested action withing its submodule
| params | object | input parameters (optional)

##### Non-blocking Response (not in scope for Ankeny)

The results of the requested action should be included in the *results* object;
its schema is action-specific. *transaction_id* in the only other required
entry.

```
{
    "properties" : {
        "transaction_id" : { "type" : "string" },
        "results" : { "type" : "object" }
    },
    "required" : ["transaction_id", "job_id", "results"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| transaction_id | string | free format id of the request/response transaction
| results | object | action results in action-specific format

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

### Error Message

An *RPC error message* follows the structure of PCP Error messages, as
described [here][1], plus the mandatory *transaction_id* field. The
*message_type* is the one specified above. The format is:

```
{
    "properties" : {
        "transaction_id" : { "type" : "string" },
        "id" : { "type" : "string" },
        "description" : { "type" : "string" }
    },
    "required" : ["transaction_id", "id", "description"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| transaction_id | string | free format id of the request/response transaction
| id | string | ID of the received request that originated the error
| description | string | error description

### Error Handling

The agent should reply with an *RPC error message* in case of:
 - unknown module
 - unknown action
 - failure when starting the action execution
 - failure during the action execution

The agent should reply with an *PCP error message* in case of:
 - no data content
 - invalid data content
Note that in such cases, it is not possible to retrieve a *transaction_id* and
create an *RPC error message*.

The controller error handling operation should handle messages at both the PXP and
PCP layers, by covering the above cases.

[1]: ../pcp/error_handling.md
