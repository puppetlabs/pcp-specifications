Transaction Status
===

Agents must implement a *transaction status* [module][1] that includes a *query*
action. Such action must provide information regarding *non-blocking*
requests previously issued:
 - the status of the action requested in a given transaction ("running", "success", "failure", or "unknown");
 - in case that action has completed its execution, the action outcome (stdout, stderr, and exit code).

Note that PCP client nodes refer to requested actions by the transaction id of
the request; see the [Request/Response][2] section for more details.

### Request

Transaction status requests can be *blocking* or *non-blocking*. They must
follow the schemas presented in the [Request/Response][2] section, where the
*module* and *action* entries must be set to, respectively, `status` and
`query`; also the *params* entry must comply with the following JSON schema:

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


### Blocking Response

*TODO(ale):* extend to non-blocking responses once we enable those

Transaction status responses must have the *results* entry complying with the
following schema:

```
{
    "properties" : {
        "transaction_id" : { "type" : "string" },
        "status" : { "enum" : [ "unknown", "success", "failure", "running" ] },
        "stdout" : { "type" : "string" },
        "stderr" : { "type" : "string" },
        "exitcode" : { "type" : "integer" }
    },
    "required" : ["transaction_id", "status"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| transaction_id | string | the id of the queried transaction
| status | string | state of the action ("success", "failure", "running") or "unknown" in case an unknown transaction_id was specified in the request
| stdout | string | output of the action, in case it did complete
| stderr | string | possible errors of the action, in case it did complete
| exitcode | integer | the exit code of the action, in case it did complete

[1]: actions.md
[2]: request_response.md
