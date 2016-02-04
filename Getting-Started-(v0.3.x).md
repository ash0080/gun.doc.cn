---

# COMING SOON TO A COMPUTER NEAR YOU!

--- 

Until we complete this Getting Started Guide, please review the [API documentation](API-(v0.3.x)) and [examples' source code](../blob/master/examples).

(_And, if you'd like to contribute, helping with this page is a great place to start._ :wink:)

## Overview

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