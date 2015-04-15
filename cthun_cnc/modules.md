Modules and Actions
===

*TODO(ale):* formalize the agent concept

Within the Cthun C&C layer, the services provided by agents are made available
as actions.

*DISCUSS(ale):* CthunAgent allow loading external modules remotely

Actions related to a given application are grouped together in a module.

To start the execution of an action, a controller sends a request message to
one or more agents by specifying the action module and its parameters. This is
described in the [Request Response][1] section.

Ex. puppet module ...

Blocking Actions
---

Response contains the outcome --> sent synchronously once the action has
executed ...
Ex. puppet status

Non-blocking Action
---

Response contains a reference to the outcome location; such reference will be
used to obtain the outcome (status module, query action) --> the status module
is mandatory

Ex. puppet run

Adding a Module
---

Requirements for External modules ...

##### Action Metadata

*TODO*
...

[1]: request_response.md
