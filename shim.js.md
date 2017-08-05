GUN has a unique and powerful API. We recommend you learn it, but we have started `shim.js` to provide some abstraction/wrappers that look similar to Firebase, Parse, or other tools. This **is not a complete shim** to those APIs, nor will it ever be. It is just a gateway drug to playing with gun, so use responsibly.

Contributions would be highly appreciated, consider this the place to add common abstractions/wrappers from other systems that other developers would be familiar with. It will be a community driven shim.

# Use

## NodeJS
```javascript
var Gun = require('gun');
require('gun/lib/shim');
```

## Browser
Without a build tool, you'll need to include each extension that the shim depends upon - as shown below.

With a build tool, you should know how to bundle them into 1 file based on the tags below, then use that.
```
<script src="/gun.js"></script>
<script src="/gun/lib/open.js"></script>
<script src="/gun/lib/shim.js"></script>
```

# Firebase

## `.on('value', cb)`

This will loosely approximate Firebase's behavior. Here is an example:

```javascript
var gun = Gun('http://localhost:8080/gun'), ID = 'asdf';

gun.get('interview/' + ID).on('value', function(tree){
  console.log(tree); // no need for calling snapshot
});
```

This will give you the full document every time it changes. It works for documents with circular references too!

```javascript
var interview = gun.get('interview/' + ID);

var data = {
  id: ID,
  position: {
    role: "engineer",
    experience: "ninja"
  }
};
data.position.parent = data;

interview.put(data);
```

When the data is saved with `put` it will trigger `.on('value', cb)` to be called with the full interview document.

[Try this shim out now, with this live demo on jsbin](http://jsbin.com/xodoxolawa/edit?html,js,console)!

This shim wraps [gun.open](https://github.com/amark/gun/wiki/API#open) in the extended API.