To sync with other peers on a browser, you'll need to connect to one or more servers. While learning gun or building small apps, you can simply use our community server:

 - `http://gungame.herokuapp.com/gun`
   Only keeps data in memory, purging often. Great for real-time apps like [`trace`](http://trace.gundb.io/) that don't need long term storage.

However, as soon as you start doing more with gun, you'll likely want full control over your own server, since with community servers you have no way of authenticating requests, no way of purging data, and no way of knowing that data won't be accidentally overwritten by someone else.

The good news: gun comes bundled with a server plugin when downloaded from [`npm`](https://npmjs.com/package/gun). Setting up a simple Node.js server is just a few lines of Javascript code.

If you don't have the npm version of gun installed, you can run this in your terminal:

```bash
$ npm install gun
```

If you don't have node or npm installed, [you can install it here](https://nodejs.org/en/).

```javascript
// import the native http module
var http = require('http')

// import gun
var Gun = require('gun')

// create a new gun instance
var gun = new Gun()

// create a new server instance
var server = new http.Server()

// add the /gun.js route to your server
server.on('request', gun.wsp.server)

/*
  Handle incoming gun traffic
  from clients (that's where the
  real-time goodness comes from).
*/
gun.wsp(server)

// start listening for requests on `localhost:8080`
server.listen(8080)
```

Now you can start the server by running this in your terminal:

```bash
# Where "server-file.js" is the file we just created
$ node server-file.js
```

It should print out stuff like "Hello wonderful person!..." and should continue running indefinitely (just what we want from a server).

> To end the server, you can hit <kbd>ctrl</kbd> + <kbd>c</kbd>.

Now, you should see the `gun.js` file when going to `localhost:8080/gun.js` in a browser.
Connecting to the gun server from a browser should look something like this:

```html
<script src="http://localhost:8080/gun.js"></script>
<script>
  // connects to your server!
  var gun = new Gun('http://localhost:8080/gun')
</script>
```

## Support
If you have any issues or feature ideas, please let us know in our gitter channel!