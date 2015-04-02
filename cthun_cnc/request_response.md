Request/Response transactions
===

Between client endpoints ...

Message Flow
___

...

```
    controller C                agent A
       |                           |
       |   puppet status request   |
     1 |-------------------------->|
       |                           | puppet status
       |                           |   |
       |                           |   |
       |                           |   |
       |          response         |   V
     3 |<--------------------------|
       |                           |
```



Non-blocking Action
___

...

```
    controller C                agent A
       |                           |
       |     puppet run request    |
     1 |-------------------------->|   2
       |          response         | puppet run
     3 |<--------------------------|   |
       |                           |   |
       |                           |   |
       |                           |   |
       |                           |   |
       |       status request      |   |
     4 |-------------------------->|   |
       |<--------------------------|   |
       |                           |   |
       |                           |   |
       |                           |   V
       |                           |   5
       |                           |
       |       status request      |
     6 |-------------------------->|
       |<--------------------------| 7
       |                           |
```


Request Message
---

*DISCUSS(ale):* consider changing "params" - it's ambiguos

Must have JSON data content - schema: "module", "action", "params"

Response Message
---


Error Handling
---

Follows the semantics described in the [Error Handling][1] section.

In case of unknown module ...

In case of unknown action ...

In case of processing error ...

[1]: ../cthun/error_handling
