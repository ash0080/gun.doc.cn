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

First, we need to include [GUN](https://gun.eco/), [SEA](https://gun.eco/docs/SEA), and the WebRTC adapter right above the closing `</body>` tag! (Plus some jQuery, but you could use React/Vue/etc.)

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

GUN is a graph database. SEA is a [cryptographic](https://gun.eco/explainers/data/security.html) security library for GUN, and WebRTC enables P2P connections to other browsers. GUN is designed to be modular and has many layers. For a high level view of the ecosystem, check out the main [readme](https://github.com/amark/gun). But we'll learn more about these things in the next steps.

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

::: {step: 'Peers'} :::

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

Browsers (and internet firewalls) and even WebRTC, for legacy security reasons, won't let you directly connect to other machines unless they have a publicly accessible IP address (your `localhost` might! If you have an IPv6 address and no firewall). To get around this, WebRTC uses public "signaling servers" to coordinate where non-IPv6 peers (like a browser) are, and **then** attempts to establish a P2P connection if possible.

So to answer the question, yes - GUN is P2P but the internet is not, it is broken and we're working on fixing that with [AXE](http://axe.eco/).

### How do peers discover each other?

GUN makes things better via "relay peers". They automatically run a "signaling server" inside of them, but can also be fully decentralized. They have no centralized logic, and even if peers fail to establish a WebRTC connection, they can then use a [DAM](./DAM) (Daisy-chain Ad-hoc Mesh-network) networking algorithm to relay messages between peers that are not directly connected. They are easy to run, require no maintenance, and can be deployed in 1 click:

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/amark/gun)

> Note: Soon, they'll form into an automatic DHT with AXE! If you do not want your peer to be listed, you'll need to disable the DHT feature.
 
You can also run one on your local machine, which is great for dev purposes, right from the terminal: (check out the [README](https://github.com/amark/gun) if you have any problems with the command, or for other ways to deploy relay peers - like with Docker, Now, etc.)

$`npm install gun && cd node_modules/gun && npm start`

Or here is a **crazy 1-liner** for running a node peer: (We don't recommend you do this though, check the [HTTP example for a better documentation reference](https://github.com/amark/gun/blob/master/examples/http.js).)

```javascript
gun = (Gun = require('gun'))({web: require('http').createServer(Gun.serve(__dirname)).listen(8765) })
```

### Where does data get stored?

Unlike Bitcoin which has to store all data on all peers, GUN can have any peer store any (or all) data. What they actually store is usually decided by what data the peer is subscribed to (we'll cover this in the next sections). Relay peers, however, will try to opt into "superpeer" mode and store everything (if they can).

In browsers, data will get stored in `localStorage` by default, but an `indexedDB` adapter also exists using RAD (our Radix storage engine).

In node, RAD will by default dump to disk with `fs`, but plugins for AWS S3 and other storage systems are also available.

::: {nextstepcompare: 'start'} :::
```
::: {startblock: '5'} :::
::: {insertblock: '3'} :::
    var gun = Gun(['http://localhost:8765/gun', 'https://gunjs.herokuapp.com/gun']);
::: {endblock: '5'} :::
::: {insertblock: '4'} :::
```
::: {nextstepcompare: 'end'} :::

::: {step: 'Users'} :::

```html
::: {codepen: 'link', tab1: 'codemirror'} :::
::: {editor: 'main'} :::
::: {insertblock: '5'} :::
/* Add code here... */
::: {insertblock: '4'} :::
```

It is important to note that *peer topology* (what machines you are connected to in a network) has nothing to do with *user or data security*. You might be connected to Alice and Bob, but be syncing data about Carl and Dave in a perfectly secure manner. As such, GUN has a [User](./User) system built on the cryptographic primitives of [SEA](./SEA).

We now need to add some code to handle user registration and login:

```javascript
::: {startblock: '6'} :::
    var user = gun.user();

    $('#up').on('click', function(e){
      user.create($('#alias').val(), $('#pass').val());
    });

    $('#sign').on('submit', function(e){
      e.preventDefault();
      user.auth($('#alias').val(), $('#pass').val());
    });
::: {endblock: '6'} :::
```

As appropriately named, we instantiate a `user` [chain](./Functional-Reactive-Programming) off of `gun`, and use that chain context to do further user related operations. Like registering a new user with `.create` or logging in with `.auth` by passing it the HTML form's input values via jQuery (or whatever UI framework you choose).

### But usernames and passwords aren't unique or secure!

Correct. So be warned, **usernames are not unique** in SEA (yet logins will still work), and SEA does **not** generate keys based on passwords. To learn more about how this works, check out the 1 minute animated [Cartoon Cryptography](https://gun.eco/explainers/data/security.html) explainers series.

### What happens if a user forgets their password?

Passwords [can be reset](./FAQ#how-can-i-change-a-user-password) (without a server), but need a UI/UX around it - before adding that, yes, if your user forgets their password, their account won't be able to be recovered. So **be warned**, and prepare for this early! An optimal solution for social (not banking), would be to do a 3-factor "friend" recovery.

### Isn't it dangerous for passwords and keys to be in JS?

Yes, if you aren't careful, your user's password (or worse, their private keys) could be stolen by XSS or other attacks. So **be warned**, and encourage users to use [MetaMask](https://metamask.io/) with the SEA plugins! This will keep passwords and keys in the browser, not in the app.

::: {nextstepcompare: 'start'} :::
```
::: {startblock: '7'} :::
::: {insertblock: '5'} :::
::: {insertblock: '6'} :::
::: {endblock: '7'} :::
::: {insertblock: '4'} :::
```
::: {nextstepcompare: 'end'} :::

::: {step: 'App'} :::

```html
::: {codepen: 'link', tab1: 'codemirror'} :::
::: {editor: 'main'} :::
::: {insertblock: '7'} :::
/* 1 */

/* 2 */

/* 3 */
::: {insertblock: '4'} :::
```

Now we can finally build the todo logic! There are 3 things we want to achieve:

1. When the user adds an item, save it to GUN.
2. Update the UI.
3. Change the UI upon logging out or into the app.

So first up, we need to handle a form submission. Replace `/* 1 */` with this:

```javascript
::: {startblock: '8'} :::
    $('#said').on('submit', function(e){
      e.preventDefault();
      if(!user.is){ return }
      user.get('said').set($('#say').val());
      $('#say').once("");
    });
::: {endblock: '8'} :::
```

`user.is` will be falsy if the user isn't logged in (we will handle the asynchronous login event in 3).

... WIP ...

Next up, paste this into `/* 2 */`:

```javascript
::: {startblock: '9'} :::
    function UI(say, id){
      var li = $('#' + id).get(0) || $('<li>').attr('id', id).appendTo('ul');
      $(li).text(say);
    };
::: {endblock: '9'} :::
```

Finally, add this in place of `/* 3 */`:

```javascript
::: {startblock: '10'} :::
    gun.on('auth', function(){
      $('#sign').hide();
      user.get('said').map().once(UI);
    });
::: {endblock: '10'} :::
```

[SEA](./SEA) calls the `'auth'` event when the user successfully logs in. This can be used to trigger a UI change, like hiding the login form.

`user.get('said').map().once(UI)` is actually one of the more complex commands in GUN, even if it looks simple. It performs a graph traversal that does:

 - On the `user` **document**, get the `'said'` property.
 - This property happens to be a **table** in the graph
... WIP ...