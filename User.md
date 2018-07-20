This page will explain [SEA](./SEA)'s `user` API as well as explain architectural decisions underneath each method for security purposes. 

Assume:

```javascript
gun = Gun();
user = gun.user(); 
```

## create

`user.create(alias, passphrase, cb)`

### API
 - coming soon

### Security

See [SEA](./SEA) for algorithm choices.

Essentially, it works like this:

```javascript
//<script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>
//<script src="https://cdn.jsdelivr.net/npm/gun/sea.js"></script>
var sea = Gun.SEA;
var pair = await sea.pair(); // generate a new key pair
console.log(pair);
var alias = "alice"
var pass = "secret";
var salt = 1; // random
var proof = await sea.work(alias, pass); // don't do this! (pass, salt) instead!
var auth = await sea.encrypt(pair, proof);
console.log(auth); // data saved in a cryptographically linked user graph
// now on another machine...
var login = await sea.work(alias, pass);
var keys = await sea.decrypt(auth, login); // encrypted auth loaded from graph
console.log(keys); // equal to the original key pair
```