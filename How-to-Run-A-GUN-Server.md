> These docs are out of date! Please check https://gun.eco/docs/Installation#server instead.

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

Before we begin, lets setup our project with the following structure:

```
|- server.js
|- index.html
```

Add this code to **server.js**:
```javascript
/* server.js */

// import the native http module
var http = require('http')

// import gun
var Gun = require('gun')

var server = http.createServer(function(req, res){
  if (Gun.serve(req, res)){ return } // filters gun requests!
  require('fs').createReadStream(require('path').join(__dirname, req.url)).on('error',function(){ // static files!
  res.writeHead(200, {'Content-Type': 'text/html'});
  res.end(
    require('fs')
      .readFileSync(require('path')
      .join(__dirname, 'index.html') // or default to index
    ));
  }).pipe(res); // stream
});

// start listening for requests on `localhost:8080`
server.listen(8080)
```

Add this code to **index.html**:
```html
<!-- index.html -->
<html>
  <head>
    <meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=0">
  </head>
  <body>
    <div id="App">
      <div id="Output"></div>
      <input
        type="text"
        id="Input"
        placeholder="Enter a message..."
        autofocus
      >
    </div>
    <script src="http://localhost:8080/gun.js"></script>
    <script>
      const gun = Gun('http://localhost:8080' + '/gun');

      const $Input = document.querySelector('#Input');
      gun.get('test-input').val((data) => {
        $Input.value = data.value;
      })
      $Input.addEventListener('input', e => {
        const { value } = e.target;
        gun.get('test-input').put({
          value
        });
      });

      let timestamp = 0;
      gun.get('test-input').on(function(data, key) {
        const ts = data._['>'].value;
        if (timestamp === ts) {
          return;
        }
        timestamp = ts;
        console.log("update:", data);
        const $Output = document.querySelector('#Output');
        $Output.innerHTML = /* @html */`
          <pre>
            </code>
              ${JSON.stringify(data, null, 2)}
            </code>
          </pre>
        `;
      });
    </script>
  </body>
</html>
```

Now lets start the server by running this in your terminal:

```bash
# Where "server.js" is the file we just created
$ node server.js
```

Now, you should see the `gun.js` file when going to `localhost:8080/gun.js` in a browser.

If we enter a message into the input, it will sync with the server and should continue running indefinitely (just what we want from a server). We can verify that data is being updated by checking the **data.json** file.

> To end the server, you can hit <kbd>ctrl</kbd> + <kbd>c</kbd>.