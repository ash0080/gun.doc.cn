**Is GUN a distributed database?**

Yes. Data is kept and shared between peers, across the network. 
To give an example: 
- We assume a network configuration of 3 peers with Alice - Bob - Charlie. Alice and Charlie do not share a direct connection. But when Charlie asks for a record that only Alice has, Alice will return the record to Charlie, because they are connected via Bob.

**Is there a single graph across all users in the network?**

Each peer initiates an instance of gun. Depending on application logic, the peers will sync their data into their instance of the graph, with a goal to be consistent across the application that they are connected to. Depending on data structure you may have multiple roots available which can act as a division of data in the instance of the peer. That way, all graphs can start with one root node or multiple root nodes to divide app logic.

**How are conflicts handled?**

Conflicts are handled by the conflict resolution algorithm. see [Hypothetical Amensia Machine](https://gun.eco/docs/Hypothetical-Amnesia-Machine)

**How is GUN distributed / replicated across peers?**

Gun uses 'websockets' underneath. In the future Gun will have a system called AXE that will do smart routing and encrypted transports. (Work is in progress)
Whenever a client 'puts' something in a graph, a message is sent to the [network](https://gun.eco/docs/DAM) and other clients that have subscribed to that data, or super peers, will pick it up and update their internal state. The guarantee is, that it will be real-time for those who are online at the same time and that even those who are offline, will eventually receive those updates. (this is called eventual consistency [CAP](https://gun.eco/docs/CAP-Theorem) )

**What is the difference between Super Peer and other Peers?**

Gun saves data in two ways. Browser - localStorage, Node - RAD (Radix Storage Engine to files).
Due to Browser Limitations, not all data is stored on all clients, persistence at this time is what is left in your localStorage (50 mb limit) and what the 'super peer' (node.js out of necessity right now) saves to files on hard disk. Clients subscribe to the data they need to stay informed on and the super peer will dispatch data. Once the data is on the client, the client may serve data to other clients as well.

**What is a soul? What does a node look like?**

When using gun to add data to a graph, or to retrieve data from a graph, gun creates metadata, such as a unique identifier for each node that is generated. This is called a soul in 'gun lingo'.
The data structure of a node is as follows, assuming a node Alice with a relation of friend Bob.
```javascript
{
'_':{
    '#': 'eJgh6QkdhlFs8Ed', // <= Soul, UUID
    '><': 12533221562251  // <= State Vector of this node, used for Conflict Resolution
    },
'friend': {
          'Bob' : 'edDKJj4Fsliojfdf' // reference to the Soul, UUID of the connection
          }
}
```
(This may vary on your data)

** Can you use SQL like queries? What about pagination and aggregation?

Working with graphs data is quite different from relational databases. Things like pagination, aggregation etc work completely different and require a different data structure design. Often the best approach is to start studying graphs in depth instead of trying to use something like SQL queries in a try to keep using old data usage. [More explanation and references to more in depth info needed here]
