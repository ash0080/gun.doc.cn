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
- The `h4` does not yet do anything, but will be used in the next step.
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
- We can then test to see if our code worked with a message, which will show up below the headline.
- In javascript, we denote text by wrapping it inside quotation marks, double `""` or single `''`.
- We instruct the computer to notify us with that text, by selecting where to show it (the `<h4></h4>` element) and setting it's content with the `text` function.
- A function is just a fancy word for a reusable piece of code that does something when we call its name, such as `text`.`
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

Wonderful! You should have gotten the message, this means writing code works! Let's replace the message line entirely with code that responds to user input.

```html
      $('form').on('submit', function(event){
        event.preventDefault();
        $("h4").text("We got your thought! " + $('input').val());
      });
```

What's going on here?

- jQuery brings a function with the name `$`, that can be called with parenthesis `()`.
- Calling `$` with `form` as the input gives us a reference to the corresponding HTML form tag.
- We then call `on` with two inputs. First the text name of an `event` we want to react to, and then a `function` we create.
  - Events are predefined ways we can interact with a user, such as `mousemove` or a `keypress`.
  - We use `submit` because it responds to both a button `click` and hitting enter on a form.
  - Our `function` will get called with the `event` every time the user does that action, allowing us to react to their input.
- The default behavior of a form is to cause the browser to change pages which is annoying, we prevent that by calling `preventDefault` on the `event`.
- Finally, calling `$` with `input` will reference the HTML input tag which we then call `val` on, giving us the text the user typed in.

::: {nextstepcompare: 'start'} :::
```
::: {insertblock: '5'} :::
::: {startblock: '8'} :::
      $('form').on('submit', function(event){
        event.preventDefault();
::: {endblock: '8'} :::
::: {startblock: '9'} :::
        $("h4").text("We got your thought! " + $('input').val());
::: {endblock: '9'} :::
::: {startblock: '10'} :::
      });
::: {endblock: '10'} :::
::: {insertblock: '7'} :::
```
::: {nextstepcompare: 'end'} :::

::: {step: '4'} :::

```html
::: {codepen: 'link', tab1: 'codemirror'} :::
::: {editor: 'main'} :::
::: {insertblock: '5'} :::
/* Replace this Comment Line with the Code in the Step below! */
::: {insertblock: '8'} :::
::: {insertblock: '9'} :::
::: {insertblock: '10'} :::
::: {insertblock: '7'} :::
```

Now that users can jot down their thoughts, we need a place to save them. Let's start using GUN for just that.

```javascript
var gun = Gun().get('thoughts');
```

- The `var`iable keyword tells javascript that we want to create a reference named `gun` that we can reuse.
- We call `Gun` to start the database, which only needs to be done once per page load.
- Now we want to open up a reference to some data, so we call `get` with the name of the data we want.
- However, no data has been saved to `thoughts` yet! Let's fix that in the next step by using `gun`.

::: {nextstepcompare: 'start'} :::
```
::: {startblock: '11'} :::
::: {insertblock: '5'} :::
      var gun = Gun().get('thoughts');
::: {insertblock: '8'} :::
::: {endblock: '11'} :::
::: {insertblock: '9'} :::
::: {insertblock: '10'} :::
::: {insertblock: '7'} :::
```
::: {nextstepcompare: 'end'} :::

::: {step: '5'} :::

```html
::: {codepen: 'link', tab1: 'codemirror'} :::
::: {editor: 'main'} :::
::: {insertblock: '11'} :::
::: {insertblock: '9'} :::
::: {insertblock: '10'} :::
::: {insertblock: '7'} :::
```

Replace the message line in the submit function with the following:

```javascript
        gun.set($('input').val());
        $('input').val("");
```

- We're telling gun to add the value of the input as an item in a `set` of thoughts.
- Then we also want the input's `val`ue to become empty text, so we can add new thoughts later.

::: {nextstepcompare: 'start'} :::
```
::: {startblock: '12'} :::
::: {insertblock: '11'} :::
        gun.set($('input').val());
        $('input').val("");
::: {insertblock: '10'} :::
::: {endblock: '12'} :::
::: {insertblock: '7'} :::
```
::: {nextstepcompare: 'end'} :::

::: {step: '6'} :::

```html
::: {codepen: 'link', tab1: 'codemirror'} :::
::: {editor: 'main'} :::
::: {insertblock: '12'} :::
/* Replace this Comment Line with the Code in the Step below! */
::: {insertblock: '7'} :::
```

Fantastic! Now that we can successfully store data, we want show the data! Replace the comment line in the editor with the following:

```javascript
      gun.map().on(function(thought, id){
        var li = $('#' + id).get(0) || $('<li>').attr('id', id).appendTo('ul');
        if(thought){
          $(li).text(thought);
        } else {
          $(li).hide();
        }
      });
```

- `map` tells gun to get each item in the set, one at a time, to do something with it.
- What we do is the same as $'s `on`, which reacts to events. So does gun, it responds to any update on 'thoughts'.
- We get the `thought` value itself and a unique `id`entifier for the item in the set.
- This next line looks scary, but read it like this, "make `var`iable `li` equal to X or Y".
  - The X part asks `$` to find the `id` in the HTML and `get` it.
  - In javascript, `||` means 'or', such that javascript will use X if it exist or it will use Y.
  - The Y part asks `$` to create a new `&lt;li&gt;` HTML tag, set its `id` `attr`ibute to our id and `append` it to the end of the HTML `ul` list.
- Finally, the javascript `if` statement either asks `$` to make `thought` be the text of the `li` if thought exists, `else` hide the `li` from being displayed.
- Altogether it says "Create or reuse the HTML list item and make sure it is in the HTML list, then update the text or hide the item if there is no text".

::: {nextstepcompare: 'start'} :::
```
::: {startblock: '13'} :::
::: {insertblock: '12'} :::
      gun.map().on(function(thought, id){
        var li = $('#' + id).get(0) || $('<li>').attr('id', id).appendTo('ul');
        if(thought){
          $(li).text(thought);
        } else {
          $(li).hide();
        }
      });
::: {endblock: '13'} :::
::: {insertblock: '7'} :::
```
::: {nextstepcompare: 'end'} :::

::: {step: '7'} :::

```html
::: {codepen: 'link', tab1: 'codemirror'} :::
::: {editor: 'main'} :::
::: {insertblock: '13'} :::
/* Replace this Comment Line with the Code in the Step below! */
::: {insertblock: '7'} :::
```

Finally we want to be able to clear off our thoughts when we are done with them. The interface for this could be done in many different ways, but for simplicity we will use a double tap as the gesture to clear it off. Replace the comment line in the editor with this code.

```javascript
      $('body').on('dblclick', 'li', function(event){
        gun.get(this.id).put(null);
      });
```

- In order to react to any `dblclick` event rather than a specific one, we call `on` on the page's `body` as a whole.
- But we want to filter the events to ones that happened only on any `li` tag. Fortunately, we can call `on` with an optional second input of `li` which does just that.
- Inside a `function` we get a special `this` keyword in javascript, which `$` uses as a reference to the original HTML tag that caused the event.
- Calling `get` tells `gun` to filter its data to just the `id` of the thought we want to clear off.
- Then calling `put` on that tells gun to update that thought to `null`, so we no longer have the thought.
- And whenever an update happens, `gun`'s `on` function from the previous step gets called again, which then hides the corresponding HTML list item.

::: {nextstepcompare: 'start'} :::
```
::: {startblock: '14'} :::
::: {insertblock: '13'} :::
      $('body').on('dblclick', 'li', function(event){
        gun.get(this.id).put(null);
      });
::: {insertblock: '7'} :::
::: {endblock: '14'} :::
```
::: {nextstepcompare: 'end'} :::

::: {step: '8'} :::

```html
::: {codepen: 'link', tab1: 'codemirror'} :::
::: {editor: 'main'} :::
::: {insertblock: '14'} :::
```

Congratulations! You are all done, you have built your first GUN app!

In the next tutorial we will use GUN to synchronize data in realtime across multiple devices. We'll start by copying the app we made here and modifying it to become a chat app.

