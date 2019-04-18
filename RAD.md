RAD is a storage adapter for GUN that stores data at disk using a radix tree, it looks like this:

![](https://gun.eco/see/radix.gif)

As you see, we are able to store 3 records (Alex, Alexandria, Andrew) in only 2 rows because "Alex" and "Alexandria" share a common prefix.

Because writing storage adapters for GUN has a bunch of nuances and performance tradeoffs, we've designed RAD to handle these nuances for you and chunk GUN's graph into traditional files that can be dumped to disk. It handles correctly merging updates into each batch, managing memory allocation on heavy load, reads across ranges of chunks, and more with generalizable performance strategy.

However, RAD does not have an opinion on what storage engine should actually be used, and requires you pass it a storage interface - whether that be [`fs`](https://github.com/amark/gun/blob/master/lib/rfs.js), [S3](https://github.com/amark/gun/blob/master/lib/rs3.js), [indexedDB](https://github.com/amark/gun/blob/master/lib/rindexed.js), localStorage, IPFS, or others.

 ## Install

RAD is now the default with GUN in NodeJS. **RAD is used automatically in NodeJS, just `require('gun')`!**. If you want to use by itself, without GUN, skip this section.

To use RAD with GUN in the **browser**, include:

```html
<script src="https://cdn.jsdelivr.net/npm/gun/gun.js"></script>
<script src="https://cdn.jsdelivr.net/npm/gun/lib/radix.js"></script>
<script src="https://cdn.jsdelivr.net/npm/gun/lib/radisk.js"></script>
<script src="https://cdn.jsdelivr.net/npm/gun/lib/store.js"></script>
<script src="https://cdn.jsdelivr.net/npm/gun/lib/rindexed.js"></script>
<script>
var gun = Gun();
// ...
</script>
```

This will automatically tell GUN to use RAD with IndexedDB.

 > Note: Pass `Gun({localStorage: false})` to disable localStorage.

**NodeJS** by default uses disk (`fs`). If you have [AWS credentials](Using-Amazon-S3-for-Storage) set in your process environment variables, then S3 will be used instead of disk. (You may need to add `aws-sdk` to your `package.json` though.)

If you want to use RAD without GUN? Just do:

```javascript
// var Rad = require('gun/lib/radisk'); // in NodeJS
var Rad = window.Radisk; // in Browser, still needs the above script tags.
var rad = Rad(opt);
```

What is `opt`? Check the *API*:

 ## API

RAD takes 1 parameter, an `option` object, with at least these 2 properties:

 - `put` **function** *key, data, cb* for saving chunks.
 - `get` **function** *key, cb* for reading chunks.

It is easier to understand with some examples, here is a localStorage plugin for RAD:

```
var opt = {}, store = localStorage;
opt.put = function(key, data, cb){
  store[''+key] = data;
  cb(null, 1);
}
opt.get = function(key, cb){
  cb(null, store[''+key])
}
```

That is all! It should be easy to implement or integrate any storage engine, or use any of the several already included.

 > Note: localStorage uses a synchronous API, most storage engines will have an asynchronous API which may make your code look ugly. But the actual integration with RAD is as simple as a file `put` and `get` command.

 ## WIP

More docs coming soon!