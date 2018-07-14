## Question

@amark Should this test fail or succeed?:
```
  it('should accept "incoming" when null is "current"', () => {
    const state = 2;
    expect(HAM(state, state, state, 'a', null as any)).toEqual({ converge: true, incoming: true });
  });
```

## Answer

@Kuirak_twitter if `null as any` is `null` then it should fail :( the reason why is I at some point in time wanted to define my own type rules, that `null`, `false`, `true`, etc. as types with lower atomic values than strings, therefore... naturally upon a direct state conflict (`2 === 2`) the "higher" value was deterministically chosen (aka `'a'` is incoming, and is `>` than `null` therefore it wins).
However... as you might expect... :/ this caused all sorts of non-standard type priorities between different programming languages and parsers I checked :( :( :(
So I unfortunately had to make the more (currently) universal decision: Let the serializer decide, as various programming languages are forced to be compatible with serialization protocols. Aka, JSON :( (which is terrible terrible terrible), I do have my own serialization format but it isn't nearly as adopted as JSON.

so.... as you can guess, what happens?
JSON.stringify('a') < JSON.stringify(null) :(
means `null` has higher precedence. :(

(for those who don't know... the reason why JSON does this is that strings are encoded with `"` which is ASCII code 34 [one up from RAD's starting key `!` bang! at 33, the lowest ASCII visible character. (whether " " is visible is debatable or not, either way, the reason why I didn't choose RAD to start at that is because filesystems don't like " ")] and JSON encodes `null` as the serialized text of `null` without the parse type indicator of `"` since it isn't a string [ASCII encodes `null` at 0 which makes sense, but most systems/parsers won't transmit (or improperly handle) ASCII 28 and below]. And in serialized format, `"`(a") < `n`(ull). :( :( :(. )