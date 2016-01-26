---

# COMING SOON TO A COMPUTER NEAR YOU!

--- 

Until we complete the [Getting Started with GUN](getting-started-(v0.3.x)) Guide, please review the [API documentation](API-(v0.2.x)) and [examples' source code](../gun/blob/master/examples).

And, if you'd like to contribute, helping with this page is a great place to start. :wink: 

## Overview

GUN is different than many other databases, in a variety of ways.  The following pages provide a background on some of the theories behind gun, as well as examples of gun's implementation.  While it isn't necessary to read these to use gun, these explanations are useful to understanding how and why gun works the way it does.

 - **[CAP Theorem](CAP-Theorem)**  
   An overview of tradeoffs that GUN decides to default to in relation to the CAP Theorem.

 - **[Conflict Resolution with Guns](Conflict-Resolution-with-Guns)**  
   An overview of how gun merges data.

 - **[DAP Theorem](DAP-Theorem)**  
   An overview of the relationship between decentralization, anonymity, and privacy.


## How To:
 - **[Delete Data](Delete)**  
   In distributed systems it is not possible to truly delete data.  This page explains why and how to practically delete data.
  
 - **[Host with Amazon AWS](Hosting-with-Amazon-AWS)**  
   While gun does not require a data server, it is frequently useful to have a centralized or always on server.  Using Amazon's AWS service is one way to spin up such a server.

 - **[Building Modules for Gun](Building-Modules-for-Gun)**
   GUN is designed to be as minimal as possible, with any additional functionality being provided via modules. This page provides an overview of module types and links to building specific types of modules.

 - **[Porting GUN](Porting-GUN)**  
   While this version of gun is written in JavaScript, there is nothing precluding porting gun to other languages.  This page provides a high-level overview of the conflict resolution algorithm behind gun, to ease the porting process.