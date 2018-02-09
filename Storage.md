`TODO: This page will probably be dedicated to the API of how to build a storage adapter, not a list of storage adapters (except official RSE one, since it has its own API that needs explaining), a list of storage adapters should be included in the community/awesome/ecosystem page along with everything else so it is easy to find!` 

To permanently store all GUN's data, your GUN server app must define which storage engine to use. By default a GUN server will store the data in a file, but this is just meant to be used during development. It is not a sound solution for production.

There are several storage engines that you can choose:

## localStorage

In browser, GUN app data is stored in the browser's localStorage, but is primarily treated as cache.

## File

This is the current default, great for development, but not meant for production.

## Radix

The Radix Storage Engine (RSE) will soon become the default and then be a good choice for production.

RSE will become the new default and will be production ready, however it is not the default yet (although you can make it the default by just turning off the current default with `Gun({localStorage: false})` in NodeJS.

## S3

Works with the RSE.

## IPFS

In the future We foresee GUN using RSE with IPFS/Filecoin bindings not S3, as RSE will allow for easy super-peer storage-sharding (although I do not recommend people do sharding by default).

## Level

https://github.com/PsychoLlama/gun-level

## sack

The currently community-agreed upon custom storage engine is @d3x0r 's. https://github.com/d3x0r/gun-file

## Flint

Allows for easy integration into using other databases as a storage engine, many of the systems below were built with this: https://github.com/sjones6/gun-flint .

We do not recommend using the Mongo/MySQL/etc. adapters, even though that is possible, simply because that is overkill and not the future - more like a toy example of integration.

## MongoDB

https://github.com/sjones6/gun-mongo

## MySQL

https://github.com/sjones6/gun-mysql

## Cassandra

https://github.com/lmangani/gun-cassandra

## Elasticsearch

https://github.com/lmangani/gun-elastic

## SQLite

https://github.com/d3x0r/gun-db
