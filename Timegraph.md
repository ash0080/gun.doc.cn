# Timegraph .time()
> Warning: The Timegraph extension isn't required by default, you would need to include at "gun/lib/time.js"

A Timegraph allows you to store and retrieve data that is stored at a relative time.


## .time( function(data, key, time) )

Subscribes to all future events that occur on the Timegraph
* `data` : The data in the node retrieved
* `key`  : The key name of the node retrieved
* `time` : The time that the node was uploaded

## .time( function(data, key, time), num)
Subscribes to all future events that occur on the Timegraph and retrieve a specified number of old events
* `data` : The data in the node retrieved
* `key`  : The key name of the node retrieved
* `time` : The time that the node was uploaded
* `num`  : The number of past nodes to retrieve

## .time( data )
Pushes data to a Timegraph with it's time set to `Gun.state()`'s time

## Example

You will need a reference to a node to store a timegraph in.
```
var node = Gun.get("<Your Node Here>");
```
> Warning: As of now a Timegraph can only be stored inside one of the top level nodes. Eg. You can do 'gun.get().get()'

Once You have your node you can use any of the **.time()**  methods on it.

