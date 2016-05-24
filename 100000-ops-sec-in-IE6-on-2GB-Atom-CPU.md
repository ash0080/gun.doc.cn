Ladies and Gentlemen.

![](https://dl.dropboxusercontent.com/u/4374976/screenshots/ie6gun.png)

Thank you.

We're getting even better numbers on other devices:

 - Android phone, **~5M ops/sec**.
 - Macbook Air, Chrome, **~30M ops/sec**.
 - Macbook Pro, Chrome Canary, **~80M ops/sec**.

## How did we accomplish 100,000 ops/sec in IE6?

This test ran on the cheapest Windows laptop I could find on the market. A $150 2GB Atom CPU which I loaded up with IETester to do some performance benchmarking. Getting these numbers has been hard work. For those who want the real answer, check out our [Read the Source](https://www.youtube.com/watch?v=70dn1oZQFCk) podcast:

[![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/70dn1oZQFCk/0.jpg)](https://www.youtube.com/watch?v=70dn1oZQFCk)

For those who want the short summary:

1. Use PTSD (Performance Testing Speed Development) to micro-optimize your code without getting lost in micro-optimizations. More on PTSD to come in the future.
2. Reduce function calls and prioritize hot code paths as the first if statements.
3. Do work only once, then use centralized caching and cache updating to skip ever doing work again.

Resulting caveats:

 - These tests are running on the bleeding edge `0.5` branch, only 15% of the API tests are currently passing.
 - These performance benchmarks rarely have cache misses, but many other database benchmarks do the same. Measuring disk I/O isn't very interesting.
 - Take all performance testing benchmarks with a huge grain of salt. That is why we're working on [panic](https://github.com/gundb/panic-server), a distributed testing runner.

Hop on the [gitter](https://gitter.im/amark/gun) to learn more and chat with us!