# Simple plugins to augment, extend, or modify gun

**Table of Contents**
- [`.subscribe()` (to replace `.on()`)](#subscribe-to-replace-on)

### `.subscribe()` (to replace `.on()`)
Solves the following problem:

Suppose you have a `set` "PlayersDb" that consists of 500+ players and you want to 

* build an Array with all players
* subscribe to the players personal 'score'

you could do:
```
var players = []
var lookup = {}
gun.get('playersDB').map().path('score').on(function(node,soul){

  if( ! lookup[soul]){ 
      players.push(node);
      lookup[soul] = players.length-1;
  } else {
     players[lookup[soul]].score = node.score
  }

})
```
The problem with this approach is that when the subscription runs the fist time it will go through all 500+ players and calls the callback. But you want the callback to run only when something changes. 
Building your initial Array is something you could do with `valMapEnd` or `Each`

`.subscribe()` will only call the callback when something actually changes without going through all nodes initially.

For more information, see the thread starting at :https://gitter.im/amark/gun?at=58b48562e961e53c7f76230c

Add the following prior to instantiating Gun:
```javascript
Gun.chain.subscribe= function(cb){
  return this.on(function(data){
    var at = this._;
    if(!Gun.obj.has(at, 'subscribe')){ return at.subscribe= data }
    if(data === at.subscribe){ return } else { at.subscribe = undefined }
    this.back(1).val(cb);
  });
}
```

Instead of using `.on()`, use `.subscribe()`.  
For example,

```javascript
// subscribe to changes to each players 'score'
playersDB.map().path('score').on(function(node,soul){
   // this runs for every player initialy
})
```
would be:
```javascript
// subscribe to changes to each players 'score'
playersDB.map().path('score').subscribe(function(node,soul){ 
    // this runs only when property 'score' changes
})
```
So remember that `.subscribe()` is just doing that...subscribing. You can NOT use it to build your initial Array, for that you can use 'valMapEnd' or 'each'