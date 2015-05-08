GUN generally considers sharding as *not a good thing.

*With the exception that you are running your own physical machines. Let's talk about that and lay out the landscape.

### 1. Database as a Service
Such as Compose, Heroku's Postgres, Dropbox Datastore, Firebase, Parse, etc.

### 2. Deployed Database in the Cloud
This is where you spin up a box on AWS EC2, Google Cloud Engine, Digital Ocean, etc. and install and maintain your own database - such as MySQL, MongoDB, RethinkDB, etc.

### 3. On your own Physical Machine
While similar to (2), you ultimately own the hardware which you do deployments on - where when resources run low, you have to physically add hard drives.

The advantage of (1) is that (2) and (3) may be hard and difficult, which is why DevOps and SysAdmins are critical roles.

... work in progress ...