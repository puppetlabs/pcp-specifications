Actions
===

Within the Cthun RPC layer, controllers invoke agent capabilities by requesting
*actions*. This is done by sending *request* messages to one or more agents.
Such messages specify the action name, module and input parameters.

Actions related to a given application are grouped together in *modules*; for
instance, the `puppet` module provides the `run` and `status` actions.

Action requests can be *blocking* or *non-blocking*; that changes the way the
action outcome is referred and stored.

To inform the requester controller about the outcome of the requested action,
the agent replies with a *response* message.

*TODO(ale):* refer to the transaction status action

*TODO(ale):* add note regarding non-blocking requests and notify_outcome

[1]: request_response.md
