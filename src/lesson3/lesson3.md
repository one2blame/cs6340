# Introduction to Random Testing

## Random testing (fuzzing)

**Random testing** or **fuzzing** is the practice of feeding random inputs to a
program, observing whether or not it behaves *correctly*. During this
observation, we determine if the execution of the program satisfies the given
specification or if it doesn't crash from random inputs.

**Fuzzing** can be viewed as a special case of mutation analysis.

## Cuzz: fuzzing thread schedules

**Cuzz**, the Microsoft multi-threaded application fuzzer, introduces `sleep()`
calls automatically in order to suss our concurrency-related bugs in the program
under test. This makes concurrency testing less tedious and error-prone in
comparison to a human analysis, and the `sleep()` calls are introduced
systematically before each statement.

This method of fuzzing multi-threaded programs provides us with the worst-case
probabilistic guarantee on finding bugs.

## Depth of concurrency bugs

* **Bug depth** - the number of *ordering constraints* a thread schedule has to
satisfy to find the bug.
* **Ordering constraints** - the order in which statements have to execute to
introduce a bug.

From real-world studies using Cuzz, testers observed that many
concurrency-related bugs have a shallow bug depth.

## Probabilistic guarantee

Cuzz provides a **probabilistic guarantee** that a bug will be discovered in the
program using the following equation:

* **n** threads
* **k** steps
* **d** bug depth
* **probability** == `1 / (n * (k**(d-1)))`

## Measured vs worst-case probability

* The worst-case guarantee to find a concurrency-related bug with Cuzz is for
the *hardest-to-find* bug of a given depth. If multiple bugs exist, the
probability of finding a bug, in general, increases. As the number of threads
increases in the program, the odds of triggering a bug increases.

## Random testing: pros and cons

* **Pros**:
  * Easy to implement
  * Good coverage given enough tests
  * Works against programs of any format
  * Useful for finding security vulnerabilities

* **Cons**:
  * Inefficient test suites
  * Finds bugs that might be unimportant
  * Poor code coverage
