# Programming languages

TODO late binding

TODO clojure (esp concurrency)

TODO F#, axum, join patterns

TODO [http://research.microsoft.com/apps/pubs/?id=115390](http://research.microsoft.com/apps/pubs/?id=115390) ConScript: Specifying and Enforcing Fine-Grained Security Policies for JavaScript in the Browser

analysis TODO

- differences btwn pointer analysis and CFA?
- relationship btwn alias analysis and shape analysis?
- escape analysis: determining the dynamic scope of pointers; eg ptrs
- pointer analysis aka points-to analysis: specific case of alias analysis
    - slow
- control flow analysis (CFA) aka data flow analysis
    - 0-CFA
    - backward
        - liveness (dead code elimination)
    - forward
        - reaching definitions
- shape analysis: properties of linked, dynamically allocated structs
    - generalization of ptr analysis
    - powerful but slow -> no widespread use
    - finding leaks; multiple frees; dangling ptrs; bounds errors; type-state
    properties; algo correctness (eg reverse, sort)
- alias analysis: if location can be accessed multiple ways
analysis, shape analysis
    - converting heap allocs to stack allocs, if pointer never escapes subroutine
    - synchronization elision, if pointer never escapes thread
    - breaking up objects/scalar replacement: store in regs instead of mem

TODOs

- region inference
- effect systems
- uniqueness types
- operational semantics
- denotational semantics
- category theory
- free theorems

people

- guy steele
    - Harv BS, MIT PhD, asst prof at CMU
    - lisp at Thinking Machines, java at Sun
    - wrote orig Lambda (The Ultimate) papers with advisor Sussman
- gerald sussman: scheme; coauthor of SICP with abelson

misc

- pit of success: design that makes it hard for users to fail

properties of macros

- hygiene
- referential transparency

immutability advantages

- nice list of args for immutability from
[http://www.javapractices.com/topic/TopicAction.do?Id=29](http://www.javapractices.com/topic/TopicAction.do?Id=29)
    - are simple to construct, test, and use
    - are automatically thread-safe and have no synchronization issues
    - do not need a copy constructor
    - do not need an implementation of clone
    - allow hashCode to use lazy initialization, and to cache its return value
    - do not need to be copied defensively when used as a field
    - make good Map keys and Set elements (these objects must not change state
    while in the collection)
    - have their class invariant established once upon construction, and it never
    needs to be checked again
    - always have "failure atomicity" (a term used by Joshua Bloch) : if an
    immutable object throws an exception, it's never left in an undesirable or
    indeterminate state

Optimizations

- method specialization: compile several copies of a given procedure, each
customized for one receiver type, so that the type of the receiver is bound
at compile time
    - used in Hotspot
    - avoids dynamic dispatch
    - can lead to code explosion; HotSpot only does this for certain things

garbage collection

- design axes
    - moving (compacting) vs non-moving
        - moving: no extra work to reclaim dead space; fast subseq allocs; survivor
        locality
        - non-moving: no need to copy; no need to update refs
    - copying vs mark-and-sweep vs mark-and-don't-sweep
        - copying: switch btwn 2 regions; copy while traversing; requires 2x space
        - mark-sweep: mark while traversing; sweep separately; sweep can be either
        moving or non-moving depending on avail mem
    - generational aka ephemeral: inspect younger gens more frequently
        - cross-gen links are perf problem
    - stop-the-world vs incremental vs concurrent
    - precise vs conservative
        - internal ptrs: ptrs to fields in objects (C)
- boehm: conservative, incremental, mark-sweep; competitive speed
- hybrids in Java/.NET: generational with occasional mark-and-sweep and rarer
full-copying
- great big FAQ: [http://www.iecc.com/gclist/GC-faq.html](http://www.iecc.com/gclist/GC-faq.html)

azul's pauseless GC (managed runtime initiative aka MRI)

- GC pauses: biggest pain point in modern JVMs
    - every other GC delays moving objects around
- azul is different: the only way it collects is by compaction
- single pass marking of live objs; no need to revisit refs
    - special read barrier: ea time read ref, VM intercepts to ensure obj is
    marked if it isn't already
    - GC thus insensitive to ref mutation rate
- concurrent compaction: move an object without updating all its refs
    - self healing: read barriers also allow refs to be updated as they're
    deref'd
- massive and rapid manipulation of virtual mem mappings, at rate of allocation
    - requires changes to OS kernels
    - "quick release": once relocated/compacted objs out of a page, keep
    forwarding info outside the page, so can immediately release the physical
    page behind the physical mapping (refs are all intercepted) and recycle it
    at a diff virt addr for allocation/app use again
- slightly lower all-around perf
- [http://www.artima.com/lejava/articles/azul_pauseless_gc.html](http://www.artima.com/lejava/articles/azul_pauseless_gc.html)

hotspot GC

- generational GC
    - objs count number of GCs they survived; big counts promote them to older
    generation
- parallel new collector: stop the world copying collector
    - stops, marks, and copies/compacts
- concurrent mark-sweep (CMS)
    - initial-mark (stop the world): mark root objects
    - concurrent-mark (concurrent)
    - remark (stop the world): catch changed refs and new objs; special data
    structure allows us to inspect only objects modified in prior phase
    - concurrent-sweep (concurrent): does not relocate objects, leading to frag
- failure modes
    - concurrent mode failure: if CMS heap fills up during concurrent-mark phase,
    must fall back to copying collector
    - promotion failure due to frag: if no free space in CMS heap due to frag,
    must fall back to copying collector
- [http://www.cloudera.com/blog/2011/02/avoiding-full-gcs-in-hbase-with-memstore-local-allocation-buffers-part-1/](http://www.cloudera.com/blog/2011/02/avoiding-full-gcs-in-hbase-with-memstore-local-allocation-buffers-part-1/)

B programming language

- basically C without types
- everything was a word
- anything can be dereferenced (everything is a pointer)
- predecessor to C, inspired by BCPL

Go (11/10/2009 Robert Griesemer, Rob Pike, Ken Thompson, Ian Taylor, Russ Cox,
Jini Kim and Adam Langley)

- ["Pike is sometimes maddening to read or listen to because of the way he
doesn't ack prior art and sometimes goes on too long about obvious points."]
- no generics, exceptions, inheritance
- most interesting aspect: interface polymorphism
    - interfaces are not bound to types; can freely retrofit by defining the
    methods on that type (physically, this takes place externally to the class
    code)
- plain mark-&-sweep GC
- goroutines: coro's/csp
- pike's limbo/newsqueak influence
- decent std lib
- fast compilers & execution: plan-9 and gcc
- a memory model for concurrency
- choose stack or heap allocation
- simple type system + inference
- mem-safe: bounds-checked arrays, no ptr arithmetic, type safety, GC
- uses a module system
- built-in arrays are value types
- Python like slice syntx `a[lo:hi]`
- immutable strings
- switch has no break; use "fallthrough" to fallthrough
- nested functions
- tuples
- := for assignment
- uses `var` to declare variables (this was chapmpioned by some here instead of
auto)
- variable type comes after declaration and is optional
- return type of functions comes after parameters
- no Windows port yet
- iota!?
- walter bright on what's missing that D has, off top of head [but: bloat]:
    - exception handling
    - generic programming
    - metaprogramming
    - inline assembler
    - interface to C
    - RAII
    - immutability for anything but strings
    - vector operations
    - operator overloading
    - purity
    - CTFE
    - unit testing
    - documentation generation
    - ability to write systems code like implement a GC
    - conditional compilation
    - contracts
    - 80 bit floating point
    - introspection (runtime or compile time)
    - delegates
    - reference parameters
- [http://www.youtube.com/watch?v=rKnDgT73v8s](http://www.youtube.com/watch?v=rKnDgT73v8s)
- [http://lambda-the-ultimate.org/node/3676](http://lambda-the-ultimate.org/node/3676)
- [http://www.digitalmars.com/webnews/newsgroups.php?art_group=digitalmars.D&article_id=100626](http://www.digitalmars.com/webnews/newsgroups.php?art_group=digitalmars.D&article_id=100626)

Revisiting Coroutines (2004)

- very nice organization and juxtaposition of coros and even continuations; a
good bookmark!
- coroutines
    - control transfer mechanism
        - symmetric: single operation, `transferTo(some other coro)`
            - tends to be more difficult to follow
        - asymmetric: two operations, `resume("child" coro)` and `yield()`
            - tends to yield more structured code
    - first-class vs. restricted (Java iterators: use coro only from for loop)
    - stackful vs. non-stackful (Python generators)
- continuations
    - multi-shot
        - harder to implement and less performant because you must copy a
        captured continuation before it is modified (before you call back into
        it)
    - one-shot
        - implementation
            - create a new stack segment and swap that in for the current one
            - on call, replace with orig stack segment
        - partial continuations aka subcontinuations TODO
            - restricted but still useful for most apps
- equivalence
    - sym and asym coros can be implemented in terms of each other
    - full coros equiv to one-shot continuations
    - continuations implemented using coros more efficient than vice-versa,
    since implementing coro using continuations means each time you transfer
    control you must create a continuation (i.e., create a new stack segment)
    - cooperative threading: similar to coros
- apps: some high-quality examples, all straightfwd to understand
    - producer-consumer, generators
    - goal-oriented programming: logic programming, pattern matching
    - cooperative multitasking
    - exception handling
- implemented asym coros for Lua

Ada/SPARK

- ada: orig lang
    - gnat: ada compiler in gcc
- akacore: company based on it
    - gnat pro: better ada compiler; can emit JVM, .NET
    - spark: lang based on ada; has formal verification
    - gnatbench: IDE

Erlang

- example:
    
    start() ->
    pid = spawn(module, function [, args...])
    pid ! {example, msg}
    pid.
    
- receiving: pattern-matching
    
    msg_printer() ->
    receive
    exit ->
    ...
    {format, N} ->
    ...
    msg_printer()
    MSG ->
    ...
    msg_printer()
    after 10000 ->
    ...
    msg_printer()
    end
    
- receive doesn't necessary dequeue in order, but gets best match
- OTP has things like process groups
- features
    - process monitoring: notify when a process dies
    - hot code loading: load side by side, then switch
    - distributed messaging
    - impure functional, eg key value store
    - interpreted or JIT
- critiques
    - you must always match all messages, otherwise throw exceptions
    - FFI sucks

Lisp

- elisp
    - dynamically scoped
- clisp
    - lexically scoped
- scheme
    - lexically scoped
    - `fluid-let`: dynamic binding

.NET

- DLR: TODO expression trees, dynamic dispatch, call site caching

C# 3

- extension methods: similar to expanders

C# 4

- dynamic: late binding via DLR
    - don't know object type (ie type `object`) but want to use it
    - options before dynamic:
        - use reflection, eg GetType, InvokeMember
        - manually discover type and cast
        - use specialized interface, eg ScriptObject
    - through DLR, get unified interface IDynamicObject, or fall back on lang reflection

## Continuations etc.

coroutines

- symmetric: yield command takes extra arg specifying who to yield to
    - behave more like threads
- asymmetric: yield and resume are separate commands; similar to generators
    - behave more like routines; control is always transferred back to caller
    - can be composed like regular functions
    - lua implements this; "simpler"
    - similar arg made for partial continuations
- can simulate each other

continuations

- partial
- delimited
- full

software metrics

- coverage, comment density, coupling, SLOC, bugs per SLOC, perf
- cyclomatic complexity: num of linearly indep paths
- cohesion: how strongly related/focused a module's responsibilities are

## Reactivity

TODO RP, FRP

flapjax (leo meyerovich, shriram krishnamurthy, OOPSLA09 best student paper)

- [not convinced this brings anything new to the table vs. eg openlaszlo, javafx]
- optional compiler
    - automatic lifting of expressions into reactions, eg `timeB(1000)+1` instead of `liftB(function(t) { return t+1; }, timerB(1000))`
    - inline flapjax binding expressions in html attribs
- [talks about behaviors, but can ignore; really just event streams]
- first-class values: eg higher-order event streams, rebinding
    - [claim: unlike openlaszlo/javafx, but not convinced]
- glitch-freedom: process dataflow graph in topological order to avoid transient inconsistencies (breaking app invariants)
    - [claim: unlike openlaszlo/javafx, but is that true? seems obvious, and silly to do anything else]
    - cycles should be broken with delays [no argument there; makes sense]
- in use since 10/06 (pretty early)

## Dependent Types

why dependent types matter TODO

## Parsing

higher order abstract syntax (HOAS)

- implementing operations like substitutions on ASTs requires tedium, eg renaming variables to avoid variable capture
- use the host lang's existing substitution capabilites; instead of
    
    data Exp = Var String | App Exp Exp | Lam String Exp
    
    use
    
    data Exp = App Exp Exp | Lam (Exp -> Exp)
    
    eg
    
    omega = App (Lam (\x -> App x x)) (Lam (\x -> App x x))
    
- for *open terms* (terms with free vars), nest in more lambda abstractions
- not scalable: depends on number of free vars, and can't analyze (only apply)
- ref: [http://lambda-the-ultimate.org/node/3627](http://lambda-the-ultimate.org/node/3627)

## Capabilities

E

- basically java wo globals; can't import; objects get everything passed by ref
- one root parent obj starts w access to everything

## Extensible

active libraries

- [http://lambda-the-ultimate.org/node/1339](http://lambda-the-ultimate.org/node/1339)
- problem: libraries encode a language of their own, but the compiler
doesn't/can't analyze (check/optimize) its use
- libraries provide not just runtime but also extensions to the compiler
    - compile-time functions that eg do static checking and optimizations
- see also: meta-compilation of language abstractions
[http://lambda-the-ultimate.org/node/2019](http://lambda-the-ultimate.org/node/2019)

java syntactic extender (jonathan bachrach)

- dylan-inspired hygienic macros for java
- pretty simple; don't try to solve any big problems; java special cases
- [strikes nice feature set vs ease-of-use balance]

## Expressivity and Abstractions

on understanding data abstraction, revisited (william cook, 2009)

- explain diff btwn objects and abstract data types
- objects have interfaces and private impls; always go through interface methods
    - even cannot reach into other objects of same type; this is *autognosis*
    - interchangeable at runtime
- ADTs have public impls
    - interchangeable at compile time
- common to start with ADTs then upgrade into objects when more flexibility needed

ejava: statically scope object adaptation with expanders (todd millstein, ucla, oopsla06)

- [excellent, thorough discussion; presents many subtleties, some highlighted here]
- design
    - semantically, changes the existing class
    - explicitly import
    - can expand interfaces; can implement interfaces
    - expander families for overriding; dispatch on dynamic type (hence expander methods are always invoked on an expander *family*, not an individual expander)
    - expanders are fully modular (don't actually recompile orig classes); as is augmenting expander families
- method lookup
    - static
        - always use orig methods if exist
        - can explicitly say what expander family to use in case of multiple ambiguous expanders
    - dynamic
        - java requires method families be *exhaustive* and *unambiguous* so run-time lookup always works
        - ie classes must implement all inherited abstracts
        - hence, cannot have abstract methods in expanders, due to unknown subclasses of expanded class (which we may not have expanders for) [but then must still throw run-time exceptions!]
        - overriding expanders can only expand classes, not interfaces
- implementation
    - maintain a weak-ref cache of wrappers
    - instantiate wrappers using if-else chain of instanceof checks
- comparisons
    - visitors
        - must plan in advance
        - invasive changes to visitor classes when type hierarchy changes
        - visitor advantage: can inherit code
            - eg many visitors just traverse into children
            - expander families are isolated from each other
- related work
    - classboxes: closest work
    - scala views
        - no support for augmentation; new type must forward ops
        - no overriding
    - also: aspectj, multijava, half & half
    - not for changing existing classes: mixins, traits, parameterized modules

## Haskell

Parallel Performance Tuning for Haskell (spj 09)

- threadscope: nifty profiling viz tool that is esp. useful given haskell's
tricky combination of its evaluation machinery with its `par` abstraction,
plus the whole suite of combinators (including the mind-bending "strategies"
library) built on top of those.

Asynchronous exceptions in Haskell

- TODO: review this and take notes

Haskell Type Constraints

- address naming problems
    - aliases for long constraints: `(A a, B a, C a, D b, E b, F b, G a b)`
    - refactoring Num into Additive, Multiplicative, FromInteger using
    super/subclasses requires rewriting programs that use Num
- address uniformity problems
    - constraints (contexts) on type classes: `Functor f` has `fmap :: (a -> b) -> f a -> f b`, but `Set.map` requires extra constraint `(Ord a, Ord b)`
- [http://lambda-the-ultimate.org/node/3670](http://lambda-the-ultimate.org/node/3670)

## Scala

scala

- users: yammer, twitter, linkedin, foursquare, the guardian, xerox, sony,
siemens, novell

PiLib: A Hosted language for Pi-Calculus Style Concurrency *

- TODO: review this and take notes

[akka](http://akkasource.org/): OTP for scala; actors, supervision, STM, dataflow vars

## Aspects

Verification for Aspects (Shmuel, CSAIL talk 08/18/08)

- aspect: same piece of code (*advice*) applied at many different places (*join points*)
    - set of join points is a *join cut*

## Dynamic Analysis

dhard (formerly dihard, umass)

- randomized heap allocation that's very spaced out
- run multiple replicas and compare their output

## Systems

"safe systems programming languages" TODO

BitC

Cyclone

Nitro: A low-level functional programming language

javascript monitoring & optimization (Joao, Ben Livshitz, MSR 09)

- improve web app load times by segmenting and lazily loading
- doloto training tool: like a wizard with 3 steps
    - training: collect runtime profile
    - clustering: group functions together
    - rewriting: stub out unnecessary code
- initial js size reduction: savings of 20-80%, usu. around 50%
- limitations
    - doloto caches transformed html; problem if dynamically generated
    - clustering may actually increase download size, because functions may be clustered together from different js files, and html pages may reference only particular js files
    - CDNs vs SOP: if the js and html are on a CDN, then the rewritten js references/does XHR to original site to get the cluster js; SOP disallows this
    - synchronous on-demand loading instead of background loading; fairly difficult to prevent concurrent events from executing
- released on 9/4/09 on devlabs; got good press coverage
- something about js events and event handling and cancellable events

automated end-to-end OS verif (Jean Yang, Chris Hawblitzel, MSR 09)

- SeL4: verified L4; 10 yrs, 13 ppl, mathematicians; 600 lines ARM asm
- compiled C# ukernel on top of trusted kernel
- used verified boogiepl; translated to c/asm
    - verified: thread ctx/switching, c-to-c#, c#-to-c, GC (prior work)
    - trusted base: stack switching is 2 lines asm (20-30 lines spec), mem acccesses, devices
- boogiecc translator translates verified boogiepl to generated c++/x86 (running on top of hand-impl trusted runtime)
    - checks for valid treatment of regs in asm, valid reqs for c/c# interface procs
- stack switches: ContinuationInvariant(stack ptr, instr ptr, stack base, stack lim, stack mem)
    - source stack requires wrt dst stack before switch, and vice versa after switch
- interface c/c#: c# needs to maintain stack, c needs to maintain heap

## Java

java 7

- project jigsaw
    - download footprint
    - jmod package manager
- da vinci project
    - invokedynamic
- more powerful hotswap

maxine research vm

- not quite as fast as hotspot
- tracing jit
- continuations: stack-tree implementation

noop: google lang with built-in DI

gwt

- idea: sort functions to cluster similar ones together and yield higher
huffman compression ratios, in the spirit of the burrows-wheeler transform
- implementation: sort by length then scan functions making best-so-far matches
using edit distance
- other tricks:
    - larger-base charset for var renames
    - rename from bottom-up scopes to reuse vars
    - stable ordering of renames (so always get f(a,b,c))

## Parallel Programming Languages

Sequoia

X10

ZPL

Chapel

- TODO crawl
    - [http://play.cs.berkeley.edu/wiki/cs294/fa07/index.php/Main_Page](http://play.cs.berkeley.edu/wiki/cs294/fa07/index.php/Main_Page)

## Dynamic Program Analysis

MUVI (Shan Lu, UIUC, SOSP07) *

AVIO (Shan Lu, UIUC, ASPLOS06) *

- introduction
    - most prev concurrency bug detection focuses on data races
    - atomicity (serializability) violations are as important as data races
    - TM will make atomicity more important than data races
    - data races are not always bugs
    - most prev techniques rely on specific sync semantics
- AVIO idea
    - exploit non-determinism
    - assumes that the program mostly runs OK

other work by Shan Lu

Black-box Tracing and Safe Parallelism : Tools for a Concurrent World (John
Jannotti, Computer Science/Brown University, CSAIL talk 2008-08-26)

After many years of predictions, developers must design for concurrency.
Nearly all general purpose computers sold today are multi-core, and many
applications are deployed as services running on clusters of cooperating
computers. We are developing two approaches to help developers keep pace with
these changes.

First, we have created BorderPatrol to extract causal request traces from
heterogeneous concurrent systems. BorderPatrol monitors message passing
between unmodified applications in order to aid development and debugging by
producing a "distributed stack trace". We have used BorderPatrol to trace a
litany of applications - apache, thttpd, PostgreSQL, TurboGears, BIND and
notably Zeus, a closed-source event-driven web server. BorderPatrol obtains
precise traces for black-box systems that cannot be traced by any other
technique.

While BorderPatrol aids in understanding and debugging existing systems, we
are also developing Elyze to aid in the creation of correct concurrent
applications in the first place. Elyze uses a conservative static analysis to
determine when code segments may safely run in parallel, and a custom runtime
scheduler that respects these constraints. The aim is to produce applications
that are safe by default. Elyze currently analyzes event-driven servers
written in C. For example, we have analyzed thttpd to extract previously
unavailable parallelism. We hope to generalize the approach to threaded
systems.

- tracing
    - replace syscalls with versions that yield one event/protocol request at a
    time
    - need to understand protocols
- elyse
    - determine coarse-grained parallelism
    - alias analysis using CIL

## Debugging

Beyond Time Travel Debugging (CSAIL talk, 2008-10-23)

- system is called "Chronicle"
- use valgrind for recording
- visualization:
    - time/space x/y axes; operations are on ranges of memory during instances of
    time (so you have a bunch of vertical bars in the graph)
    - issue spatial queries (eg what are the last operations that affected a
    certain memory range)
- techniques
    - divide space into pages
    - bunch up a consecutively written memory ranges into one continuous range
        
        |       |
        |  --> |
        |     |
        
        - detect this statically
    - compression
        - disk is slow
        - low-level execution very repetitive, predictable, compressible
        - preprocess data to improve compressibility: repetitive program behavior
        -> repetitive input to compressor
        - memory effect: (address, timestamp, data) -> (delta address, delta
        timestamp, data)
            - eg: memset(0) loop -> (4,k,0) each iteration
        - 20:1 compression of memory effect list (zlib)
    - register reconstruction
        - coarse-grained recording
        - divide time into epochs
        - record register state at start of epoch
        - record summary bitmap for regs changed in epoch
        - record code blocks executed in epoch
        - record register writes by each code block
        - except for simple common cases like R := R + k
        - compress delta tricks
- system
    - threads: valgrind, indexer, compression, compression, ...
    - can utilize many cores, many disks
    - balance by tuning compression speed vs. quality
- numbers
    - ff debug build startup + display web page
        - dual core x86 laptop
        - 1.2B instrs
        - 885MB DB (.74 B/instr)
        - 7.5 min (127X slowdown)
        - valgrind itself: 5-10X slowdown
    - CPU bottleneck with zlib max compression
    - disk bottleneck with hand-rolled LZ compressor (faster compression, 4X
    larger DB)
- found 1 real ff memory bug with this
- engineering: straightforward (spare time for 1-2 years, 3 months of solid
work)
    - valgrind tool: 2600 lines
    - indexer: 4900 lines
    - query engine: 12900 lines
    - 5000 for DWARF2: fetch debug info, evaluate variables, etc.
- history based stack reconstruction
    - stack memory hard to interpret (frame ptr/tail call opts), may be corrupt
    - idea: determine for time T which function calls have occurred that have not
    yet returned; this information is avail. in the history
    - "call": execution of `CALL` instruction or jump to function entry
    - "return": when the stack pointer's value > stack pointer's value on entry
    - incredibly robust, incredibly simple to implement! with a bit of help from
    the recorder
    - much simpler than what gdb has to do to cope with gcc's optimizations
- integration with VM replay
    - 30% overhead for recording -> small DB -> applying multiple Chronicle
    engines to it; stripe checkpoints over many machines, aggregate Chronicle
    DBs
    - key idea: you *can* replay with instrumentation without perturbing
    execution
    - VMWare is working on a tool for valgrind (no VM involved)
- omniscient profiling
    - fire timer/HW events during VM recording: do nothing
    - replay under Chronicle: all info avail with *original timings*
    - replay with subsystem simulation (cachegrind)
        - validate sim results against captured hardware counters
    - VM perturbation: can it be compensated for?
- related work
    - omniscient debugging (bil lewis)
    - TOD (u of chile)
    - nirvana (ms): record/replay debugger; probably in vs in few yrs
    - TTVM (umchi): VM approach to record/replay at OS level
    - time machine (green hills): seems to keep a rolling buffer of last million
    instrs; hard to tell given the
    - VMWare VM replay, VMWare-RR
- released on google code
- others' questions
    - support for other languages (high level debugging): eg GC can move objects
    around, you want to track the logical object not a physical location
        - you can do this from the history; may be easier by changing the runtime
        - someone extended Chronicle to support JS execution display as well
- questions
    - how long have you been with moz? what do you do there?
    - what did you work on here with dnj?
    - lots of researchy programming folks there? do you publish papers

Do You Have to Reproduce the Bug at the First Replay Attempt? -- PRES: Probabilistic Replay with Execution Sketching on Multiprocessors (Soyeon Park, UCSD/UIUC, SOSP09)

- problem: hard/slow to record for multi-processor replays
    - prev SW approaches record all input/scheduling info
    - HW: faster but only proposals exist
- insight: prev SW approaches try to record all exec path info to repro replay
in "1 try"
- soln: record less info and search for matching replay
    - maintain checkpoints, aborting/retrying differently on failure to match
    partial recording
- evaluated several levels of recording and effects on repro
- [system can generate input that causes certain exec path, but how useful is
this? what if we know we'd never receive certain inputs? specify add'l
constraints?]

# Philosophy

expert to expert: web programming, javascript with types, and flapjax (channel 9)

- working on types and javascript
    - but not typed js (don't want to type all of js), or type inference (algo);
    these are hopeless
    - want semantics
        - problem with C: no real semantics; semantics are "whatever you get from
        running the program"
        - all of verification is about redundancy; check that two specifications
        agree
        - TI should be viewed as a programming environment feature
    - js and js_t; program starts off in js; move parts gradually to js_t through
    "type enrichment"
        - cross-modular links: need some barrier that enforces guarantees across js
        and js_t
        - currently using contracts; eventually want to use other things like
        static flow analyses and verification tools
        - type inference: not scalable
        - flow analysis: worst-case fallback since need a *flow*, hence need to
        model entire DOM; not clear what polymorphism means here (FA just an
        abstraction over values)
    - idea: be able to take scripts to programs; a "continuum of guarantees"
    - research: ways to enforce contracts
        - crosses into IFC
- flapjax
    - have long designed languages for computation, not interaction
    - callbacks invert program structure
    - algorithmic language is not designed to be *notified* of events
    - when OS notifies us, we return, but OS doesn't are what we return
    - soln 1: design langs around interaction; composition of interactions;
    flapjax does this
    - soln 2: make OS smarter
        - OS conflates neutrality with hostility
        - world/universe programming model
        - can pass copies of the same world to several programs concurrently
        - worked on this for 5 years; was turned down several years ago
        - thinks it's better than monads, bc monads delimit non-functional parts
        - closer to concurrent clean than to haskell
    - flapjax: whose job is it to keep things consistent?
        - language's, not developer's
        - the OS is in control; actually, the outside world is the one in control,
        but OS is the proxy (what's visible to the program)
- [http://channel9.msdn.com/shows/Going+Deep/Expert-to-Expert-Web-Programming-with-Flapjax/](http://channel9.msdn.com/shows/Going+Deep/Expert-to-Expert-Web-Programming-with-Flapjax/)

barbara liskov's keynote at oopsla09 (same as turing award lecture)

- named influential papers
    - Djikstra - "Goto Statement Considered harmful" - which raised the innovative claim that programs ought to be easy to reason about, and language elements that made them hard to reason about were bad
    - Wirth - "Program development by stepwise refinement" - which pointed out that a program is a series of design decisions, and these decisions give rise to a family of related programs, corresponding to different design choices.
    - Parnas - "Information Distribution Aspects of Design Methodology". I haven't read this one! I thought I had read all his papers. It sounded a lot like his modularity paper. "The connections between modules are the assumptions that the modules make about each other."
    - Liskov - "A Design Methodology for Reliable Systems"
    - Balzer - Dataless Programming - I read this one a long time ago but I don't remember it and ought to read it again
    - Dahl and Hoare - Hiearchical Program Structures", in "Structured Programming" from 1972. She said there were three very good papers in this book. I think I have a copy, but I don't remember this paper.
    - Morris - "Protection in Programming Languages" - I don't think I read this one
    - Wolf and Shaw - "Global Variables Considerd Harmful"
    - Liskov and Zilles - "Programming with Abstract Datatypes"
    - Goodenough - "Exception Handling" Issues and a Proposed Notation"
- tom malone TODO

# fringe

coherence (jonathan edwards, onwards09)

- introduces *co-actions* (coherent actions)
- each reaction executed before all others that it has any side effects upon
    - setup: all reactive bindings are bidirectional
    - super useful in *callback hell*, eg GUI frameworks
    - eg: given `object{start,len,end=start+len}`
        - setting `start+=1;end+=2` is wrong; increments `end` by 3
            - must do `end+=2;start+=1`
            - changing impl to eg `start=end-len,len,end` requires diff order
- maintain to-do queue; execute in reverse order
    - in above example `start+=1;end+=2`
        - `end=end+2`
        - triggers `len=end-start`, pushing onto to-do queue; immed execute
        - `start=start+1`
        - R-W conflict on `start` with `len=end-start`, so undo triggered actions
        - redo `len=end-start`
- more than just firing all reactions at end; reactions must be ordered among
themselves as well
    - build up graph of reactions (reads -> writes) and sort topologically
    - thus focuses on constraint *discovery* (vs satisfaction)
- actually first discovered by [Trellis](http://pypi.python.org/pypi/Trellis)

roomy: disk-based computation (talk at MIT 12/21/09)

- transparently utilize disk as working space memory
- has 3 basic structs: array, list, hash table
    - sequential accesses are efficient and immediately satisfied
    - random accesses are delayed until they can be done in batch
- [what if he used B+ trees instead? then he could make LSM trees happen
transparently; would be neat]

# symbolic execution

KLEE (engler, osdi08)

- generate tests that achieve high coverage for real-world OS-heavy programs
- works with LLVM
- architecture
    - state = (storage, PC) where storage = (regs, stack, heap, ...)
    - KLEE may be executing many states at a time
    - select a state and interpret instr; loop till no states left or timeout
    - storage locations are expressions (trees) instead of raw values
        - leaves are symbolic vars/consts; interior are LLVM bytecode ops
    - uses STP constraint solver at branches
        - potentially dangerous instrs (eg ld/st, division) implicitly generate
        branches to check if any value could cause error
    - doesn't model one process-wide memory array, but as separate objects
        - dramatically improves perf by allowing obj sharing (using COW)
    - STP time dominates; simplify exprs & elim queries before STP
        - expr rewriting: as in compilers; arith simplif (`x+0=0`), strength
        reduction (`x*2**n=x<<n`), linear simplif (`2*x-x=x`)
        - constraint set simplification: add `x<10`, later `x=5`, so `true`
        - implied value concretization: `x+1==10` implies `x=9`
        - constraint independence: elim irrelevant constraints from query
        - counter-example cache: interesting map data structure that yields
        satisfying example assignments for constraints, but also works with
        supersets of these constraints
    - state scheduling heuristics
        - random path selection: binary tree records program path for all states;
        leaves are current states, interior are forks
            - randomly walk down the tree; this favors states high in tree
            - avoids starvation when some part is fork-bombing
        - coverage-optimized search: heuristics choose state likely to cover new
        code soon
            - heuristics: min dist to uncovered instr, call stack of state, whether
            state recently covered new code
- model environment
- eval: beat devs' coverage in coreutils, busybox; found 56 bugs in 430K LOC
- predecessor: EXE
    - used one process per state rather than internalized object-granularity repr

TODO

- catchconv
- stp (vijay ganesh; used by klee)
- tmp? tpm?
- dmolnar's work?
- what's used by armando/jeanyang?
- what's used by akcheung?
- what's used by the two guys working on auto exploit generation?

# static checking

first-order contracts

- this talk: compare the two types of contracts
    - latent contracts: contractrs and types (if any) are separate
    - manifest contracts: contracts *are* types
- dependent contracts: can relate input args to each other
- abusive contracts: contract is self-contradictory (eg require arg to be a Pos->Int but then pass it 0)
    - picky checking: complain about the contract
    - lax checking: don't complain about it
    - only higher order contracts can be abusive, since one contract is violating another contract
- TODO incomplete

Exterminator (zorn, msr, pldi07)

- intro
    - memory debuggers, eg purify/valgrind: slow
    - fail-stop systems, eg safec, suif ptr analysis: abort, need src, req
    gc/pool alloc for dangling pointers
    - failure-oblivious systems: same but manufacture read values and ignore bad
    writes
    - fault-tolerant systems: mask errors
        - logging/replaying inputs in an environment that pads alloc reqs and
        defers deallocs (rx)
        - randomization + optional voting-based replication to reduce chances of
        error having effect (diehard)
- software architecture
    - diehard overview
        - allocator: miniheaps of same-sized objs, bitmaps of free slots
            - overflows likely to clobber free slots (tolerate overflows)
            - unlikely to reuse recently freed slots (tolerate dangling ptrs)
        - process replicas vote on outputs
    - exterminator's heap layout
        - pad each obj with: obj seqno, alloc/dealloc sites, canary bitset (was obj
        filled with canaries?)
        - diefast
            - use free space as fence-posts
            - randomize canaries
            - read-only dangling ptr errs don't necessarily crash; write canaries
            with prob p to help isolate these errs (at expense of detection)
            - verify canaries on alloc and neighboring canaries on dealloc
    - modes of operation
        - iterative, replicated, cumulative
- reminders
    - safec: uses enhanced ptr/arr repr

<!--
vim: ft=markdown
-->