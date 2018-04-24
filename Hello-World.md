Let's create a simple Hello World app with GUN.

>This page contains code editors with live preview. You can change the code and see the result straight away.

## Hello world

Let's create a html file that will contain both our markup and Javascript code:

```html
::: {codepen: 'link', tab1: 'codemirror'} :::
::: {editor: 'main'} :::
<html>
  <body>
    <h1>Hello</h1>
    <form>What is your name? <input><button>Submit</button></form>

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