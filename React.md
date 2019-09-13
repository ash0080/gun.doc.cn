# Under Development

https://github.com/rm-rf-etc/weir

## Motivation

Fully abstract / automate the process of attaching & detaching GUN listeners to any React component using a higher order component. All the developer should need is to provide a root node (GUN object) to specify which part of the graph we are binding. Any attributes we want the component to receive from GUN, we auto-attach and inject via React hooks API:

```javascript
import { useBucket } from 'weir';
import { thingsBucket } from './buckets';

const Component = () => {
    const bucketState = useBucket(thingsBucket, 'prop1 prop2')

    return ( ... );
};
```

Please visit the issues page of the repo to find latest updates and plans. Development has been moving pretty rapidly but effort is being made to keep documentation and plans current. The library isn't yet to the point of general readiness but should be within the next month or two (as of 2019-09-13).