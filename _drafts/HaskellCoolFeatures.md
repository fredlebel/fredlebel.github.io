---
layout: post
title: "A case for Haskell"
date: 2014-08-28 21:00:00
categories: haskell programming
---

I've been using Haskell for a couple of years now, writing small programs (web crawler, SKI/lambda reducer, one-time pad crypt tool) and some slightly larger ones (terminal games).  I feel the need to make a case for the use of Haskell over other general purpose languages.

My Background
-------------

I'm a software architect for an IT company.  While the definition of architect differs across the industry, in my case it involves putting together the initial architecture of a software project so that it can be carried to completion and supported by a team of developers.  Some things I consider while putting together an architecture are:

1. Some of the developers working on the project will be junior so the architecture should accommodate them by providing safe sandboxed areas that don't require extensive knowledge of either the programming language or the architecture as a whole.  They should be able to contribute quickly and not have to ramp up for weeks.
1. Similarly, team members will come and go and proper knowledge transfers are not always possible.  The architecture should provide a smooth slope for ramping up.
1. Requirements always change and new features are periodically added.  The architecture and the choice of tools should facilitate refactoring.
1. All developers make mistakes, and all are tempted to cut corners and introduce hacks when deadlines are near.  The architecture should be resistant to both mistakes and intentional hacks that circumvent the original design.

With these points in mind I'll go over some of the best features of Haskell and see how they address my concerns as an architect.

Static Typing
-------------

I consider static typing, where the compiler checks that types line up and function parameters match the function's signature, the most important feature in a language.  Static typing catches a large range of mistakes that programmers make on a regular basis.  These mistakes are caught at compile time while the same mistakes in a dynamically typed language only happen at runtime, and not always in front of the programmer.

Besides catching coding errors, a type checker is a great boon when it comes to refactoring.  When I refactor a section of code, I try to make the types of changes that will cause compilation errors where additional changes have to be made.  Examples of those changes include changing the types of variables, adding parameters to functions and renaming functions.

Haskell's type system is very powerful.  It supports rank-N parametric polymorphism, type level functions and other features rarely seen in other modern programming languages.  It also features a powerful type inference algorithm meaning that type signatures are rarely required.

Purity
------

Functional purity effectively means that all functions behave like little sandboxes.  All variables in Haskell are constant *(read only)* so the return values of functions only depend on their parameters.  Calling a function can't have any side effects, it can't modify a global variable and can't do file or network access.  It's harder to get caught by the unexpected behavior of a function if it can't reach out an modify other sections of the program.

```haskell
-- TODO: Example of a purity
```

Purity makes reading and reasoning about the code a lot easier.  I can't stress enough the usefulness of working with little sandboxes.  Purity and static typing are Haskell's top strengths.

Monads
------

Monads provide the ability to write code in an imperative fashion which tends to be easier for most programmers.  Better yet, monads act as a sandbox that can hide state data behind a base API.  This is somewhat analogous to hiding state inside a class in C++/C#.

The powerful difference from classes is that, while ___in___ a monad, the available API is restricted to that of the monad's.  This means that as an architect, I can entirely control everything that can be done in a given portion of the program, from the hidden state data to failure handling to the set of base monadic functions that can be used.  While in a monad, programmers can create additional monadic functions but those can only be composed of base API calls or other functions using of them.

In such an air-tight environment, it's difficult for a programmer to introduce a quick hack that circumvents the original design of the monad.  It's also reassuring for someone new to a project that they can't accidentally go in a wrong direction and break the intended design.

```haskell
-- TODO: Example of a monad
```

STM
---

Software transactional memory (STM) is a complete replacement to the locks, semaphores and condition variables used for multi-threading in imperative languages.  It provides atomicity and isolation through its transactions.

STM has great benefits over locks and condition variables.  Since it doesn't use locks, a program can't deadlock and doesn't have the kinds bugs caused by forgetting to take a lock *(some of the hardest bugs to track down)*.

It's important to mention that this is only possible because Haskell functions are pure.  Otherwise there would need to be a complex system in place to prevent side effects or simply rely on the programmer to not do anything which can't be undone in a transaction.

Quasiquotations
---------------

It's hard to claim that a particular general purpose language (GPL) like Haskell, C++, C#, Java or Javascript is all you need.  Domain specific languages (DSL) are always going to have an advantage over GPLs when it comes to their domain. For example, database queries are easier to write in SQL than in Haskell and mostly gone are the days where user interfaces were written in code.

Here are some examples of DSLs:

1. SQL for database queries
1. HTML or XAML for defining user interfaces
1. CSS for styling

Fortunately in Haskell if you have a parser for a particular DSL that converts from an input string to an internal data structure then it's fairly easy to support the splicing of that DSL within the Haskell code.  The DSL is then parsed, compiled and type checked when the source code is compiled.  Any additional semantic error checking done in the parser can even be made to emit a useful compiler error message.

Here's an example of an SQL query contained in SQL code.

```haskell
-- Define a function that creates an SQL query.
let findUserQuery name = [sql|SELECT * FROM Users WHERE Name = '{name}'|]

queryResult <- runSql (findUserQuery "Bob")
```

In fact it's so easy in Haskell to support DSLs in the source files that it becomes feasible for architects and senior developers to introduce small targeted DSLs throughout their architecture as a means to simplify the syntax and enforce specific invariants at compile time.

Conclusion
----------

With its static typing, functional purity and support for quasi-quotations, Haskell stands out as a powerful language upon which to build a stable architecture.