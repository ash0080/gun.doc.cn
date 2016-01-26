> ###**note:** this guide is written for gun v0.2.x and will be outdated with gun v0.3.x, currently being tested on #develop.

[abstraction]: Adding-methods-to-the-gun-chain-(v0.2.x)
[persistence]: Adding-persistence-layers-(v0.2.x)
[communication]: Adding-communication-layers-(v0.2.x)

# Gun Plugins: a how-to guide

This guide explains how to build drivers and extensions for gun. Basic knowledge about [gun's API](API-(v0.3.x)) is recommended, but not required to follow along.

First, let's get an idea for how gun can be extended and the sorts of hooks it provides to developers. Most plugins for gun will fit into 3 main categories:

- [abstraction layers][#abstraction]
- [persistence layers][#persistence]
- [communication layers](#communication)

### Abstraction

Likely the easiest of the three, gun's API is fully extensible, allowing you to define your own methods and abstraction layers or pull in existing ones. If this interests you or if you just want to jump in and start coding, read [this guide][abstraction].

### Persistence

Since gun's main goal is to synchronize data between clients and servers, it doesn't take a strong stance on how your data is saved. Client side, it defaults to localStorage, while server side it exports to a file named `data.json`, although it's designed with plugins in mind so that your favorite storage engine, whether it be levelDB, Hadoop, IndexedDB, AWS, etc., should painlessly integrate with your project. Building a plugin for gun's persistence layer is fairly straightforward, and is outlined in [this guide][persistence].

### Communication

Gun uses websockets to synchronize information, falling back on jsonp if sockets aren't supported in your environment. However *both* transport layers could be exchanged with WebRTC, DDP or any other method of sending and receiving data. The process is essentially the same as writing a persistence driver, where the peer you're messaging is treated as your persistence layer.


## How To:
 - **[Add Methods to the Gun Chain](Adding-Methods-to-the-Gun-Chain)**  
   All of gun's methods are exposed through it's prototype, or as we call it, the gun chain. You can define your own methods by attaching them to that chain. It behaves exactly like an object prototype, so your extension will be accessible to every gun instance within your application.
 - **[Add Storage Services](Adding-Storage-Services)**  
   GUN is a synchronization engine and can be used with a variety of storage services.  Additional storage services can be  added.