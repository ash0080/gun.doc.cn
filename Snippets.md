This page documents simple code snippets which augment or modify basic gun functionality.

## Strip metadata from returned nodes

### `.live()` (to replace `.on()`)

Add the following prior to instantiating Gun:
```javascript
Gun.chain.live = function(cb, opt){ 
  return this.on(function(val, field){ 
    delete val._; 
    cb.call(this, val, field); 
  }, opt); 
}
```

Instead of using `.on()`, use `.live()`.  
For example,

```javascript
// subscribe to changes to my player bucket
playerNamesDB.on(function(data){
```
would be:
```javascript
// subscribe to changes to my player bucket
playerNamesDB.live(function(data){
```
For more information, see the thread starting at : https://gitter.im/amark/gun?at=566de558187e75ea0e48771e



### `.value()` (to replace `.val()`)

Add the following prior to instantiating Gun:
```javascript
Gun.chain.value = function(cb, opt){
  return this.val(function(val, field){
    delete val._;
    cb.call(this, val, field);
  }, opt);
}
```

Then, instead of using `.val()`, use `.value()`.  
For example,

```javascript
// subscribe to changes to my player bucket
playerNamesDB.val(function(data){
```
would be:
```javascript
// subscribe to changes to my player bucket
playerNamesDB.value(function(data){
```
For more information, see the thread starting at : https://gitter.im/amark/gun?at=566de558187e75ea0e48771e

### `Gun.create()` to instantiate without `new`

Linters will complain if a gun instance is created without the `new` keyword.
You could either use `new`, or you could use `Gun.create([args])`.

```javascript
Gun.create = function () {
  return Gun.apply(this, arguments);
};
```

Referenced and suggested in [issue #6](https://github.com/amark/gun/issues/6)