GUN is a small, easy distributed data sync and storage solution that runs everywhere JavaScript does. GUN lets you focus on the data you need to store, retrieve, and share without worrying about servers, network calls, databases, or handling offline edits and merge conflicts. This lets you build cool apps fast, like:

![](https://gun.eco/see/3dvr.gif "3D multiplayer VR games")
[![](https://gun.eco/see/aiml.gif "Distributed AI/ML")](https://github.com/cstefanache/cstefanache.github.io/blob/master/_posts/2016-08-02-gun-db-artificial-knowledge-sharing.md#gundb)
[![](https://gun.eco/see/gps.gif "Realtime GPS tracking")](http://gps.gundb.io/)
[![](https://gun.eco/see/dataviz.gif)](https://github.com/lmangani/gun-scape#gun-scape "Data Viz")
[![](https://gun.eco/see/p2p.gif "P2P dApps")](https://github.com/amark/gun/wiki/Auth)
[![](https://gun.eco/see/iot.gif "IoT sync")](https://github.com/Stefdv/gun-ui-lcd#okay-what-about-gundb-)

## Offline-First

When a browser peer sends a request, it'll merge the response with its own data using our [graph-based CRDT conflict resolution algorithm](https://github.com/amark/gun/wiki/Conflict-Resolution-with-Guns), then cache the result. Since it's cached in the browser, there are a few interesting side effects:

 - The next time the browser sends that request, the response is instantaneous, even when offline.
 - Data is replicated on each browser that requested it.
 - If your server catastrophically fails, you can still recover your data from the browsers.

This makes the loss of important information nearly impossible, as all copies of the data must be destroyed for it to be unrecoverable. A server is also just a peer, but isn't as picky about what they cache.

## Distributed

GUN is peer-to-peer (multi-master decentralized replication), meaning updates do not need a centralized server. You save data on one machine, and you can sync it with other peers without needing a complex consensus systems. It just works.

However, you do not need peers or servers to use GUN, they're completely additive!