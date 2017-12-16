Ladies and Gentlemen,

I am proud to announce an early preview of our end-to-end encrypted authorization framework, SEA (Security, Encryption, Authorization)!

It uses ECDSA, PBKDF2, AES, to create proof of work, encrypt data, generate signatures, and verify data. This is done entirely peer-to-peer and replaces the dangerous model of having a server be a middleman for performing authorization.

Here is an early-preview demo of it working in action:

<a href="https://youtu.be/52Z72bDCtMU" title="2 min demo of auth"><img src="http://img.youtube.com/vi/52Z72bDCtMU/0.jpg" width="425px"></a>

And another newer demo of the API:

<a href="https://youtu.be/ik_dqXBMBHw" title="2 min demo of auth"><img src="http://img.youtube.com/vi/ik_dqXBMBHw/0.jpg" width="425px"></a>

To learn more about how it works, check out our [1 minute explainer series on cryptography](http://gun.js.org/explainers/data/security.html).

We'll be releasing the example and the SEA framework soon as part of our 0.7 version, and we are **looking for crackers to audit the system** - email me if you'd like to volunteer. We have now switched to using the browser native Web Cryptography API instead of jsrsasign and cryptojs, thanks to the work of [@mhelander](https://github.com/mhelander).

To get started, you can use SEA in the browser with the following:

```html
<script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>
<script src="https://cdn.jsdelivr.net/npm/gun/lib/cryptomodules.js"></script>
<script src="https://cdn.jsdelivr.net/npm/gun/sea.js"></script>
```

> Note: `lib/cryptomodules` name will change in the future, you'll need to keep your app up to date.

Now in your javascript you can do:

```javascript
var gun = Gun();
var user = gun.user();
```

To create a cryptographic identity, use the following command:

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
user.get('profile').put({name: "Mark Nadal", lives: "Bay Area"});
```

Shared objects and private data will be available next with SEA.

Feel free to hit us up with questions on the [gitter](https://gitter.im/amark/gun) in the meanwhile.

Cheers,

The GUN Team