GUN is a small, easy, and fast data sync and storage system that runs everywhere JavaScript does. The aim of GUN is to let you focus on the data that needs to stored, loaded, and shared in your app without worrying about servers, network calls, databases, or tracking offline changes or concurrency conflicts. This lets you build cool apps fast, like:

![](https://gun.eco/see/3dvr.gif "3D multiplayer VR games")
[![](https://gun.eco/see/aiml.gif "Distributed AI/ML")](https://github.com/cstefanache/cstefanache.github.io/blob/master/_posts/2016-08-02-gun-db-artificial-knowledge-sharing.md#gundb)
[![](https://gun.eco/see/gps.gif "Realtime GPS tracking")](http://gps.gundb.io/)
[![](https://gun.eco/see/dataviz.gif)](https://github.com/lmangani/gun-scape#gun-scape "Data Viz")
[![](https://gun.eco/see/p2p.gif "P2P dApps")](https://github.com/amark/gun/wiki/Auth)
[![](https://gun.eco/see/iot.gif "IoT sync")](https://github.com/Stefdv/gun-ui-lcd#okay-what-about-gundb-)

## Offline-First

When a browser peer asks for data, it'll merge the reply with its own data using a [CRDT](https://github.com/amark/gun/wiki/Conflict-Resolution-with-Guns), then cache the result. This means that:

 - The next time the browser asks for that data, the reply is instant, even when offline.
 - Data is replicated on each browser that asked for it.
 - If your server fails, you can still recover your data from the browsers.

This makes the loss of important information nearly impossible, as all copies of the data would need to be destroyed before it is lost.

## Distributed

GUN is fully decentralized (peer-to-peer or multi-master), meaning that changes are not controlled by a centralized server. A server can be just another peer in the network, one that may have more reliable resources than a browser. You save data on one machine, and it will sync it to other peers without needing a complex consensus protocol. It just works.