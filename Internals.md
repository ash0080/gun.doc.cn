- **set**, writes to `this._.node`, if `this._.keys` exists then links them to it.
- **key**, writes to `this._.keys`, if `this._.node` exists then links them to it.


- **load**, reads from `this.__.keys`, and links `this._.keys` and `this._.node`.
- **get**, reads from `this._.node`.


path is a wrapper around load.

on emulates get, but over changes.

insert is a wrapper around set.

map is a wrapper around multiple paths.