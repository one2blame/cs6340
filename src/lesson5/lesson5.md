# Dataflow Analysis

## What is dataflow analysis?

* Static analysis reasoning about the flow of data within a program
* Different kinds of data can be tracked with dataflow analysis:
  * Constants
  * Variables
  * Expressions
* Dataflow analysis is used by bug-finding tools and compilers

## Soundness, completeness, and termination

It is impossible for a software analysis to achieve all three of these
objectives - dataflow analysis sacrifices **completeness**. Dataflow analysis is
sound in that it will report all facts that could occur in actual runs. Dataflow
analysis is incomplete in that it may report additional facts that can't occur
in actual runs.

## Abstracting control-flow conditions

Dataflow analysis abstracts away control-flow conditions that contain
non-deterministic choices - it assumes that some conditions are able to evaluate
to true or false. It considers all paths possible in actual runs (sound) and
maybe paths that are never possible during (incomplete).

## Applications of dataflow analysis

* Reaching definitions analysis
  * This software analysis application using dataflow analysis is aimed at
finding the usage of uninitialized variables in the program
* Very busy expressions analysis
  * This software analysis application using dataflow analysis is aimed at
reducing code size
* Available expressions analysis
  * This software analysis application using dataflow analysis is aimed at
avoiding recomputing expressions - useful for compiler design
* Live variables analysis
  * This software analysis application using dataflow analysis is aimed at
allocating registers efficiently for data storage - useful for compiler design

## Reaching definitions analysis

The goal of **reaching definitions analysis** is to determine, for each program
point, which assignments have been made and not overwritten when execution
reaches that point along some path

### Does the reaching definitions analysis always terminate?

Yes, the **chaotic iteration algorithm** used by RDA *always* terminates. This
is because:

* The two operations of RDA are *monotonic* - meaning the **IN** and **OUT**
sets never shrink, only grow
* Largest the **IN** and **OUT** sets can be are all definitions in the program
 - they cannot grow forever
* **IN** and **OUT** will stop changing after some iteration

## Very busy expressions analysis

The goal of **very busy expressions analysis** is to identify very busy
expression at the exit from the point. An expression is very busy if, no matter
what path is taken, the expression is used before any of the variables used in
it are redefined.

## Available expression analysis

The goal of **available expressions analysis** is to identify, for each program
point, which expressions must already have been computed, and not later
modified, on all paths to the program point.

## Live variables analysis

The goal of **live variables analysis** is to identify, for each program point,
which variables could be *live* at the point's exit. A variables is *live* if
there is a path to a use of the variable that doesn't redefine the variable.