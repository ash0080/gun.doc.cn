# NOTE: WORK IN PROGRESS; MAY HAVE ERRORS

### **Gun** `var gun = Gun(options)`

  - Instantiates a gun database.

  - *Options* as

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

    - `var gun = Gun({file: 'data.json'})` to change the name of the file that gets dumped to. **Warning!** The file module is the server's default persistence, and only be used for local development testing only!

    - `var gun = Gun({s3: {key: '', secret: '', bucket: ''}})` on the server dumps to AWS S3, this is the preferred persistence layer.

### **put** `gun.put(data, cb, options)`

 - When given a location –via a peer, `.get()`, or `.path()`– puts the attached primitive or JavaScript object data at that location.

 - **Examples**:  
   - _`.put` object without a key_

     ```javascript  
     var gun = Gun(location.origin + '/gun')  
     gun.put({"field": "value"})  
     ```   
     - Assuming `gun` is the peer variable, this places the object directly on the peer's gun instance.  
     - **CAUTION**: Without a key, this object can only be accessed with a subsequent `.all()` call.    

   - _`.put` object on an existing key without a new, direct key_

     ```javascript  
     var gun = Gun(location.origin + '/gun')  
     gun.get('example/app/data')  
         .put({"field": "value"})  
     ```  
     - Assuming `gun` is the peer variable and there is already an object at the `.get` key, then this merges the object within the specified `.get` key object.  
     - This object can be accessed through the existing `.get` key or with an `.all()` call.

   - _`.put` object on an existing key without a new, direct key_

     ```javascript  
     var gun = Gun(location.origin + '/gun')  
     gun.get('example/app/data')  
         .put("field": {"subfield1": "value1", "subfield2": "value2"})  
     ```  
     - Assuming `gun` is the peer variable and there is already an object at the `.get` key, then this merges the object within the specified `.get` key object.  
     - This object can be accessed through the existing `.get` key or with an `.all()` call.

   - _`.put` object on an existing key with a new, direct key_

     ```javascript  
     var gun = Gun(location.origin + '/gun')  
     gun.get('example/app/data')  
         .put({"field": "value"})  
         .key(`example/app/data/field`)
     ```  
     - Assuming `gun` is the peer variable and there is already an object at the `.get` key, then this merges the object within the specified `.get` key object.  
     - This object can be accessed directly through the newly assigned key (`.get('example/app/data/field')`), indirectly through the previous `.get` key (`.get('example/app/data'`), or with an `.all()` call. 

##`.get`