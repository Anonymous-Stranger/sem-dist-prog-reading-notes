# Notes on Linearizability: A Correctness Condition for Concurrent Objects

### Akash Gaonkar
##### 2019.23.01

source: http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf

$\newcommand{\event}[3]{\langle #1 \:\: #2 \:\: #3 \rangle}$
$\newcommand{\rep}{\mathtt{rep}}$
$\newcommand{\abs}{\mathtt{abs}}$
$\newcommand{\tbool}{\mathtt{BOOL}}$
$\newcommand{\trep}{\mathtt{REP}}$
$\newcommand{\tabs}{\mathtt{ABS}}$
$\newcommand{\lin}{\mathtt{Lin}}$

## Intuition

Consider a system of objects, each having a type, a set of
possible values, and a set of operations modifying its value.

**Sequential.** Given a set of computations, we call the system
*sequential* if a single processor executes each computation one after
the other (i.e. in sequence).

**Linearizable.** Given a set of computations we call the system
*linearizable* if a set of processors executes the computations in
parallel, such that simultaneous computations can be ordered in a way
that (1) maintains correctness and (2) could be also be produced through
a sequential execution.

**Correctness conditions.** To validate a linearizable execution, the authors
suggest the following conditions:
1. Each operation should appear to take place at a single timepoint.
2. If a computation A ends before a computation B starts, then the
   corresponding sequential execution should order A before B.


## Formal Definitions (for quick lookup)

**Defn 1. (Events)**: For an object $x$ executing operation $op$ with
arguments $args^*$ on process $A$, there will be an *invocation* event
$\event{x}{op(args^*)}{A}$. If the If the operation is not *pending* there
will be a *response* event $\event{x}{term(res^*)}{A}$, where $term$ is the
termination status (e.g. $Ok$) and $res^*$ are the results. We say this
response *matches* the invocation.

**Defn 2. (History):** A *history* is a finite sequence of operation
invocation and response events, used to model the execution of a
concurrent system. A *subhistory* of a history $H$ is a subsequence of
the events of $H$, and we define $complete(H)$ as the maximal subhistory
consisting only of invocations with matching responses.

**Defn 3. (Sequential History):** A history is *sequential* if (1) its
first event is an invocation, and (2) each invocation, except possibly the
last, is immediately followed by a matching response, and each response is
immediately followed by a matching invocation. If a history is not
sequential, we call it *concurrent*.

**>> Q: What does it mean for a response to be followed by a matching
invocation?**

**Defn 4. (Process and Object Subhistories):** Given a history $H$, a process
$A$, and an object $x$, the *process subhistory* of $H$ at $A$, $H \mid A$ is
the subsequence of all events in $H$ whose process is $A$. Likewise, the
*object subhistory* of $H$ for $x$, $H \mid x$ is the subsequence of all
events whose object is $x$.

**Defn 5. (Equivalence Between Histories):** Two histories, $H, H'$ are
said to be equivalent if each process's history is unchanged. That is, for
every process $P$, $H \mid P = H' \mid P$.

**Defn 6. (Well-formed Histories):** A history $H$ is *well-formed* if all
process histories $H \mid P$ are sequential. We assume throughout the paper
that all histories are well-formed.

**Defn 7. (Operation):** An operation $e$ is a pair of an invocation $inv(e)$
and its matching response $res(e)$. It is denoted
$[q\:\:op(args^*)/term(res^*)\:\:A].$

**Defn 8. (Sequential Specification):** A *sequential specification* $S$ for
an object $x$ is a set of sequential single-object histories such that if $H \in S$, then for any prefix $H'$ of $H$, $H' \in S$. A sequential history
$H$ is *legal* if for each object $x$ and its specification $S_x$,
$(H \mid x) \in S_x$.

**Defn 9. (Returns Before):** A history $H$ has a partial order $<_H$ on
operations, $e_0 <_H e_1$ if $res(e_0)$ precedes $inv(e_1)$. Operations
unrelated by $<_H$ are said to be *concurrent*. If $H$ is sequential,
then $<_H$ is a total order.


**Defn 10. (Linearizable):** A history $H$ is *linearizable* if it can be
extended (by appending zero or more response events) to some history $H'$
such that:

  - **(L1):** $complete(H')$ is equivalent to some legal sequential
    history $S$.
  - **(L2):** $<_H \subseteq <_S$.

We call $S$ a *linearization* of $H$.


## Properties of Linearizability

**Thm 1. (Locality):** *A history $H$ is linearizable iff for each object
$x$, $H \mid x$ is linearizable.*

**Defn 11. (Total Operation):** We call an operation *total* if it is
defined for all possible values of the object. Otherwise we call it
*partial*.

**Thm 2. (Nonblocking):** *Let $inv$ be the invocation of a total operation.
If $\event{x}{inv}{P}$ is a pending invocation in a linearizable history $H$, then there exists some response $\event{x}{res}{P}$ such
that $H \cdot \event{x}{res}{P}$ is linearizable.*

Note that partial operations, such as a queue-dequeue (undefined for an
empty queue) can still block.

## Comparison to Other Notions of Consistency

| Property  | Linearizability | Sequential Consistency | Serializability |
|-----------|:---------------:|:----------------------:|:---------------:|
|Equivalent Legal Sequential History                           | X | X | X |
|$<_H \subseteq <_S$ (Happens Before)                          | X |   | X |
|$\forall P \ldotp <_{H \mid P} \subseteq <_S$ (Read My Writes)| X | X | X |
|Locality                                                      | X |   |   |
|Nonblocking (total operations)                                | X | X |   |
|Multi-operation transactions                                  |   |   | X |


## Verifying Linearizability

**Defn 12. (Implementation)** An *implementation* is a set of histories in
which the events of two objects, a *representation* object $\rep$ and an
*abstract* object $\abs$, such that for any history $H$ and process $P$ in
the implementation, for any $e_\rep$ in $H \mid P \mid \rep$ there is some
$e_\abs$ in $H \mid P \mid \abs$ such that $inv(e_\abs)$ precedes $inv(e_\rep)$ and $res(e_\rep)$ precedes $res(e_\abs)$.

**Defn 13. (Correctness)** An implementation is said to be *correct* with
respect to a specification for $\abs$ if for every history $H$ in the
implementation, $H \mid \abs$ is linearizable.


In the sequential case, we verify correctness using two functions:
 - the *rep. invariant*, $I: \trep \to \tbool$, which tells us which
   values of $\trep$ are legal representations of $\tabs$.
 - the *abstraction function* $A: \trep \to \tabs$, which given a
   legal representation, converts to the abstract value.

Then we know that a sequence of $\rep$ operations $\rho$ is a correct
implementation of an abstract operation $\alpha$ if for any legal
representation $r: \trep \land I(r)$, executing $\rho$ at $r$ produces
the legal representation $r': \trep \land I(r')$ and executing $\alpha$
at $A(r)$ produces $A(r')$.

In order to extend to the concurrent case, we must make some changes:
1. Because multiple processes may attempt to access and modify the
   representation simultaneously, it is no longer sufficient for $\rho$
   to produce a legal representation upon completion---some other process
   may interrupt mid-change. Therefore, the new rep. invariant is such that
   given any operation $\rho_i$ in $\rho$, $I(r) \implies I(\rho_i(r))$.
2. Multiple processes simultaneously making changes also leads to multiple
   interpretations of the execution, any of which might be the one finally
   used to produce a sequentialization. Therefore, the abstraction function
   may need to keep its options open, rather than forcing a single value.
   The new abstraction function $A: \trep \to (2^\tabs \setminus \{\emptyset\})$ expresses this
   nondeterminism.

These two functions allow us to transition from consider histories to instead
considering execution values. Given a representation $\rep$ with a
linearizable history $H$, we can use define a function $\lin(H)$ that
computes the set of all possible values of the representation after
evaluating the history.

This allows us to use standard verification methods to show for an arbitrary linearizable history $H$:
\[\forall r \in \lin(H \mid \rep) \ldotp I(r) \land A(r) \subseteq \lin(H \mid \abs).\]

If this holds, then we know from $\lin(H \mid \abs)$ that $H \mid \abs$ is
linearizable. Of course, there may be ways to represent the abstract
object other than $\rep$, and those ways might have different linearizations
and produce different $A(r)$ s. This is why we don't need to prove going
from $\abs$ to $\rep$ --- that would be constricting the other
implementations. We need only show our implementation works.

## Thoughts

Linearizability allows us to get very close to sequentialization without
significantly blocking concurrency, and it especially allows us to reason
with greater modularity than other forms of consistency, due to locality.
This makes it a very powerful tool for various applications. However,
we should recall that linearizable cannot be guaranteed in systems that
must be both available and partition tolerant (by CAP), so we may not
always be able to achieve it.
