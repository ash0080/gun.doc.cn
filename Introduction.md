GUN is a small, distributed data sync and storage solution that runs everywhere JavaScript does. GUN lets you focus on the data you need to store, retrieve and share without worrying about merge conflicts, network partitions, or synchronizing offline edits.

#### Offline-First
When a browser peer sends a request, it'll merge the responses with its own model using our [conflict resolution](https://github.com/amark/gun/wiki/Conflict-Resolution-with-Guns), then cache the result. Since it's cached on the client, there are a few interesting side effects:

 - The next time the client sends that request, the response is instantaneous, even when offline.
 - Data is replicated on each client that requests it.
 - If your server catastrophically fails, you can still recover your data from the clients.

This makes the loss of important information nearly impossible, as all copies of the data must be destroyed for it to be unrecoverable.

Servers act pretty much the same, but aren't as picky about what they cache.

#### Distributed
GUN is peer-to-peer (multi-master replicated), meaning updates don't need a centralized controller. You save data on one machine, and you can sync it with other peers without needing complex consensus systems. It just works.

However, you don't need peers or servers to use GUN, they're completely additive.