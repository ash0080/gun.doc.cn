> This page is currently being written
======================================

With the recent news of the [Parse shutdown](http://blog.parse.com/announcements/moving-on/), we've decided to write a Parse-specific migration guide to [gunDB](https://github.com/amark/gun).

> **Note:** until gunDB reaches a stable 1.0, we are focusing our development solely on JavaScript platforms.

> **Note:** gun does not have a user authentication module yet, although one is in development.

## What is gunDB?
Gun is a modular database that works for both clients and servers. It's graph oriented, meaning it can contain any data structure, such as tabular, document, key-value, and even cyclical objects. One of it's most powerful features is real-time client/server data sync out of the box.

## How is it different from Parse?
Parse uses a centralized, Master/slave, server-centric model, while gunDB is just the opposite, operating as a fully decentralized system, with the focus primarily on clients.


## API
How the Parse API compares to gun's.

### Parse.initialize()
Setting up the connection.

For Parse, they need the ID for your app, and the platform ID
```javascript
Parse.initialize(appID, javascriptID)
```

Since gun is modular, by default all data is global. Modules like [Reticle](https://github.com/PsychoLlama/Reticle) can create a namespace for your app, essentially doing the same thing as Parse' app ID. If you're using a scoping module, it might look something like this:
```javascript
Gun.scope(appID)
```

To connect to one or more databases, give the full URL to the Gun constructor:
```javascript
var servers = [
	'https://server1.com/gun',
	'https://server2.com/gun'
]
var db = Gun(servers)
```

### Creating a target
Gun and Parse are similar when it comes to creating a database interface. Opening up a channel for Parse might look something like this:

```javascript
// open the "highscores" class
var Highscores = Parse.Object.extend('highscores')
var highscores = new Highscores()
```

And gun has something very similar:
```javascript
// create a Gun instance
var gun = Gun(servers)

// get the "highscores" node
var highscores = gun.get('highscores')

// get the "players" node
var players = gun.get('players')
```

### Parse.Object
The most comparable thing to Parse' classes is a gun "key", although the major difference is that you cannot set a prototype for just that class, although you can extend it across all instances by extending the `Gun.chain`.

**Parse**
```javascript
var Potato = new Parse.Object.extend('potato', {
	method: function () { /*...*/ }
})
var potato = new Potato();
```

**gun**
```javascript
var potato = Gun().get('potato')
Gun.chain.method = function () { /*...*/ }
```

### Saving keys/values
Parse has the `.set` method, gun has the [`.put`](API-%28v0.3.x%29#put) method. The main difference is that GUN performs updates as it receives them, where Parse requires the `.save` method to terminate a batch.

**Parse**
```javascript
parse.set('field', value)
// OR
parse.set({
	field: value
})

parse.save()
```

**gun**
```javascript
gun.path('field').put(value)
// OR
gun.put({
	field: value
})
```

> **Note:** every value given to `.put` is treated as a [partial](Partials-and-Circular-References-%28v0.2.x%29).

### Queries
Instead of doing an expensive database search every time you need data, we strongly encourage our users to index data on entry. It almost always improves performance drastically, and can have a strong impact on how your app is perceived. Since sometimes there's no way around performing a query, it's made fairly trivial to do it in vanilla javascript, just by using the API. Once again, we **strongly** recommend indexing the data as you run the query.

**example**
```javascript
// expensive query and indexing
// `.map` is essentially a `forEach`
function runQuery() {
	// potentially expensive query
	gun
		.get('peers').map()
		.path('friends').map()
		.path('birthday').val(function (birthday) {
			if (Date.parse(birthday) > value) this.key('query/' + name)
		});
}

// only run the query if previous results can't be found
gun.get('query/' + name).not(runQuery).map(...);
```




