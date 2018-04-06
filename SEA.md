SEA is split into two parts, the `gun.user()` chain and `Gun.SEA` utility. This page focuses on documentation for the utility library.

SEA is an easy API for the cryptographic primitives explained in the [1min animated explainer cartoon series](https://gun.eco/explainers/data/security.html), that wraps painful ones like the browser native WebCrypto API. We hope to have it swappable with WASM libsodium and/or local proxies to Electron/NodeJS or browser extensions.

```html
<script src="https://cdn.jsdelivr.net/npm/gun/gun.js">
<script src="https://cdn.jsdelivr.net/npm/gun/sea.js">
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
if(!data){ ... }
```

If `data` is `undefined` then something went wrong. No matter what.

So then where is the error?

You might not like this, but it makes sense - it is an always easily accessible location, without worry about scope or context, that makes debugging happen faster:

```javascript
console.log(SEA.err)
```

This is a good thing. And it works the same with the official callback style:

```javascript
SEA.util(a, b, function(data){
  if(!data){ return console.log(SEA.err) }
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

### Example

The `user` system uses this to allow for username/password based login methods without risking an attacker being able to brute-force crack your password. You can use this to create a forgot password hint system:

```javascript
function forgot(username, A1, A2){
  // A1 and A2 are answers to security questions they made earlier.
  var scrambled = await gun.get(username).get('hint').then();
  var proof = await Gun.SEA.work(A1, A2);
  var hint = await Gun.SEA.enc(scrambled, proof);
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

You will need this for most of SEA's API, see those method's examples.