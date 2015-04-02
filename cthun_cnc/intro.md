Introduction
===

In a Cthun C&C framework, agents and controllers communicate with
request/response transactions in order perform remote tasks. Such tasks are
called *actions* and they are grouped in *modules*.

In this document we describe:

 - actions and modules
 - request/response transactions
 - the status service provided by agents

*TODO(ale):* update when (if) we enable Pegasus run/bundle/play over Cthun

Note that the remote execution of OS commands or scripts (for/SSH model) is out
of the scope of this document; only *modules* are covered. Note that such
capability is currently provided by [Pegasus][1] (run/bundle/play over SSH).
