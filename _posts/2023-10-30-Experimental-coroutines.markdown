---
layout: single
title: Experimental support for coroutines
---

There is a branch with experimental support for coroutines in *Trompeloeil*.

Only `co_return` is supported so far.

To use it, get branch
[`coro_experiment`](https://github.com/rollbear/trompeloeil/tree/coro_experiment)
and enable the experiment with

```Cpp
#define TROMPELOEIL_EXPERIMENTAL_COROUTINES
#include <trompeloeil.hpp>
```

Use with **`CO_RETURN()`** or **`LR_CO_RETURN()`**.

Example:

```Cpp
#define TROMPELOEIL_EXPERIMENTAL_COROUTINES
#include <trompeloeil.hpp>

using iptr = std::unique_ptr<int>;

struct co_mock {
    MAKE_MOCK0(unique, task<iptr>());
    MAKE_MOCK1(func, void(int));
};

task<void> waiting_func(co_mock& m)
{
    auto ptr = co_await m.unique();
    m.func(*ptr + 1);
}

TEST(...)
{
    co_mock m;
    REQUIRE_CALL(m, unique())
      .CO_RETURN(std::make_unique<int>(5));
    REQUIRE_CALL(m, func(6));
    std::invoke([&m]()->task<void>{co_await waiting_func(m);}); 
}
```

Above `task<T>` is an awaitable coroutine return type.

**NOTE!** If you need to throw an exception from within the coroutine, you must
fake this by using **`.CO_RETURN(<expr>)`** such that **`expr`** throws and has
the correct return type. For example
`.CO_RETURN(false ? 1.0 : throw std::runtime_error("message"))`. This is ugly,
but gets the job done.