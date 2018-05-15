# Daisy-chain Ad-hoc Mesh-network

DAM is GUN's default transport layer abstraction and P2P networking algorithm. Decentralized communications can be rather chatty, so it helps "hold back" an excess flood of messages by improving upon the base brute force method.

This page focuses on both its architectural and API. DAM is the transport counterpart to RAD.

## API

> Needs to be Documented

DAM's API allows for swapping in different transport layers (websocket, WebRTC, etc.) in the same way RAD lets you swap out storage layers (disk, S3, IPFS, etc.).

## Architecture

An explanation of how DAM works was ripped out from a chatroom discussion, [bounty](./Bounty) for anyone who cleans it up and documents it into wiki format: 

**Mark Nadal**: DAM is best understood with visual illustrations, but since I haven't had a time to draw cartoons for it, here it goes.

Here is an image of the base brute force method, DAM improves on top of this:

![Brute Force](https://gun.eco/see/ad-hoc-mesh-network.gif)

a GET requests #soul.key or #soul (full node) OUT to all peers it is connected to every message gets a unique ID

each peer receives IN that request, checks the message ID and deduplicates against it (if it has seen it before, it ignores it)

if it hasn't seen it before

it (A) rebroadcasts OUT the message to all of its peers

and (B) processes the message by seeing if it has the requested data that it can ACK back

DAM specifically adds a couple intelligent upgrades to these first couple of steps ^

DAM (Daisy-chain Ad-hoc Mesh-network)

also (when possible) adds the IP/url/ID of the peers the current peer is connected to to each message

this allows on hearing IN on other peers

if they rebroadcast to other peers, they will "drop" echoing to any overlapping peer.

(right now, unfortunately, browser peer IDs aren't stable, so this advantage only really happens with gateway peers)

when each peer with DAM re-broadcasts the message (to any remaining peers not listed)

it deletes that list of IP/url/IDs of peers on the message and replaces it with its own

this prevents the message from getting bogged down with a bunch of appended IPs

IF a peer has data to ACK, then in DAM that peer also (if it is fast enough, aka if it is cached) calculates a fast hash

of the data it is replying with

and adds it to the GET message it is re-broadcasting (that it is daisy chaining)

so that way if those peers receive the GET and attempt to reply, they can ALSO deduplicate their response by the hash

aka Alice asks -> Bob daisy chains -> Carl

assume Bob and Carl have same data to ACK back

because Bob added his fast hash onto the daisy chain out to Carl

when Carl attempts to reply, he stops, and doesn't

because Carl sees his reply would be the same as Bob.

okay, resuming

Alice's GET propagates out daisy chained to all peers

peers now reply by making a PUT operation with an ACK on the outbound message where the ACK is the ID of the GET message's ID

that peer then sends OUT that PUT ACK to all its peers in base system, with DAM, it only sends it back to the previous daisy chained peer that sent the GET, this means in DAM that ACKs can be directly routed (no need to propagate to everyone).

Alice is subscribed to all messages coming IN that have an ACK on the same ID as the GET she sent OUT.

this allows her to receive responses (note: may be many, in a P2P network! Stream replies)

the beauty of DAM is that it "holds back" a bunch of unnecessarily duplicate messages, because ontop of the base message ID deduplication, it also deduplicates against neighbor peer ID and fast hashes.

Finally, outbound updates are trivial

when Alice changes data on something, she sends OUT a PUT command that has the diff she made, to all of her peers, those peers who receive IN the PUT command deduplicate off the message ID, rebroadcast if needed, and process the message depending upon whether they are subscribed to that data or not. If they do save the update, they ACK back a success or failure using the same technique as the ACK to the GET, that way Alice can get notified about replication.

and that is DAM! the now default messaging that happens across any transport wire that is implemented.

----

To learn more about how peers might more efficiently connect to each other, check out [Service Discovery](./Service-Discovery).