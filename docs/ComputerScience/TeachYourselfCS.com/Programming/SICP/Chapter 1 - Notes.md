# Building Abstractions with Procedures

The various kinds of expressions, each with its associated
evaluation rule, constitute the syntax of the programming
language.

## 1.1.4 Compound Procedures

Elements that must appear in any powerful programming language:

* Numbers and arithmetic operations are primitive data and procedures.
* Nesting of combinations provides a means of combining operations.
* Definitions that associate names with values provide a
limited means of abstraction.

*Procedure definition* is a much more powerful abstraction
technique by which a compound operation can be given a name
and then referred to as a unit.

We begin by examining how to express the idea of "squaring".
We might say, "to square something, multiply it by itself".
This is expressed in Schemee as:

```scheme
(define (square x) (* x x))
```

We have here a *compound procedure*, which has been given the name `square`.
The procedure **represents** the operation of multiplying something by itself.
Evaluating the definition creates this compound procedure
and associates it with the name `square`.

> A procedure **represents** an operation

In Lisp / Scheme, compound procedures are used in exactly
the same way as primitive procedures.

* primitive: (+ 5 7)
* compound: (sum 5 7)

## 1.1.5 The Subtitution Model for Procedure Application

For compound procedures, the application process is as follows:

* To apply a compound procedure to arguments, evaluate the body
of the procedure with each formal parameter replaced by the
corresponding argument.

The *substitution model* for procedure application is a model
that determines the "meaning" of procedure application.

Example of *substitution model*:

```scheme
(+ (square 6) (square 10))
```

If we use the definition of square, this reduces to

```scheme
(+ (* 6 6) (* 10 10))
```

which reduces by multiplication to

```scheme
(+ 36 100)
```

and finally to

136

* The purpose of the substitution is to help us think about
procedure application, not to provide a description of how
the interpreter works.

* The substitution model is a way to get started with thinking
formally about the evaluation process.
In general, when modeling phenomena is science and engineering,
we begin with simplified, incomplete models. As we examine
things in greater detail, these simplified models become
inadequate and must be replace by more refined models.

## 1.1.6 Conditional Expressions and Predicates

```scheme
(define (abs x)
  (cond ((> x 0) x)
        ((= x 0) 0)
        ((< x 0) (- x))))
```

The general form of a conditional expression consists of
the symbol `cond` followed by parenthesized pairs of
expressions called *clauses*. The first expression in each
pair is a *predicate* -- that is, an expression whose value
is interpreted as either true or false.

If none of the predicates is true, the value of the `cond` is undefined.

The word *predicate* is used for procedures that return true or false,
as well as for expressions that evaluate to true or false.

The special form `if` is a restricted type of conditional
that can be used when there are precisely two cases in the
case analysis. The general form of an `if` expression is

```scheme
(if <predicate> <consequent> <alternative>)
```

In addition to primitive predicates such as <, =, and >, there
are logical composition operations, which enable us to
construct compound predicates. The three most frequently used are these:

* (and <e1> .. <en>)

* (or <e1> .. <en>)

* (not <e>)

Notice that `and` and `or` are special forms, not procedures,
because the subexpressions are not necessarily all evaluated.
`Not` is an ordinary procedure.

## 1.1.7 Example: Square Roots by Newton's Method

Procedures, as introduced above, are much like mathematical
functions. They specify a value that is determined by one
or more parameters. But there is an important difference
between mathematical functions and computer procedures:
Procedures must be effective.

As a case in point, consider the problem of computing
square root.

√r = the y such that y >= 0 and y<sup>2</sup> = r

This describes a perfectly legitimate mathematical function.
We could use to recognize whether one number is the
square root of another; or to derive facts about
square roots in general. On the other hand, the definition
does not describe a procedure. Indeed, it tells us almost
nothing about how to actually find the the square root of
a given number.

The contrast between function and procedure is a reflection
of the general distinction between describing properties
of things and describing how to do things, or, as it is
sometimes referred to, the distinction between declarative
knowledge and imperative knowledge. In mathematics we are
usually concerned with declarative (what is) descriptions,
whereas in computer science we are usually concerned with
imperative (how to) descriptions.

How does one computer square roots? The most common way
is to use Newton's method of successive approximations,
which says that whenever we have a guess *y* for the value
of the square root of a number *x*, we can perform a simple
manipulation to get a better guess (one closer to the actual
square root) by averaging *y* with *x/y*. For example, we
can compute the square root of 2 as follows. Suppose our
initial guess is 1:

|Guess|Quotient|Average|
|:---:|:------:|:-----:|
|1|(2/1) = 2| ((2 + 1)/2) = 1.5|
|1.5|(2/1.5) = 1.3333| ((1.3333 + 1.5)/2)=1.4167|
|1.4167|(2/1.4167)= 1.4118|((1.4167 + 1.4118)/2) = 1.4142|
|...|...|...|

Continuing this process, we obtain better and better
approximations to the square root.

Now let's formalize the process in terms of procedures. We start
with a value for the radicand (the number whose values we are
trying to computer) and a value for the guess. If the guess
is good enough for our purposes, we are done; if not, we
must repeat the process with an improved guess. We write this
basic strategy as a procedure:

```scheme
(define (sqrt-iter guess x)
  (if (good-enough? guess x)
      guess
      (sqrt-iter (improve guess x)
                 x)))
```

A guess is improved by averaging it with the quotient of
the radicand and the old guess:

```scheme
(define (improve guess x)(average guess (/ x guess)))
```

where

```scheme
(define (average x y)(/ (+ x y) 2))
```

We also have to say what we mean by "good enough". The
following will do for illustration, but it i not really a
very good test. The idea is to improve the answer until it
is close enough so that its square differs from the
radicand by less than a predetermined tolerance (here 0.001):

```scheme
(define (good-enough? guess x)
  (< (abs (- (square guess) x)) 0.001))
```

Finally, we need a way to get started. For instance, we can
always guess that the square root of any number is 1:

```scheme
(define (sqrt x)
  (sqrt-iter 1.0 x))
```

If we type these definitions to the interpreter, we can use
`sqrt` just as we can use any procedure:

```scheme
(sqrt 9)
3.00009155413138
(sqrt (+ 100 37))
11.704699917758145
(sqrt (+ (sqrt 2) (sqrt 3)))
1.7739279023207892
```

`Sqrt-iter` demonstrates how iteration can be accomplished
using no special construct other than the ordinary ability
to call a procedure. (recursion)

## Exercise 1.6

Scheme uses applicative-order evaluation, so all procedures
have their arguments evaluated first. If an argument is a
recursion, an infinite loop will occur, because it will
keep expanding the recursive procedure.

The ordinary `if` need not evaluate its arguments first.
This is the different with the ordinary `if` and an `if` defined as a function.