Terminology
===

The following terms rely on the definitions outlined in the
[Cthun protocol terminology][1] section.

### Agent

Cthun client that interprets requests and performs actions

### Controller

Cthun client that can request the RPC capabilities offered by agents and process
their outcome

### Request

Cthun message by which a controller requires a capability offered by one or more
agents

### Response

Cthun message by which an agent replies to a controller, containing the outcome
of a request

### Action

a processing capability offered by an agent that may be invoked by an RPC
request

### Blocking action

once a *blocking action* is requested by a controller, the agent replies with a
response message containing its outcome after its execution completes

### Non-blocking action

action for which the agent replies with a response message containing a pointer
to the action outcome; the response is sent as soon as the action execution
starts and the outcome will be made available successively by the *status*
action

### Status Action

action that implements the *status query* capability, by which an agent provides
the status of a non-blocking action; it also provides the action outcome, once
the action execution is completed

[1]: ../cthun/terminology.md