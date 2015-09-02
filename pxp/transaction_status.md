Transaction Status
===

Agents must implement a *transaction status* [module][1] that includes a *query*
action. Such action must provide information regarding *non-blocking*
requests previously issued: the action status and, in case the action has
completed its execution, the action outcome. Note that PCP client nodes refer to
requests by their transaction id.

*TODO(ale):* ensure this respects the execution API, once it's stable

### Request

Transaction status requests can be *blocking* or *non-blocking*. They follow the
schemas presented in the [Request/Response][2] section, where the *params* entry
must be comply with the following JSON schema:

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
        "stderr" : { "type" : "string" }
    },
    "required" : ["transaction_id", "status"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| transaction_id | string | the transaction id of the queried action
| status | string | state of the queried action ("success", "failure", "running") or "unknown" in case an unknown transaction_id was specified in the query request
| stdout | string | output of the queried action, in case it did complete
| stderr | string | possible errors of the queried action, in case it did complete

[1]: actions.md
[2]: request_response.md
