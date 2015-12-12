# Opt



 - `raw`, when set to
    - `true` the callback gives you the event, not the values. If you need the listener itself, your option gets mutated to have `{raw:true}` become `{raw:event}`.
 - `once` when set to
    - `true` should only let the callback be called ONCE, however it may be per hash. (Cascades to `.on`)

# THIS PAGE IS UNFINISHED AND THEREFORE MIGHT BE WRONG

# Unique

 - `.on()`
    - `change` when set to
      - `true` gives the change delta rather than the full node. Cannot be used in conjunction with `{raw:true}`.
    - `on` when an event
      - `{soul:'soul'}` it passes it through and pseudos it if it is a keynode.


# At

 - `soul`
 - `field`
 - `at`
 - `not`
 - `err`