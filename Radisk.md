## Radisk Storage Engine
The **Radisk Storage Engine (RSE)** is an _in-memory_, as well as _on-disk_, radix trie that saves the GUN database graph for fast and performant look-ups.

Radix tries lookups have a constant time.

### Flow of Data

GUN is a modular system, which allows adapters to 'hook' into the GUN instance through subscriptions to general events (such as gun.on('in')) or data events specifically subscribed to via data.on(callback).

When data is put via gun.get(key).put({object}), GUN adds the {object} into it's internal graph (in-memory) and then hands the data to the next subscribed adapter. (This may be RSE, localStorage or AWS S3 etc.)

RSE is called from gun/lib/store.js, which should be the template for anyone to start building their own storage adapter for GUN.

### Store.js

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
This programming pattern is called [functional reactive programming](https://gun.eco/docs/Functional-Reactive-Programming).

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

TODO: Go back to Lines 1 to 56 and explain how Radisk receives data from store.js

### Radisk.js


```javascript
var fs = require('fs');
var Gun = require('../gun');
var Radix = require('./radix');

function Radisk(opt){

	opt = opt || {};
	opt.file = String(opt.file || 'radata');
	opt.until = opt.until || opt.wait || 1000; // default for HDDs
	opt.batch = opt.batch || 10 * 1000;
	opt.chunk = opt.chunk || (1024 * 1024 * 10); // 10MB
	opt.code = opt.code || {};
	opt.code.from = opt.code.from || '!';
```

_opt_ comes from the GUN instance.

We make sure opt.file is a string or defaults to 'radata'.

opt.until - the delay before writing to disk

opt.batch - the limit of 'entries' per batch

opt.chunk - file size limit before splitting to a new file

opt.code - TODO

opt.code.from - TODO

```javascript

	function ename(t){ return encodeURIComponent(t).replace(/\*/g, '%2A') }
```

ename makes sure files are not named with symbols that would create errors on OS's (such as / - etc)

```javascript

	if(!opt.store){
		return Gun.log("ERROR: Radisk needs `opt.store` interface with `{get: fn, put: fn, list: fn}`!");
	}
	if(!opt.store.put){
		return Gun.log("ERROR: Radisk needs `store.put` interface with `(file, data, cb)`!");
	}
	if(!opt.store.get){
		return Gun.log("ERROR: Radisk needs `store.get` interface with `(file, cb)`!");
	}
	if(!opt.store.list){
		return Gun.log("ERROR: Radisk needs a streaming `store.list` interface with `(cb)`!");
	}
```

Check if store interface has been created and if the 3 API's are available to us.

```javascript
	/*
		Any and all storage adapters should...
		1. Because writing to disk takes time, we should batch data to disk. This improves performance, and reduces potential disk corruption.
		2. If a batch exceeds a certain number of writes, we should immediately write to disk when physically possible. This caps total performance, but reduces potential loss.
	*/
	var r = function(key, val, cb){
		key = ''+key;
		if(val instanceof Function){
			cb = val;
			val = r.batch(key);
			if(u !== val){
				return cb(u, val);
			}
			if(r.thrash.at){
				val = r.thrash.at(key);
				if(u !== val){
					return cb(u, val);
				}
			}
			//console.log("READ FROM DISK");
			return r.read(key, cb);
		}
		r.batch(key, val);
		if(cb){ r.batch.acks.push(cb) }
		if(++r.batch.ed >= opt.batch){ return r.thrash() } // (2)
		clearTimeout(r.batch.to); // (1)
		r.batch.to = setTimeout(r.thrash, opt.until || 1);
	}
```
Function _r_ can be called 2 ways:
GET (key, cb) PUT (key, val, cb)

GET Case

_key_ stringify input

_r.batch(key)_ reads back in-memory batch to check if key is waiting

    to be written to disk

If found, return it to caller

If not found, check the batch about to be written (staged for thrashing/flushing)

     If found, return to caller

If not found, read from disk

PUT Case

_key_ stringify input

_r.batch(key,val)_ write key/val pair to batch

If a callback was attached we attach it in turn to acks in the batch.

 (acks are acknowledgments, sent out after the batch is written to disk)

Check if the batch count limit is reached and if so, flush to disk.

 Also increase the counter _r.batch.ed_

_opt.until_ or as default 1ms is he idle time between put calls, before a flush occurs

```javascript
	r.batch = Radix();
	r.batch.acks = [];
	r.batch.ed = 0;

	r.thrash = function(){
		var thrash = r.thrash;
		if(thrash.ing){ return thrash.more = true }
		thrash.more = false;
		thrash.ing = true;
		var batch = thrash.at = r.batch, i = 0;
		clearTimeout(r.batch.to);
		r.batch = null;
		r.batch = Radix();
		r.batch.acks = [];
		r.batch.ed = 0;
		r.save(batch, function(err, ok){
			if(++i > 1){ return }
			if(err){ Gun.log(err) }
			Gun.obj.map(batch.acks, function(cb){ cb(err, ok) });
			thrash.at = null;
			thrash.ing = false;
			if(thrash.more){ thrash() }
		});
	}

	/*
		1. Find the first radix item in memory.
		2. Use that as the starting index in the directory of files.
		3. Find the first file that is lexically larger than it,
		4. Read the previous file to that into memory
		5. Scan through the in memory radix for all values lexically less than the limit.
		6. Merge and write all of those to the in-memory file and back to disk.
		7. If file to large, split. More details needed here.
	*/
	r.save = function(rad, cb){
		var s = function Span(){};
		s.find = function(tree, key){
			if(key < s.start){ return }
			s.start = key;
			opt.store.list(s.lex);
			return true;
		}
		s.lex = function(file){
			file = (u === file)? u : decodeURIComponent(file);
			if(!file || file > s.start){
				s.mix(s.file || opt.code.from, s.start, s.end = file);
				return true;
			}
			s.file = file;
		}
		s.mix = function(file, start, end){
			s.start = s.end = s.file = u;
			r.parse(file, function(err, disk){
				if(err){ return cb(err) }
				Radix.map(rad, function(val, key){
					if(key < start){ return }
					if(end && end < key){ return s.start = key }
					disk(key, val);
				});
				r.write(file, disk, s.next);
			});
		}
		s.next = function(err, ok){
			if(s.err = err){ return cb(err) }
			if(s.start){ return Radix.map(rad, s.find) }
			cb(err, ok);
		}
		Radix.map(rad, s.find);
	}