
Now that we've looked at [[connecting to a peer|API-(v0.2.x)#gun]] and [[putting values in gun|Putting-Data-(v0.2.x)]], let's look at retrieving them again. For starters, let's go get Sarah Jane out of the database. We'll start with the values we added during [[Putting Data|Putting-Data-(v0.2.x)]].
```javascript
gun.get('companion/sarahjane').val(function(sarahjane) {
    console.log(sarahjane.location); // Prints `London` to the console
});
```

To explain the different methods going on here, `.get(__key__)` gives us access to a particular "key" in gun, and then `.val()` takes a callback that will have the value passed to it as a parameter. Once you've loaded a key, you can also tell gun to "step inside" that object and return an associated object. For example, we can easily get K-9 through Sarah Jane by using `.path()`

```javascript
gun.get('companion/sarahjane').val(function(k9) {
    console.log(k9.species); // Prints `canine` to the console
});
```