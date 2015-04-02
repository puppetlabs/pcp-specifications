Terminology
===

The following terms rely on the definitions provided in the
[Cthun protocol terminology][1] section.

*DISCUSS(ale):* "service" is used in its communications connotation; should we
    use a more specific term to avoid ambiguity with "OS services"? Same thing
    in the protocol specs ...

*DISCUSS(ale):* should CthunAgent enable Pegasus run/bundle/play?

*DISCUSS(ale):* should CthunAgent allow loading external modules remotely?

<dl>
  <dt>Agent</dt>
  <dd>Cthun client node that enables services at the C&C layer such as the execution of pre-installed applications or shell commands</dd>

  <dt>Controller</dt>
  <dd>Cthun client node that can request the services offered by agents</dd>

  <dt>Request</dt>
  <dd>Cthun message by which a controller requires a service offered by one or more agents</dd>

  <dt>Response</dt>
  <dd>Cthun message by which an agent replies to a controller request with the outcome of a service</dd>

  <dt>Action</dt>
  <dd>A processing capability offered by an agent that may be invoked by a C&C request</dd>

  <dt>Blocking action</dt>
  <dd>Action for which the agent replies with the action outcome when its execution completes</dd>

  <dt>Non-blocking action</dt>
  <dd>Action for which the agent replies with a response message containing a pointer to the action outcome; the response is sent as soon as its execution starts and the action outcome will be made available successively by the *status* service</dd>

  <dt>Module</dt>
  <dd>The implementation model used to group a set of actions</dd>

  <dt>External Module</dt>
  <dd>A module that is not hard-coded in the agent application (CthunAgent); its logic resides in a separate script</dd>

  <dt>Status Module</dt>
  <dd>Module that implements the *status query* service, by which an agent provides the status of a non-blocking action; it also provides the action outcome, in case its execution is completed</dd>
</dl>

[1]: ../cthun/terminology.md
