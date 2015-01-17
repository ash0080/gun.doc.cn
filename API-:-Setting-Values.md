Thanks to the [chaining](#) that's built into GunDB, we can really easily assign multiple keys to the same object, too

```javascript
gun.set({
  username: "marknadal",
  name: "Mark Nadal",
  email: "mark@gunDB.io"
}).key('usernames/marknadal').key('emails/mark@gunDB.io');
```

Now, both `usersnames/marknadal` and `emails/mark@gunDB.io` can be used to look up the same object.