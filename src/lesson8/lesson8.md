# Type Systems

Type systems are usually specified as part of the programming language and are
usually built into compilers or interpreters fro the language. The main purpose
of type systems is to reduce the possibility of software bugs by checking for
logic errors.

A common type of error detected using type systems analysis is applying
operations to operands, e.g. adding an integer to a string in a Java program.
Type systems catch these error using a collection of rules, assigning types to
different parts of the program, i.e.:

* Variables
* Expressions
* Functions

## What is a type?

A **type** is a **set of values**. For example, in Java:

* `int` is the set of all integers between `-2^31` and `(2^31)-1`
* `double` is the set of all double-precision floating point numbers
* `boolean` is the set {`true`, `false`}

Some more examples related to object-oriented types and functions are as
follows:

* `Foo` is the set of all objects of class `Foo`
* `List<Integer>` is the set of all `Lists` of `Integer` objects
  * `List` is a **type constructor**
  * `List` acts as a function from types to types
* `int -> int` is the set of functions taking an `int` as a parameter and
returning another `int`

## Abstraction

All static analyses use **abstraction**, representing sets of concrete values
as abstract values.

### Why?

Without abstraction, we can't directly reason about infinite sets of concrete
values - this would not guarantee **termination**. **Abstraction** improves
performance even in the case of large, finite sets. In type systems,
abstractions are called **types**.

## Notation for inference rules

Inference rules have the following form:

* If **hypothesis** is true, then **conclusion** is true.

Type checking computes via reasoning:

* If **e1** is an **int** and **e2** is a **double**, then **e1 * e2** is a
**double**

The above statement in translated from English to a type analysis
**inference rule** is as follows:

* `(e1 : int ^ e2 : double) => e1 * e2 : int`

## Soundness

**Soundness** is extremely useful, program type checks lead to no errors at
runtime and **verifies** the absence of a class of errors relating to bad
typing. A **sound** type systems analysis provides a strong guarantee, verifying
the property of soundness holds across all executions:
*"Well-typed programs cannot go wrong."*

**Soundness** comes at a price and can lead to **false positives**.

## Global analysis

Also called **top-down analysis**, **global analysis** requires the entire
program to be inspected, constructing a **model** of the environment, required
to analyze sub-expressions.

## Local analysis

Also called **bottom-up analysis**, **local analysis** infers multiple
environments from each expression analyzed, combining its inferences at the end
of the analysis.

## Global vs local analysis

The following is a comparison of global vs local analysis:

* **Global analysis**:
  * Usually technically simpler than local analysis
  * May need extra work to model environments for unfinished programs
* **Local analysis**:
  * More flexible in application
  * **Technically harder**: Need to allow unknown parameters, more side
conditions

## Flow insensitivity

**Type systems** are generally flow-insensitive meaning their analysis is
independent of the ordering of sub-expressions - analysis results are
unaffected by permuting statements.

This attribute of **type systems analysis** is helpful for the following
reasons:

* No need for modeling a separate state for each sub-expression
* Flow insensitive analyses are often very efficient and scalable

This is nice but, due to these attributes, type systems analysis can also be
**imprecise**.

## Flow sensitivity

**Dataflow analysis** in contrast is an example of flow-sensitive analysis. As
rules produce new environments, the analysis of a sub-expression cannot take
place until its environment is available. The analysis results in this case are
dependent on the **order of statements**.

## Path sensitivity

For **path sensitive** analysis using a **type systems analysis**, part of the
environment now contains a **predicate** declaring under what conditions an
expression is executed. Each sub-expression has different predicates defined
at different decision points.

At points where control paths merge, different paths will still be denoted
in the output results / final environment.

**Symbolic execution** is an example of a path-sensitive analysis.
Path-sensitive analyses can be expensive, as there are an exponential number of
paths to track. This program analyses are often implemented with backtracking,
i.e. the exploration of one path until it ends and then starting another.