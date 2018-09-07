We're going to build a P2P dApp! This tutorial assumes you have moderate web development experience (don't? Check out our [beginner's guide](./My-First-Todo-App)!), and will walk you through each step. Let's start with some basic HTML, to create a multi-user public todo app:

```html
::: {codepen: 'link', tab1: 'codemirror'} :::
::: {editor: 'main'} :::
::: {startblock: '1'} :::
<html>
  <body>
    <h1>Todo</h1>

    <form id="sign">
      <input id="alias" placeholder="username">
      <input id="pass" type="password" placeholder="passphrase">
      <input id="in" type="submit" value="sign in">
      <input id="up" type="button" value="sign up">
    </form>

    <ul></ul>

    <form id="said">
        <input id="say">
        <input id="speak" type="button" value="speak">
    </form>

::: {endblock: '1'} :::
::: {startblock: '2'} :::
  </body>
</html>
::: {endblock: '2'} :::
```

First, we need to include [GUN](https://gun.eco/), [SEA](https://gun.eco/docs/SEA), and the WebRTC adapter right above the closing `</body>` tag! (Plus some jQuery, but you can use React/Angular/etc.)

```html
    <script src="https://cdn.jsdelivr.net/npm/gun/examples/jquery.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/gun/sea.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/gun/lib/webrtc.js"></script>
    <script>
    // dApp code will go here!
    </script>
```

(Add this code to the above interactive editor!)

### But CDNs are dangerous!

For security purposes, we recommend you include these dependencies with your app, rather than trusting a public CDN. (Although we do love jsDelivr! It is free, open source, and give us download stats!)

> Pro tip: If all your dependencies are local, your app can work offline-first! And then GUN will sync when it comes back online.

### What about require, import, etc.?

What about `require` and minified bundles? Yupe, you can do that too! Just follow the [instructions](./Installation). For sake of simplicity and accessibility, this article uses the most-common-denominator of tools. Any developer with advanced experience should be able to easily follow along and independently switch from plain script tags into ES6 syntax. However, any developer that does not understand how script tags work, needs to re-learn the basics.

### What is RTC, SEA, etc. ?

GUN is a graph database. SEA is a [cryptographic](https://gun.eco/explainers/data/security.html) security library for GUN, and WebRTC enables P2P connections to other browsers. GUN is designed to be modular and has many layers. For a high level view of the ecosystem, check out the main [readme](https://github.com/amark/gun).

::: {nextstepcompare: 'start'} :::
```
::: {startblock: '3'} :::
::: {insertblock: '1'} :::
    <script src="https://cdn.jsdelivr.net/npm/gun/examples/jquery.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/gun/sea.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/gun/lib/webrtc.js"></script>
    <script>
::: {endblock: '3'} :::
    // dApp code will go here!
::: {startblock: '4'} :::
    </script>
::: {insertblock: '2'} :::
::: {endblock: '4'} :::
```
::: {nextstepcompare: 'end'} :::

::: {step: 'Connecting'} :::

```html
::: {codepen: 'link', tab1: 'codemirror'} :::
::: {editor: 'main'} :::
::: {insertblock: '3'} :::
/* replace this line */
::: {insertblock: '4'} :::
```

The first thing we want to do is initialize GUN and connect to other peers in a network. To start, let's connect to 2 peers, one in your local network (which we will show you how to setup later), and one in a public network. Add this line to the app, replacing the comment:

```javascript
    var gun = Gun(['http://localhost:8765/gun', 'https://gunjs.herokuapp.com/gun']);
```

### Aren't those servers? I thought GUN was P2P!

Browsers (and internet firewalls) and even WebRTC, for legacy security reasons, won't let you directly connect to other machines unless they have a publicly accessible IP address (your `localhost` might! If you have an IPv6 address and no firewall). To get around this, WebRTC uses "signaling servers" to coordinate where non-IPv6 peers (like a browser) are, and **then** attempts to establish a P2P connection.

So to answer the question, yes - GUN is P2P but the internet is not, it is broken and we're working on fixing that with [AXE](http://axe.eco/).

### How do peers discover each other?

GUN makes things better via "relay peers". They automatically run a "signaling server" inside of them, but can also be fully decentralized. They have no centralized logic, and even if peers fail to establish a WebRTC connection, they can then use a [DAM](./DAM) (Daisy-chain Ad-hoc Mesh-network) networking algorithm to relay messages between peers that are not directly connected. They are easy to run, require no maintenance, and can be deployed in 1 click:

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/amark/gun)

See the [README](https://github.com/amark/gun) for other ways (Docker, Now, etc.) to deploy relay peers. 
You can also run one on your local machine from the terminal: (check the [README](https://github.com/amark/gun) if you have any problems with the command)

$`npm install gun && cd node_modules/gun && npm start`

### Where does data get stored?

Unlike Bitcoin which has to store all data on all peers, GUN can have any peer store any (or all) data. What they actually store is usually decided by what data the peer is subscribed to (we'll cover this in the next sections). Relay peers, however, will try to opt into "superpeer" mode and store everything (if they can).

In browsers, data will get stored in `localStorage` by default, but an `indexedDB` adapter also exists.

In node, data will be stored on disk by RAD (Radix storage engine), but plugins for S3 and other storage systems also exist.