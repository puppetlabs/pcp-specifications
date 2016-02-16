Actions
===

Within the PCP Execution Protocol layer, controllers invoke agent capabilities
by requesting *actions*. This is done by sending *request* messages to one or
more agents. Such messages specify the action name, module and input parameters.

Actions related to a given application are grouped together in *modules*; for
instance, a `puppet` module may provide the `run` and `status` actions.

Action requests can be *blocking* or *non-blocking*; that changes the way the
action outcome is referred and stored. Please refer to the [Request/Response][1]
and [Transaction Status][2] sections for more details.

To inform the requester controller about the outcome of the requested action,
the agent replies with a *response* message.

[1]: request_response.md
[2]: transaction_status.md
