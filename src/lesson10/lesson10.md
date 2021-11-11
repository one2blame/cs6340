# Delta Debugging

**Delta debugging** is a technique used to determine the source of a particular
software bug. It follows the scientific method: hypothesize, experiment, and
refine. With **delta debugging**, we continually reduce the input that produces
a bug until we reach a minimum amount of input required to trigger the bug.

## Simplification

We simplify our testing input to generate bugs for the following reasons:

* Ease of communication
* Easier debugging
* Identification of duplicates

## Simplification techniques

The first technique discussed in this lecture is the use of a **binary search**
to remove input until the crash-producing input is identified.

## Delta debugging algorithm

Delta debugging is similar to a binary search, however, when it fails to find a
bug it increases the granularity of the **changes** or **deltas** that it tests.
**Deltas** are blocks of input being used to test a target program. The delta
debugging algorithm continues to try different subsets of deltas until it
encounters a bug. The bug producing input is retained and the deltas increase
in granularity until no bugs can be triggered.