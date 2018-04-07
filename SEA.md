SEA is split into two parts, the `gun.user()` chain and `Gun.SEA` utility. This page focuses on documentation for the utility library.

SEA is an easy API for the cryptographic primitives explained in the [1min animated explainer cartoon series](https://gun.eco/explainers/data/security.html), that wraps painful ones like the browser native WebCrypto API. We hope to have it swappable with WASM libsodium and/or local proxies to Electron/NodeJS or browser extensions.

```html
<script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>
<script src="https://rawgit.com/amark/gun/master/sea.js"></script>
<script>
// var Gun = require('gun'); // in NodeJS 
// require('gun/sea');
var SEA = Gun.SEA;
</script>
```

## Return

By default, SEA requires all shims to be compatible with callbacks so it can work in different javascript environments.

However, for the WebCrypto shim, it also supports an optional async await API because any browser that has WebCrypto is also going to have promise support.

As a result, and for sake of simplicity, documentation will use this for readability. Keep in mind that:

```javascript
const data = await SEA.util(a, b)
```

Is swappable for the **official API**:

```javascript
SEA.util(a, b, function(data){

});
```

 > Note: `util` doesn't actually exist, it is just a placeholder for the actual utility methods documented below.

## Errors

Unfortunately, the beauty of `await` is often ruined with a try catch pyramid of doom in practice. To get around this, errors get passed forward. However, passing errors forward with standard NodeJS callback style is awkward:

```javascript
const [err, data] = await SEA.util(a, b) // awkward
if(err){ ... }
```

So we have opted for a cleaner approach:

```javascript
data = await SEA.util(a, b)
if(!data){ ... } // undefined === data is safer
```

If `data` exactly equal to `undefined` then something went wrong. No matter what.

> Note: Careful not to confuse it with `0` or other falsy values

So then where is the error?

You might not like this, but it makes sense - the last error will be in an always easily accessible location, without worry about scope or context, which makes debugging happen faster:

```javascript
console.log(SEA.err) // last known error
```

This is a good thing. And it works the same with the official callback style:

```javascript
SEA.util(a, b, function(data){
  if(!data){ return console.log(SEA.err) } // undefined === data is safer
});
```

## work

```javascript
proof = await SEA.work(text, mix)
```

This gives you a Proof of Work (PoW)!

 - `text` is the data you want to hash (aka, do work based off of it).
 - `mix` **optional** salt (something that prevents attackers from pre-computing the work in advance).
 - - If it is not specified, it will be random, which ruins your chance of ever being able to deterministically re-derive the work (most apps will want to do this, see examples below).

> Note: **API subject to change**, in the future we may change the output format.

### Example

The `user` system uses PoW to allow for truly decentralized (and even offline-first) username/password based login without risking an attacker being able to brute-force crack your password. You can use this to create a forgot password hint system, or a variety of things:

```javascript
function forgot(username, A1, A2){
  // A1 and A2 are answers to security questions they made earlier.
  var scrambled = await gun.user().get('hint').then();
  var proof = await Gun.SEA.work(A1, A2);
  var hint = await Gun.SEA.decrypt(scrambled, proof);
  return hint; // your password hint!
}
```

 > Note: Make sure the security questions cannot have answers that are easily guessed - most banks use ones that are terrible, have finite or short answers or ones that can be found online or in public records. Be careful!

Without the Proof of Work, an attacker could easily guess millions of answers a second and crack the hint at most in a few years. With the Proof of Work, every guess has to go through an exponential amount of work, limiting them to only a few guesses per second, making it possibly take 100+ years to crack.

The default cryptographic primitive that we chose for Proof of Work is PBKDF2, since our primary use case is for extending passwords.

## pair
 
```javascript
var pair = await SEA.pair()
```

This generates a cryptographically secure public/private key pair - be careful not to leak the private keys!

> Note: **API subject to change** we may change the parameters to accept data and work, in addition to generation.

You will need this for most of SEA's API, see those method's examples.

The default cryptographic primitives for the asymmetric keys are ECDSA for signing and ECDH for encryption.

## sign

```javascript
message = await SEA.sign(data, pair)
```

Adds a signature to a message, for data that you want to prevent attackers tampering with.

 - `data` is the content that you want to prove is authorized.
 - `pair` is from [`.pair`](#pair).

### Example

The `user` system uses this to make it so that only you can write to your own data, and all other write attempts are rejected from being synchronized. For example:

```javascript
var other = await SEA.pair();
var msg = await SEA.sign("I wrote this message! You did not.", pair);
console.log(await SEA.verify(msg, other)); // false
```

The default cryptographic primitive signs a SHA256 fingerprint of the data.

## verify

```javascript
data = await SEA.verify(message, pair)
```

Gets the data if and only if the message can be verified as coming from the person you expect.

 - `message` is what comes from [`.sign`](#sign).
 - `pair` from [`.pair`](#pair) or its public key text (`pair.pub`).

### Example

The `user` system uses this to make it so that only you can write to your own data, and all other write attempts are rejected from being synchronized. For example:

```javascript
var data = "I wrote this message! You did not.";
var msg = await SEA.sign(data, pair);
console.log(await SEA.verify(msg, pair)); // true
```

> Note: You must be careful to use a public key that you already trust, not one that just anybody could send to you (an attacker would try to send you a fake public key).

The default cryptographic primitive verifies a SHA256 fingerprint of the data.

## encrypt

```javascript
message = await SEA.encrypt(data, pair)
```

Takes some data that you want to keep secret and encrypts it so nobody else can read it.

 - `data` is the content that you want to encrypt.
 - `pair` from [`.pair`](#pair) or a passphrase you want to use as a cypher to encrypt with.

### Example

```javascript
var msg = await SEA.encrypt("Please do not tell this to anybody", pair);
console.log(await SEA.decrypt(msg, pair)); // true
```

## decrypt

```javascript
data = await SEA.encrypt(message, pair)
```

Read the secret data, if and only if you are allowed to.

 - `message` is what comes from [`.encrypt`](#encrypt).
 - `pair` from [`.pair`](#pair) or the passphrase to decypher the message.

### Example

```javascript
var msg = await SEA.encrypt("Please do not tell this to anybody", 'secret passphrase');
console.log(await SEA.decrypt(msg, 'passphrase secret')); // false
```

---

Let's try putting all of them together:

```html
<script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>
<script src="https://rawgit.com/amark/gun/master/sea.js"></script>
<script>
;(async () => {
var SEA = Gun.SEA;
var pair = await SEA.pair();
var enc = await SEA.encrypt('hello self', pair);
var data = await SEA.sign(enc, pair);
console.log(data);
var msg = await SEA.verify(data, pair);
var dec = await SEA.decrypt(msg, pair);
var proof = await SEA.work(dec, pair);
var check = await SEA.work('hello self', pair);
console.log(dec);
console.log(proof === check);
})();
</script>
```