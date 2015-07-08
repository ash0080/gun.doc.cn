NOTE: WORK IN PROGRESS; MAY HAVE ERRORS

### **<a name="Gun">Gun</a>** `var gun = Gun(options)`

  - Instantiates a gun database.

  - `options` as

     - `undefined` creates a local datastore using the default persistence layer, either localStorage or a file.

     - `'string'` URL creates a datastore that attempts to sync with the specified peer.

     - `['array']` of URL strings creates a datastore that attempts to syncs with multiple peers.

     - `{obj:'ect'}` - All the previous options are aggregated into an actual options object, with

        - `option['module']` if you are using a GUN module, you can pass it options via its name.

        - `options.peers` is an object with the URLs as the field and a value of an object.

        - `options.hooks` is an object with functions on `put`, `key`, `get`, or `all` hook fields.

        - `options.uuid` is a function to override the default 24 random alphanumeric soul generator.

  - Examples

    - `var gun = Gun()`

    - `var gun = Gun("http://localhost:8080/gun")`

    - `var gun = Gun(["http://localhost:8080/gun", "http://gunjs.herokuapp.com/gun"])`

    - `var gun = Gun({file: 'data.json'})` to change the name of the file that gets dumped to. **Warning!** The file module is the server's default persistence, and should only be used for local development testing only!

    - `var gun = Gun({s3: {key: '', secret: '', bucket: ''}})` on the server dumps to AWS S3, this is the preferred persistence layer.

# **put** `gun.put(object, callback, options)`

 - Saves the object.

 - `object` is a javascript `{obj:'ect'}`, it can be deeply nested, be a [[partial|Partials and Circular References]], or have [[circular references|Partials and Circular References#circular-references]] in it, but it cannot have the following values inside of it: `undefined`, `Infinity`, `NaN`, `[]`.

 - `callback` is a `function(){}` which gets called as `callback(err, ok)` after the highest level of persistence has happened per each connected peer. As long as there is no `err` then things should be ok, `ok` may not be defined. Acknowledgement of persistence is slow, but the write propagates across the network as fast as the pipes connecting them.

   - *no* - If you are connected to no peers, it gets called back once from your local persistence hook.

   - *one* - If you are connected to one peer, it gets called back once after that peer's persistence hook has finished; in a traditional client-server setup where the server is backing up to S3, you will not get an acknowledgement until S3 has either succeeded or failed to persist your data.

   - *many* - If you are connected to many peers, it gets called back multiple times for each individual peer. That peer will respond as if it was the only peer in the above case.

 - `options` is an `{obj:'ect'}`, no options are currently available except maybe hook specific.

 - Examples

   - `gun.put({hello: 'world'})` blindly fire and forget.

   - `gun.put({hello: 'world'}, function(err, ok){ })` to handle errs or know that your data actually got persisted.

   - `gun.put({field: "value", multiple: "pairs!"})`

   - `gun.put({some: {deeply: {nested: 'object'}}})` for document based data.

   - Graph based data
     
     ```javascript
     var doctor = {species: "Time Lord", home: "Gallifrey"};
     var TARDIS = {name: "Time And Relative Dimension In Space", cloak: "Police Box"};
     TARDIS.pilot = doctor;
     doctor.ship = TARDIS;
     gun.put(doctor);
     ```
     both objects get saved into a graph.

# **key** `gun.put(object).key(key, callback, options)`

  - Without a key, we cannot get our data back unless we scan over the entire graph. To get fast and easy access to the data, we give it a unique key. A key is a lot like keys in real life, once made they allow you to open up doors so you can enter into your data in many different ways.

  - `key` is a `'string'`, the name you want to give your data to reference it later. Common practice is to namespace your keys into routes corresponding to resources.

  - `callback` is a `function(){}` which gets called as `callback(err, ok)` in the same way **put** does.

  - `options` as a `'string'` is a soul that you forcibly want to associate the `key` with, ignoring the current context that key is chained on.

  - Examples

    - `gun.put({hello: 'world'}).key('message/from/thedoctor')` blindly fire and forget.

    - `gun.put({hello: 'world'}).key('message/from/thedoctor', function(err, ok){})` to handle errs or know that your key actually got persisted.

    - `gun.put({title: "The Doctor", phone: '770-090-0461'}).key('user/thedoctor').key('phone/07700900461')` you can chain keys together to create multiple unique references to the same node.

# **get** `gun.get(key, callback, options)`

  - Opens up a gun reference to the root object you had saved to that key. It will load the node so you can further manipulate it.

  - `key` is a `'string'` name that exactly matches a key you have made earlier.

  - `callback` is a `function(){}` which gets called as `callback(err, graph)` used for err handling and the raw graph. **Note** that you do not want to use this callback for every day development, as it gets called repeatedly to comply the wire protocol. Use **on** or **val** instead, they are convenience methods, this `callback` is for hooks and extensions.

  - `options` is a `{obj:'ect'}` with

    - `options.force` as `true` to pull from the local persistence layer or a peer rather than memory.

  - Examples

    - `gun.get('user/thedoctor`)` returns a gun reference to The Doctor.

    - `gun.get('user/thedoctor', function(err, graph){})` to handle errs or inspect the graph from the wire.

    - `gun.get('user/thedoctor', function(){}, {force: true})` for a slower response, skipping memory.

# **on** `gun.get(key).on(callback, options)`

  - Retrieve a raw javascript `{obj:'ect'}` of the node on the `key`, and subscribe to all subsequent realtime changes. You want to use this method to actually synchronously react to your data, rather than being stuck in async land.

  - `callback` is a `function(){}` which gets called as `callback(data, field)` and every time the node changes. Where

    - `data` is the raw javascript object of the node.

    - `field` is the field name it came from, if any.

  - `options` as `true` aggregates into an `{obj:'ect'}` with

    - `options.change` as `true` makes `data` only have the delta difference of the change that happened, rather than the full node again and again.

  - Examples

    - ... 
      ```javascript
      gun.get('user/thedoctor').on(function(who, field){
        console.log('The Doctor', who, field); // {title: "The Doctor", phone: '770-090-0461'}
      })
      ```

    - While this looks no different from above, the `delta` will be on subsequent events. 
      ```javascript
      gun.get('user/thedoctor').on(function(delta, field){
        console.log('What changes happened to The Doctor?', delta, field); // {title: "The Doctor", phone: '770-090-0461'}
      }, true)
      ```

# **val** `gun.get('user/thedoctor').val(callback, options)`

  - Is the same as **on** except only gets called once after the first peer, including the local peer, replies. It is slower than **on** and should be avoided, but is sometimes necessary. If you are using **val** it probably means your code has become spaghetti and very procedural, **on** is much cleaner and more functional.

  - `callback` is a `function(){}` which gets called as `callback(data, field)` once after the node has been loaded, and has no realtime features.

  - `options` none currently available.

  - Examples

    - ...
      ```javascript
      gun.get('user/thedoctor').val(function(who, field){
        console.log('The Doctor', who, field); // {title: "The Doctor", phone: '770-090-0461'}
      })
      ```

# **path**