Let's create a simple todo application with GUN.

You can follow along by creating your own version live on [CodePen](https://codepen.io/pen/). If you like, you can also click on the CodePen icons in the top right of each code block instead.

## The basic html

First let's create the html:

```html#showCodePen
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
    <style>
  </body>
</html>
```

The `<ul></ul>` will be used by the javascrypt code will will soon create.

The `<form>` will be used to add new todos.

You can ignored the `<style>` tag. This just contains some simple styling of (future) things on the page.

## Add gun

Next let's add GUN and make the form operational.

Delete the `<!--111-->` line and replace it by:

```html#showCodePen
<!-- docDef
startAddHtmlBefore
<html>
  <body>
    <h1>Todos</h1>

    <ul></ul>
    
    <form><input><button>Add</button></form>

endAddHtmlBefore
deleteLinesAtEnd 1
startAddHtmlAfter
    </script>
    
    <style>
      ul { padding: 0; }
      li { display: flex; }
      img { height: 20px; margin-left: 8px; }
      input { margin-right: 8px; }
    <style>
  </body>
</html>
endAddHtmlAfter
docDef -->
    <!-- Load GUN itself. -->
    <script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>

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
```

First we load GUN itself.

Then we initialize GUN and tell it we will place all data under the key `todos`.

Finally we handle what happens when the user clicks the `Add` button. The key line is `todos.set({title: input.value, done: false})`. Here we tell GUN to store our object. In GUN we use `set()` to store values in a list (set, array). So this object is added by GUN to the list of todos.

## Test

```js
<!-- {test} -->
var a = 10
```

```css
<!-- {test} -->
.test {
width: 10px;
}
```
