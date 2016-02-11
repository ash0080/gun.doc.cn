# Deleting Data from GUN

Gun takes a different approach to deletion than most other centralized services. Since other (potentially offline) peers might still have a copy of the node, removing your own copy is only temporary. As soon as that peer comes back online, the data will resynchronize and the node is back again. While this is pretty cool from the perspective of data recovery, it might be annoying if you're trying to get rid of something. This can be solved by replacing the references to the node with a different value, like a primitive or a pointer to different node.

```javascript
var gun = Gun().get('data').put({
  object1: {'#': 'pointer1'},
  object2: {'#': 'pointer2'}
})
var removeMe = gun.path('object3').put({
  data: true
})
// gun now has a pointer to `object3`
// you can break the reference to "removeMe" by updating the field
gun.path('object3').put(null)
```

Now if you were to map over each item in the above example, it would not contain the pointer to `object3`.

As soon as the value is set, in this case `null`, the update propagates to each peer, breaking the reference to the old node. If storage space is a concern, a garbage collection module could be built on top of gun that removes data when it's sat on the shelf too long. At that point you might want to consider migrating to a hosted solution such as AWS, which has ridiculously cheap rates and oodles of storage space.

Another way of thinking about deletion is that you're not removing the data, but rather it's *discoverability*. It would be like Google removing a result from it's search. You can still enter the URL, but the website is effectively invisible.

If you have any more questions about removing data from gun, ping us on [Gitter](https://gitter.im/amark/gun) and we'll do our best to help out.