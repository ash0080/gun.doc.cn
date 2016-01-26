---------------------------------------------------------------------

***Please post documentation comments, questions, and suggestions to
[[issue #70|https://github.com/amark/gun/issues/70]].***

---------------------------------------------------------------------

**Table of Contents**
 - [Gun constructor](#Gun)
 - [gun.put](#put)
   - [examples](#examples-1)
   - [chain](#chain-context)
 - [gun.key](#key)
   - [examples](#examples-2)
   - [chain](#chain-context-1)
 - [gun.get](#get)
   - [examples](#examples-3)
   - [chain](#chain-context-2)
 - [gun.path](#path)
   - [examples](#examples-4)
   - [chain](#chain-context-3)
 - [gun.back](#back)
   - [examples](#examples-5)
   - [chain](#chain-context-4)
 - [gun.on](#on)
   - [examples](#examples-6)
   - [chain](#chain-context-5)
 - [gun.map](#map)
   - [examples](#examples-7)
   - [chain](#chain-context-6)
 - [gun.not](#not)
   - [examples](#examples-8)
   - [chain](#chain-context-7)
 - [gun.val](#val)
   - [examples](#examples-9)
   - [chain](#chain-context-8)

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

   - `options.init` is a boolean that tells the system that you want to explicitly create
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

  // disable implicit object saves
  init: true
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

> **Note:** when using `.put`, if any part of the chain is undefined, it is implicitly set as an empty object.
```javascript
gun.get('undefined key').path('undefined.properties').put(true)
// `.put` creates an empty object on each undefined path/key,
// and places the value at the last point.
```

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

We can think of keys as variables pointing to an object, and a node can have more than one name.

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
Aggregating fragmented data into a named object

```javascript
// each member of the chatroom
gun.get('trace').path([userID, 'profile']).key(userID + '/profile')
gun.get('battlefleet').path([userID, 'profile']).key(userID + '/profile')

gun.get(userID + '/profile')
// contains the merged sum of all profile information
```
This is especially useful since the intermediate objects might be large, and by putting a key on the data you're looking for, you won't need to perform the costly lookup in the future.

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
Load all data under a [key](#key) into the context.

It takes three parameters:

 - `keyName`
 - `callback`
 - `options`

You'll almost never need to use the `callback` or `options` parameters, unless you're building bare-metal extensions.

> No options are currently available for this method.

## Name
The [`keyName`](#key) string is the name of the data you want to retrieve.

> Note that if you use `.put` at any depth after retrieving a key that doesn't exist, the key is implicitly initialized as an empty object.

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

## Examples
Navigating to a property
```javascript
/*
  where `user` is {
    name: 'Bob'
  }
*/
gun.get('user').path('name')
```
Once you've focused on the `name` property, you can chain other methods like [`.put`](#put) or [`.on`](#on) to interact with it.

Moving through multiple properties
```javascript
/*
  where `user` is {
    name: { first: 'bob' }
  }
*/
gun.get('user').path('name').path('first')
// or the shorthand...
gun.get('user').path('name.first')
```

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
Subscribe to updates changes on a node or property real-time.

## Callback(value, property)
When the property or node you're focused on is changed, the callback is immediately fired and given the value as it is at that point in time.

Since gun uses data streams, the callback will probably be called multiple times as new chunk comes in.

## Options
Currently, the only option is to filter old data, and be given the changes, but nothing more. If you're listening to a node with 100 fields, and just one changes, you'll be passed a node with a single property representing that change. Since it's often useful, there's a shorthand for it...

**Longhand syntax**
```javascript
gun.on(callback, {
  delta: true
})
```

**Shorthand syntax**
```javascript
gun.on(callback, true)
```

## Examples
Listening for updates on a key
```javascript
gun.path('users/' + username).on(function (user) {
  // update in real-time
  if (user.online) {
    view.show.active(user.name)
  } else {
    view.show.offline(user.name)
  }
})
```

Listening to updates on a field
```javascript
gun.get('lights').path('living room').on(function (state, room) {
  // update the UI when the living room lights change state
  view.lights[room].show(state)
})
```

## Chain Context
`gun.on` does not change the chain context.
```javascript
gun.get(keyName).on(handler) /* is the same as */ gun.get(keyName)
```

-------------------------------------
# <a name="map"></a>gun.map(callback)
Loop over each property in a node, and subscribe to future changes. It's essentially performing a [`.path`](#path) on each field.

If you don't know the property names to [`.path`](#path) into, or need to `"forEach"` over a group of data, the `.map` function is usually your best choice. It accepts two arguments:

 - a `Callback` function
 - an `Options` object

## Callback(value, property)
The callback is invoked as new data becomes available, and is subscribed to subsequent changes like [`.on`](#on). Since there may be multiple peers and several layers of data, the callback may be invoked with the same data multiple times, but it will always be the latest at that point in time. It's given both the value and the field it was placed under.

## Options
Currently, there is just one option. By passing an object with `node` set to true, you can skip primitive values and only iterate over nested objects. That option is so handy that it's found it's own shorthand...

**Longhand syntax**
```javascript
gun.map(callback, {
  node: true
})
```

**Shorthand syntax**
```javascript
gun.map(callback, true)
```

## Examples
Loop over a collection of objects
```javascript
// for each status update
gun.get(userID + '/feed/tweets').map(function (tweet, ID) {
  // render it to the view
  view.show.tweet(tweet, ID)
}, true)
// `true` filters primitives, ensuring only objects are shown.
```

Iterate over an object
```javascript
/*
  where `visitor/stats` are {
    'new customers': 35,
    'returning': 65
  }
*/
gun.get('visitor/stats').map(function (percent, category) {
  pie.chart(category, percent)
})
```

## Chain context
The context for `.map` is a bit tricky. All [dependent](Chaining#chaining-dependency-table) methods will treat a `.map` context as though it were split into every possible [`.path`](#path) from that object. Stay with me.

If you run a [`.put(null)`](#put) directly after a `.map`, every field will be changed to null. Effectively, the `.map` statement applied the `.put` operation to every property on the object.
```javascript
/*
  where `object` is {
    field1: 'one',
    field2: 'two'
  }
*/
gun.get('object').map().put(null)
/*
  now `object` is {
    field1: null,
    field2: null
  }
*/
```
Hopefully this demonstrates some of `.map`s expressive power. But to summarize and stick with the `Chain Context` tradition, here's the gist:
```javascript
gun.get(keyName).map() /* is not the same as */ gun.get(keyName)
```

--------------------------------------
# <a name="not"></a> gun.not(callback)
Handle cases where data can't be found.

If you need to know whether a property or key exists, you can check with `.not`. It will consult the connected peers and invoke the callback if there's reasonable certainty that none of them have the data available.

No options are currently available for this method.

> **Warning:** `.not` is dangerous, since there is no guarantee that the data doesn't exist elsewhere. Always consider that you may be overwriting other data.

## Callback(key, kick)
If there's reason to believe the data doesn't exist, the callback will be invoked. It's purpose is to allow you to handle those cases intelligently. For example, you could check to see if a property doesn't exist, setting it to a default value. As mentioned previously, that can be dangerous, and should only be done if your app seriously depends on it.

The callback takes two arguments, `key` and `kick`.

> **Note:** returning inside a callback is the same as calling `kick`.

### Key/Property
The name of the property or key that could not be found.

### Kick
`kick` is a function that accepts a chain context. It can be a bit tricky to wrap your head around. Essentially, gun uses your argument as it's new chain context, so no matter what context you were at previously, you can overwrite it using `kick`, sending it out to methods chained after. This is also discouraged, because it makes your code difficult to follow, and also may cause chain divergence. Let's go for something more concrete:
```javascript
gun.get(original).not(function (key, kick) {
  kick(gun.get(otherKeyName))
}).map(backup)
```
In that example, `.not` might have been called if the connected peers could find the data, meaning that `.get(backup)` was used instead. The problem arises when new data is received on `original`, meaning that the original chain finally has data to work from. `.map` is now running on two keys, `original` and `backup`.

## Examples
Providing defaults if they aren't found
```javascript
// if not found
gun.get('players/3').not(function (key) {
  // put in an object and key it
  gun.put({
    active: false
  }).key(key)
}).on(handler)
// listen for changes on that key
```

Setting a property if it isn't found
```javascript
gun.path('chat.enabled').not(function (path) {
  gun.path(path).put(false)
})
```

Those examples demonstrate the power and drama from `.not`. If a peer with the data you're looking for (client or server) goes offline, you change the value of something, then they eventually rejoin, that information will converge, overwriting their data with yours. `.not` is a powerful tool, but should be used wisely.

## Chain Context
`.not` may or may not change the context, depending on how it's used. It has developer-facing methods for manually changing the context, depending on whether or not data was found.
```javascript
gun.get(keyName).not(handler) /* _might_ be the same as */ gun.get(keyName)
```

--------------------------------------
# <a name="val"></a> gun.val(callback)
Read a full object without subscribing updates.

`.val` will send a request for the node, and read out the value from the first peer that finishes their response. Since `.val` has to wait for a termination, it cannot stream data, slowing down the transmission of large objects considerably. If you simply want to read out an object without subscribing to changes, `.val` is your best option.

> This method is excellent for learning and debugging, but should be avoided in production applications. Instead, use [`.on`](#on).

`.val` takes two arguments:

 - a `callback` function
 - an `options` object

> **Tip:** If no callback is given, it will automatically log the result to the console.

No options are currently available for this method.

## Callback(value, property)
The value is the fully-loaded object/primitive, and the property is the field it belongs under.

## Examples
Reading a full object
```javascript
// load in the full profile object
gun.get('peers/' + userID).path('profile').val(function (profile) {
  // render it, but only once. No updates.
  view.show.user(profile)
})
```

Reading a property
```javascript
gun.path('temperature').val(function (number) {
  view.show.temp(number)
})
```

Printing a value to the console
```javascript
gun.path('property').val()
// `console.log` is automatically called
```

## Chain Context
`gun.val` does not change the chain context.
```javascript
gun.get(keyName).val() /* is the same as */ gun.get(keyName)
```