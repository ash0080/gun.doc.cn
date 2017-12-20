So you want to build a decentralized Twitter with end-to-end encryption, that works right in the browser? All with MIT licensed Open Source code?

Then you've come to the right spot. First, watch these 1 minute explainer videos on how cryptography works:

<a href="http://gun.js.org/explainers/data/security.html" title="2 min demo of auth"><img src="http://img.youtube.com/vi/ccKThyaDR30/0.jpg" width="425px"></a>

Second, thanks to the work of [@mhelander](https://github.com/mhelander) on the SEA (Security, Encryption, Authorization) framework, your app will use the latest native Web Crypto API for all the functions explained in the video series, like ECDSA, PBKDF2, AES, and more. Here is a demo of it working in action:

<a href="https://youtu.be/52Z72bDCtMU" title="2 min demo of auth"><img src="http://img.youtube.com/vi/52Z72bDCtMU/0.jpg" width="425px"></a>

To get started building your app, just include SEA in your app:

```html
<script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>
<script src="https://cdn.jsdelivr.net/npm/gun/lib/cryptomodules.js"></script>
<script src="https://cdn.jsdelivr.net/npm/gun/sea.js"></script>
```

> Note: If the CDN becomes compromised, your app could be hacked, consider Electron-ifying your app to remove any hosts. Also, `lib/cryptomodules` name will change in the future, you'll need to keep your app up to date.

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
user.get('profile').get('boss').get('slave').get('name').val(function(data){
  console.log("The boss's slave's name is:", data); // Alice
});
```

Try running it yourself at https://codepen.io/anon/pen/QajxOz?editors=1012 !

Now that you have P2P identities, you can combine it with the logic from the [5min interactive ToDo app tutorial](http://gun.js.org/think.html), to create a list of tweets from the user.

We'll leave creating the UI to you, since that is harder than building realtime decentralized apps with gun! We will follow up with some example apps in the future. For now, this is an introduction to getting started with SEA and GUN!

Feel free to hit us up with questions on the [gitter](https://gitter.im/amark/gun) in the meanwhile.

Shared objects and private data will be available next with SEA.

Cheers,

The GUN Team