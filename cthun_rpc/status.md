Status
===

The *status query* action provides a mechanism to retrieve the status and
outcome of non-blocking actions, as described in the *"Non-blocking
Action - Message Flow"* section [here][1].

### Status Request

Status requests are RPC *blocking requests* that follow the format described in
the [Request Response][1] section. The *params* entry (JSON object) must comply
with the following schema (NB: that's not the entire data content schema; just
the *params* entry):

```
{
    "properties" : {
        "job_id" : { "type" : "string" },
    },
    "required" : ["job_id"],
    "additionalProperties" : false
}
```

Where *job_id* is the reference to the execution job of the requested action.

### Status Response

Status responses are RPC *blocking responses* with the following format:

*TODO(ale):* enumerate status values, once we decide about the above DISCUSS

```
{
    "properties" : {
        "transaction_id" : { "type" : "string" },
        "status" : { "type" : "string" },
        "stdout" : { "type" : "string" },
        "stderr" : { "type" : "string" },
        "error" : { "type" : "string" }
    },
    "required" : ["transaction_id", "status"],
    "additionalProperties" : false
}
```

| name | type | description
|------|------|------------
| transaction_id | string | free format id of the status request/response transaction
| status | string | status of the requested action ("Unknown", "Running", "Completed")
| stdout | string | action output
| stderr | string | action error
| error | string | error message (in case of unknown job id)

### Error Handling

In case of invalid request message, the error handling follows what specified
in the [Request Response][1] section.

In case of unknown job id, the status response must have the *status* entry set
to "Unknown" and contain the *error* entry.

[1]: request_response.md
