- **set**, writes to `this._.node`, if `this._.keys` exists then links them to it.
- **key**, writes to `this._.keys`, if `this._.node` exists then links them to it.


- **load**, pulls `this._.node` in from cache or remote, via a key or a soul, and links.
- **get**, reads from `this._.field` on `this._.node` or the node itself.


**path** wraps load recursively, to traverse depth, and writes `this._.field`.

**on** emulates get, but over time as changes are made.

**map** wraps around parallel paths.

**blank** is a conditional which executes if the node from load is empty.

**insert** wraps around set, putting in objects without human friendly fields, may not stay in core.