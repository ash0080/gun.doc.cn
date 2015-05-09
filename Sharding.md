GUN generally considers sharding as *not a good thing.

*With the exception that you are running your own physical machines. Let's talk about that and lay out the landscape.

### 1. Database as a Service
Such as Compose, Heroku's Postgres, Dropbox Datastore, Firebase, Parse, etc.

### 2. Deployed Database in the Cloud
This is where you spin up a box on AWS EC2, Google Cloud Engine, Digital Ocean, etc. and install and maintain your own database - such as MySQL, MongoDB, RethinkDB, etc.

### 3. On your own Physical Machine
While similar to (2), you ultimately own the hardware which you do deployments on - where when resources run low, you have to physically add hard drives.

## Advantages

The advantage of (1) is that (2) and (3) may be hard and difficult, which is why DevOps and SysAdmins are critical roles. Database as a Services outsource these roles for you with the downside of typically being expensive when you surpass the freemium tier, slower network latency, and not owning your data.

The advantage of (3) is that you own your data, which for security or regulatory concerns might be necessary. It suffers a serious pain point though, upfront costs and manual intervention as well as extensive knowledge across disciplines.

Deploying to the cloud is the middle ground, you are paying to outsource the physical work and take advantage of provisioning as many virtual machines as possible. The important distinction is that things are virtualized, whether that be the computation or the storage - meaning the cloud provider has abstracted the scaling of these resources for you.

## Observations

Traditionally database servers had to be provisioned, acting as a transactor for data on one or more disks. To be precise, this transactor is an abstraction and virtualization layer for storing structured data. That way the application developer does not have to worry about storage location. But cloud providers have already virtualized storage, such as S3 or GCS which are "infinite" hard drives. This is not to say that fixed size allocated disks are unavailable, they are - and significantly faster, but the constraints on their size are arbitrarily limited by the underlying virtualization.

These arbitrary constraints are the very things that the database and cloud are trying to remove. Yet because there is such a prevalent expectation that things are limited, people keep resorting to finite drives by default. And how do you get around those defaults? By chunking data into tiny fixed sizes and storing and replicating them on a growing set of hard drives that are dedicated to a specific "shard" key or range. This is when sharding is necessary, such as when you run your own physical machines.

However,