Gun can be installed and used in both browser (client side) and server applications. We have made it very easy to be be used in different environments.

## In the browser / client side

Here a three different ways to include Gun in your browser application:
- Include as script tag
- Require
- Import (ES6)

Just pick your favorite way of including scripts...

### Include as script tag

The easiest way is to just include Gun in the `<head>` tag of your `index.html` page:

```html
<script src="https://cdn.rawgit.com/amark/gun/master/gun.js"></script>
```

And you can then test it by adding this to your `index.html`:

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

Another way to include Gun in your browser app is with `require`.

First you need to install Gun with NPM or Yarn:

```sh
$ npm install gun
```
or
```sh
$ yarn add gun
```

Then require Gun in your script:

```javascript
var Gun = require('gun');
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

After running you should see the message in the browser console.

### Import (ES6)

Similar to `require` you can include Gun with `import`.

First you need to install Gun with NPM or Yarn:

```sh
$ npm install gun
```
or
```sh
$ yarn add gun
```

Then import Gun in your script:

```javascript
import 'gun'
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

After running you should see the message in the browser console.

> Note that right now, even though you import, Gun is defined and used as a global variable.

## On the server (Node.js)

First you need to install Gun with NPM or Yarn:

```sh
$ npm install gun
```
or
```sh
$ yarn add gun
```

> **Note:** If you don't have [node](http://nodejs.org/) or [npm](https://www.npmjs.com/) installed, [read this](https://docs.npmjs.com/getting-started/installing-node).

Then require Gun in your script `hello.js`:

```javascript
var Gun = require('gun');
```

For testing add to `hello.js`:

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

After running you should see the message in the console output.