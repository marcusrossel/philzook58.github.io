

One of my big regrets on the VIBES project was not writing up a checkpoint on the ideas and state of the constraint compiler aspect of it. There were a couple of reasons for this:
1. It was not clear the degree to which it is allowed for me to write about work projects
2. The state is and was highly imperfect

Nevertheless, it is quite a cool approach and even in it's imperfect state it is interesting

# Unison

A major touchpoint for compiling with constraints is the [Unison compiler](https://unison-code.github.io/).

Unison is an alternative LLVM backend that solves the register allocation problem and instruction selection problem in "unison" using a constraint solving backend.

Unison supported multiple architectures, but one, [Hexagon](https://en.wikipedia.org/wiki/Qualcomm_Hexagon) was a [very long instruction word](https://en.wikipedia.org/wiki/Very_long_instruction_word) (VLIW) architecture. My 10cent discussion of VLIW is that the instruction set allows explicit specification of parallelism / data dependencies. This enables perhaps a more direct model of the actual parallel interworkings of the cpu at the cost of significant compiler complexity. It is here that perhaps constraint compilation carries its weight.

This paper is particular is useful [Combinatorial Register Allocation and Instruction Scheduling])https://arxiv.org/abs/1804.02452). Also very interesting was the [manual](https://unison-code.github.io/doc/manual.pdf).

LLVM has multiple IRs. One of them is [Machine IR](https://llvm.org/docs/MIRLangRef.html) (MIR), which is an IR that has more of the information filled in. Unison takes this output, fiddles with it, and then performs solving. This reuses the frontend, middle end passes, and instruction selection of llvm, which is huge.

The actual unison minizinc model is [here](https://github.com/unison-code/unison/blob/master/src/solvers/multi_backend/minizinc/code-generation.mzn). I spent a good month probably studying this and trying to pare this down. Ultimately, I decided to write it from scratch again, adding in pieces as we understood we needed them. 

This is an example of a unison IR taken from the manual:

```
function: factorial
b0 (entry, freq: 4):
    o0: [t0:r0,t1:r31] <- (in) []
    o1: [t2] <- A2_tfrsi [1]
    o2: [t3] <- C2_cmpgti [t0,0]
    o3: [] <- J2_jumpf [t3,b2]
    o4: [] <- (out) [t0,t1,t2]
b1 (freq: 85):
    o5: [t4,t5,t6] <- (in) []
    o6: [t7] <- A2_addi [t5,-1]
    o7: [t8] <- M2_mpyi [t5,t4]
    o8: [t9] <- C2_cmpgti [t5,1]
    o9: [] <- J2_jumpt [t9,b1]
    o10: [] <- (out) [t6,t7,t8]
b2 (exit, return, freq: 4):
    o11: [t10,t11] <- (in) []
    o12: [] <- JMPret [t11]
    o13: [] <- (out) [t10:r0]
adjacent:
    t0 -> t5, t1 -> t6, t1 -> t11, t2 -> t4, t2 -> t10, t6 -> t6, t6 -> t11,
    t7 -> t5, t8 -> t4, t8 -> t10
```

# Minizinc

Given that we already had experience using z3 and that smt solvers are not that different from constraint programming systems, why go for minizinc?

- Optimization
- Useful bits

- text file vs bindings. Speed, flexibility, readability, serialization (cost and boon)


# The model

# Was it worth it?

# Ideas on improvements
