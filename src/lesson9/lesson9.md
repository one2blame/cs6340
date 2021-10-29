# Statistical Debugging

## Introduction to statistical debugging

In layman terms, **statistical debugging** is the process of acquiring bug
statistics from dynamic runs of a particular piece of code from remote clients.
So, if an error occurs, a user can provide the development team backtraces,
crash dumps, etc. via the internet to a central debug server for the team's
review. Acquisition of debug information allows teams to narrow down where a
particular bug exists in the code.

## Statistical debugging motivations

Despite our best efforts, bug will escape developer testing and analysis tools,
and these bugs will eventually be introduced into production. This is due to the
following reasons:

* Dynamic analysis is unsound, and will inevitably not find all existing bugs.
* Static analysis is incomplete, and may report false bugs.
* Software development has limited resource, e.g. time, money, and people

Due the reasons above, sometimes our software ships with unknown, and sometimes
known, bugs.

## Benefits of statistical debugging

Actual runs of code a great resource for debugging for the following reasons:

* Crowdsource-based testing
  * In this case the number of real runs is greater than the number of testing
runs.
* Reality-directed debugging
  * Real-world runs are the ones that matter most

## Practical challenges

The following are some practical challenges that face statistical debugging:

1. Complex systems
  * Millions of lines of code are deployed by developers
  * This code usually contains a mix of controlled and uncontrolled code
2. Remote monitoring constraints
  * On remote devices, we will have limited disk space, network bandwidth, and
power to send bug reports
3. Incomplete information
  * We must limit performance overhead on deployed applications for statistical
debugging
  * We must ensure to respect the privacy and security of our users

## The approach

Our approach to statistical debugging follows three steps:

1. Guess the behaviors that are "potentially interesting"
  * To do this, we'll start with a compile-time instrumentation of the program,
allowing us to tag and identify any interesting behaviors
2. Collect sparse, fair subset of these behaviors
  * Here, developers create a generic sampling framework
  * This framework encompasses a feedback profile and outcome labels (success
vs. failure) for each run
3. Finally, developers analyze behavioral changes in successful vs. failing runs
to find bugs
  * This step is what gives this process the name **statistical debugging**

## A model of behavior

To begin a breakdown of "potentially interesting" behaviors, we can follow these
steps:

* Assume any interesting behavior is expressible as a predicate **P** on a
program state at a particular program point:

  * **Observation of behavior = observing P**

* Instrument the program to observe each predicate
* Which predicates should we observe?

## Branches are interesting

In the lecture an example is given for a conditional branch where two predicates
are identified:

* `p == 0`
* `p != 0`

Through instrumentation of this particular branch, **cells** are maintained that
increment each time a specific predicate is executed for this instrumented
branch.

## Return values are interesting

Similar to the technique above for conditional branches, we instrument function
calls and their return types, tracking different predicates for each possible
return value - incrementing the cell count for each occurrence of a particular
predicate. In our example for `fopen`, we track the following predicates:

* `n < 0`
* `n > 0`
* `n == 0`

## What other behaviors are interesting?

The entirely depends on the problems you need to solve, some examples are:

* Number of times each loop runs
* Scalar relationships between variables, e.g. `i < j`, `i > 42`
* Pointer relationships, e.g. `p == q, p != nullptr`

## Summarization and reporting

At the end of a particular analysis, we summarize the results for each predicate
and report them. This involves conglomerating each array of counts for
predicates per branch instruction, e.g.:

* `branch_17`
  * `p == 0`
  * `p != 0`
* `call_41`
  * `n < 0`
  * `n > 0`
  * `n == 0`

Becomes:

* `p == 0`
* `p != 0`
* `n < 0`
* `n > 0`
* `n == 0`

After the summarization and reporting of our run, we utilize the provided
feedback to determine the **outcome**: "Did we **succeed** or **fail**? We also
label each predicate with a series of states and in this example, we'll provide
the following states:

* `-` - This denotes that a particular predicate was not observed, neither true
nor false
* `0` - This denotes that a particular predicates was observed to be `false` at
least once and **never** observed to be `true`
* `1` - This denotes that a particular predicate was observed to be `true` at
least once and **never** observed to be `false`
* `*` - This denotes that a particular predicate was observed at least once to
be `true` and at least once to be `false`

In the examples provided in the lectures videos, **succeed** or **fail** is
determined by whether or not our **assert** call is `0` or `1`.

## The need for sampling

As is discussed, tracking all predicates within a program can be computationally
expensive. From all of the interesting predicates within a program, we need to
decide which predicates we'll examine and which we'll ignore. With this in mind,
we use the following principles for sampling:

* **Randomly** - sample random predicates
* **Independently** - sample random predicates independent of the sampling of
other predicates
* **Dynamically** - sample random predicates, independently, determined at
runtime

Why do we do all of the above? To institute **fairness** and acquire an accurate
picture of **rare events**.

## Feedback reports with sampling

Feedback reports per run is a vector of **sampled** predicate states:

* (`-`, `0`, `1`, `*`)

And a **success** or **failure** outcome label. With sampling, we can be certain
of what we did observe *but we may miss some events*. Given enough runs of the
instrumented program, our **samples** begin to approach **reality**. Common
events are seen most often in the feedback, and rare events are seen at a
proportionate rate.

## Finding causes of bugs

How likely is failure when predicate **P** is observed to be true?

* `F(P)` = number of failing runs where **P** is observed to be true
* `S(P)` = number of successful runs where **P** is observed to be true
* `Failure(P) = F(P) / (F(P) + S(P))`
* Example:
  * `F(P) = 20`
  * `S(P) = 30`
  * `Failure(P) = 20 / 50 = 0.4 probability of failing when P is true`

## Tracking context and increase

In this slide we introduce **context**, determining the background chance of
failure, regardless of **P**'s value. The following are definitions in the
**context** equation:

* `F(P observed)` = number of failing runs observing **P**
* `S(P observed)` = number of successful runs observing **P**
* `Context(P) = F(P observed) / (F(P observed) + S(P observed))`
* Example: `F(P observed) = 40, S(P observed) = 80`
  * `Context(P) = 40 / 120 = 0.333...`

With a combination of **failure** and **context**, we can calculate the
**increase** metric, a true measurement that can correlate a predicate to
failing runs. The equation for **increase** is as follows:

* `Increase(P) = Failure(P) - Context(P)`

**Increase** is ratio that can determine the likelihood of failure with a
predicate **P**, i.e.:

* `Increase(P) approaches 1` - high correlation with failing runs
* `Increase(P) approaches -1` - high correlation with successful runs

## A first algorithm

The following algorithm is used to filter and determine predicates that result
in a crash. This algorithm is comprised of two steps:

1. Discard predicates having `Increase(P) <= 0`
  * e.g. bystander predicates, predicates correlated with success
  * Exact value is sensitive to few observations
  * Use lower bound of 95% confidence interval
    * Basically filtering out predicates that failed but did not appear often
2. Sort remaining predicates by `Increase(P)`
  * Again, use 95% lower bound
  * Likely causes with determinacy metrics

## Revised algorithm

A revised algorithm used to determine predicates that result in a crash are as
follows:

* Repeat the following steps until no runs are left:
  1. Compute **increase**, **F()**, etc. for all predicates
  2. Rank the predicates
  3. Add the top-ranked predicate **P** to the result list
  4. Remove **P** and discard all runs where **P** is true
    * This simulates fixing the bug corresponding to **P**
    * Discard reduces rank of correlated predicates

## Ranking

A couple of strategies exist for ranking, we'll discuss some here:

* Ranking by **increase** - at first glance, useful, however, this often results
in a high **increase** score but few failing runs. Not useful because of how
rare it is in relation to the number of runs.
  * These are known as **sub-bug predictors**, covering special cases of more
general bugs.
* Ranking by **failure** - the problem here is that there could be many failing
runs but low **increase** scores.
  * These are known as **super-bug predictors**, covering several different bugs
together.

## A helpful analogy

To better select bug predictors, we want to use the following concepts:

* **Precision** - fraction of retrieved instances that are relevant
* **Recall** - fraction of relevant instances that are retrieved
* **Retrieved instances** - predicates reported as bug predictors
* **Relevant instances** - predicates that are actual bug predictors

With regard to the described concepts above, we need to achieve both
**high precision** and **high recall**.

## Combining precision and recall

The **increase** metric has high precision and low recall, whereas **F()** has
high recall and low precision. A standard solution to combine these is to take
the **harmonic mean** of both metrics:

* `2 / (1/increase(P) + 1/F(P))`

This **harmonic mean** rewards high scores in both dimensions.