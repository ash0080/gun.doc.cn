The javascript implementation of GUN is built on layers of functional utility libraries.

## Type

The first and most important one is a Type library that normalizes javascript's behavior across different browsers and environments. This is unfortunately necessary due to javascript's language quirks. GUN's constructor directly inherits from this library.

## Event

The second is an Event library that lets multiple listeners attach themselves to some event emitter. Because GUN uses a [functional reactive programming](./Functional-Reactive-Programming) coding style, almost everything is built on top of this. It lets the API be an observable on streams of data.

## State

Every stream of data is run through a [conflict resolution algorithm](Conflict-Resolution-with-Guns). This lets GUN correctly synchronize state regardless of concurrent updates to the data. So while the Event library lets us observe changes to data over time, the State system lets us always resolve data to its most recent value. Kind of like a promise, since some data may be loaded asynchronously.

## Graphs

Not all data can be expressed as a document or as a table. But graphs can, which is why GUN uses them. This is particularly important for handling conflicts on sub objects. Every object in GUN has its own universally unique identifier, requiring no dependency upon its parent object. These nodes can only have properties with simple values or a pointer to another node. So there is a utility library for handling values, states, nodes, and graphs.

## Deduplication

Because events might be sent multiple times, it is useful to deduplicate them. This means every event needs to have an unique identifier to deduplicate against. If each event has an identifier, we can also use that to create request/response acknowledgements, like asking for data and getting a reply or sending data and getting a confirmation of receipt.