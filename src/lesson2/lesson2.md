# Introduction to Software Testing

**Software testing** checks whether a program implementation agrees with a
program specification. Without a specification, there is nothing to test.
Testing is a form of consistency checking between implementation and
specification.

## Automated vs manual testing

* **Automated testing**:
  * Finds bugs more quickly
  * No need to write tests
  * if software changes, no need to maintain tests
* **Manual testing**:
  * Efficient test suites
  * Potentially better code coverage

## Black-box vs white-box testing

* **Black-box testing**:
  * Can work with code that cannot be modified
  * Does not need to analyze or study code
  * Code can be in any format
* **White-box testing**:
  * Efficient test suite
  * Better code coverage

## Pre- and post-conditions

* pre-condition - a predicate assumed to be true before a function executes
* post-condition - a predicate expected to be true after a function executes,
whenever a pre-condition holds

Pre and post-conditions are most useful if they are executable and written in
the same programming language as the program under test. These conditions are
a special case of *assertions*. Pre and post-conditions don't have to be
precise, otherwise they might become more complex than the program under test.

## Using pre and post-conditions

Summarily, we check to see if a pre-condition holds prior to executing a test
If not, the test fails. Next we execute the function with the inputs defined by
the pre-condition, checking to see if the post-condition is true based upon the
output of the function. If we detect deviation from the post-condition, the test
fails. Else, the test succeeds and we move onto the next test case.

## Code coverage

**Code coverage** is a metric to quantify the extent to which a program's code
is tested by a given test suite. **Code coverage** is quantified as a percentage
of some aspect of the program executed in the tests. Some of the most popular
types of code coverage can be found below:

* Function coverage - what percentage of functions were called?
* Statement coverage - what percentage of statements were executed?
* Branch coverage - what percentaged of branches were taken?

## Mutation analysis

**Mutation analysis** is founded on the *"competent programmer assumption"* -
the program is close to right to begin with and the existing bugs are minor. The
key idea behind mutation analysis is to test mutants of the program to determine
if the test suite is good. If the test suite is good, mutants should fail the
tests because their code is incorrectly mutated. If a test suite is unsound, it
will accept mutants - we must continue to add test cases to the test suite to
distinguish mutants from the original program.