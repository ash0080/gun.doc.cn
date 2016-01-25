---------------------------------------------------------------------

***Please post documentation comments, questions, and suggestions to
[[issue #70|https://github.com/amark/gun/issues/70]].***

---------------------------------------------------------------------

> **This page is currently being written**

**Table of Contents**
 - [Gun constructor](#Gun)
 - [gun.put](#put)
   - [accepted values](#allowed-types)
   - [callback](#callbackerror-ok)
   - [examples](#examples-1)
   - [chain](#chain-context)
 - [gun.key](#key)
   - [name](#name)
   - [callback](#callbackerror-ok-1)
   - [options](#options-1)
   - [examples](#examples-2)
   - [chain](#chain-context-1)
 - [gun.get](#get)
   - [name](#name-1)
   - [callback](#callbackerror-node)
   - [options](#options-2)
   - [examples](#examples-3)
   - [chain](#chain-context-2)
 - [gun.path](#path)
   - [property](#property)
   - [callback](#callbackerror-data-property)
   - [chain](#chain-context-3)
 - [gun.back](#back)
   - [examples](#examples-4)
   - [chain](#chain-context-4)
 - [gun.on](#on)
 - [gun.map](#map)
 - [gun.val](#val)
 - [gun.not](#not)

# <a name="Gun"></a>Gun(options)
Used to creates a new gun database instance.

```javascript
var gun = new Gun(options)
```
> **note:** `Gun` works with or without the `new` operator.

## Options

 - `no params/undefined` creates a local datastore using the default persistence layer, either localStorage or a JSON file.

 - passing an `array` of URLs creates a local datastore and attempts to sync it with each URL.

   - when only syncing with a single peer, you can leave out the array, sending in just a `string`.

 - `{ peers, wire, uuid... }` the previous options are actually aggregated into an `object` under the hood.

   - `options.peers` is an object, where the URLs are properties, and the value is an empty object.

   - `options.wire` allows you to manually override module hooks for `put` and `get`
     (however, gun exposes a more streamlined approach for building extensions and modules).

   - `options.uuid` allows you to override the default 24 random alphanumeric soul generator with
      your own function.

   - `options['module name']` allows you to pass options to a 3rd party module. Their project README
     will likely list the exposed options.
     [Here is a list of such modules...](https://github.com/amark/gun/wiki#modules)

   - `options.init` is a boolean that tells the system whether you want to implicitly create
	   data if it doesn't exist.

### Examples
Sync with one peer
```javascript
Gun('http://yourdomain.com/gun')
```

Sync with many peers
```javascript
Gun(['http://server1.com', 'http://server2.com'])
```

Working with modules
```javascript
Gun({
  // Amazon S3 (comes bundled)
  s3: {
    key: '',
    secret: '',
    bucket: ''
  },

  // simple JSON persistence (bundled)
  // meant for ease of getting started
  // NOT meant for production
  file: 'file/path.json'
})
```

Advanced options
```javascript
Gun({
  // set your own UUID function
  uuid: function () {...},

  // set your own get/put handlers
  wire: {
    get: getHandler,
    put: putHandler
  },

  // disable implicit collections
  init: false
})
```
---------------------------------------------------------
<h1><a name="put"></a>gun.put(data, callback)</h1>
Save data into gun, syncing it with your connected peers.

It has three parameters, and only the first is required:

 1. the `data` to save
 2. an optional `callback`, invoked on each acknowledgment
 3. an `options` object, though no options are available yet

`gun.put(data, callback)`

## Allowed types

`.put` restricts the input to a specific subset:

 - `objects`:
   [partial](https://github.com/amark/gun/wiki/Partials-and-Circular-References#),
   [circular](https://github.com/amark/gun/wiki/Partials-and-Circular-References#circular-references), and nested
 - `strings`
 - `numbers`
 - `booleans`
 - `null`

Gun will refuse `undefined`, `NaN`, `Infinity`, and `arrays`.

> Traditional arrays are dangerous in real-time apps

## Callback(error, ok)
  
The `callback` is fired each time a peer responds with an error or successful persistence message, including the local cache. Acknowledgement can be slow, but the write propagates across networks as fast as the pipes connecting them.

If the error argument is undefined, then the operation succeeded, although the exact values are left up to the module developer.

## Examples

Saving objects
```javascript
gun.path('propertyName').put({
  property: 'value',
  object: {
    nested: true
  }
})
```

Saving primitives
```javascript
// strings
gun.path('name.first').put('Dave')

// numbers
gun.path('temperature').put(58.6)

// booleans
gun.path('enabled').put(true)
```

Using the callback
```javascript
gun.path('survey.submission').put(submission, function (err) {
  if (err) {
    return ui.show.error(err)
  }
  ui.show.success(true)
})
```

## Chain context
`gun.put` does not change the gun context.
```javascript
gun.path('key').put(value) /* same context as */ gun.path('key')
```

--------------------------------------------------
# <a name="key"></a>gun.key(name)
Index your data, so you can find it later, faster.

We can think of keys as variables pointing to an object, and a node can have more than one name. Keys are one of gun's more powerful features.

The `.key` method takes 3 arguments, and only the first is required:
 - the `name` of the category to join under
 - the `callback` to invoke on each acknowledgment
 - `options` for configuration

## Name
A string representing the name of a group to attach a node to. Common practice is to namespace your keys into routes: `.key('users/cities/Provo')`

> **note:** keys are case sensitive

## Callback(error, ok)
The same behavior as the callback in [gun.put](#put).

## Options
Ignore the chain context, tagging a node directly by it's soul.
```javascript
// find node by soul "Zwl6..." and tag it under "examples"
gun.key('examples', null, 'Zwl6PaS4oo7Ivt21X5bU0nds')
```

## Examples
Aggregating data into a named object

```javascript
gun.get(group).path('members').map(function (member) {
  // group each member's interests together
  this.path('interests').key(interests)
})

// each member's interests
// now indexed into a single group
gun.get(interests)
```

Tagging data on insertion
```javascript
gun.get(group).path([memberID, 'contact']).put({
  twitter: '@databaseGun',
  gitter: 'gitter.im/amark/gun'
}).key(group + '/' + memberID + '/contact')
```
By putting a name on the data, the operation will be faster in the future since it won't need to request the potentially large `group` object, or even the member object. We can skip straight to the data we need. This functionality pairs nicely with [`.not`](#not).

## Chain context
`gun.key` does not change the context.
```javascript
gun.path('node').key('nodes') /* same context as */ gun.path('node')
```

------------------------------------------------------------------------------------------
# <a name="get"></a>gun.get(name)
Load all data under that [key](#key) into the context, saving it to local persistence.

It takes three parameters:

 - `keyName`
 - `callback`
 - `options`

You'll almost never need to use the `callback` or `options` parameters, unless you're building bare-metal extensions.

## Name
The [`keyName`](#key) string is the name of the data you want to retrieve.

> Note that if you use `.path` or `.put` after retrieving a key that doesn't exist, the key is implicitly initialized as an empty object.

```javascript
gun.get(existingData).put({ property: 'value' })
/*
  {
    data: ...,
    property: 'value',
    data: ...
  }
*/

gun.get(nonExistentKey).put({ property: 'value' })
// { property: 'value' }
```

## Callback(error, node)
The callback is a listener for errors and raw node data. It may be called multiple times for a single request, since gun uses a streaming architecture. If you need to listen for incoming data, use [`.not`](#not) or [`.val`](#val).

> The callback is exposed for bare-metal extensions. Most developers will never need it.

```javascript
gun.get(key, function (error, node) {
  // called many times
})
```

## Options
Currently one option is standardized: force gun to draw from persistence, not memory.

```javascript
// read from storage, not memory
gun.get(key, null, {
  force: true
})
```

## Examples

Retrieving a key
```javascript
// retrieve all available seats
gun.get('seats/available').map(ui.show.seats)
```

Using the callback
```javascript
gun.get(key, function (error, node) {
  if (error) {
    server.log(error)
  }
})
```

## Chain context
After calling `gun.get`, the chain context refers to the data you requested, regardless of what happened before calling `gun.get`.

```javascript
gun.get('materials').get('users') /* same context as */ gun.get('users')
```

---------------------------------------
# <a name="path"></a>gun.path(property)
Navigate through a node's properties

`.path` accepts three parameters, but only the first is required.

 - the `property` name
 - an optional `callback` function
 - an `options` object, although none are supported yet

## Property
The `property` is the name of the field to move to.

```javascript
// move to the "themes" field on the settings object
gun.get('settings').path('themes')
```

Once you've changed the context, you can read, write, and `path` forward from that field. Although you can just chain one `path` after another, that can be unnecessarily verbose, so there are two shorthand styles:

 - dot format
 - array format

Here's dot notation in action:
```javascript
// verbose
gun.get('settings').path('themes').path('active')

// shorthand
gun.get('settings').path('themes.active')
```

And the array format, which really becomes useful when using variables instead of literal strings:
```javascript
gun.get('settings').path(['themes', themeName])
```

> If at any point the path is undefined, it's implicitly initialized as an empty object.

### Unexpected behavior
The dot notation can do some strange things if you're not expecting it. Under the hood, everything is changed into a string, including floating point numbers. If you use a decimal in your path, it will split into two paths...
```javascript
gun.path(30.5)
// interprets to
gun.path(30).path(5)
```

This can be especially confusing if you've disabled implicit initialization, as the chain might never resolve to a value.

We may change this in the near future.

## Callback(error, data, property)
The `callback` is used by bare-metal extensions to provide handling for errors and raw data streams. Like `.get`, if you want to listen for new data, use the [`.on`](#on) or [`.val`](#val) methods. With that said, the callback takes three arguments:

 - `error`
 - the `data` partial
 - the `property` name

## Chain context
`gun.path` creates a new context each time it's called, and is always a result of the previous context.
```javascript
gun.get('API').path('path').path('chain')
/* is different from */
gun.get('API').path('path')
/* and is different from */
gun.get('API')
```

-----------------------------
# <a name="back"></a>gun.back
Move up to the parent context on the chain.

Every time a new chain is created, a reference to the old context is kept in the `back` property. It's not a function, but a reference to the `this` value you had *before* it was changed. For example, when calling [`.path`](#path), a new context is assigned to the `this` value, allowing you to chain directly off your context in jQuery style. If you want to return to your old context, just use `gun.back`.

## Examples
Moving to a parent context
```javascript
gun.get('peers')
  /* change the context to my peer */
  .path(myself)
  .put(me)
  /* go back to the "get" statement */
  .back.map(...)
```

Another example
```javascript
gun.get('players').get('game/history').back
// is the same as...
gun.get('players')
```

## Chain Context
The context will always be different, since `.back`'s only purpose is to change it.
```javascript
gun.path('property') /* is not the same as */ gun.path('property').back
```

------------------------------------
# <a name="on"></a> gun.on(callback)


-------------------------------------
# <a name="map"></a>gun.map(callback)


--------------------------------------
# <a name="not"></a> gun.not(callback)


--------------------------------------
# <a name="val"></a> gun.val(callback)