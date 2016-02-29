**Table of Contents**
 - [Introduction](#Introduction)
    - [Example of an embedded peer](#Example-of-an-embedded-peer)
    - [Example of server peers](#Example-of-server-peers)
    - [Additional Learning Resources](#Additional-Learning-Resources)
 - [Theory, Implementation, and Extensions of GUN](#Theory-Implementation-and-Extensions-of-GUN) 
 - [How To's](#How-To)   

## Introduction
GUN is a tiny, distributed data storage and synchronization solution that runs everywhere. GUN lets you focus on the data you need to store, retrieve and share without having to focus on the how, why or where. Different gun instances can have different persistence models which are transparent to the storage and retrieval code.

GUN is an offline-first, object storage and retrieval engine based in JavaScript that is tiny and fast. While having extensive options for customization, gun comes with sane defaults that make getting started just about as simple as it gets. While you can start storing and retrieving data with virtually no setup, you can extend the connectivity of gun to multiple gun peers that stay in sync, are crash resistant, always preserve the integrity of your data and operations. 

GUN can run standalone in your JavaScript context or it can connect to one or multiple partner storage processes. You can add more gun peers at any time, choosing to mix and match back end persistence mechanisms with no knowledge of the remote persistence models required. 

Out of the box, gun makes data synchronization effortless, handling offline changes and gruesome merge conflicts across processes both seamless, version tracked and controlled.

Each time a client receives data, gun updates a local copy for speed and efficiency, meaning that your most crucial data is backed up on every peer that uses it.  This makes the loss of important information nearly impossible, as all copies of the data must be destroyed for it to be lost. 

With gun's unique architecture and graph database technology, your data is stored, retrieved and exchanged as serialized gun nodes, which themselves wrap standard JavaScript objects. This means that every form of data that you store, manipulate, and share can be expressed natively by the developer without having to think outside of JavaScript objects. This design allows for emergent, flexible, and powerful data structures in your application and shared across processes. 

### Example of an embedded peer

> **Note:**  For embedded peer usage, there is *no* need to run `npm`.

```JavaScript
<script src="https://rawgit.com/amark/gun/master/gun.js"></script>
<script>

  var endpoints
  var gun = Gun(endpoints);  // connect to an endpoint [] == in memory only

  gun.put({Hello:"world"}).key('Hello');  // Store a json { Hello: 'World'} at path 'Hello'

  var HelloContainer = gun.get('Hello');  // Retrieve json from key 'Hello' 
  HelloContainer.on(function(data){
                console.log (data);       // Retrieve json from key 'Hello' 
          });
</script>
```

### Example of server peers


> **Note:** If you don't have [node](http://nodejs.org/) or [npm](https://www.npmjs.com/), read [this](https://github.com/amark/gun/blob/master/examples/install.sh) first.

```bash
npm install gun
cd node_modules/gun
node examples/http.js 8080
```

Then visit [http://localhost:8080](http://localhost:8080) in your browser and review the [examples' source code](../blob/master/examples)

If that did not work it is probably because npm installed it to a global directory. To fix that try `mkdir node_modules` in your desired directory and re-run the above commands. You also might have to add `sudo` in front of the commands.

### Additional Learning Resources

Additional examples, along with tutorials, are available via the [Learning Resources Page](http://gun.js.org/learning.html).

## Theory, Implementation, and Extensions of GUN

GUN is different than many other databases, in a variety of ways.  The following pages provide a background on some of the theories behind gun, as well as examples of gun's implementation.  While it isn't necessary to read these to use gun, these explanations are useful to understanding how and why gun works the way it does.

 - **[Glossary](Glossary)**  
   Graph databases and distributed systems each have field specific terminology.  On top of those, gun has some of its own terminology.  The glossary provides a concise list of terms you may not have seen before.

 - **[GUN’s Data Format (JSON)](GUN’s-Data-Format-(JSON))**  
   GUN saves data in its own subset of JSON.  This page provides an overview of gun's use of JSON.

 - **[Partials and Circular References (v0.2.x)](Partials-and-Circular-References-(v0.2.x))**  
   Because gun is a graph database, it supports circular references between data.  This is accomplished via the use of partial nodes and references.  This page provides an overview and examples of gun's partials and circular references.

 - **[CAP Theorem](CAP-Theorem)**  
   An overview of tradeoffs that GUN decides to default to in relation to the CAP Theorem.

 - **[Conflict Resolution with Guns](Conflict-Resolution-with-Guns)**  
   An overview of how gun merges data.

 - **[DAP Theorem](DAP-Theorem)**  
   An overview of the relationship between decentralization, anonymity, and privacy.


## How To:
 - **[Delete Data](Delete)**  
   In distributed systems it is not possible to truly delete data.  This page explains why and how to practically delete data.
  
 - **[Using Amazon S3 for Storage](Using-Amazon-S3-for-Storage)**  
   While gun does not require a data server, it is frequently useful to have a centralized or always on server. You can host this server anywhere, but you may want to use Amazon's AWS S3 service to persist data to storage for cheap.

 - **[Build Modules for Gun](Building-Modules-for-Gun)**  
   GUN is designed to be as minimal as possible, with any additional functionality being provided via modules. This page provides an overview of module types and links to building specific types of modules.

 - **[Port GUN](Porting-GUN)**  
   While this version of gun is written in JavaScript, there is nothing precluding porting gun to other languages.  This page provides a high-level overview of the conflict resolution algorithm behind gun, to ease the porting process.