The way GUN stores and communicates data is through a specific subset of denormalized JSON. This might sound confusing, but it is actually pretty simple.

For instance, take this object.

```javascript
{
  hacker: true,
  age: 23,
  name: "Mark Nadal",
}
```

This object is composed of **field** and **value** pairs, where a value is always either `null`, `true` or `false`, a _number_ or _decimal_, a _string_, or a _relation_. For clarity, GUN will use a consistent [[vocabulary|Semantics]], where values are primitives that get saved as a whole update on their field.

In order for GUN to deterministically converge on an update across many decentralized peers, it has to hold meta data about the fields and their values. This meta data is included in the GUN object itself, which is called a **node**.

```javascript
{
  _: {}
  hacker: true,
  age: 23,
  name: "Mark Nadal",
}
```

That meta data is stored on the reserved `_` field. Every node also must have a universally unique ID, called a **soul**, which is also stored in the meta data under the reserved `#` field. Like so:

```javascript
{
  _: {'#':'ASDF'}
  hacker: true,
  age: 23,
  name: "Mark Nadal",
}
```

For the sake of comprehension, our example is using a very short soul. Souls should never be this short, and should generated as a UUID or GUID or as some sufficiently long random alphanumeric string. They also should never conflict with any other node in any other app that uses GUN.

... TO BE CONTINUED ...