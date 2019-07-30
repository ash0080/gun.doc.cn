# Under Development

https://github.com/rm-rf-etc/react-gun

## Motivation

Fully abstract / automate the process of attaching & detaching GUN listeners to any React component using a higher order component. All the developer should do is provide a root node (GUN object) to specify which part of the graph we are binding. Any attributes we want the component to receive from GUN, we auto-attach and inject by reading the function signature of the component, assuming the component is a function component like this:

`const Component = ({ gun: { prop1, prop2 } }) => ( ...`

No strategy has been decided on yet for class-based components.

## Other Ideas

* a convenient way to lookup root nodes dynamically
* schema validation to ensure all puts and reads match with the developer's intent
* framework to provide auto-magic handling of arrays (@amark, thoughts?)

More to come... Plans related to use of `gun.user()` vs `gun.get()` need to be added.
