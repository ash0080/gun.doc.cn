Gun can be installed and used in both browser (client side) and server applications. We have made it very easy to be be used in different environments.
## In the browser / client side
Here a three different ways to include Gun in your browser application:
* Include as script tag
* Require
* Import (ES6)
Just pick your favorite way of including scripts...
### Include as script tag
The easiest way is to just include Gun in the `head` tag of your `index.html` page:
```html
<script src="https://cdn.rawgit.com/amark/gun/master/gun.js"></script>
```
### Require
```javascript
var Gun = require('/path/to/gun');
```
### Import (ES6)
```javascript
import '/path/to/gun'
```
## On the server
### NPM
```sh
$ npm install gun
```
### Yarn
```sh
$ yarn add gun
```