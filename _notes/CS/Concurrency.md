---
layout: post
title: Concurrency
---

See also:
- Computer Architecture

# Memory Models
What's the point?
There is a mismatch between both
1. High level languages (C/Java/Ocaml) and assembly
2. Assembly and CPU internals

This mismatch is good. The mismatch is abstraction. It gives simpler programming models to users. It allows certain optimizations to happen that the programmer wouldn't have thought of. The programmer doesn't necessarily want to say exactly which transistor is flipped at exactly what moment. There is a higher level goal of solving a matrix problem, or writing a record to memory, or swapping two variables.

In a non threaded code we actually don't care if the follwing two instruction are reordered.
store 1 in a
store 42 in b

And if there is some reason why it would be faster to do so, sure, go for it compiler. So the program order is not really the execution order insisted upon by single threaded code. It doesn't however alllow rearrangement of

print("hello")
print("world")


Perhaps however, we are relying on this order to implement some shared primitive.

When you compile, there are intermediate states of the target that don't really correspond to anything. Depending on the conceptual distance between the source and target, these states may be short. If you point at some particular section of assembly, you probably say, ah yes, it is in the middle of trying to achieve this statement in the source. "In the middle" is not a very precise statement though.

Some things that seem intuitively atomic at the source are not in the target. One statement may become multiple, that a concurrent processor could screw with.

## Memory Order vs Program Order
[Memory order](https://en.wikipedia.org/wiki/Memory_ordering)
[Program](order)

Program order is the code order in the source. This is vaguely evokes the image of a linear sequence of statements. This is not really what high level languages look like. They have compound statements and expressions, and control flow. The syntax commonly is thought to be tree-like. Is a statement in a loop "before" another? They are executed multiple times, so sure the action during the execution of the loop comes before another, but syntactic statements in the loop correspond to a whole family of actions across all executions of the loop.

Even in single threaded code, execution does not need to follow program order. Non interfering stores and loads are allowed to be slid over each other. Common subexpression elimination means computations may not even happen when they do in the program. Function calls and system calls are probably required to stay in the same order as the source.

We could describe some realatively concrete concurrent machine, with some layer of caching. Does the variable "a" in code exist in the execution of this machine. We are already used to "a" depending on time. But it also not depends on location. It is conceivable that Thread 1, Thread 2, Cache 1 have different opinions on what "a" at the "same" time. Their clocks stay synchronized? Quite an assumption.

## Consistency vs Coherence
## Sequential Consistency (SC)

## Total Store Order (TSO)
## Relaxed Memory Models

## Language Models
### C++
### Java
### Ocaml
[a memory model for multicore ocaml](https://kcsrk.info/papers/memory_model_ocaml17.pdf) [bounding data races in space and time](https://kcsrk.info/papers/pldi18-memory.pdf)
You can see in the references to also Go, Swift, Rust, wasm work

# Data Structures
Lock Mutex Semaphore
Barriers
Lock free

Data Race Freedom

[Shared memory concurrency - a tutorial](https://www.hpl.hp.com/techreports/Compaq-DEC/WRL-95-7.pdf)
[A Promising Semantics for Relaxed-Memory Concurrency](https://www.cs.tau.ac.il/~orilahav/papers/popl17.pdf)
Mapping variables to histories
Each thread can pick arbitrary values out of histories given timestamp constraints

[thin air problem - Sewell Batty](https://www.cl.cam.ac.uk/~pes20/cpp/notes42.html)
[The Problem of Programming Language Concurrency Semantics](https://www.cl.cam.ac.uk/~jp622/the_problem_of_programming_language_concurrency_semantics.pdf)
[A Primer on Memory Consistency and Cache Coherence,](https://www.morganclaypool.com/doi/abs/10.2200/S00962ED2V01Y201910CAC049)

<https://www.youtube.com/watch?v=N07tM7xWF1U&ab_channel=CppCon> abusing your memory model for fun and profit
<https://news.ycombinator.com/item?id=29109156> what memory model should rust use

John regehr thread https://twitter.com/johnregehr/status/1451355617583460355?s=20 Really good
https://mirrors.edge.kernel.org/pub/linux/kernel/people/paulmck/perfbook/perfbook.html  Is Parallel Programming Hard, And, If So, What Can You Do About It?
https://deadlockempire.github.io/
https://github.com/BIT-SYS/KDR list of kernel data races
Herlihy is discussed as a given? https://www.amazon.com/Art-Multiprocessor-Programming-Revised-Reprint/dp/0123973376
https://www.amazon.com/-/en/dp/0470093552 concurrency state models and jabva programsd
https://www.manning.com/books/c-plus-plus-concurrency-in-action-second-edition
https://leanpub.com/concurrencywithmodernc
https://greenteapress.com/wp/semaphores/ little book of semaphors
https://googleprojectzero.blogspot.com/2021/10/how-simple-linux-kernel-memory.html kernel memory corruption bug from data race prokject zero
Concurrent Programming (Andrews) -- uses pseudo-code with synchronization/concurrency notation.
Transactional Memory (Harris) -- for non-lock based concurrency.
https://lwn.net/Articles/844224/ an introduction to lockless algorithms 
"Shared-Memory Synchronization" by Michael Scott
https://herbsutter.com/2013/02/11/atomic-weapons-the-c-memory-model-and-modern-hardware/
http://plv.mpi-sws.org/gps/paper.pdf GPS: Navigating Weak Memory with Ghosts, Protocols, and Separation
Weak memory models?
https://pvk.ca/Blog/2019/01/09/preemption-is-gc-for-memory-reordering/ - 
https://pvk.ca/Blog/2020/07/07/flatter-wait-free-hazard-pointers/
https://www.cs.rochester.edu/u/scott/papers/1991_TOCS_synch.pdf algorithms for scababe synchronization on shared-memory. Same Michiael Scott. BBN butterfly?
https://arxiv.org/abs/1106.5730 HOGWILD - parallizingf lock free SGD
A Primer on Memory Consistency and Cache Coherence  https://course.ece.cmu.edu/~ece847c/S15/lib/exe/fetch.php?media=part2_2_sorin12.pdf
https://pure.tue.nl/ws/files/4279816/344354178746665.pdf cooperating seauentail prcoesses by dikstra
http://www0.cs.ucl.ac.uk/staff/p.ohearn/papers/concurrency.pdf ohearn resources concurrency and local reasoning
http://pascal.hansotten.com/uploads/pbh/Monitors%20and%20Concurrent%20Pascal.pdf monitors and concrurent pacscal hitsotry - per brinch hansen https://twitter.com/PeterOHearn12/status/1452240719725346827?s=20 " atomic "release and sleep" is the key as far as I'm concerned, it's so easy to invent bad solutions without that primitive"
https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.227.3871&rep=rep1&type=pdf the 12 commandments of synchrnonizatiojn


Concurrency
Mutex
Lock
Concurrent hash table https://en.wikipedia.org/wiki/Concurrent_hash_table
barriers

 https://www.youtube.com/watch?v=80ifzK3b8QQ&list=PL1835A90FC78FF8BE&index=1&ab_channel=BartoszMilewski - bartosz on c++11 concurrency

pthread vs std::thread
Arc in rust
condition variables

https://www.manning.com/books/c-plus-plus-concurrency-in-action-second-edition

https://www.coursera.org/learn/concurrent-programming-in-java


C++/Java/Ocaml memory models
https://github.com/herd/CoqCat - interesting thesis too
https://github.com/herd/herdtools7
https://github.com/ocamllabs/ocaml-memory-model
https://en.wikipedia.org/wiki/Memory_model_(programming) 
https://www.hboehm.info/c++mm/ hans boehm link farm - "impossible as a library"
http://www.cl.cam.ac.uk/~pes20/cpp/popl085ap-sewell.pdf - mathematizzing C++ memory model, kodkod, isabelle

Various kinds of relations.


futex - fats userspace mutex

 lockless algorithsm
https://lwn.net/Articles/844224/
MPI Cilk OpenMP

rust model checker actor system thing
https://www.stateright.rs/

Leslie lamport

Lindsey kuper has a dist sys course
It's probably pretty good
https://www.youtube.com/playlist?list=PLNPUF5QyWU8O0Wd8QDh9KaM1ggsxspJ31

Lecture 1. 
  several node,s connected by network, characterized by partial failure
cloud vs hpc
cloud
treat failure as expected and work around it "Eveerything fails all tyhe timne" - werner vogels
hpc- 
partial is total failure. checkpointing

M1 -> M2 x?
Posslbe problems:
1. lost request
2. request from M1 is slow
3. M2 crashes
4. M2 slow to respond
5. seponse is slow
6. response could get lost
7. corrupt - byzantine faults.

important: send request to another node and don't receive a response, it is impossible for you to know why

How do real systems cope?
Timeout - wait a certain amount of time then assume failure - imperfect solutuojn

max delay d. max process time r. cn't really do this
asynchronous network - no max 
Palvaro - partiasl failure + unbounded latency

https://people.ucsc.edu/~palvaro/ - dedalus - datalog in time and space. https://www.youtube.com/watch?v=R2Aa4PivG0g&ab_channel=StrangeLoopConference bloom blazes, data lineage multiple proofs are fault tolerant. lineage. Why did happen: produce explanation / proof. Cegis? concolic kind of? observability.

https://martin.kleppmann.com/  https://www.youtube.com/playlist?list=PLeKd45zvjcDFUEv_ohr_HdUFe97RItdiB

https://github.com/aphyr/distsys-class
https://www.distributedsystemscourse.com/

kyle kingsbury

chaos engineering - nora jones netflix

http://book.mixu.net/distsys/
https://www.youtube.com/watch?v=cQP8WApzIQQ&list=PLrw6a1wE39_tb2fErI4-WkMbsvGQk9_UB&ab_channel=MIT6.824%3ADistributedSystems


### Lecture 2 - kuper
clocks. scheduling. intervals in time
time of day clocks, monotonic clocks
NTP network time protocl. not so good for measuring inervals. can jump forward or backward
cloudflare leap second bug
monotonic - only goes forward

import time
time.monotonic() . not comparable between machines

logical clocks
only measuring order of events.  A can cause B, not vie versa
lamport diagrams/ spacetime daigrams

network models
synhcrnous network is one where exists an n such no message takes longer than n 
asynchrnous - one where the is no n such that no message takes longer than n units
"partially synchrnous" n exists but you don't know what it is

happens before - on same process. if a is send,, and b is receive, then a is before c, trasntivity
concurrent - not ordered

state - 


mate soos talking about adding mpi
z3 had mpi but was disabled.
armin biere talking about pthreads - pligenling uses pthreads
https://twitter.com/SoosMate/status/1375935222907305988?s=20
https://www.researchgate.net/publication/221403132_A_Concurrent_Portfolio_Approach_to_SMT_Solving