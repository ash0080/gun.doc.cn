All data is described in 4 simple properties:
 - a **Universally Unique Identifier** to contain named values and distinguish them from other data with conflicting names.
 - a **Name** to reference the actual value without knowing what it is in advance.
 - the **Value** which is an indivisible piece of information, or a UUID that points to complex information which is itself composed of these 4 properties.
 - a **State** to represent change or continuity between values of a name within a UUID.

Graphs accurately model these 4 properties, so these properties are correspondingly called node, field, value, and state. Data is delivery is **at least once**, where every peer is a state machine operating over an open-closed boundary using the following conflict resolution rule for idempotence:

  1. If the **state** of the value in question is **above** the **upper boundary** then computing on that value should be deferred until another state.
  2. If the **state** of the value in question is **equal or below** the **upper boundary** then computing on that value is valid unless:
  3. There is another known **state** on the name of the value in question that is **above** the **state** we are computing. Or
  4. There is another known **state** on the name of the value in question that is **equal** to the **state** we are computing on. Then
  5. The **value** of **higher lexical order** should be preferred.
  6. If they are of the **same lexical order** then the values are identical other than their source.

This next part may be confusing, but it is summarizing the above: The specified algorithm guarantees the deterministic convergence of every value at the known states over every machine within the operating boundary. It however does not guarantee linearizability of states because not all states may be known during the operating boundary of the machine, thus it is eventually consistent. If linearizability must be achieved then the data itself needs to explicitly link its sequencing which can be done ontop of this specification.

Please see `function HAM` in [gun core](../../blob/master/gun.js) for the javascript implementation of this algorithm.

### Considerations

 - No strong consistency, no linearizability, and no serializability, see [CAP Theorem](../CAP-Theorem).
 - Vulnerable to the Double Spending problem.
 - No implicit ordering like arrays, ordering must be explicitly maintained by the data.
 - Because of at least once delivery mechanics, arithmetic operations on the data directly is not commutative.

It is important to note that GUN's conflict resolution algorithm is the ground work, it does not provide much other than deterministic synchronization by its own. Most useful behavior like ordering or arithmetic is more complex and needs a Directed Acyclic Graphs (DAGs) or Convergent Replicated Data Types (CRDTs) built on top of the underlying algorithm. A nice article introducing these ideas is [here](http://codedependents.com/2014/01/13/mathematical-purity-in-distributed-systems-crdts-without-fear/). We plan to provide extensions for these at some point.