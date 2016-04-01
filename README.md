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
> Signing with your master key from ~/.config/trusty/
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
