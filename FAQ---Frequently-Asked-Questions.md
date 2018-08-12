**Is GUN a distributed database?**

Yes. Data is kept and shared between peers, across the network. 
To give an example: 
- We assume a network configuration of 3 peers with Alice - Bob - Charlie. Alice and Charlie do not share a direct connection. But when Charlie asks for a record that only Alice has, Alice will return the record to Charlie, because they are connected via Bob.

**Is there a single graph across all users in the network?**

The graph is shared across all peers in the network. Depending on data structure you may have multiple graphs available. All graphs can start with one root node or multiple root nodes to divide app logic.

**How are conflicts handled?**

Conflicts are handled by the conflict resolution algorithm. see [Hypothetical Amensia Machine](https://gun.eco/docs/Hypothetical-Amnesia-Machine)

**How is GUN distributed / replicated across peers?**

Gun uses 'websockets' underneath. In the future Gun will have a system called AXE that will do smart routing and encrypted transports. (Work is in progress)
Whenever a client 'puts' something in a graph, a message is sent to the [network](https://gun.eco/docs/DAM) and other clients that have subscribed to that data, or super peers, will pick it up and update their internal state. The guarantee is, that it will be real-time for those who are online at the same time and that even those who are offline, will eventually receive those updates. (this is called eventual consistency [CAP](https://gun.eco/docs/CAP-Theorem) 
