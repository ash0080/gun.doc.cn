We will not be changing the Wire Protocol again, but if you have persisted data or code from the unstable 0.1.x that you want to update to a more recent version, you'll want to read through this article.

Any persisted data on AWS S3 should work between versions just fine without any extra migration (you might have to refresh your app twice though). So this article is only focused on upgrading your local testing data. We'll go over some of the changes in general though.

Previously the wire protocol looked this:

 - `get`, the response was a single node. 
 - `put`, pushing out was a graph.
 - `key`, was an association.

Now it looks like this:

 - get, the response is now a graph.
 - put, pushing out stays as a graph.
 - key, stays as an association.
 - all, is being added.

Meaning we have broken how `get` behaves between versions, and we are adding `all` which shouldn't effect any old or new code.

Let's go over that in more detail, `get` had a response that previous looked something like this:

```javascript
{
  _: {'#': "SOUL", '>': {
    hello: 123,
    hi: 1234
  },
  hello: "world!",
  hi: "Mars!"
}
```

And now it looks like this:

```javascript
{'SOUL': {
  _: {'#': "SOUL", '>': {
    hello: 123,
    hi: 1234
  },
  hello: "world!",
  hi: "Mars!"
}}
```

As a result, this breaks a lot of stuff but it should be a fairly easy fix to update the data. Without migrating the format of the data the new versions of gun will receive the old format, which is an individual node, and then complain that a node is not a graph.

However, this itself is not what causes the problem of data not loading, but instead how the persistence drivers previously stored the key -> node associations.

First off, in your 0.1.x app make sure your server has replicated all the data from your browser. This should be as simple as loading the app in the browsers that you use. Once you have it loaded, open up your console and run

```javascript
localStorage.clear()
```

This will clear out all the data on the browser. We are going to rely upon the server for the migration.

Now that that is done, on the server, in NodeJS run the following script in the folder that contains the persisted data, which is typically named `data.json`. This will update the data.

```javascript
var fs = require('fs');
var json = fs.readFileSync('./data.json');
var all = JSON.parse(json);
for(var i in all.keys){ 
  var val = all.keys[i], graph = {}, rel = {};
  if(val){
    rel['#'] = val;
    graph[val] = rel;
    all.keys[i] = graph;
  }
};
fs.writeFileSync('./data.json', JSON.stringify(all));
console.log("migrated");
```

Note! Change the `'./data.json'` accordingly if the file is named something else.

Now spin your app back up and load it in the browser and it should work just fine (although you might have to refresh twice).

Let us know if you have any issues.

## Code

Please rename the following method names, in this order:

1. `get` -> `val`
2. `load` -> `get`
3. `set` -> `put`
4. `blank` -> `not`