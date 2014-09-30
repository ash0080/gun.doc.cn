Because of an overlap in words, establishing a precise vocabulary is important.

Example of conflict:
- _Node_ can refer to a node in a graph, a node in a network, or node as in NodeJS.

Therefore, please conduct your language with the following semantics:
- **Group**, is a known, finite, unordered collection of things.
- **Peer**, is a gun server that can be connected to.
- **Mesh**, is a group of one or more peers connected via some topology.
- **Partition**, is the disconnection of peers, forming a divergent mesh.
- **Value**, is a binary, number, text, or relation that exists as a singular whole.
- **Field**, is a symbol used to reference a value at any time.
- **Node**, a group of no, one, some, or all fields, as they change over time.
- **Graph**, all nodes over all time over all meshes.
- **Key**, is an index used to hopefully find a node in a mesh.

Now, for matters of operations, we will define the following:
- **Set**, as a verb, is changing the value on a field, or fields.
- **Stream**, the sets on a node over a series of divisible time.
- **State**, the sum of streams on a node that a peer had at some time.
- **Sign**, verifying the hashed signature of a compressed state.
- **Sync**, exchanging states and streams between peers to arrive at a signature.
- **Save**, to snapshot the state of a node or nodes into storage by a peer.
- **Soul**, is the practically unique, immutable identifier for a node.
- **Sent**, confirmation of communication, in contrast 'sending' has an unknown status.

Summaries of the API vocabulary...
- Key, creates an index to remember a node by, typically for human use.
- Load, open a key or bring a relation into cache, this is how you start exploring a graph.
- Path, navigate through a graph, from node to node via fields, by chaining relations together.
- Get, brings the node or value from your path into view to operate on, immediately and as they change.
- Set, to change the value on a field or merge a node or nodes.