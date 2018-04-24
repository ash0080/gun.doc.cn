Let's create a simple Hello World app with GUN.

>This page contains code editors with live preview. You can change the code and see the result straight away.

## Hello world

Let's create a html file that will contain both our markup and Javascript code:

```html
::: {codepen: 'link', tab1: 'codemirror'} :::
<html>
  <body>
    <h1>Hello</h1>
    What is your name?<br>
    <form><input><button>Submit</button></form>

    <script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>
    <script src="https://code.jquery.com/jquery-1.12.4.min.js"></script>
    <script>
      var gun = Gun()

      $('form').on('submit', function (event) {
        var input = $('form').find('input')
        gun.get('hello').put({ name: input.val() });
        input.val('')
        event.preventDefault()
      })

      gun.get('hello').on(function(data, key) {
        var h1 = $('h1')
        h1.text('Hello ' + data.name)
      })
    </script>
  </body>
</html>
```

Try it out but entering your name above. The Hello message will show your name.

Now refresh the whole page. After the refresh your name will still be there as GUN will retrieve it for you.

### What's going on?

First we load GUN:
`<script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>`

And initialise it:
`var gun = Gun()`

Then each time the user submits a new name (`$('form').on`) we create a new object with that name: `{ name: input.val() }` and `put` it in a GUN node with the key `hello`:
`gun.get('hello').put(...)`

Finally by using `gun.get('hello').on` we get called each time the `hello` node changes, so we can update the h1.

Notice that when we refresh the page or load it in a new window, the `gun.get('hello').on` is called automatically because GUN figures out there is already an existing `hello` node. All data is automatically cached by GUN in the browser's localStorage.

## Synchronising multiple computers

GUN is able to fully automatically synchronise all data between multiple computers. Theoretically this could be done without the need for any server, but unfortunately browsers are not yet able to 'discover' each other without a little help from a server.

So we will now create a very simple server (which we prefer to call a super-peer) on Heroku. If you do not yet have a Heroku account please create one now. It's free.

The super-peer code looks like this:

```javascript
var Gun = require('gun')
var http = require('http')
var fs = require('fs')

var server = http.createServer((req,res) => {
  if (Gun.serve(req, res)) { return } // filters gun requests!
  res.writeHead(200)
  res.end(fs.readFileSync(__dirname + '/index.html'))
}).listen(process.env.PORT || process.argv[2] || 8080)

var gun = Gun({web: server})
```

You can run it on Heroku by clicking this button:

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/robertheessels/gun-super-peer-example){:target="_blank"}

Create a unique app name and deploy the app.

After it has started, in the code at the top of this page, change this line `var gun = Gun()` into: `var gun = Gun('https://unique-app-name.herokuapp.com/gun')` (Make sure you replace `unique-app-name` by the name you entered in Heroku).

Now open this very page in another computer, or open it in a different browser (Chrome, Firefox, etc) and make the same change as you just did (`var gun = Gun('https://unique-app-name.herokuapp.com/gun')`).

If all went well, the two browser pages (which we call peers) have found each other via the super-peer and are now syncing. Whenever you change the name in one place, it will update in both. Thanks to the power of GUN.

>Attention! Make sure you destroy the Heroku app after you're done testing to avoid costs.

The code for the super-peer (server) is just a very basic web server with just one line added for GUN: `var gun = Gun({web: server})`. Make sure to always include a reference to the web server when initialising GUN in Node.js.

## What's next?

[Next steps](Next-Steps)

[A Crash course explaining further how to use GUN](Crash-Course)

[An interactive GUN todo app tutorial](Basic-Todo-App-Tutorial)