---
layout: single
title: Experimental macros for mocking functions with inferred arity
---

Not having to state the number of arguments to a function when defining a
mock has been on the wish list from the very beginning of Trompeloeil,
and is an [open issue](https://github.com/rollbear/trompeloeil/issues/28)
since 2017!

There is now experimental support for new mocking macros that, finally,
does away with that.

`MAKE_MOCK(name, signature)` and `MAKE_CONST_MOCK(name, signature)`.
The caveat is that the signature must be written in trailing return type
syntax.

```C++
struct S
{
    MAKE_MOCK(func, (int, double)->bool);
};        
```

Please try it out and report your findings.

Also, a matter of taste; would it be better if the syntax required
the `auto` keyword? `MAKE_MOCK(func, auto (int, double)->bool)`?

The functionality is available on branch
[`trailing_return_type`](https://github.com/rollbear/trompeloeil/tree/trailing_return_type)
