# Introduction to Software Analysis

## What is program analysis?

**Program Analysis** is a body of work to automatically discover useful facts
about programs and can be classified into three kinds of analysis:

* Dynamic (run-time)
* Static (compile-time)
* Hybrid (combination of dynamic and static)

### Dynamic analysis

**Dynamic program analysis** infers facts about a program by monitoring its
runs (executions). Some of examples of well known dynamic analysis tools are as
follows:

* Array bounds checking - Purify
* Data race detection - Eraser
* Memory leak detection - Valgrind
* Finding likely invariants - Daikon
  * An **invariant** is a program fact that is true for every run of the program

### Static analysis

**Static program analysis** infers facts about of a program by inspecting its
code. This can be done on both source code and compiled binaries. Some of
examples of well known static analysis tools are as follows:

* Suspicious error patters - Lint, FindBugs, Coverity
* Memory leak detection - Facebook Infer
* Checking API usage rules - Microsoft SLAM
* Verifying invariants - ESC / Java

## Discovering invariants using dynamic analysis

Dynamic analysis is useful for detecting likely invariants, but has difficulty
following programs that has loops or recursion - this can lead to arbitrarily
many paths. Dynamic analysis tools like Daikon, while being unable to execute
all arbitrary paths of a binary dynamically, can rule out entire classes of
invariants by observing a run of a program.

## Discovering invariants using static analysis

Static analysis and inspection of the source code of a program enables us with
the ability to *definitively* identify invariants.

## Terminology

* Control flow graph - a graphical representation of each statement within a
program, with nodes being instructions and edges being possible execution paths
between instructions.
* Abstract state - an abstract state represents variables with a symbolic value
* Concrete state - a concrete state assigns values to variables
* Termination - the resolution of all paths within a program
* Completeness - a program analysis that accepts correct programs and rejects
incorrect programs. An *incomplete* program analysis rejects correct programs
* Soundness - a sound analysis accepts bug-free programs and rejects buggy
programs. An *unsound* program analysis accepts buggy programs.

## Iterative approximation

**Iterative approximation** is a static analysis technique that iterates over
the paths of a program, constantly updating inferred facts until it arrives at
a complete understanding of the necessary values for each variable to reach a
desired program path.

## Dynamic vs static analysis

The following are statements comparing to the *cost* and *effectiveness* of
dynamic and static program analysis:

* Dynamic
  * Cost - Proportional of program's execution time
  * Effectiveness - Unsound (may miss errors)
* Static
  * Cost - Proportional to program's size
  * Effectiveness - Incomplete (may report spurious errors)

### Advantage of static analysis over dynamic analysis:

**Static analysis may achieve soundness** - it is possible to design an analysis
that does not miss an error in a program, even if some of errors reported may be
false positives. Since static analysis does not require the program to be run,
the cost to analyze a piece of software is proportional to its code size, not
it’s runtime. Since static analysis does not require the program to be run, it
can be performed on a machine without requiring that machine to be able to run
the code.

### Advantage of dynamic analysis over static analysis:

**Dynamic analysis may achieve completeness** - it is possible to design an
analysis that will only report errors that can be triggered in a concrete
execution of the program, even if some errors may be undiscovered due to
limitations on the number of runs inspected. Since dynamic analysis is performed
by observing a concrete run of a program, bugs found using this method are
typically easy to report / reproduce.

## Undecidability of program properties

Questions like *"Is a program point reachable on some input?"* are
**undecidable** meaning, we would need an infinite amount of time to conduct the
program analysis to discover the answer to the proposed question. Program
analysis can be sound and complete, but only if we never terminate. Thus,
designing a program analysis is an art in which we balance how thorough the
analysis is - the tradeoffs are dictated by the consumer of the analysis.

## Consumers of program analysis

* Compilers - compilers utilize program analysis to optimize code, removing
unnecessary code that results in the same outcome for an invariant.
* Software quality tools - tools for testing, debugging, and verification.
Software quality tools are used for:
  * Finding programming errors
  * Proving program invariants
  * Generating test cases
  * Localizing causes of errors
* Integrated development environments (IDEs) - IDEs use program analysis to help
programmers:
  * understand programs
  * refactor programs