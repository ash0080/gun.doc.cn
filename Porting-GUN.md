# Porting GUN to Other Languages

We'll walk you through creating a reduced GUN server in your language of choice. It will not be useful as a library to other developers, but it will help you understand what is going on and communicate with GUN peers. You should be able to then expand upon it, adding a nice language specific API so it is usable by other developers.

There are three main categories we'll need to implement for our minimal GUN server.

1. Running a server that speaks GUN's wire spec.
2. Understanding and implementing GUN's graph structure.
3. Processing data through GUN's conflict resolution algorithm.

That's it! Our target implementation will be WebSockets, JSON, and HAM.

## Running a Server

Your first objective is to get a WebSocket server up in your language which sends "hello world!" once every second to a browser which connects to it. Might as well append the count to the message as well. This tests to see if your language can do asynchronous and event driven logic. If it can't, then it will be pretty difficult to implement GUN. Hopefully your language of choice also has a WebSocket server module that somebody has written for you - if not, you can get your hands dirty and build it for everybody else or emulate WebSockets with JSONP over HTTP which we won't cover here.

Here is the solution implemented in NodeJS:

> Note: Code samples throughout this entire article should be considered as more pseudo code than anything else. There is no guarantee that any of them are perfectly working.

```
var WebSocketServer = require('ws').Server
  , wss = new WebSocketServer({ port: 8080 });

wss.on('connection', function connection(ws) {
  ws.on('message', function incoming(message) {
    console.log('received:', message);
  });
  var count = 0;
  setInterval(function(){
    count += 1;
    ws.send('hello world ' + count);
  }, 1000);
});
```

And here is the code that you can paste into the browser to see if it and your WebSocket server works.

```
// paste this into your browser console to test your WebSocket server!
var ws = new WebSocket('ws://localhost:8080'); // change this address to your server, if different!
ws.onopen = function(o){ console.log('open', o) };
ws.onclose = function(c){ console.log('close', c) };
ws.onmessage = function(m){ console.log('message', m.data) };
ws.onerror = function(e){ console.log('error', e) };
```

You might need to paste it into a browser tab that isn't on HTTPS or blocking cross origin. If you have problems, just wrap it into an HTML file that you save to your computer and then open it in the browser.

## Conflict Resolution - THIS SECTION IS NOT FINISHED AND WILL PROBABLY BE ENTIRELY REWRITTEN

Any GUN library in any language must first start with the Hypothetical Amnesia Machine, which is is the conflict resolution algorithm. Also check out the wiki page on [Conflict Resolution with Guns](Conflict-Resolution-with-Guns).

Parameters:

 * `machineState`: The current time of the local machine.
 * `incomingState`: The time of the update from the remote machine.
 * `currentState`: The time of the last update on the local machine.
 * `incomingValue`: The data coming from the remote machine.
 * `currentValue`: The data stored in the local machine.

```javascript
function HAM(machineState, incomingState, currentState, incomingValue, currentValue){ // TODO: Lester's comments on roll backs could be vulnerable to divergence, investigate!
	if(machineState < incomingState){
		// the incoming value is outside the boundary of the machine's state, it must be reprocessed in another state.
		return {defer: true};
	}
	if(incomingState < currentState){
		// the incoming value is within the boundary of the machine's state, but not within the range.
		return {historical: true};
	}
	if(currentState < incomingState){
		// the incoming value is within both the boundary and the range of the machine's state.
		return {converge: true, incoming: true};
	}
	if(incomingState === currentState){
		if(incomingValue === currentValue){ // Note: while these are practically the same, the deltas could be technically different
			return {state: true};
		}
		/*
			The following is a naive implementation, but will always work.
			Never change it unless you have specific needs that absolutely require it.
			If changed, your data will diverge unless you guarantee every peer's algorithm has also been changed to be the same.
			As a result, it is highly discouraged to modify despite the fact that it is naive,
			because convergence (data integrity) is generally more important.
			Any difference in this algorithm must be given a new and different name.
		*/
		if(String(incomingValue) < String(currentValue)){ // String only works on primitive values!
			return {converge: true, current: true};
		}
		if(String(currentValue) < String(incomingValue)){ // String only works on primitive values!
			return {converge: true, incoming: true};
		}
	}
	return {err: "you have not properly handled recursion through your data or filtered it as JSON"};
}
```
If you can implement this in your language of choice, then congratulations you now have a GUN driver. Everything else about GUN is pretty much just an API surface that wraps around this, providing convenience functions for the end developer. So first thing first, write this in your language of choice.

To recap, all data in GUN gets boiled down to these 5 parameters such that they can be compared and converged on, given the HAM results. It is an incredibly simple algorithm but it is universally detailed in how to handle data synchronization.

Here are some [slides](https://docs.google.com/presentation/d/1XEj6tgt0NbBzzMKIlJ2kuMWH41hY7IkUdCmUWahipdA/edit#slide=id.gba5bd53d5_0_72) from a Tech Talk I gave in Germany on the HAM. However it unfortunately was not recorded and may not be helpful without video or audio. I did a quick summary of it in [this talk](https://youtu.be/kzrmAdBAnn4?t=21m11s).