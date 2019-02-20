This page will explain [SEA](./SEA)'s `user` API as well as explain architectural decisions underneath each method for security purposes. 

Assume:

```javascript
gun = Gun();
user = gun.user(); 
```

## User.create

Creates a new user and calls callback upon completion.

### Syntax

```
user.create(alias, pass, cb, opt)
```

### Parameters

alias (string) - Username or Alias which can be used to find a user. 

pass (string) - Passphrase that will be extended with PBKDF2 to make it a secure way to login.

cb(ack) (function) - Callback that is to be called upon creation of the user.

opt (object) - Option Object containing options for creation. (In gun options are added at end of syntax. opt is rarely used, hence is added at the end.)

### Return via Callback

On successful creation of the user the callback is called with an 'ack' object. (acknowledgment)
```
//calls cb(ack) where ack is an object as below
{
    ok: 0,
    pub: 'fe4...ee3' //public key of the user that was just created
}
```

On failure you will receive an 'ack' object. (acknowledgment)
```
//calls cb(ack) where ack is an object as below
{
    err: // with one of 2 possible errors described below
}
```

If user is already being created:
"User is already being created or authenticated!"

If user already exists:
"User already created!"

### Unexpected Behavior

There is no enforcement of uniqueness for the alias. If this is required for your application, you need to enforce it with a gun.get("~@alias").once(callback) to check if user already exists.

## User.auth

Authenticates a user, previously created via User.create.

### Syntax

```
user.auth(alias, pass, cb, opt)
```

### Parameters

alias (string) - Username or Alias which can be used to find a user.

pass (string) - Passphrase for the user

cb(userReference) (function) - Callback that is to be called upon authentication of the user.

opt (object) - Option Object containing options for creation. (In gun options are added at end of syntax. opt is rarely used, hence is added at the end.)

### Return via Callback

```
// on success calls callback with a reference to the gun user
// cb(at) where at is an object as below
{
    ack: 2,
    back: object, //reference to the root object (internal)
    get: "~publicKeyOfUser",
    gun: object, //gun root (internal)
    id: 6, //id of the node in graph (internal)
    on: function onto(),
    opt: object, //uuid function object (internal)
    put: object, //object containing pub, alias and epub of the user
    root: object, //gun root reference (internal)
    sea: object, //object containing keys of the user
    soul: "~publicKeyOfUser",
    tag: object //gun in and out reference (internal)
}


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