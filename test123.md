Before we can start building anything interesting, we should have a way to jot down our thoughts. Therefore the first thing we will build is a tool to keep track of what needs to be done. The infamous To-Do app, allowing us to keep temporary notes.

So what are the requirements? The ability to add a note, read our notes, and to clear them off. We will also need a space to keep these notes in, and a web page to access them through. Let's start with the page! You can edit the code below, which will update the live preview.

```html
::: {codepen: 'link', tab1: 'codemirror'} :::
::: {editor: 'main'} :::
::: {startblock: '1'} :::
<html>
  <body>
::: {endblock: '1'} :::
    <h1>Title</h1>
::: {startblock: '2'} :::
    <h4></h4>

    <form>
      <input><button>Add</button>
    </form>
			
    <ul></ul>
::: {endblock: '2'} :::
::: {startblock: '3'} :::
  </body>
</html>
::: {endblock: '3'} :::
```
What does this do? HTML is how we code the layout of a web page.

- We first must wrap all our code in an open and closing `html` tag so our computer knows it is a web page.
- The `body` tag tells it to display the contents enclosed within.
- `h1` is one of many semantic tags for declaring a title, others include `h2`, `h3` and so on of different sizes.
- A `form` is a container for getting information from a user.
- Forms have `input`s which let the user type data in, it is a self-closing tag.
- The `button` can be pressed, causing an action that we code to happen.
- `ul` is an unordered list which we will display our thoughts inside of.

Now, try changing the `h1` text in the editor from "Title" to the name of our app, "Thoughts".

::: {nextstepcompare: 'start'} :::
```
::: {startblock: '4'} :::
::: {insertblock: '1'} :::
    <h1>Thoughts</h1>
::: {insertblock: '2'} :::
::: {endblock: '4'} :::
::: {insertblock: '3'} :::
```
::: {nextstepcompare: 'end'} :::

::: {step: '2'} :::

```html
::: {codepen: 'link', tab1: 'codemirror'} :::
::: {editor: 'main'} :::
::: {insertblock: '4'} :::
<!-- Replace this Comment Line with the Code in the Step below! -->
::: {insertblock: '3'} :::
```

HTML controls the layout, but how do we control what happens when a user presses the 'add' button? This is done with javascript. But using raw javascript quickly becomes verbose, so to keep things concise we will use a popular tool called jQuery (there are other examples for tools, like React/Angular, as well). We also need a tool to store data, so we will include GUN as well.

Insert the following as new lines between `<ul></ul>` and `</body>`, replacing the comment line:
```html
    <script src="https://code.jquery.com/jquery-1.11.3.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>
    <script>
      $("h4").text("Good job! You'll replace this line in the next step!");
    </script>
```

- The `script` tag tells the browser to use some javascript code, and `src` is where to load it from.
- We can then test to see if our code worked with an `alert` message, which pops up and forces you to press ok.
- In javascript, we denote text by wrapping it inside quotation marks, double `""` or single `''`.
- We instruct the computer to notify us with that text by calling the `alert` function using parenthesis `()`.
- A function is just a fancy word for a reusable piece of code that does something when we call its name, such as `alert`.`
- A semicolon `;` marks the end of a javascript sentence in the same way a period marks the end of a sentence.

::: {nextstepcompare: 'start'} :::
```
::: {startblock: '5'} :::
::: {insertblock: '4'} :::
    <script src="https://code.jquery.com/jquery-1.11.3.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>
    <script>
::: {endblock: '5'} :::
::: {startblock: '6'} :::
      $("h4").text("Good job! You'll replace this line in the next step!");
::: {endblock: '6'} :::
::: {startblock: '7'} :::
    </script>
::: {insertblock: '3'} :::
::: {endblock: '7'} :::
```
::: {nextstepcompare: 'end'} :::

::: {step: '3'} :::

```html
::: {codepen: 'link', tab1: 'codemirror'} :::
::: {editor: 'main'} :::
::: {insertblock: '5'} :::
::: {insertblock: '6'} :::
::: {insertblock: '7'} :::
```

Wonderful! You should have gotten the alert message, this means writing code works! Let's replace the alert line entirely with code that responds to user input.

```html
      $('form').on('submit', function(event){
        event.preventDefault();
        alert("We got your thought! " + $('input').val());
      });
```

What's going on here?

- jQuery is a function like `alert`, its name is `$` which can be called with parenthesis `()`.
- Calling `$` with `form` as the input gives us a reference to the corresponding HTML form tag.
- We then call `on` with two inputs. First the text name of an `event` we want to react to, and then a `function` we create.
  - Events are predefined ways we can interact with a user, such as `mousemove` or a `keypress`.
  - We use `submit` because it responds to both a button `click` and hitting enter on a form.
  - Our `function` will get called with the `event` every time the user does that action, allowing us to react to their input.
- The default behavior of a form is to cause the browser to change pages which is annoying, we prevent that by calling `preventDefault` on the `event`.
- Finally, calling `$` with `input` will reference the HTML input tag which we then call `val` on, giving us the text the user typed in.

::: {nextstepcompare: 'start'} :::
```
::: {startblock: '8'} :::
::: {insertblock: '5'} :::
      $('form').on('submit', function(event){
        event.preventDefault();
        alert("We got your thought! " + $('input').val());
      });
::: {insertblock: '7'} :::
::: {endblock: '8'} :::
```
::: {nextstepcompare: 'end'} :::

::: {step: '4'} :::

```html
::: {codepen: 'link', tab1: 'codemirror'} :::
::: {editor: 'main'} :::
::: {insertblock: '8'} :::
```

...step 4...