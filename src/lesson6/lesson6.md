# Pointer Analysis

In the previous lesson we learned about tracking the flow of primitive data
types like integers. In this lesson, we'll learn how to track and analyze more
complex types of data, namely pointers, objects, and references.

This sort of analysis is called **pointer analysis**.

## Introducing pointers

* **Field write** - an operation where a value is written to an attribute of
a class or structure via a pointer to that object or structure.
* **Field read** - an operation where a value is read from an attribute of a
class or structure via a pointer to that object or structure.

## Pointer aliasing

**Pointer aliasing** is a situation in which the same address in memory is
referred to in different ways.

## Approximation to the rescue

**Pointer analysis** is hard because there are so many different ways to
reference pointer values - the lectures provide the example of a linked list.
We must sacrifice some combination of **soundness**, **completeness**, and
**termination** in order to have a decidable pointer analysis. We sacrifice
completeness, causing us to have **false positives, but no false negatives**.

What does this mean? In the **May-Alias** case, a pointer may be aliased or it
may not. This creates a false positive because we'll have two determinations
about the outcome of a specific line of code - one of these will be a
**false positive**.

There are many sound, approximate algorithms for pointer analysis with varying
levels of precision. Most of these algorithms differ in two key aspects:

* How to abstract the **heap**
* How to abstract control-flow

## Abstracting the heap

**Points-to Graph** is an abstraction of allocations that occurs within the
program. It collapses arrays of allocated chunks into a single node, denoting
the existence or multiple chunks within a single array using an asterisk. This
abstraction is only concerned with creating a graph of relationships between
allocated objects, and creates directed edges based upon these allocations in
the source code.

## Kinds of statements

The following are the kinds of statements that we must track in order to
implement the pointer analysis chaotic iteration algorithm:

* **Object allocation** - `v = new ...`
* **Object copy statement** - `v = v2`
* **Field read** - `v2 = v.f`
* **Field write** - `v.f = v2`
* **Array-based field read** - `v2 = v[*]`
* **Array-based field write** - `v[*] = v2`

## Rule for object allocation sites

When an object is allocated, we use a weak re-assignment rule. Essentially, once
an object is allocated and assigned to a variable, a new node is created on the
graph and the variable points to the new node - if the variable didn't already
exist then it is created on the graph.

The weak re-assignment rule comes in when, if a variable already exists and
points to an object, we allow the variable to point to the new node recently
allocated, accumulating assignments.

## Rule for object copy

When an object is copied to a new variable, the new variable just copies all
existing edges to nodes of the copied variable. No existing nodes are
overwritten, just accumulated.

## Rule for field writes

In this example, if a variable points to Node A and another variable points to
Node B, when the field of the first variable is assigned to the second variable,
an edge is drawn between Node A and Node B. Thus, Node A's field is assigned
to be the value of the second variable, or Node B.

## Rules for field reads

In this example, we assigned a variable the value of another variable's field.
So if the second variable points to Node B, and Node B's field is assigned Node
C, then the first variable is assigned Node C (Node B's field).

## Classifying pointer analysis algorithms

The following are dimensions upon which pointer analysis algorithms are
classified:

* Is it flow-sensitive?
* Is it context-sensitive?
* What heap abstractions scheme is used?
* How are aggregate data types modeled?

## Flow sensitivity

**Flow sensitivity** describes how an algorithm models control-flow **within**
a procedure. There are two kinds of flow sensitivity:

* **Flow-insensitive** - weak updates on data, never killing any previously
generated facts.
  * Suffices for may-alias analysis
* **Flow-sensitive** - strong updates on data, kills previously generated facts
when encountering new data
  * Required for must-alias analysis

## Context sensitivity

**Context sensitivity** describes how an algorithm models control-flow
**across** procedures. There are two kinds of context sensitivity:

* **Context-insensitive** - analyze each procedure only once, regardless of how
many times the program encounters a procedure
* **Context-sensitive** - analyze each procedure possibly multiple times, once
per abstract calling context

## Heap abstraction

**Heap abstraction** is the scheme to partition unbounded sets of concrete
objects into finitely many **abstract objects** - represented by oval nodes
in our point-to graph representations.

**Heap abstraction** ensures that pointer analysis terminates. There are various
sound schemes, however, the following are determinations about precision and
efficiency for different schemes:

* Too few abstract objects results in **efficient** but **imprecise** analysis
* Too many abstract objects results in **expensive** but **precise** analysis

### Scheme 1: allocation-site based

This scheme of heap abstraction is the same one we covered during this lesson.
It tracks one abstract object per **allocation site**. Allocation sites are
identified by:

* the **new** keyword in Java/C++
* the **malloc()** (or equivalent) call in C

Because there are finitely many allocation sites in programs, this scheme
results in finitely many abstract objects.

### Scheme 2: type based

The allocation-site based scheme can be costly due to the following:

* The analysis of large programs
* Clients needing a quick turnaround time for analysis
* Overly fine granularity of sites unnecessary

In the **type based** scheme, on abstract object is tracker per **type** in the
program. This results in finitely many types and finitely many abstract objects.

### Scheme 3: heap-insensitive

The **heap-insensitive** scheme tracks a **single** abstract object representing
the entire heap. This is obviously imprecise, but has its uses for programming
languages like C where stack-directed pointers derive better pointer analysis.
This scheme is unsuitable for languages that make great usage of the heap like
Java.

## Modeling aggregate data types: records

Three choices exist for the modeling of **records**, otherwise known as structs
or objects:

1. **Field-insensitive** - merge **all** fields of each record object
  * This makes it difficult for an analysis to track reads and writes to
specific attributes of an object or structure
2. **Field-based** - merge **each** field of all record objects
  * This makes it difficult for an analysis to distinguish between fields of
different objects or structures
3. **Field-sensitive** - keep **each** field of **each** record separate