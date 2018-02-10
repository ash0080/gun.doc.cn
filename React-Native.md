Gun works out of the box with React Native.

# Importing Gun

Use the minified version of Gun that is bundled with the standard distribution:

```javascript
import Gun from 'gun/gun.min.js';

// Connect to a remote server
const gun = new Gun("https://your-gun-server.com/gun");

// use Gun as usual
```

## Long Term Storage

In the browser, Gun will dump the graph into LocalStorage to achieve longer-term storage. On a Node server, it can dump into a JSON file or into another storage system via an adapter.

In React Native, if you want to retain application data for either offline use or for long-term storage on the device itself, you can plug Gun into a few community-built adapters:

* [AsyncStorage](https://github.com/staltz/gun-asyncstorage) by [staltz](https://github.com/staltz)
* [SQLite](https://github.com/sjones6/gun-react-native-sqlite) by [sjones6](https://github.com/sjones6)
* [Realm](https://github.com/sjones6/gun-realm) by [sjones6](https://github.com/sjones6)

If you choose not to use any long-term storage option, that's totally fine. Gun will keep as much of the graph as possible in memory, which will be dumped every time the application is restarted.

## A Tip for Local Development

Let's say you've got a Gun server running on a Node process on your machine; while you're developing your React Native application, you want to connect up to that server rather than some remote, production server over the internet. Instead of giving `localhost`, provide the Local-Area-Network (LAN) IP address for your machine:

```javascript
const gun = new Gun("http://192.168.0.01:gun");
```

## Example Application

[Co-Libate](https://github.com/sjones6/co-libate) by [sjones6](https://github.com/sjones6): A collaborative wine-tasting application
