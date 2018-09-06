We're going to build a P2P dApp! This tutorial assumes you have moderate web development experience, and walk you through each step. Let's start with some basic HTML:

```html
::: {codepen: 'link', tab1: 'codemirror'} :::
::: {editor: 'main'} :::
::: {startblock: '1'} :::
<html>
  <body>
    <h1>Todo</h1>

    <form id="sign">
      <input id="alias" placeholder="username">
      <input id="pass" type="password" placeholder="passphrase">
      <input id="in" type="submit" value="sign in">
      <input id="up" type="button" value="sign up">
    </form>

    <ul></ul>

    <form id="said">
        <input id="say">
        <input id="speak" type="button" value="speak">
    </form>
::: {endblock: '1'} :::
::: {startblock: '2'} :::
  </body>
</html>
::: {endblock: '2'} :::
```

First, we need to include GUN, SEA, and the WebRTC adapter! (Plus some jQuery, but you can use React/Angular/etc.)

```
    <script src="https://cdn.jsdelivr.net/npm/gun/examples/jquery.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/gun/sea.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/gun/lib/webrtc.js"></script>
```

::: {nextstepcompare: 'start'} :::
```
::: {startblock: '3'} :::
::: {insertblock: '1'} :::
    <script src="https://cdn.jsdelivr.net/npm/gun/examples/jquery.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/gun/sea.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/gun/lib/webrtc.js"></script>
::: {endblock: '3'} :::
::: {insertblock: '2'} :::
```
::: {nextstepcompare: 'end'} :::

::: {step: 'Connecting to Peers'} :::

```html
::: {codepen: 'link', tab1: 'codemirror'} :::
::: {editor: 'main'} :::
::: {insertblock: '3'} :::
// This tutorial is currently a work-in-progress.
::: {insertblock: '2'} :::
```

... To help improve this tutorial, try hitting "edit" link, and help combine features from examples/simple folder (user.html, etc.) with fully functional multi-peer data sync.