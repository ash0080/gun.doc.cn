Let's create a simple todo application with GUN.

You can follow along by creating your own version live on [CodePen](https://codepen.io/pen/). If you like, you can also click on the CodePen icons in the top right of each code block instead.

## The basic html.

First let's create the html:

```html
<html>
  <body>
    <h1>Todos</h1>

    <ul></ul>
    
    <form>
      <input>
      <button>Add</button>
    </form>

    <!--111-->
    
    <style>
      ul {
        padding: 0;
      }
      li {
        display: flex;
      }
      img {
        height: 20px;
        margin-left: 8px;
      }
      input {
        margin-right: 8px;
      }
    <style>
  </body>
</html>
```

The `<ul></ul>` will be used by the javascrypt code will will soon create.

The `<form>` will be used to add new todos.

You can ignored the `<style>` tag. This just contains some simple styling of (future) things on the page.

