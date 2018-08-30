### FAQ <a name="top"></a>
[Is GUN a distributed database?](#is-gun-a-distributed-database)

[Is there a single graph across all users in the network?](#is-there-a-single-graph-across-all-users-in-the-network)

[How are conflicts handled?](#how-are-conflicts-handled)

[How is GUN distributed / replicated across peers?](#how-is-gun-distributed--replicated-across-peers)

[What is the difference between Super Peer and other Peers?](#what-is-the-difference-between-super-peer-and-other-peers)

[What is a soul? What does a node look like?](#what-is-a-soul-what-does-a-node-look-like)

[Can you use SQL like queries? What about pagination and aggregation?](#can-you-use-sql-like-queries-what-about-pagination-and-aggregation)

[How does GUN work underneath?](#how-does-gun-work-underneath)

[How do subscriptions work?](#how-do-subscriptions-work)

[How can I change a user password? (SEA)](#how-can-i-change-a-user-password)

***

#### Is GUN a distributed database?<a name="is-gun-a-distributed-database"></a>

Yes. Data is kept and shared between peers, across the network. 
To give an example: 
- We assume a network configuration of 3 peers with Alice - Bob - Charlie.
- Alice and Charlie do not share a direct connection. 
- When Charlie asks for a record that only Alice has, Alice will return the record to Charlie, because they are connected via Bob.

#### Back to <a href='/docs/FAQ#top' style="color:#036">Top</a>
***

#### Is there a single graph across all users in the network?<a name="is-there-a-single-graph-across-all-users-in-the-network"></a>

Each peer initiates an instance of gun. Depending on application logic, the peers will sync their data into their instance of the graph, with a goal to be consistent across the application that they are connected to. Depending on data structure you may have multiple roots available which can act as a division of data in the instance of the peer. That way, all graphs can start with one root node or multiple root nodes to divide app logic.

###### Back to <a href='/docs/FAQ#top'>Top</a>
***

#### How are conflicts handled?<a name="how-are-conflicts-handled"></a>

Conflicts are handled by the conflict resolution algorithm. see [Hypothetical Amnesia Machine](https://gun.eco/docs/Hypothetical-Amnesia-Machine)

###### Back to <a href='/docs/FAQ#top'>Top</a>
***

#### How is GUN distributed / replicated across peers?<a name="how-is-gun-distributed--replicated-across-peers"></a>

Gun uses 'websockets' underneath. In the future Gun will have a system called AXE that will do smart routing and encrypted transports. (Work is in progress)
Whenever a client 'puts' something in a graph, a message is sent to the [network](https://gun.eco/docs/DAM) and other clients that have subscribed to that data, or super peers, will pick it up and update their internal state. The guarantee is, that it will be real-time for those who are online at the same time and that even those who are offline, will eventually receive those updates. (this is called eventual consistency [CAP](https://gun.eco/docs/CAP-Theorem))

###### Back to <a href='/docs/FAQ#top'>Top</a>
***

#### What is the difference between Super Peer and other Peers?<a name="what-is-the-difference-between-super-peer-and-other-peers"></a>

Gun saves data in two ways. Browser - localStorage, Node - RAD (Radix Storage Engine to files).
Due to Browser Limitations, not all data is stored on all clients, persistence at this time is what is left in your localStorage (50 mb limit) and what the 'super peer' (node.js out of necessity right now) saves to files on hard disk. Clients subscribe to the data they need to stay informed on and the super peer will dispatch data. Once the data is on the client, the client may serve data to other clients as well.

###### Back to <a href='/docs/FAQ#top'>Top</a>
***

#### What is a soul? What does a node look like?<a name="what-is-a-soul-what-does-a-node-look-like"></a>

When using gun to add data to a graph, or to retrieve data from a graph, gun creates metadata, such as a unique identifier for each node that is generated. This is called a soul in 'gun lingo'.
The data structure of a node is as follows, assuming a node Alice with a relation of friend Bob.
```javascript
{
'_':{
    '#': 'eJgh6QkdhlFs8Ed', // <= Soul, UUID of Alice
    '>': 12533221562251  // <= State Vector of this node, used for Conflict Resolution
    },
'friend': {
          'Bob' : 'edDKJj4Fsliojfdf' // reference to the Soul, UUID of the connection
          }
}
```
(This may vary on your data)

###### Back to <a href='/docs/FAQ#top'>Top</a>
***

#### Can you use SQL like queries? What about pagination and aggregation?<a name="can-you-use-sql-like-queries-what-about-pagination-and-aggregation"></a>

Working with graphs data is quite different from relational databases. Things like pagination, aggregation etc work completely different and require a different data structure design. Often the best approach is to start studying graphs in depth instead of trying to use something like SQL queries in a try to keep using old data usage. [More explanation and references to more in depth info needed here]

###### Back to <a href='/docs/FAQ#top'>Top</a>
***

#### How does GUN work underneath?<a name="how-does-gun-work-underneath"></a>

GUN is a modular library. [Internal Workings](https://gun.eco/docs/javascript)

The main functional 'layers' of the system are:
1. Conflict Resolution Algorithm ([Hypothetical Amnesia Machine](https://gun.eco/docs/Hypothetical-Amnesia-Machine))
2. Graph Data Structure (In-Memory Graph)
3. Signing, Encryption, Authentication ([SEA](https://gun.eco/docs/SEA), [Users](https://gun.eco/docs/User),[Security](https://gun.eco/docs/Auth))
4. Data Storage (We call [RAD](https://gun.eco/docs/Radisk), supports Browser/Files/S3/IPFS)
5. Network ([DAM](https://gun.eco/docs/DAM), uses websockets at this point)
6. _WIP_ Routing and Sharding ([AXE](https://gun.eco/docs/AXE))
7. _WIP_ App Integration for global distribution (includes decentralized OpenAuth service)

Each component builds on top of the others and can be used based on the needs of the developer.

###### Back to <a href='/docs/FAQ#top'>Top</a>
***

#### How do subscriptions work?<a name="how-do-subscriptions-work"></a>

Subscriptions are like event hooks that each instance of GUN can add at specific points. 
When data is subscribed to by using gun.get('myKey').on(callback, change-only-flag) [ON](https://gun.eco/docs/API#on), peers indicate to their Storage Adapter, that the data of 'myKey' should be stored when seen, and if seen, to then fire the callback provided. 

To subscribe or not subscribe from data, does not mean, that the network will reject or stop propagating data requested by other peers via your peer instance. The network layer operates independent from the storage layer and all peers participate in data relay regardless of subscriptions.

###### Back to <a href='/docs/FAQ#top'>Top</a>
***

#### How can I change a user password? (SEA)<a name="how-can-i-change-a-user-password"></a>

```javascript
user.auth(alias, passphrase, callback, { newpass: 'new-pass-value' })
```

###### Back to <a href='/docs/FAQ#top'>Top</a>
***
