<a href="https://youtu.be/qKIn9L2obug" target="_blank" title="GUN map"><img src="http://img.youtube.com/vi/qKIn9L2obug/0.jpg" width="425px"></a><br>

The conflict resolution algorithm (also called HAM) is at the center of everything gun does. It's how peers eventually arrive at the same state, and how offline edits are merged. Every change in the system goes through HAM.

## Requirements
These are the constraints HAM operates under.

### Offline-First
Favor high-availability over strong consistency, allowing users to make edits even when the machine is entirely offline (like a cellphone user without a network connection). This immediately rules out group consensus algorithms like  [Paxos](https://en.wikipedia.org/wiki/Paxos_(computer_science)) or [Raft](https://raft.github.io/).

### Ordering
The same state should be reached regardless of what order updates arrive in (update commutativity).

### Conflict Handling
When merge conflicts happen, every machine should independently choose the same value (Strong Eventual Consistency).

## Implementation
Ultimately, we want to accept an update and merge it into our own data. Since gun's data structure is graph-oriented, updates will be in graph-format.

Because graphs only contain nodes, and nodes only contain key-value pairs, if we know how to merge key-value pairs, we can merge nodes, and in turn we can merge graphs. This means we'll focus on merging key-value pairs.

A key-value pair is atomic to HAM, meaning it won't try to merge primitives together, it'll just choose which to keep. Choosing is the tricky part, and requires an extra bit of metadata, called `state`. State is used to determine ordering of updates, and is always relative to the machine which receives it (including the machine creating the update).

Let's consider an example:

We have a node with one property, "username", a value of `@UberLlama`, and a state of `10`.

<!-- twitter handle? -->

```json
{
	"username": {
		"value": "@UberLlama",
		"state": 10
	}
}
```

We get an update that lists "username" as `@TopLlama` and state as `8`. Since the update state is less than our current state, we list it as only historically important, and don't include it in our data.

> Unless you're using a journaling plugin, historical updates are ignored.

There's a bit more nuance to updates with greater state, so we'll discuss that in a bit. The next obvious question is what happens when we get an update with the same state as us, *but with a conflicting value?*

### Conflicts
Well, according to the goals we listed, **it doesn't matter** what value we choose, so long as everyone chooses it. We just need to be consistent. Another advantage is that gun supports a subset of JSON, so we only need to handle conflicts in that subset.

This allows us to define some simple rules that guarantee convergence, mostly through type and lexical comparisons.

##### Both are equal
Then there's no conflict, it doesn't matter which you choose.

##### Both are pointers
Get the UIDs they point to, and see which string is greater. That's the winner.

##### Only one is a pointer
Choose that one.

##### Both are primitives
Compare their string values with `JSON.stringify`, choosing the greater of the two.

### States
This is dangerous territory, and if handled wrong can expose crippling application vulnerabilities. For example, a devious user submits an update with a state of 10 zillion. Now, no one gets to write until their state reaches 10 zillion plus 1.

That's generally frowned on.

HAM handles this with user-relative merges. When the "10 zillion" update comes in, HAM simply waits until your machine reaches state 10 zillion before acknowledging it's existence. If an update isn't acknowledged, it never escapes volatile memory onto disk. We call this a deferred update.

If no other machines have reached that state, only the attacker will acknowledge the update and persist it to disk.

By now you might have noticed we're not talking about Vector Clocks. Instead, gun uses timestamps. At this point, some people are feeling uncomfortable, and rightly so. Timestamps [can be dangerous](https://aphyr.com/posts/299-the-trouble-with-timestamps), since:

 - System time doesn't always move forward (NTP corrections).
 - Clock synchronization isn't always reliable.
 - Some clocks move faster than others.
 - Your system time might be off by any amount (especially when considering user meddling).

One of our constraints with HAM is that synchronization algorithms should not be required, including [NTP](https://en.wikipedia.org/wiki/Network_Time_Protocol) and it's variants, so accurate clocks can never be assumed.

Luckily, HAM doesn't care if your clock is accurate. It only cares about *machine-relative* ordering, and whether an update should be part of history, current state, or ignored until some point in the future.

### State Boundaries
Each value has two dividing lines, the state of the last update, and what it considers your device's current time.

Back to our `username` example:

```json
{
	"username": {
		"value": "@UberLlama",
		"state": 10
	}
}
```

Your system clock is at state `15`.

> We're using smaller numbers than `Date.now()` because they're easier to mentally compare and reason about.

#### Historical State
Any update with a state less than the last update (`10`) is considered stale and no longer relevant.
```javascript
// This update is too old.
var update = {
	username: {
		value: "@TopLlama",
		state: 8,
	},
}
```

#### Operating State

Any update with state greater than the last update (`10`), yet less than your process state (`15`) is immediately merged. The state of the last update now refers to what we just merged (`12`).

```javascript
// Sweet spot!
// This will be merged.
var update = {
	username: {
		value: "@PsychoLlama",
		state: 12,
	},
}
```

#### Deferred State
Any update with a state greater than your system clock (`15`) is considered deferred, and won't be processed until your clock reaches that point.
```javascript
// Nope, ignore this until the
// clock reaches state `22`.
var update = {
	username: {
		value: "@SupremeLlama",
		state: 22,
	},
}
```

### Applying It
Merging two nodes can be done by iterating over each field of an update, and deciding which field to choose: the one you already have, or the one proposed by the update.

If an update node has a field that the source object doesn't, the source node's field state is assumed to be `-Infinity`, meaning you always add that new field.

The same process can be repeated for graphs, iterating over each node in an update graph and merging it with the source.

You can find the HAM implementation in [`gun.js`](https://github.com/amark/gun/blob/master/gun.js) under the name `Gun.HAM`.

### Considerations
HAM doesn't guarantee multi-process linearizability because in highly-available systems, you don't know when all updates have finished network propagation. Instead, it guarantees Strong Eventual Consistency (SEC). If linearizability is necessary, either use a consensus system like [Paxos](http://research.microsoft.com/en-us/um/people/lamport/pubs/paxos-simple.pdf) (sacrificing availability), or explicitly build it into your data.

 - No Strong Consistency, linearizability, or serializability.
 - Vulnerable to the [Double Spending problem](https://en.wikipedia.org/wiki/Double-spending).

## Questions
If you want more information about how the conflict engine works, you can message us [on Gitter](http://gitter.im/amark/gun/) or post a question on Stack Overflow with the `#gun` tag.

## Further Reading
 - [Where gun stands](https://github.com/amark/gun/wiki/CAP-Theorem) on the CAP theorem.
 - Some challenges to [distributed operations](http://codedependents.com/2014/01/13/mathematical-purity-in-distributed-systems-crdts-without-fear/).
 - [Concerns about timestamps](https://aphyr.com/posts/299-the-trouble-with-timestamps) (Aphyr).
 - [Eventual Consistency vs Strong Eventual Consistency vs Strong Consistency](http://stackoverflow.com/questions/29381442/eventual-consistency-vs-strong-eventual-consistency-vs-strong-consistency).
