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

#### Gun Store Adapters need 3 API interfaces
**PUT** - to write data to disk
**GET** - to get data from disk
**LIST** - to get a list of files written to disk (if your get writes to multiple files,
 like radix does)

```javascript
store.js


// Here is where we define the store interface for RSE

function Store(opt){
	opt = opt || {}; 
	opt.file = String(opt.file || 'radata'); 
```
We populate opt from the GUN instance.
Then we populate the folder name for our data from opt or if undefined default to
'radata'. (radix data)
```javascript
	var store = function Store(){};
```

_store_ becomes an empty object, which we will populate with functions.
We can call a sequence of functions, while allowing for readability
and async nature of calls to be completed.
This programming pattern is called[functional reactive programming](https://gun.eco/docs/Functional-Reactive-Programming).

```javascript
	store.put = function(file, data, cb){
		var random = Math.random().toString(36).slice(-3)
		fs.writeFile(opt.file+'-'+random+'.tmp', data, function(err, ok){
			if(err){ return cb(err) }
			move(opt.file+'-'+random+'.tmp', opt.file+'/'+file, cb);
		});
	};
```

PUT
We generate a random name for a temp file.
fs.writeFile writes data to the temp file. Callback is called upon completion, at which time 
we move the temp file and rename it to the actual filename at the right path.
(Node fs.writeFile and fs.readFile clash when reading/writing concurrently, hence the temp file for writes)

```javascript
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
```

GET
read the specified File, if no errors and there is data, return data to the callback.

```javascript
	store.list = function(cb, match){
		fs.readdir(opt.file, function(err, dir){
			Gun.obj.map(dir, cb) || cb(); // Stream interface requires a final call
                                                      // to know when to be done.
		});
	};
```

LIST
Call readDir, which returns an array of filenames in the directory.
Gun.obj.map acts as a 'forEach' and iterates over the array.
RSE needs an indication that the whole list has been returned, which we achieve by calling 'cb()'
when map has completed, which will pass _undefined_ as the last data.

```javascript
	if(!fs.existsSync(opt.file)){ fs.mkdirSync(opt.file) }
	//store.list(function(){ return true });
	return store;
}
```

