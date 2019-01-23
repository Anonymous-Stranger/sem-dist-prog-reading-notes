# Notes on Linearizability: A Correctness Condition for Concurrent Objects

### Akash Gaonkar
##### 2019.23.01

source: http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf

<img src="/tex/b23879d67d363c16f28cfba0f90ab9e2.svg?invert_in_darkmode&sanitize=true" align=middle width=8.21920935pt height=14.15524440000002pt/>
<img src="/tex/c57a0802e00939892c1abc7458176925.svg?invert_in_darkmode&sanitize=true" align=middle width=8.21920935pt height=14.15524440000002pt/>
<img src="/tex/84cdeae38f027bf1aac47a1dcf48177b.svg?invert_in_darkmode&sanitize=true" align=middle width=8.21920935pt height=14.15524440000002pt/>
<img src="/tex/58c63fc4a2f5309d98fc89e617753e45.svg?invert_in_darkmode&sanitize=true" align=middle width=8.21920935pt height=14.15524440000002pt/>
<img src="/tex/63539bddb3412ec3ba7197c471a1d18a.svg?invert_in_darkmode&sanitize=true" align=middle width=8.21920935pt height=14.15524440000002pt/>
<img src="/tex/bac1d15346308f165bd2bfa9698c5d6a.svg?invert_in_darkmode&sanitize=true" align=middle width=8.21920935pt height=14.15524440000002pt/>
<img src="/tex/10dfcc905b989d237d448d87585b7a73.svg?invert_in_darkmode&sanitize=true" align=middle width=8.21920935pt height=14.15524440000002pt/>

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

**Defn 1. (Events)**: For an object <img src="/tex/332cc365a4987aacce0ead01b8bdcc0b.svg?invert_in_darkmode&sanitize=true" align=middle width=9.39498779999999pt height=14.15524440000002pt/> executing operation <img src="/tex/41a4a2664dc92e86e6a0e515d40d209a.svg?invert_in_darkmode&sanitize=true" align=middle width=16.23861689999999pt height=14.15524440000002pt/> with
arguments <img src="/tex/7a0d7af9c973978063ace43fee5836a4.svg?invert_in_darkmode&sanitize=true" align=middle width=39.43314044999999pt height=22.63846199999998pt/> on process <img src="/tex/53d147e7f3fe6e47ee05b88b166bd3f6.svg?invert_in_darkmode&sanitize=true" align=middle width=12.32879834999999pt height=22.465723500000017pt/>, there will be an *invocation* event
<img src="/tex/56ffe19cccef8dc7998ecba6306742b5.svg?invert_in_darkmode&sanitize=true" align=middle width=91.00279484999999pt height=24.65753399999998pt/>. If the If the operation is not *pending* there
will be a *response* event <img src="/tex/2a359158c8fafee6dcc0bd1272670fdb.svg?invert_in_darkmode&sanitize=true" align=middle width=101.19509729999999pt height=24.65753399999998pt/>, where <img src="/tex/d16287e1bb170e7080be11734c5a83d0.svg?invert_in_darkmode&sanitize=true" align=middle width=35.89629119999999pt height=20.221802699999984pt/> is the
termination status (e.g. <img src="/tex/d519ce57f133b75e33626c2f0bcc0d38.svg?invert_in_darkmode&sanitize=true" align=middle width=22.070792699999988pt height=22.831056599999986pt/>) and <img src="/tex/fa52f0c6d2b5e2f7da95d5773b030fca.svg?invert_in_darkmode&sanitize=true" align=middle width=29.96776694999999pt height=22.63846199999998pt/> are the results. We say this
response *matches* the invocation.

**Defn 2. (History):** A *history* is a finite sequence of operation
invocation and response events, used to model the execution of a
concurrent system. A *subhistory* of a history <img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/> is a subsequence of
the events of <img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/>, and we define <img src="/tex/6bb81ee70f9eaa590a243f58c762f235.svg?invert_in_darkmode&sanitize=true" align=middle width=92.0436429pt height=24.65753399999998pt/> as the maximal subhistory
consisting only of invocations with matching responses.

**Defn 3. (Sequential History):** A history is *sequential* if (1) its
first event is an invocation, and (2) each invocation, except possibly the
last, is immediately followed by a matching response, and each response is
immediately followed by a matching invocation. If a history is not
sequential, we call it *concurrent*.

**>> Q: What does it mean for a response to be followed by a matching
invocation?**

**Defn 4. (Process and Object Subhistories):** Given a history <img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/>, a process
<img src="/tex/53d147e7f3fe6e47ee05b88b166bd3f6.svg?invert_in_darkmode&sanitize=true" align=middle width=12.32879834999999pt height=22.465723500000017pt/>, and an object <img src="/tex/332cc365a4987aacce0ead01b8bdcc0b.svg?invert_in_darkmode&sanitize=true" align=middle width=9.39498779999999pt height=14.15524440000002pt/>, the *process subhistory* of <img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/> at <img src="/tex/53d147e7f3fe6e47ee05b88b166bd3f6.svg?invert_in_darkmode&sanitize=true" align=middle width=12.32879834999999pt height=22.465723500000017pt/>, <img src="/tex/bcdd2cdd3121e543542e60cce7fb4bf7.svg?invert_in_darkmode&sanitize=true" align=middle width=41.02718894999999pt height=24.65753399999998pt/> is
the subsequence of all events in <img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/> whose process is <img src="/tex/53d147e7f3fe6e47ee05b88b166bd3f6.svg?invert_in_darkmode&sanitize=true" align=middle width=12.32879834999999pt height=22.465723500000017pt/>. Likewise, the
*object subhistory* of <img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/> for <img src="/tex/332cc365a4987aacce0ead01b8bdcc0b.svg?invert_in_darkmode&sanitize=true" align=middle width=9.39498779999999pt height=14.15524440000002pt/>, <img src="/tex/aefdb8a424cdb976e74c9a9d48825089.svg?invert_in_darkmode&sanitize=true" align=middle width=38.09337839999999pt height=24.65753399999998pt/> is the subsequence of all
events whose object is <img src="/tex/332cc365a4987aacce0ead01b8bdcc0b.svg?invert_in_darkmode&sanitize=true" align=middle width=9.39498779999999pt height=14.15524440000002pt/>.

**Defn 5. (Equivalence Between Histories):** Two histories, <img src="/tex/5b2e7c011c47d0e441db70e9bb9da593.svg?invert_in_darkmode&sanitize=true" align=middle width=40.18253744999999pt height=24.7161288pt/> are
said to be equivalent if each process's history is unchanged. That is, for
every process <img src="/tex/df5a289587a2f0247a5b97c1e8ac58ca.svg?invert_in_darkmode&sanitize=true" align=middle width=12.83677559999999pt height=22.465723500000017pt/>, <img src="/tex/7d16ba955e2218b98fca90307f983766.svg?invert_in_darkmode&sanitize=true" align=middle width=109.59983264999998pt height=24.7161288pt/>.

**Defn 6. (Well-formed Histories):** A history <img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/> is *well-formed* if all
process histories <img src="/tex/0ba9163acfea25eb91c9a806622bde71.svg?invert_in_darkmode&sanitize=true" align=middle width=41.53516454999999pt height=24.65753399999998pt/> are sequential. We assume throughout the paper
that all histories are well-formed.

**Defn 7. (Operation):** An operation <img src="/tex/8cd34385ed61aca950a6b06d09fb50ac.svg?invert_in_darkmode&sanitize=true" align=middle width=7.654137149999991pt height=14.15524440000002pt/> is a pair of an invocation <img src="/tex/ad89a3f500b6303fde0369b5bb63da1d.svg?invert_in_darkmode&sanitize=true" align=middle width=44.52751544999999pt height=24.65753399999998pt/>
and its matching response <img src="/tex/7a8d56a6cabf87b474d07eefbcd8ab9a.svg?invert_in_darkmode&sanitize=true" align=middle width=43.67214389999999pt height=24.65753399999998pt/>. It is denoted
<img src="/tex/db5ea894b64ca895261912320f96572b.svg?invert_in_darkmode&sanitize=true" align=middle width=205.53675449999997pt height=24.65753399999998pt/>

**Defn 8. (Sequential Specification):** A *sequential specification* <img src="/tex/e257acd1ccbe7fcb654708f1a866bfe9.svg?invert_in_darkmode&sanitize=true" align=middle width=11.027402099999989pt height=22.465723500000017pt/> for
an object <img src="/tex/332cc365a4987aacce0ead01b8bdcc0b.svg?invert_in_darkmode&sanitize=true" align=middle width=9.39498779999999pt height=14.15524440000002pt/> is a set of sequential single-object histories such that if <img src="/tex/ef6ad6dbbcf9a24bd5ac7eb917e2fd22.svg?invert_in_darkmode&sanitize=true" align=middle width=46.11850814999999pt height=22.465723500000017pt/>, then for any prefix <img src="/tex/ab33cc30475673ad7eba955d62a80801.svg?invert_in_darkmode&sanitize=true" align=middle width=18.78993104999999pt height=24.7161288pt/> of <img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/>, <img src="/tex/b5fec4116e69593a75abe5bbb9865e68.svg?invert_in_darkmode&sanitize=true" align=middle width=50.73038354999999pt height=24.7161288pt/>. A sequential history
<img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/> is *legal* if for each object <img src="/tex/332cc365a4987aacce0ead01b8bdcc0b.svg?invert_in_darkmode&sanitize=true" align=middle width=9.39498779999999pt height=14.15524440000002pt/> and its specification <img src="/tex/38c509876b6a635e12a1e1c9513c88a8.svg?invert_in_darkmode&sanitize=true" align=middle width=17.53429754999999pt height=22.465723500000017pt/>,
<img src="/tex/b42cf758b2875650a710e72e857b2682.svg?invert_in_darkmode&sanitize=true" align=middle width=88.50424604999998pt height=24.65753399999998pt/>.

**Defn 9. (Returns Before):** A history <img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/> has a partial order <img src="/tex/d49ae6155422ec08be0efbddec3c7152.svg?invert_in_darkmode&sanitize=true" align=middle width=24.43159454999999pt height=17.723762100000005pt/> on
operations, <img src="/tex/23d1bed0ee56d9deaa86866691b62519.svg?invert_in_darkmode&sanitize=true" align=middle width=63.62096564999998pt height=17.723762100000005pt/> if <img src="/tex/248e75e43994808317dfce769b4956e2.svg?invert_in_darkmode&sanitize=true" align=middle width=51.04660274999999pt height=24.65753399999998pt/> precedes <img src="/tex/2bab08b17d341d9ea662b0cf517a8150.svg?invert_in_darkmode&sanitize=true" align=middle width=51.90197594999998pt height=24.65753399999998pt/>. Operations
unrelated by <img src="/tex/d49ae6155422ec08be0efbddec3c7152.svg?invert_in_darkmode&sanitize=true" align=middle width=24.43159454999999pt height=17.723762100000005pt/> are said to be *concurrent*. If <img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/> is sequential,
then <img src="/tex/d49ae6155422ec08be0efbddec3c7152.svg?invert_in_darkmode&sanitize=true" align=middle width=24.43159454999999pt height=17.723762100000005pt/> is a total order.


**Defn 10. (Linearizable):** A history <img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/> is *linearizable* if it can be
extended (by appending zero or more response events) to some history <img src="/tex/ab33cc30475673ad7eba955d62a80801.svg?invert_in_darkmode&sanitize=true" align=middle width=18.78993104999999pt height=24.7161288pt/>
such that:

  - **(L1):** <img src="/tex/2bac0bb6e23f8117bd88e1a51b860491.svg?invert_in_darkmode&sanitize=true" align=middle width=96.65551664999998pt height=24.7161288pt/> is equivalent to some legal sequential
    history $S$.
  - **(L2):** <img src="/tex/ab6125c7fa573f45997201c6bc8e2266.svg?invert_in_darkmode&sanitize=true" align=middle width=59.525304299999995pt height=20.908638300000003pt/>.

We call <img src="/tex/e257acd1ccbe7fcb654708f1a866bfe9.svg?invert_in_darkmode&sanitize=true" align=middle width=11.027402099999989pt height=22.465723500000017pt/> a *linearization* of <img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/>.


## Properties of Linearizability

**Thm 1. (Locality):** *A history <img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/> is linearizable iff for each object
<img src="/tex/332cc365a4987aacce0ead01b8bdcc0b.svg?invert_in_darkmode&sanitize=true" align=middle width=9.39498779999999pt height=14.15524440000002pt/>, <img src="/tex/aefdb8a424cdb976e74c9a9d48825089.svg?invert_in_darkmode&sanitize=true" align=middle width=38.09337839999999pt height=24.65753399999998pt/> is linearizable.*

**Defn 11. (Total Operation):** We call an operation *total* if it is
defined for all possible values of the object. Otherwise we call it
*partial*.

**Thm 2. (Nonblocking):** *Let <img src="/tex/3dd3004c8e29ca2137a708b715cf6c7f.svg?invert_in_darkmode&sanitize=true" align=middle width=24.087963899999988pt height=21.68300969999999pt/> be the invocation of a total operation.
If <img src="/tex/df3292c019441957b4310022949acc82.svg?invert_in_darkmode&sanitize=true" align=middle width=46.31968934999998pt height=22.465723500000017pt/> is a pending invocation in a linearizable history <img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/>, then there exists some response <img src="/tex/f141b3f9da1dd34594327044c738f9d8.svg?invert_in_darkmode&sanitize=true" align=middle width=45.46431119999998pt height=22.465723500000017pt/> such
that <img src="/tex/1daf1ab725aff8b371aa0068c5293a38.svg?invert_in_darkmode&sanitize=true" align=middle width=72.33626069999998pt height=22.465723500000017pt/> is linearizable.*

Note that partial operations, such as a queue-dequeue (undefined for an
empty queue) can still block.

## Comparison to Other Notions of Consistency

| Property  | Linearizability | Sequential Consistency | Serializability |
|-----------|:---------------:|:----------------------:|:---------------:|
|Equivalent Legal Sequential History                           | X | X | X |
|<img src="/tex/ab6125c7fa573f45997201c6bc8e2266.svg?invert_in_darkmode&sanitize=true" align=middle width=59.525304299999995pt height=20.908638300000003pt/> (Happens Before)                          | X |   | X |
|<img src="/tex/d6e72ed4421a135af0baddacadd51bb1.svg?invert_in_darkmode&sanitize=true" align=middle width=101.01670259999997pt height=22.831056599999986pt/> (Read My Writes)| X | X | X |
|Locality                                                      | X |   |   |
|Nonblocking (total operations)                                | X | X |   |
|Multi-operation transactions                                  |   |   | X |


## Verifying Linearizability

**Defn 12. (Implementation)** An *implementation* is a set of histories in
which the events of two objects, a *representation* object <img src="/tex/236dd1afb0a6ab3fb51eae521b3ebf0e.svg?invert_in_darkmode&sanitize=true" align=middle width=8.21920935pt height=14.15524440000002pt/> and an
*abstract* object <img src="/tex/6c17387786b58b5cdb0c4fb38c9e0a71.svg?invert_in_darkmode&sanitize=true" align=middle width=8.21920935pt height=14.15524440000002pt/>, such that for any history <img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/> and process <img src="/tex/df5a289587a2f0247a5b97c1e8ac58ca.svg?invert_in_darkmode&sanitize=true" align=middle width=12.83677559999999pt height=22.465723500000017pt/> in
the implementation, for any <img src="/tex/75a2ce53e8a1f69acc7d5a45ae27bfb6.svg?invert_in_darkmode&sanitize=true" align=middle width=7.654137149999991pt height=14.15524440000002pt/> in <img src="/tex/ea05a1017a3450c221086bd695970e1c.svg?invert_in_darkmode&sanitize=true" align=middle width=50.667483899999986pt height=24.65753399999998pt/> there is some
<img src="/tex/3a773dacd08e1ae139a2f62e88c8f677.svg?invert_in_darkmode&sanitize=true" align=middle width=7.654137149999991pt height=14.15524440000002pt/> in <img src="/tex/89d26e492210fa0a4d1585ad927e2964.svg?invert_in_darkmode&sanitize=true" align=middle width=50.667483899999986pt height=24.65753399999998pt/> such that <img src="/tex/9bfa3bec9be47a92cfa59bedad919b23.svg?invert_in_darkmode&sanitize=true" align=middle width=43.27181594999999pt height=24.65753399999998pt/> precedes <img src="/tex/663f0bed68431cca0f5316175c440e0b.svg?invert_in_darkmode&sanitize=true" align=middle width=43.27181594999999pt height=24.65753399999998pt/> and <img src="/tex/d627df8c8f7e4b0ba87c2fb234f514dd.svg?invert_in_darkmode&sanitize=true" align=middle width=42.41644274999999pt height=24.65753399999998pt/> precedes <img src="/tex/bc2d50258eb5eca12bf83b9100cdc5cf.svg?invert_in_darkmode&sanitize=true" align=middle width=42.41644274999999pt height=24.65753399999998pt/>.

**Defn 13. (Correctness)** An implementation is said to be *correct* with
respect to a specification for <img src="/tex/6c17387786b58b5cdb0c4fb38c9e0a71.svg?invert_in_darkmode&sanitize=true" align=middle width=8.21920935pt height=14.15524440000002pt/> if for every history <img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/> in the
implementation, <img src="/tex/85532c944beaa2a4639c481a8a056850.svg?invert_in_darkmode&sanitize=true" align=middle width=24.13229114999999pt height=24.65753399999998pt/> is linearizable.


In the sequential case, we verify correctness using two functions:
 - the *rep. invariant*, <img src="/tex/bf51d200985f0d36aaf6d5401e72f0ac.svg?invert_in_darkmode&sanitize=true" align=middle width=34.086691649999985pt height=22.465723500000017pt/>, which tells us which
   values of $\trep$ are legal representations of $\tabs$.
 - the *abstraction function* <img src="/tex/9043c0701d35c64edefba0f65e95a2ac.svg?invert_in_darkmode&sanitize=true" align=middle width=37.89952484999999pt height=22.465723500000017pt/>, which given a
   legal representation, converts to the abstract value.

Then we know that a sequence of <img src="/tex/236dd1afb0a6ab3fb51eae521b3ebf0e.svg?invert_in_darkmode&sanitize=true" align=middle width=8.21920935pt height=14.15524440000002pt/> operations <img src="/tex/6dec54c48a0438a5fcde6053bdb9d712.svg?invert_in_darkmode&sanitize=true" align=middle width=8.49888434999999pt height=14.15524440000002pt/> is a correct
implementation of an abstract operation <img src="/tex/c745b9b57c145ec5577b82542b2df546.svg?invert_in_darkmode&sanitize=true" align=middle width=10.57650494999999pt height=14.15524440000002pt/> if for any legal
representation <img src="/tex/c707629e492907adaefe067dff5b0d02.svg?invert_in_darkmode&sanitize=true" align=middle width=61.70467049999999pt height=24.65753399999998pt/>, executing <img src="/tex/6dec54c48a0438a5fcde6053bdb9d712.svg?invert_in_darkmode&sanitize=true" align=middle width=8.49888434999999pt height=14.15524440000002pt/> at <img src="/tex/89f2e0d2d24bcf44db73aab8fc03252c.svg?invert_in_darkmode&sanitize=true" align=middle width=7.87295519999999pt height=14.15524440000002pt/> produces
the legal representation <img src="/tex/98fc5fe535b6b832c396f1f8720f1fb6.svg?invert_in_darkmode&sanitize=true" align=middle width=70.92842129999998pt height=24.7161288pt/> and executing <img src="/tex/c745b9b57c145ec5577b82542b2df546.svg?invert_in_darkmode&sanitize=true" align=middle width=10.57650494999999pt height=14.15524440000002pt/>
at <img src="/tex/15c46f610203be730a0737212a30a4de.svg?invert_in_darkmode&sanitize=true" align=middle width=32.98718774999999pt height=24.65753399999998pt/> produces <img src="/tex/f10b04238a107fa58ec8cabc34c8833a.svg?invert_in_darkmode&sanitize=true" align=middle width=37.59906149999999pt height=24.7161288pt/>.

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
considering execution values. Given a representation <img src="/tex/236dd1afb0a6ab3fb51eae521b3ebf0e.svg?invert_in_darkmode&sanitize=true" align=middle width=8.21920935pt height=14.15524440000002pt/> with a
linearizable history <img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/>, we can use define a function <img src="/tex/1ab006477fba94555c54527e2dd78819.svg?invert_in_darkmode&sanitize=true" align=middle width=27.785402699999988pt height=24.65753399999998pt/> that
computes the set of all possible values of the representation after
evaluating the history.

This allows us to use standard verification methods to show for an arbitrary linearizable history <img src="/tex/7b9a0316a2fcd7f01cfd556eedf72e96.svg?invert_in_darkmode&sanitize=true" align=middle width=14.99998994999999pt height=22.465723500000017pt/>:
\[\forall r \in \lin(H \mid \rep) \ldotp I(r) \land A(r) \subseteq \lin(H \mid \abs).\]

If this holds, then we know from <img src="/tex/95a0b2a74d6bca2242f961891c5a222f.svg?invert_in_darkmode&sanitize=true" align=middle width=36.91772534999999pt height=24.65753399999998pt/> that <img src="/tex/85532c944beaa2a4639c481a8a056850.svg?invert_in_darkmode&sanitize=true" align=middle width=24.13229114999999pt height=24.65753399999998pt/> is
linearizable. Of course, there may be ways to represent the abstract
object other than <img src="/tex/236dd1afb0a6ab3fb51eae521b3ebf0e.svg?invert_in_darkmode&sanitize=true" align=middle width=8.21920935pt height=14.15524440000002pt/>, and those ways might have different linearizations
and produce different <img src="/tex/15c46f610203be730a0737212a30a4de.svg?invert_in_darkmode&sanitize=true" align=middle width=32.98718774999999pt height=24.65753399999998pt/> s. This is why we don't need to prove going
from <img src="/tex/6c17387786b58b5cdb0c4fb38c9e0a71.svg?invert_in_darkmode&sanitize=true" align=middle width=8.21920935pt height=14.15524440000002pt/> to <img src="/tex/236dd1afb0a6ab3fb51eae521b3ebf0e.svg?invert_in_darkmode&sanitize=true" align=middle width=8.21920935pt height=14.15524440000002pt/> --- that would be constricting the other
implementations. We need only show our implementation works.

## Thoughts

Linearizability allows us to get very close to sequentialization without
significantly blocking concurrency, and it especially allows us to reason
with greater modularity than other forms of consistency, due to locality.
This makes it a very powerful tool for various applications. However,
we should recall that linearizable cannot be guaranteed in systems that
must be both available and partition tolerant (by CAP), so we may not
always be able to achieve it.
