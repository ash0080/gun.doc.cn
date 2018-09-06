We're going to build a P2P dApp! This tutorial assumes you have moderate web development experience (don't? Check out our [beginner's guide](./My-First-Todo-App)!), and will walk you through each step. Let's start with some basic HTML, to create a multi-user public todo app:

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

First, we need to include [GUN](https://gun.eco/), [SEA](https://gun.eco/docs/SEA), and the WebRTC adapter right above the closing `</body>` tag! (Plus some jQuery, but you can use React/Angular/etc.)

```html
    <script src="https://cdn.jsdelivr.net/npm/gun/examples/jquery.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/gun/sea.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/gun/lib/webrtc.js"></script>
```

(Add this code to the above interactive editor!)

### CDNs are dangerous! What about require, import, etc.?

For security purposes, we recommend you include these dependencies with your app, rather than trusting a public CDN. (Although we do love jsDelivr! It is free, open source, and give us download stats!)

> Pro tip: If all your dependencies are local, your app can work offline-first! And then GUN will sync when it comes back online.

What about `require` and minified bundles? Yupe, you can do that too! Just follow the [instructions](./Installation). For sake of simplicity and accessibility, this article uses the most-common-denominator of tools. Any developer with advanced experience should be able to easily follow along and independently switch from plain script tags into ES6 syntax. However, any developer that does not understand how script tags work, needs to re-learn the basics.

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