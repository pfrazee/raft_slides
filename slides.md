title: Distributed Consensus (RAFT presentation)
author:
  name: Paul Frazee
  twitter: pfrazee
output: index.html
controls: true

--

# Distribute Consensus
## Using RAFT to coordinate multi-node systems

--

#### RAFT

- Diego Ongaro and John Ousterhout
"In Search of an Understandable Consensus Algorithm" (2013)
- Lots of talks online
- 49 implementations (including, yes, javascript)

raftconsensus.github.io

--

#### Premise/1

Paxos is error-prone due to its complexity, even for experts

--

#### Premise/2

If you can't reason about it, you can't implement it

(there's no following the side of the box)

--

#### How to improve?/1

Make the problem decompose into its parts

--

#### How to improve?/2

Reduce the number of possible states in the system

--

#### Did it work?

When taught to 43 students in 2 universities, 33 were able to answer questions about Raft better than Paxos

--

#### Overview

<a href="http://thesecretlivesofdata.com/raft/">thesecretlivesofdata.com/raft/</a>

--

#### The Guarantees/1

Election Safety: at most one leader can be elected in a
given term.

--

#### The Guarantees/2

Leader Append-Only: a leader never overwrites or deletes
entries in its log; it only appends new entries.

--

#### The Guarantees/3

Log Matching: if two logs contain an entry with the same
index and term, then the logs are identical in all entries
up through the given index.

--

#### The Guarantees/4

Leader Completeness: if a log entry is committed in a
given term, then that entry will be present in the logs of
the leaders for all higher-numbered terms.

--

#### The Guarantees/5

State Machine Safety: if a server has applied a log entry
at a given index to its state machine, no other server
will ever apply a different log entry for the same index.

--

#### Reconfiguration/1

For clients: when a new leader is elected, always redirected to that leader

--

#### Reconfiguration/2

Adding nodes: "Joint Consensus." Combines old and new configs.

--

#### Reconfiguration/3

Log entries are replicated to all servers in both configs.

--

#### Reconfiguration/4

Any server from either configuration may act as leader.

--

#### Reconfiguration/5

Agreement (for elections and entry commit) requires majorities from both the old and new configurations.

--

#### Reconfiguration/6

Cluster conﬁgurations are stored and communicated using special entries in the replicated log.

--

#### Reconfiguration/7

The process: when the leader receives a request to change config, it replicates the joint consensus (old,new) as a log entry.

--

#### Reconfiguration/8

Once under (old,new), leader then replicates (new). On commit, phased-out machines become irrelevant and shut down.

--

#### Reconfiguration/9

Complication 1: if leader is in (old), must step down before shift to (new)

--

#### Reconfiguration/10

Complication 2: new nodes must catch up their logs, so are non-voting until caught up

--

#### Reconfiguration/11

Complication 3: candidates make sure their log is up-to-date before requesting votes to make sure they don't disrupt a config transition
