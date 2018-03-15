GUN can be used in both browsers and servers. We have made it easy to install in many different environments.

## Browser

You may want to:

- Include it as a script tag,
- Use a require syntax,
- Or import it in ES6.

Just pick your favorite way:

### Script Tag

The easiest way is to just add:

```html
<script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>
```

to your HTML. Then you can test it, like so:

```html
<script>
  var gun = Gun()
  var greetings = gun.get('greetings')
  greetings.put({hello: 'world'})
  greetings.on(function (data) {
    console.log('Update!', data)
  })
</script>
```

Now you should see the message in the browser console.

### Require

Assuming you are using something like Webpack or Browserify, you need to first:

```sh
$ npm install gun
```
or
```sh
$ yarn add gun
```

Then require GUN in your code:

```javascript
var Gun = require('gun/gun');
```

And test it like this:

```javascript
  var gun = Gun()
  var greetings = gun.get('greetings')
  greetings.put({hello: 'world'})
  greetings.on(function (data) {
    console.log('Update!', data)
  })
```

After running you should see the message in the console.

### Import (ES6)

Similar to `require` you can include GUN with `import`. Like before, make sure you have:

```sh
$ npm install gun
```
or
```sh
$ yarn add gun
```

Then import GUN in your code:

```javascript
import Gun from 'gun/gun'
```

And test it like this:

```javascript
  var gun = Gun()
  var greetings = gun.get('greetings')
  greetings.put({hello: 'world'})
  greetings.on(function (data) {
    console.log('Update!', data)
  })
```

After running that, you should see the message in the console.

> Note that for now, even though you import, GUN is defined and used as a global variable.

## Node

First you need to install GUN with NPM or yarn:

```sh
$ npm install gun
```
or
```sh
$ yarn add gun
```

> **Note:** If you don't have [node](http://nodejs.org/) or [npm](https://www.npmjs.com/) installed, [read this](https://docs.npmjs.com/getting-started/installing-node).

Then require GUN in a `hello.js` file:

```javascript
var Gun = require('gun');
```

For testing add this to it:

```javascript
  var gun = Gun()
  var greetings = gun.get('greetings')
  greetings.put({hello: 'world'})
  greetings.on(function (data) {
    console.log('Update!', data)
  })
```

Then run the script:

```sh
$ node ./hello.js
```

After running you should see the message in the console output!