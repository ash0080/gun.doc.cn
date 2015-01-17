## API
At its core, gun is just a synchronization protocol - but that is boring and useless by itself. So I've implemented a really exciting and easy API for you to use. If you don't like my approach or naming convention, you can simply rename things yourself or better yet fork the project and build a beautifully custom API and everything will still work via the protocol.

#### Approach
- If you are unfamiliar with **reactive** programming, it is a code structure that emphasizes vertical readability by avoiding nested loops and callbacks. Instead of doing
```javascript
// ugly
for(var i = 0; i < PDFs.length; i += 1){
	var fileName = PDFs[i];
	readFromDisk(filename, function(file){
		var pages = splitIntoPages(file);
		for(var j = 0; j < pages.length; j += 1){
			var page = pages[j];
			saveToFolder('page' + j, page, function(err, done){
				console.log("Done! If no," err);
			});
		}
	});
}
```
you can simplify to something more clearer and reusable
```javascript
var PDF = {};
PDF.read = function(fileName){
	readFromDisk(filename, PDF.split);
}
PDF.split = function(file){
	splitIntoPages(file).forEach(PDF.save);
}
PDF.save = function(page, number){
	saveToFolder('page' + number, page, PDF.done);
}
PDF.done = function(err, done){
	console.log("Done! If no," err);
}
PDFs.forEach(PDF.read);
```
- If you are unfamiliar with **chaining**, it is an API style that allows you to zoom into a context without having to create new variables. So rather than doing
```javascript
// ugly
var page = document.getElementById("page");
var child = page.firstElementChild;
child.style.background = "blue";
child.style.color = "green";
child.addEventListener('click', function(event){
  console.log("hello world!");
}, true);
```
you can just do
```javascript
$('#page')
	.children()
	.first()
	.css("background", "blue")
	.css("color", "green")
	.on("click", function(event){
		console.log("hello world");
	});
```