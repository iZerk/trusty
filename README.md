# trusty

Trusty is an easy to set up, transitive trust based distributed network.
It is meant to connect a group of computers (nodes) where each pair of nodes must not be connected bidirectionally and some nodes not even unidirectionally connected. Additionally, it should be tolerant about vanishing nodes.
Every nodes needs to authenticate itself to at least one other member that trusts him.

That way, a new node can join the group by authenticating against one other node that needs to know him (or his key / tbd authorization item).

To ensure fast information transfer, a minimum spanning tree (MST) should connect each nodes at all times. This MST is for fast communication of small event packages. Big Data packages doesn't neccessarily have to be transferred on the same route if a faster route can be found (maybe via DSR). If a node goes offline, the MST hast to be re-established as soon as possible.

Unidirectional connections (imagine as Client - Server) are made bidirectional be establishing a long lasting connection that serves as a channel for bidirectional message transfer.

## Installation

If you know ant, ivy and java, it should look familiar to you.
Otherwise take a look at the INSTALL.md.

## Usage

Trusty ships as a single jar (if not packaged with utility scripts) that can starts the node or serve as a client to instruct the node (e.g. to connect to other nodes).

```BASH
java -jar trusty.jar node --port 42000 > node.log 2>&1 &

java -jar trusty.jar client --port 42000
  > Connecting client to node on 127.0.0.1:42000... connected

  $ connect 203.0.113.1
  > No node key pair found.
  > Should I generate a new one for this node? [Y/n]
  Y
  > Generating node.key .........................
  > Signing with your master key from ~/.config/trusty/ ("Alice")
  > Connecting node to node on 203.0.113.1:42000... 
  > 203.0.113.1:42000 authenticated with a key from known user "Bob". Continue? [Y/n]
  Y
  > Connection Established. Receiving subnet information...
  > Received 12 potential nodes. Checking availability...
  > Bidirectionally available nodes: 2
  > Unidirectionally available nodes: 1
  > Entering MST of network using node 203.0.113.1:42000
  > Checking availability of other nodes............
  > 5 nodes indirectly available
  > Network established.
  
  $ exit
  > Exiting trusty client. The node keeps running. Bye!
```
(This assumes that the master key at ~/.config/trusty/ has already been generated and it's public key has been exchanged with "Bob"s public master key).

## Encryption

//TODO

## Networking

// TODO define protocol in PROTOCOL.md

Given the following scenario:
```
+-+          +-+        +-+
|A| +------> |B| <----> |C|
+-+          +-+        +-+
```

A can establish a connection to the publicly available B but B cannot directly connect to A (e.g. because its behind a firewall).
C can connect to B and vice versa but C can't connect to A at all and A can't connect to C (e.g. because B is the gateway to C's network).
With trusty, A can communicate with C because B forwards the messages and because every Node knows every other nodes public key, B can't decrypt the message from A to C (but he knows that they communicate).
Because the connection between A and B is kept open and serves as a bidirect channel, messages from C to A can be forwarded by B through the bidirectional channel that uses the unidirectional connection.

// TODO how to establish / agree on the MST

### Alternative routes

If an inner node of the MST goes offline, an alternative route has to be established (and found in order to do so). To find routes, DSR is used to find new, fast connections.

### QoS

#### Speed

For small informational packages, speed is most important.
DSR ensures that at least at one point in time, the fastest connections were known and those are used by the MST.
Even though, a direct connection between sender and receiver (if possible) might be faster than routing over the MST, the MST is known to connect all nodes so the message (normally) doesn't have to be re-sent and no new connection needs to be established because all connections of the MST are already opened, ready for data. 

// TODO define if / when / how to re-establish QoS
// TODO maybe DSR over the MST or a clique network might be suitable

#### Bandwidth

Currently, no bandwith optimization or guarantees are made.
The protocol optionally supports to define an explicit route between the sender and the receiver in addition to the required receiver that should be used by best afford. This might be used to ensure a high bandwidth route. However, if the defined route is not possible, an alternative route will be chosen automatically.

### Routing

// TODO

## Dicision making

Trusty supports an abstract layer of voting.
It ensures, that every available node receives the broadcasted message about the vote, allows to define the type of answer (yes / no or an item from a fixed list), allows a client to abstain from voting. It also keeps track of unreachable nodes for the voting process, tracks time of the defined timelimit and tries to verify the decision even of the network is partitioned and not all nodes can be reached.

Hovever, it does not care about the vote itself. It provides an interface for other application to register for votes / types of votes and allows them to define the answer that it forwards. The evaluation, what should be voted on an how the decision is made is left to other applications on top of trusty as a networking layer.
