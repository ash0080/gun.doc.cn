Getting Values
=============

Now that we've looked at setting values in Gun, let's look at retrieving them again.  For starters, let's go get Cecil out of the database.  If you haven't gone over the setting values, [[you probably want to start there|Setting Values]].

```javascript
gun.load('person/cecil').get(function(cecil) {
    console.log(cecil.location); // Prints `Night Vale` to the console
});
```

To explain the different methods going on here, `.load(__key__)` gives us access to a particular "key" in Gun, and then `.get()` takes a callback that will have the value passed to it as a parameter.  Once you've loaded a key, you can also tell Gun to "step inside" that object and return an associated object.  For example, we can easily get Khoshekh through Cecil by using `.path()`

```javascript
gun.load('person/cecil').path('cat').get(function(khoshekh) {
    console.log(khoshekh.species); // Prints `cat` to the console
});
```
