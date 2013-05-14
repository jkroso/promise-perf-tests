# Promise implementation performance tests

This is a set of *basic* performance tests for promise implementations.  As is almost always the case, take these with the usual grains of salt.  That said, they should give a reasonable ballpark comparison of the performance characteristics of common, basic operations that most promise libraries provide.

Of course, performance is not the only thing to consider in a promise library.  Interoperability via a proposed standard, such as Promises/A, API convenience, safety, and even code size (for browser applications) are all important, application-specific considerations.

## Running the tests

Right now, the tests are runnable en masse via `npm test` in unix-like environments, and individually via node in other envs.

### Setup

1. Clone the repo
1. `npm install` to install the promise implementations to be tested
1. Run tests:
    * Run all tests: `bin/pperf`
    * Run a single test: `bin/pperf -t <test>`
    * Run a single implementation `bin/pperf -a <implementation>`
    * Print available tests and implementations `bin/pperf -h`

# Implementation-specific notes

## when.js

[when.js](https://github.com/cujojs/when) uses synchronous resolutions, and no longer uses `Object.freeze()` as of v1.6.0, to avoid this unfortunate [v8-imposed performance penalty](http://stackoverflow.com/questions/8435080/any-performance-benefit-to-locking-down-javascript-objects).

## avow

[avow](https://github.com/briancavalier/avow) is an example [Promises/A+](http://promises-aplus.github.com/promises-spec/) implementation.  In its default configuration, it uses asynchronous resolutions and does not call `Object.freeze`.  However, it can be configured to use synchronous resolutions, and/or `Object.freeze`.  Performance tests are run using the default configuration.

## Q

[Q](https://github.com/kriskowal/q) uses asynchronous resolutions, and calls `Object.freeze` on its promises, and so it incurs the [v8-imposed performance penalty](http://stackoverflow.com/questions/8435080/any-performance-benefit-to-locking-down-javascript-objects).

## RSVP

[RSVP](https://github.com/tildeio/rsvp.js) uses asynchronous resolutions, and doesn't use `Object.freeze`.

## deferred

[deferred](https://github.com/medikoo/deferred) uses synchronous resolutions, and doesn't use `Object.freeze`.

## jQuery Deferred

[jQuery](http://jquery.com) uses synchronous resolutions, and it doesn't use `Object.freeze`.

These tests use jQuery via [jquery-browserify](https://github.com/jmars/jquery-browserify), with [jsdom](https://github.com/tmpvar/jsdom) for support.  This approach was taken from the [Promises Test Suite](https://github.com/domenic/promise-tests), and currently, appears to be the only way to use jQuery 1.8.x in node.

jQuery Deferred is not intended to be fully Promises/A compliant in its forwarding behavior.  We've done our best to design the tests so that that does not affect the performance characteristics.  While this *does* affect the *computation results* of some tests, it can be ignored for most performance testing purposes.

## Laisseze-faire

[Laisseze-faire](https://github.com/jkroso/Laissez-faire) uses synchronous resolutions, and it doesn't provide proxies by default.

## Micro-promise

Uses synchronous resolutions an doesn't freeze its objects.

# Test Environment

These tests were run on a MacBook Pro Intel Core i7, 2.3Ghz, 8g RAM, 256g SSD, using Node.js v0.8.14 installed via [nvm](https://github.com/creationix/nvm) and the following library versions (`npm ls`):

```
├── avow@1.0.0
├── b@2.0.0
├─┬ cli-table@0.2.0
│ └── colors@0.3.0
├── colors@0.6.0-1
├─┬ commander@1.1.1
│ └── keypress@0.1.0
├─┬ deferred@0.6.1
│ ├── es5-ext@0.9.1
│ ├── event-emitter@0.2.1
│ └── next-tick@0.1.0
├── exec@0.0.4
├── jquery-browserify@1.8.1
├─┬ jsdom@0.3.4
│ ├─┬ contextify@0.1.3
│ │ └── bindings@1.0.0
│ ├── cssom@0.2.5
│ ├── cssstyle@0.2.3
│ ├── htmlparser@1.7.6
│ ├── nwmatcher@1.3.0
│ └─┬ request@2.12.0
│   ├─┬ form-data@0.0.6
│   │ ├── async@0.1.22
│   │ └─┬ combined-stream@0.0.3
│   │   └── delayed-stream@0.0.5
│   └── mime@1.2.9
├── laissez-faire@0.10.1
├── micro-promise@0.1.0
├── promises-a@2.3.0
├─┬ promisify@0.1.0
│ ├── laissez-faire@0.10.1
│ └── sliced@0.0.3
├── q@0.8.12
├── randy@1.4.0
├── rsvp@1.2.0
├── stripcolorcodes@0.1.0
└── when@1.7.1

```

# Test Results

Each test is sorted from best to worst time. Times are in milliseconds, and Diff is the percentage difference from the best time.

```
   fulfill x50
  ┌───────────────┬────────────┬──────────────┬──────────┐
  │               │ total (ms) │ average (ns) │ diff (%) │
  ├───────────────┼────────────┼──────────────┼──────────┤
  │ laissez-faire │          1 │       22,154 │        - │
  │ micro-promise │          1 │       23,523 │        6 │
  │ then.promise  │          1 │       29,292 │       32 │
  │ deferred      │          2 │       34,915 │       58 │
  │ rsvp          │          2 │       45,015 │      103 │
  │ when          │          3 │       52,621 │      138 │
  │ q             │          6 │      112,225 │      407 │
  │ avow          │         21 │      419,085 │    1,792 │
  └───────────────┴────────────┴──────────────┴──────────┘

  fulfilled x50
  ┌───────────────┬────────────┬──────────────┬──────────┐
  │               │ total (ms) │ average (ns) │ diff (%) │
  ├───────────────┼────────────┼──────────────┼──────────┤
  │ laissez-faire │          0 │        3,238 │        - │
  │ q             │          0 │        8,942 │      176 │
  │ deferred      │          1 │       10,012 │      209 │
  │ then.promise  │          1 │       11,181 │      245 │
  │ when          │          1 │       15,343 │      374 │
  │ rsvp          │          1 │       18,343 │      467 │
  │ avow          │          1 │       28,720 │      787 │
  │ micro-promise │          2 │       32,549 │      905 │
  └───────────────┴────────────┴──────────────┴──────────┘

  pending-sequence x50
  ┌───────────────┬────────────┬──────────────┬──────────┐
  │               │ total (ms) │ average (ns) │ diff (%) │
  ├───────────────┼────────────┼──────────────┼──────────┤
  │ laissez-faire │          4 │       71,567 │        - │
  │ when          │          5 │       96,487 │       35 │
  │ then.promise  │         11 │      211,406 │      195 │
  │ rsvp          │         18 │      368,980 │      416 │
  │ deferred      │         19 │      382,560 │      435 │
  │ avow          │         23 │      463,866 │      548 │
  │ micro-promise │         42 │      839,174 │    1,073 │
  │ q             │      1,046 │   20,929,492 │   29,144 │
  └───────────────┴────────────┴──────────────┴──────────┘

  pending x50
  ┌───────────────┬────────────┬──────────────┬──────────┐
  │               │ total (ms) │ average (ns) │ diff (%) │
  ├───────────────┼────────────┼──────────────┼──────────┤
  │ laissez-faire │          0 │        2,009 │        - │
  │ then.promise  │          0 │        6,157 │      207 │
  │ when          │          0 │        8,465 │      321 │
  │ avow          │          0 │        8,536 │      325 │
  │ rsvp          │          1 │       12,898 │      542 │
  │ deferred      │          1 │       20,508 │      921 │
  │ micro-promise │          1 │       28,069 │    1,297 │
  │ q             │         22 │      445,010 │   22,053 │
  └───────────────┴────────────┴──────────────┴──────────┘

  reject x50
  ┌───────────────┬────────────┬──────────────┬──────────┐
  │               │ total (ms) │ average (ns) │ diff (%) │
  ├───────────────┼────────────┼──────────────┼──────────┤
  │ laissez-faire │          1 │       22,888 │        - │
  │ micro-promise │          1 │       24,174 │        6 │
  │ deferred      │          2 │       30,424 │       33 │
  │ then.promise  │          2 │       31,622 │       38 │
  │ rsvp          │          2 │       47,550 │      108 │
  │ when          │          3 │       55,588 │      143 │
  │ q             │          7 │      144,399 │      531 │
  │ avow          │         15 │      301,711 │    1,218 │
  └───────────────┴────────────┴──────────────┴──────────┘

  rejected x50
  ┌───────────────┬────────────┬──────────────┬──────────┐
  │               │ total (ms) │ average (ns) │ diff (%) │
  ├───────────────┼────────────┼──────────────┼──────────┤
  │ laissez-faire │          0 │        2,821 │        - │
  │ deferred      │          0 │        5,076 │       80 │
  │ q             │          0 │        6,745 │      139 │
  │ then.promise  │          1 │       10,869 │      285 │
  │ rsvp          │          1 │       18,310 │      549 │
  │ when          │          1 │       23,738 │      741 │
  │ avow          │          1 │       25,491 │      804 │
  │ micro-promise │          2 │       31,453 │    1,015 │
  └───────────────┴────────────┴──────────────┴──────────┘

  resolve-sequence x50
  ┌───────────────┬────────────┬──────────────┬──────────┐
  │               │ total (ms) │ average (ns) │ diff (%) │
  ├───────────────┼────────────┼──────────────┼──────────┤
  │ laissez-faire │          6 │      116,708 │        - │
  │ micro-promise │          9 │      170,376 │       46 │
  │ then.promise  │          9 │      172,152 │       48 │
  │ when          │         13 │      255,559 │      119 │
  │ rsvp          │         31 │      626,100 │      436 │
  │ deferred      │         34 │      680,855 │      483 │
  │ avow          │         59 │    1,185,774 │      916 │
  │ q             │        141 │    2,824,616 │    2,320 │
  └───────────────┴────────────┴──────────────┴──────────┘

  resolved-sequence x50
  ┌───────────────┬────────────┬──────────────┬──────────┐
  │               │ total (ms) │ average (ns) │ diff (%) │
  ├───────────────┼────────────┼──────────────┼──────────┤
  │ laissez-faire │          6 │      126,023 │        - │
  │ deferred      │         10 │      199,373 │       58 │
  │ when          │         12 │      241,293 │       91 │
  │ then.promise  │         18 │      350,516 │      178 │
  │ rsvp          │         49 │      983,637 │      681 │
  │ avow          │         63 │    1,254,229 │      895 │
  │ micro-promise │         67 │    1,336,711 │      961 │
  │ q             │      1,313 │   26,260,639 │   20,738 │
  └───────────────┴────────────┴──────────────┴──────────┘
```

