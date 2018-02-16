Let's create a simple todo application with GUN.

You can follow along by creating your own version live on [CodePen](https://codepen.io/pen/). If you like, you can also click on the CodePen icons in the top right of each code block instead.

## The basic html

First let's create the html:

```html
<!-- {codepen: 'link', tab1: 'codemirror'} -->
<html>
  <body>
    <h1>Todos</h1>

    <ul></ul>
    
    <form><input><button>Add</button></form>

    <!--111-->
    
    <style>
      ul { padding: 0; }
      li { display: flex; }
      img { height: 20px; margin-left: 8px; }
      input { margin-right: 8px; }
    </style>
  </body>
</html>
```

The `<ul></ul>` will be used by the javascrypt code will will soon create.

The `<form>` will be used to add new todos.

You can ignored the `<style>` tag. This just contains some simple styling of (future) things on the page.

## Add GUN

Next let's add GUN and make the form operational.

Delete the `<!--111-->` line and replace it by:

```html
<!-- {codepen: 'link', codesandbox: 'tab'} -->
<!-- {hide: 'start'} -->
<html>
  <body>
    <h1>Todos</h1>

    <ul></ul>
    
    <form><input><button>Add</button></form>

<!-- {hide: 'end'} -->
    <!-- Load GUN itself. -->
    <script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>

    <!-- Load jQuery to help make things a bit easier. -->
    <script src="https://code.jquery.com/jquery-1.12.4.min.js"></script>

    <script>
      // Initialize GUN and tell it we will be storing all data under the key 'todos'.
      var todos = Gun().get('todos')
      
      // Get the form element.
      var form = document.querySelector('form')
      // Listen for submits of the form.
      form.addEventListener('submit', function (event) {
        // Get the input element.
        var input = form.querySelector('input')
        // Tell GUN to store an object,
        // with as title the value of the input element and a done flag set to false.
        todos.set({title: input.value, done: false})
        // Clear the input element, so the user is free to enter more todos.
        input.value = ''
        // Prevent default form submit handling.
        event.preventDefault()
      })

      //222
    </script>
<!-- {hide: 'start'} -->
    
    <style>
      ul { padding: 0; }
      li { display: flex; }
      img { height: 20px; margin-left: 8px; }
      input { margin-right: 8px; }
    </style>
  </body>
</html>
<!-- {hide: 'end'} -->
```

Here we first load GUN itself.

Then we initialize GUN and tell it we will place all data under the key `todos`.

Finally we handle what happens when the user clicks the `Add` button. The key line is `todos.set({title: input.value, done: false})`. Here we tell GUN to store our object. In GUN we use `set()` to store values in a list (set, array). So this object is added by GUN to the list of todos.

When you run the code, type something in the input and click the `Add` button, it is already stored by GUN. But we will not yet see it appear. We will do that in the next step.

## Showing the todos

Now we will show the list of all existing todos on the screen.

Delete the `//222` line and replace it by:

```html
<!-- {codepen: 'link', codesandbox: 'tab'} -->
<!-- {hide: 'start'} -->
<html>
  <body>
    <h1>Todos</h1>

    <ul></ul>
    
    <form><input><button>Add</button></form>

    <!-- Load GUN itself. -->
    <script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>

    <!-- Load jQuery to help make things a bit easier. -->
    <script src="https://code.jquery.com/jquery-1.12.4.min.js"></script>

    <script>
      // Initialize GUN and tell it we will be storing all data under the key 'todos'.
      var todos = Gun().get('todos')
      
      // Get the form element.
      var form = document.querySelector('form')
      // Listen for submits of the form.
      form.addEventListener('submit', function (event) {
        // Get the input element.
        var input = form.querySelector('input')
        // Tell GUN to store an object,
        // with as title the value of the input element and a done flag set to false.
        todos.set({title: input.value, done: false})
        // Clear the input element, so the user is free to enter more todos.
        input.value = ''
        // Prevent default form submit handling.
        event.preventDefault()
      })

<!-- {hide: 'end'} -->
      // Listen to any changes made to the GUN todos list.
      // This will be triggered each time the list changes.
      // And because of how GUN works, sometimes even multiple times per change.
      todos.map().on(function (todo, id) {
        // Check if the todo element already exists.
        // This can happen because GUN sometimes sends mulitple change events for the same item.
        var li = $('#' + id)
        // Does is not yet exist?
        if (!li.get(0)) {
          // Create it.
          // Set the id to the GUN id of the item.
          // GUN automatically creates id's for all items.
          // Finally set the new todo element to the end of the list.
          li = $('<li>').attr('id', id).appendTo('ul')
        }
        // Does the GUN item contain any data?
        // (It sends null if it was removed from GUN.)
        if (todo) {
          // Create an element with the title of the GUN item in it and make it clickable.
          var html = todo.title //333
          //444
          // Set it to the element.
          li.html(html)
        } else {
          // The item was removed from GUN, because we got null.
          // Delete it from the screen.
          li.remove()
        }
      })
<!-- {hide: 'start'} -->
    </script>
    
    <style>
      ul { padding: 0; }
      li { display: flex; }
      img { height: 20px; margin-left: 8px; }
      input { margin-right: 8px; }
    </style>
  </body>
</html>
<!-- {hide: 'end'} -->
```
