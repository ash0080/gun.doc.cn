
# gun

[![NPM downloads](https://img.shields.io/npm/dm/gun.svg?style=flat)](https://npmjs.org/package/gun) [![Build Status](https://travis-ci.org/amark/gun.svg?branch=master)](https://travis-ci.org/amark/gun) [![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/amark/gun?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

GUN is a realtime, distributed, offline-first, graph database engine.

## But what makes gun **awesome**?

 - **Realtime** - GUN shares data across connected peers in real-time.  There's no need for plugins, extensions, or anything else, just use gun as directed and enjoy realtime data sharing.
 - **Distributed** - GUN is peer-to-peer by design and doesn't rely on a central data server.  This results in all kinds of data deliciousness, including being able to work offline by default.  Peers can be configured into more traditional centralized or federated systems, as appropriate for application needs.
 - **Graph** - All data in gun is encapsulated as nodes in a graph.  Graphs allow data to be related in traditional table formats, as well as tree format, and circular formats.  In other words, gun lets your data needs determine your data storage structure.


## Table of Contents
 - [Demos](#demos)
   - [Videos](#videos)
 - [How to use](#how-to-use)
 - [Gotchas](#gotchas)
 - [API Reference](#api-reference)
 - [How can I help make gun even more awesome?](#how-can-i-help-make-gun-even-more-awesome?)
 - [License](#license)
   - [Contributors](#contributors)
 - [Changelogs](#changelogs)
 - [Stay up-to-date](#stay-up-to-date)

## Demos

 - [Example applications](http://gunjs.herokuapp.com/)
 - Running demos locally

   The examples included in this repo are online [here](http://gunjs.herokuapp.com/), you can run them locally by:

   ```bash
   npm install gun
   cd node_modules/gun
   node examples/http.js 8080
   ```

   Then visit [http://localhost:8080](http://localhost:8080) in your browser. If that did not work it is probably because npm installed it to a global directory. To fix that try `mkdir node_modules` in your desired directory and re-run the above commands. You also might have to add `sudo` in front of the commands.

### Videos
 - [Fault tolerance](https://www.youtube.com/watch?v=-i-11T5ZI9o&feature=youtu.be) (01:01)
 - [Saving relational or document based data](https://www.youtube.com/watch?v=cOO6wz1rZVY&feature=youtu.be) (06:59)

## How to use
 - Download/Install

## Gotchas



## API reference
 - just the API and nothin but the API (link to more detailed documentation)

## How can I help make gun even more awesome?
 - Star this repo
 - Follow us and share your appreciation via [Twitter](https://twitter.com/databasegun), [LinkedIn](https://www.linkedin.com/company/gun-inc), and [Facebook](//TODO: need link here)
 - [Share projects you've written](projects)
 - [Build extensions or squish bugs](https://waffle.io/amark/gun)
 	 - Pick an issue
 	 - Fork the appropriate repo
 	 - Create your feature branch: git checkout -b my-new-feature
	 - Commit your changes: git commit -m 'Add some feature'
	 - Push to the branch: git push origin my-new-feature
	 - Submit a pull request :wink:
 - Donate moolah to help us keep coding

## License

Designed with ♥ by Mark Nadal, the gun team, and many very awesome contributors.  Liberally licensed under [Zlib or MIT or Apache 2.0](package.json).

## Contributors

Thanks to the following people who have contributed to GUN, via code, issues, or conversation (this list has quickly become tremendously behind! We'll probably turn this into a dedicated wiki page so you can add yourself):

[agborkowski](https://github.com/agborkowski); [alexlafroscia](https://github.com/alexlafroscia); [anubiann00b](https://github.com/anubiann00b); [bromagosa](https://github.com/bromagosa); [coolaj86](https://github.com/coolaj86); [d-oliveros](https://github.com/d-oliveros), [danscan](https://github.com/danscan); **[forrestjt](https://github.com/forrestjt) ([file.js](https://github.com/amark/gun/blob/master/lib/file.js))**; [gedw99](https://github.com/gedw99); [HelloCodeMing](https://github.com/HelloCodeMing); **[JosePedroDias](https://github.com/josepedrodias) (graph visualizer)**; **[jveres](https://github.com/jveres) ([todoMVC](https://github.com/jveres/todomvc) [live demo](http://todos.loqali.com/))**; [ndarilek](https://github.com/ndarilek); [onetom](https://github.com/onetom); [phpnode](https://github.com/phpnode); [PsychoLlama](https://github.com/PsychoLlama); **[RangerMauve](https://github.com/RangerMauve) ([schema](https://github.com/gundb/gun-schema))**; [riston](https://github.com/riston); [rootsical](https://github.com/rootsical); [rrrene](https://github.com/rrrene); [ssr1ram](https://github.com/ssr1ram); [Xe](https://github.com/Xe); [zot](https://github.com/zot);
[ayurmedia](https://github.com/ayurmedia);

This list of contributors was manually compiled, alphabetically sorted. If we missed you, please submit an issue so we can get you added!

## Changelogs

## Stay up-to-date

[![YouTube](https://img.shields.io/badge/You-Tube-red.svg)](https://www.youtube.com/channel/UCQAtpf-zi9Pp4__2nToOM8g) [![LinkedIn](https://img.shields.io/badge/Linked-In-blue.svg)](https://www.linkedin.com/company/gun-inc) [![Twitter Follow](https://img.shields.io/twitter/follow/databasegun.svg?style=social)](https://twitter.com/databasegun)