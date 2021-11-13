# Dynamic Symbolic Execution

**Dynamic symbolic execution** is a hybrid approach to software analysis,
combining dynamic and static analysis. While dynamic symbolic execution can
produce false negatives, it does not produce false positives - all of its
assertions are real. Dynamic symbolic execution provides guided inputs to a
program in an attempt to uncover all inputs to traverse all paths of execution.

## Motivation

The motivation behind dynamic symbolic execution is as follows:

* Provide **automated test generation**
* Generate a regression test suite
* Execute all reachable statements
* Catch any **assertion violations**

## Approach

Dynamic symbolic execution's approach to software analysis is as follows:

* Stores program state **concretely** and **symbolically**
* Solves **constraints** to guide execution at branch points
* Explores **all execution paths** of the unit tested

## Execution paths of a program

We can envision the execution paths of a program as a binary tree with possibly
infinite depth - a **computation tree**. Each node of the computation tree
represents the execution of a conditional statement, and each edge represents
the execution of a sequence of non-conditional statements.

Each path in the tree represents an equivalence class of inputs. The point of
dynamic symbolic execution is to discover non-equivalent inputs to uncover
new tree branches and paths.

## Symbolic execution

Given a series of branches and their condition statements, instead of
brute-forcing the correct value to satisfy a condition statement to take a
branch, **symbolic execution** leverages **theorem provers** to determine values
that satisfy symbolic constraints.

With this, we avoid using concrete values for inputs, opting for symbols, and
we execute the program symbolically to resolve input values for program paths.
One problem exists, however. Due to the number of possible branch conditions
within a program, symbolic execution is susceptible to **path explosion**.

## Combined approach

**Dynamic symbolic execution** or **concolic execution** is our hybrid approach
because it leverages concrete values during its analysis, while also conducting
symbolic analysis of a specific program under test. **Concolic exeuction**
starts with random input values, keeping track of both concrete values and
symbolic constraints. To assist in proving that a branch can be taken
symbolically, a dynamic symbolic execution engine will proceed to simplify its
symbolic constraints with concrete values. This helps to avoid issues like
**path explosion**, however, this also requires the engine to use an
**incomplete** theorem prover.

## Characteristics of DSE

The following are characteristics of dynamic symbolic execution:

* Automated, white-box
* Systematic input searching
* Path-sensitive
* Non-sampled instrumentation