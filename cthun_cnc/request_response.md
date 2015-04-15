Request/Response transactions
===

In this section, we describe the request/response message exchange between
client endpoints.

Blocking Action - Message Flow
___

The following figure shows the request/response transaction for a `puppet
status` blocking action.

```
    controller C                agent A
       |                           |
       |   puppet status request   |
     1 |-------------------------->|
       |                           | puppet status 2
       |                           |   |
       |                           |   |
       |                           |   |
       |          response         |   V 3
     4 |<--------------------------|
       |                           |
```

The controller **C** and the agent **A** are registered in the same Cthun
messaging fabric. **C** sends a `puppet status` request to **A** (1).
As soon as the request is received and validated, **A** starts the execution of
`puppet service` by relying on the logic of the puppet module (2).

Once the action terminates (3), **A** sends back to **B** a response containing
the `puppet status` outcome (4).

Note that there are no constraints regarding the controller operation; **C**
can decide whether or to not to implement a response timeout.

Non-blocking Action - Message Flow
___

This time, **C** wants to start a `puppet run` action in **A**.

```
    controller C                agent A
       |                           |
       |     puppet run request    |
     1 |-------------------------->|
       |          response         | puppet run 2
     3 |<--------------------------|   |
       |                           |   |
       |                           |   |
       |                           |   |
       |                           |   |
       |       status request      |   |
     4 |-------------------------->|   |
       |<--------------------------|5  |
       |                           |   |
       |                           |   |
       |                           |   V 6
       |                           |
       |                           |
       |       status request      |
     7 |-------------------------->|
       |<--------------------------| 8
       |                           |
```

**C** sends to **A** a `puppet run` request (1). After validating the request
message, **A** starts the requested action (2) and immediately sends back to
**C** a response containing the action job ID (3).

**C** waits for some time and sends a `status` request to **A** in which he
specifies the `puppet run` job ID he's waiting for (4).
**A** receives the `status` request and checks the progress of the job;
`puppet run` is still executing so **A** replies with a `status` response
by stating that the requested job status is "running" (5); refer to the
[status service][2] section.

**C** receives the `status` reply and determines to wait longer before
querying again about the job status.
`puppet run` terminates (6) before **C** decides to send another `status`
request (7) to which **A** replies with a `status` response containing the
outcome of the action (output and possible errors).


Request Message
---

*DISCUSS(ale):* consider changing "params" - it's ambiguos

Must have JSON data content - schema: "module", "action", "params"

Response Message
---

*DISCUSS(ale):* in case of error, should the agent add an error field
to the response or reply with an error message?

*DISCUSS(ale):* in case we decide to report errors within the error
field, should the controller look for both the action output and error
like a generic shell application (ret code, out, err)? Currently,
PEG does not show the outcome (data field) for the response message
includes the error field... Decoupling output from error can be useful.

Error Handling
---

Follows the semantics described in the [Error Handling][1] section.

In case of unknown module ...

In case of unknown action ...

In case of processing error ...

[1]: ../cthun/error_handling
[2]: status.md
