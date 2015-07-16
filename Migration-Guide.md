We will not be changing the Wire Protocol again, but if you have persisted data or code from the unstable 0.1.x that you want to update to a more recent version, you'll want to read through this article.

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

In the browser, for localStorage, a key pointing to a node was saved like this:

``javascript
localStorage["gun/example/chat/data"] = "{'#':'bE4rVGrFNmkR9KkdKatbpmVE'}";
```

Now it looks like this:

```javascript
localStorage["gun/example/chat/data"] = "{"bE4rVGrFNmkR9KkdKatbpmVE":{"#":"bE4rVGrFNmkR9KkdKatbpmVE"}}";
```

So to upgrade your data in the browser, run this command in the console of your app that you have localStorage data you want to upgrade.:

```javascript
for(var i in localStorage){
  var val = localStorage[i], tmp, graph = {};
  if(i.indexOf('gun') === 0 && i.indexOf('_/nodes') === -1){
    tmp = JSON.parse(val);
    if(tmp['#']){
      graph[tmp['#']] = tmp;
      localStorage[i] = JSON.stringify(graph);
    }
  }
}
```

Now on a server using the `file.js` driver, that typically saves to a single file of `data.json`. Try running this script in the same directory that has the json file:

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
... WIP ...