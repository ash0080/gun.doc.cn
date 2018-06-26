## Radisk Storage Engine
The **Radisk Storage Engine (RSE)** is an _in-memory_, as well as _on-disk_, radix trie that saves the GUN database graph for fast and performant look-ups.

Radix tries have lookups have a constant time, where n is the number of items retrieved from the trie.

### Flow of Data

GUN is a modular system, which allows adapters to 'hook' into the GUN instance through subscriptions to general events (such as gun.on('in')) or data events specifically subscribed to via data.on(callback).

When data is put via gun.get(key).put({object}), GUN adds the {object} into it's internal graph (in-memory) and then hands the data to the next subscribed adapter. (This may be RSE, localStorage or AWS S3 etc.)

RSE is called from gun/lib/store.js, which should be the template for anyone to start building their own storage adapter for GUN.

### The Code

When creating your GUN instance with the localStorage turned off, GUN defaults to RSE.

```javascript
var gun = GUN({
    localStorage: false,
    file: 'nameOfFile'
})
```
gun/lib/store.js is linked to your GUN instance when using in nodejs.

```javascript
store.js

// We can ignore most of the lines from 1 down to 56
// They are important for setting up RSE in your GUN instance

// Here is where we define the store interface for RSE

function Store(opt){
	opt = opt || {}; // opt is the option obj that was added into the GUN instance
	opt.file = String(opt.file || 'radata'); // name of the folder in which to save 

	var store = function Store(){};
	store.put = function(file, data, cb){
		var random = Math.random().toString(36).slice(-3)
		fs.writeFile(opt.file+'-'+random+'.tmp', data, function(err, ok){
			if(err){ return cb(err) }
			move(opt.file+'-'+random+'.tmp', opt.file+'/'+file, cb);
		});
	};
	store.get = function(file, cb){
		fs.readFile(opt.file+'/'+file, function(err, data){
			if(err){
				if('ENOENT' === (err.code||'').toUpperCase()){
					return cb(null);
				}
				Gun.log("ERROR:", err)
			}
			if(data){ data = data.toString() }
			cb(err, data);
		});
	};
	store.list = function(cb, match){
		fs.readdir(opt.file, function(err, dir){
			Gun.obj.map(dir, cb) || cb(); // Stream interface requires a final call to know when to be done.
		});
	};
	if(!fs.existsSync(opt.file)){ fs.mkdirSync(opt.file) }
	//store.list(function(){ return true });
	return store;
}
```

