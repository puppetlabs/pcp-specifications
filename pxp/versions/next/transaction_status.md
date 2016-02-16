Transaction Status
===

Agents must implement a *status query* [action][1] that provides information
about the execution of a specific *non-blocking* request that the agent has
previously received. Such information should include:

 - the *status* of the specified action (specified by the *transaction_id* or the request): *unknown*, *running*, *success*, *failure*, or *undetermined*;
 - action's output (*stdout*, *stderr*, and *exitcode*) that is stored in the *output* object;
 - processing timestamps (*start*, *end*), stored in the *action_metadata*;
 - possible errors reported by the agent (*execution_error*), also stored in the *action_metadata*.

Note that, in case the action has completed successfully, the *stdout* entry is
mandatory and it must contain the validated output on stdout of the action.

Also note that PCP client nodes identify the requested action by its
*transaction_id*; see the [Request/Response][2] section for more details.

### Request

*status query* requests must be *blocking*. They must follow the schema
presented in the [Request/Response][2] section, where the *module* and *action*
entries must be set to, respectively, `status` and `query`; also the *params*
entry must comply with the following JSON schema:

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
| transaction_id | string | the id of the transaction the controller is interested in


### Response

The agent will respond with a [blocking responses][2] including a *stdout*
entry in the *output* object that conforms to the following JSON schema:

```
{
    "properties" : {
        "transaction_id" : { "type" : "string" },
        "status" : { "enum" : [ "unknown", "running", "success", "failure", "undetermined" ] },
        "output" : {
            "type" : "object",
            "items" : {
                "stdout" : { "description" : "should be a string, in case status is failure or undetermined, or the results in an action-specific format, otherwise" },
                "stderr" : { "type" : "string" },
                "exitcode" : { "type" : "number" },
            },
            "required" : [],
            "additionalProperties" : false
        },
        "metadata" : {
            "type" : "object",
            "items" : {
                "execution_error" : { "type" : "string" },
                "module" : { "type" : "string" },
                "action" : { "type" : "string" },
                "start" : { "type" : "string" },
                "end" : { "type" : "string" },
            },
            "required" : ["module", "action", "start"],
            "additionalProperties" : false
        }
    },
    "required" : ["transaction_id", "status"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| transaction_id | string | the id of the queried transaction
| status | string | state of the action ("unknown", "success", "failure", "running", or "undetermined")
| output | object | contains the output of the requested action, if any
| metadata | object | contains information about the execution of the requested action (mandatory in case *status* is not *unknown*)

The following table provides more information about *status* values:

| status value | description
|--------------|------------
| unknown | the agent has no information about the requested *transaction_id*
| running | the action is still running
| success | the action returned valid results on *stdout* (the returned *exitcode* is not taken into account)
| failure | the agent failed to execute the action or received invalid results on *stdout*; a possible error will be reported in the *execution_error* entry
| undetermined | the agent cannot determine whether or not the specified action completed (more details this should be reported in *execution_error*)

The *metadata* object has analogous entries as the [RPC error message][2] one.
The difference here is that *metadata* is optional; in fact the agent must
not include such entry in case the requested transaction is *unknown*. When
included, the only mandatory *action_metadata* entry is *start*. Nonetheless,
the agent should include in the *status query* response as much information as
it reliably can, about the requested transaction.

Here is a description of *action_metadata* entries:

| name | type | description
|------|------|------------
| execution_error | string | any error reported by the agent while executing the action or processing its output
| start | string | this is the only mandatory entry - time entry in ISO8601 format indicating when the action started
| end | string | time entry in ISO8601 format indicating when the action completed
| stdout | string | any output of the action on stdout, in case it did complete
| stderr | string | any output of the action on stderr
| exitcode | int | exit code returned by the action

Note that, for different *status* values, the agent should guarantee that the
*stdout* entry includes:

| status | stdout
|--------|-------------
| *unknown*, *running* | no *stdout* entry (the action must complete before the agent can guarantee that the results are in the expected format)
| *failure*, *undetermined* | the action's output on stdout in a string format, without being validated
| *success* | the action's output on stdout, in the action's specific format (the agent must have validated it)


### Error Handling

In case the agent is unable to determine the status of the specified
transaction, it must respond with an [RPC error message][2], where the
*execution_error* entry must provide details about the problem.

[1]: actions.md
[2]: request_response.md
