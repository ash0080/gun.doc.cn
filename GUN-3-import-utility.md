`https://rawgit.com/amark/41d31de8911e6d5ebe13127180cf9588/raw/bb658b2460b14001e891f982073bc5a7e4e35186/gun3import.js`

or

`<script src='https://rawgit.com/amark/41d31de8911e6d5ebe13127180cf9588/raw/bb658b2460b14001e891f982073bc5a7e4e35186/gun3import.js'></script>`

Here is an example of how to use (after including the code):

Get some sample data to import, like https://api.github.com/repos/octocat/Hello-World/issues . Save this array to a variable called `githubReply`;

Now we can import the data:

```javascript
githubReply.forEach(function(data){
  importGUN3(data);
});
```

and check to see if one of the records got saved:

```javascript
var record = githubReply[0];

var gun = Gun();
gun.get(record.id).on(function(data){
  console.log("The record", data);
}).path('user').val(function(data){
  console.log("The user", data);
});
```

It works!