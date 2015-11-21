Any GUN library in any language must first start with the Hypothetical Amnesia Machine, which is is the conflict resolution algorithm. Also check out this unfinished wiki: https://github.com/amark/gun/wiki/Conflict-Resolution-with-Guns .

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