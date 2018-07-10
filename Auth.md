So you want to build a decentralized Twitter with end-to-end encryption, that works right in the browser? All with MIT licensed Open Source code?

Then you've come to the right spot. First, watch these 1 minute explainer videos on how cryptography works:

<a href="https://gun.eco/explainers/data/security.html" title="2 min demo of auth"><img src="http://img.youtube.com/vi/ccKThyaDR30/0.jpg" width="425px"></a>

Second, thanks to the work of [@mhelander](https://github.com/mhelander) on the SEA (Security, Encryption, Authorization) framework, your app will use the latest native Web Crypto API for all the functions explained in the video series, like ECDSA, PBKDF2, AES, and more. Here is a demo of it working in action:

<a href="https://youtu.be/52Z72bDCtMU" title="2 min demo of auth"><img src="http://img.youtube.com/vi/52Z72bDCtMU/0.jpg" width="425px"></a>

To get started building your app, just include SEA in your app:

```html
<script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>
<script src="https://cdn.jsdelivr.net/npm/gun/sea.js"></script>
```

> Note: If the CDN becomes compromised, your app could be hacked, consider Electron-ifying your app to remove any hosts.

Now in your javascript you can instantiate gun and and reference your user:

```javascript
var gun = Gun();
var user = gun.user();
```

To create a cryptographic identity backed by a public/private key-pair (see the video explainer), just do:

```javascript
// Browser Native Web Crypto API is used to PBKDF2 extend your password.
user.create('alice', 'unsafepassword', function(ack){
  // done creating user!
});
```

Once you have created a user, you can log them in with:

```javascript
// Browser Native Web Crypto API is used to PBKDF2 extend your password.
user.auth('alice', 'unsafepassword', function(ack){
  // logged in!
});
```

Finally, you can then save data to their account that nobody else can write to:

```javascript
var alice = {name: "Alice"};
alice.boss = {name: "Fluffy", species: "Kitty", slave: alice};
user.get('profile').put(alice);
```

When it is stored on disk or sent over the wire, it uses cryptographic signatures (see the video explainer), to secure the account and data without relying upon any trusted servers!

And then when you use GUN to read the data, it automatically verifies and decrypts the data for you:

```javascript
user.get('profile').get('boss').get('slave').get('name').once(function(data){
  console.log("The boss's slave's name is:", data); // Alice
});
```

Try running it yourself at https://codepen.io/anon/pen/QajxOz?editors=1012 !

Now that you have P2P identities, we will combine it with the logic from the [2min interactive ToDo app tutorial](https://gun.eco/think.html), to create a list of tweets from the user:

To do this, [follow along with the 4 minute interactive P2P Twitter tutorial](https://scrimba.com/c/c2gBgt4)!

Now you have built a fully decentralized end-to-end encrypted app with GUN, congratulations! Check out the rest of the docs to learn more, like how to synchronize your data with other peers or about the rest of the API.

Feel free to hit us up with questions on the [gitter](https://gitter.im/amark/gun) in the meanwhile.

Shared objects and private data will be available next with SEA.

Cheers,

The GUN Team