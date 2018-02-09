Write a quick app: ([try now in jsbin](http://jsbin.com/sovihaveso/edit?js,console))

```html
<script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>
<script>
// var Gun = require('gun'); // in NodeJS
// var Gun = require('gun/gun'); // in React
var gun = Gun();

gun.get('mark').put({
  name: "Mark",
  email: "mark@gunDB.io",
});

gun.get('mark').on(function(data, key){
  console.log("update:", data);
});
</script>
```

`TODO: This page was originally https://github.com/amark/gun/wiki/Getting-Started-(v0.3.x) but it is outdated.`

`TODO: Add more explanation. Is it possible to integrate gun.js.org Try It Now live demo?`