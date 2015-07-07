# NOTE: WORK IN PROGRESS; MAY HAVE ERRORS

## **Gun** `Gun(options)`  
  - Description: `Gun` instantiates a gun database.
  - Options:
     - empty - Creates a local, in-memory *only* datastore.
     - url string - Creates a datastore that syncs with the designated peer.
     - array of url strings -  Creates a datastore that syncs with multiple peers.
     - object - The previous options are aggregated into an object, including
        - options.


##`.put`
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