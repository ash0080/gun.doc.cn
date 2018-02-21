Before we can start building anything interesting, we should have a way to jot down our thoughts. Therefore the first thing we will build is a tool to keep track of what needs to be done. The infamous To-Do app, allowing us to keep temporary notes.

So what are the requirements? The ability to add a note, read our notes, and to clear them off. We will also need a space to keep these notes in, and a web page to access them through. Let's start with the page! You can edit the code below, which will update the live preview.

```html
<!-- {codepen: 'link', tab1: 'codemirror'} -->
<!-- {editor: 'main'} -->
<html>
  <body>
    <h1>Title</h1>

    <form>
      <input><button>Add</button>
    </form>
			
    <ul></ul>
  </body>
</html>
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

<!-- {nextstepcondition: {"code.notcontains": "<h1>Title</h1>"}} -->
<!-- {nextstepcondition: {"code.contains": "<h1>Thoughts</h1>"}} -->
<!-- {nextstepcompare: 'start'} -->
```
<html>
  <body>
    <h1>Thoughts</h1>

    <form>
      <input><button>Add</button>
    </form>

    <ul></ul>
  </body>
</html>
```
<!-- {nextstepcompare: 'end'} -->
<!-- {step: 'two'} -->
```html
<!-- {codepen: 'link', tab1: 'codemirror'} -->
<!-- {editor: 'main'} -->
<html>
  <body>
    <h1>Thoughts</h1>

    <form>
      <input><button>Add</button>
    </form>

    <ul></ul>
  </body>
</html>
```

HTML controls the layout, but how do we control what happens when a user presses the 'add' button? This is done with javascript. But using raw javascript quickly becomes verbose, so to keep things concise we will use a popular tool called jQuery (there are other examples for tools, like React/Angular, as well). We also need a tool to store data, so we will include GUN as well.

Insert the following as new lines between `<ul></ul>` and `</body>`:
```html
    <script src="https://code.jquery.com/jquery-1.11.3.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>
    <script>
      alert("Good job! You'll replace this line in the next step!");
    </script>
```

- The `script` tag tells the browser to use some javascript code, and `src` is where to load it from.
- We can then test to see if our code worked with an `alert` message, which pops up and forces you to press ok.
- In javascript, we denote text by wrapping it inside quotation marks, double `""` or single `''`.
- We instruct the computer to notify us with that text by calling the `alert` function using parenthesis `()`.
- A function is just a fancy word for a reusable piece of code that does something when we call its name, such as `alert`.`
- A semicolon `;` marks the end of a javascript sentence in the same way a period marks the end of a sentence.

<!-- {nextstepcondition: {"code.contains": "<script src=\"https://code.jquery.com/jquery-1.11.3.min.js\"></script>"}} -->
<!-- {nextstepcondition: {"code.contains": "<script src=\"https://cdn.jsdelivr.net/npm/gun/gun.js\"></script>"}} -->
<!-- {nextstepcondition: {"code.contains": "alert(\"Good job! You'll replace this line in the next step!\")"}} -->
<!-- {step: 'three'} -->

This is step 3.
