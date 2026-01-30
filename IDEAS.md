# Blog Post Ideas

## Roadmap

1. **Type erasure** - Concept/model pattern, runtime polymorphism without inheritance
2. **CRTP** - Static polymorphism, vtables, devirtualization
3. **The compilation process** - Translation units, LTO, incremental compilation
4. **CPU caches** - L1/L2/L3 hierarchy, cache lines, associativity
5. **TLB and virtual memory** - Page tables, TLB misses, huge pages
6. **False sharing** - Cache line contention, benchmarking
7. **Spin-lock optimization** - Test-and-set variants, backoff, ticket locks
8. **Synchronization primitives** - Atomics, mutexes, futexes
9. **LRU cache implementations** - Cache-aware data structure design
10. **Ring buffers** - Lock-free SPSC, memory ordering, cache awareness

---

## 1. Type erasure

- The concept/model pattern
- Building a minimal `std::any` equivalent
- `std::function` internals
- Trade-offs vs trait objects in Rust

References:
- [Foonathan - Type Erasure](https://foonathan.net/2020/01/type-erasure/)
- [Arthur O'Dwyer - Back to Basics: Type Erasure (CppCon 2019)](https://www.youtube.com/watch?v=tbUCHifyT24)

## 2. CRTP

- Curiously Recurring Template Pattern in C++
- Static polymorphism: compile-time dispatch
- Virtual dispatch mechanics (vtable, vptr) for comparison
- Devirtualization: when compilers can optimize virtual calls
- Translating CRTP to Rust (generics + trait bounds)
- Assembly comparison (RISC-V)
- LTO and whole-program optimization effects

References:
- [Performance-Aware Programming - Casey Muratori](https://www.computerenhance.com/)
- [CppCon 2017 - Louis Dionne - Runtime Polymorphism](https://www.youtube.com/watch?v=gVGtNFg4ay0)
- [The Big OOPs: Anatomy of a Thirty-five-year Mistake - Casey Muratori (BSC 2025)](https://www.youtube.com/watch?v=wo84LFzx5nI)
- [Compiler Explorer](https://godbolt.org/)

## 3. The compilation process

- Preprocessing, compilation, assembly, linking
- Translation units and their implications
- LTO and cross-TU optimization
- Incremental compilation: how compilers avoid recompiling unchanged code
- RISC-V assembly examples

References:
- [Compiler Explorer](https://godbolt.org/)
- [Matt Godbolt - What Has My Compiler Done for Me Lately? (CppCon 2017)](https://www.youtube.com/watch?v=bSkpMdDe4g4)
- [Matt Godbolt - What Everyone Should Know About How Amazing Compilers Are (C++ on Sea 2019)](https://www.youtube.com/watch?v=w0sz5WbS5AM)
- [Matt Godbolt - The Bits Between the Bits: How We Get to main() (CppCon 2018)](https://www.youtube.com/watch?v=dOfucXtyEsU)
- [Diving into C Compilation Steps](https://www.youtube.com/watch?v=H6IAPC20Oqg)
- [Compilers: Principles, Techniques, and Tools (Dragon Book)](https://suif.stanford.edu/dragonbook/)
- [Crafting Interpreters](https://craftinginterpreters.com/)
- [Compiler Engineering in Practice](https://chisophugis.github.io/2025/12/08/compiler-engineering-in-practice-part-1-what-is-a-compiler.html)

## 4. CPU caches

- L1/L2/L3 hierarchy and typical latencies
- Cache lines: size, alignment, spatial locality
- Associativity: direct-mapped, set-associative, fully-associative
- Write policies: write-through vs write-back
- Cache coherency protocols (MESI)
- Benchmarking cache effects

References:
- [Gallery of Processor Cache Effects](https://igoro.com/archive/gallery-of-processor-cache-effects/)
- [What Every Programmer Should Know About Memory](https://people.freebsd.org/~lstewart/articles/cpumemory.pdf) - Sections 3-4
- [Matt Godbolt - What Every Programmer Should Know about How CPUs Work (GOTO 2024)](https://www.youtube.com/watch?v=-HNpim5x-IE)
- [CPU Caches and Why You Care - Scott Meyers (code::dive 2014)](https://www.youtube.com/watch?v=WDIkqP4JbkE)
- [Data-Oriented Design and C++ - Mike Acton (CppCon 2014)](https://www.youtube.com/watch?v=rX0ItVEVjHc)
- [Data-Oriented Design](https://www.dataorienteddesign.com/dodbook/)
- [Memory access is O(N^[1/3])](https://vitalik.eth.limo/general/2025/10/05/memory13.html)
- [Myths Programmers Believe about CPU Caches](https://software.rajivprab.com/2018/04/29/myths-programmers-believe-about-cpu-caches/)
- [Static Search Trees](https://curiouscoding.nl/posts/static-search-tree/) - Cache-aware data structure design

## 5. TLB and virtual memory

- Page tables and address translation
- TLB: structure, misses, and performance impact
- Huge pages: 2MB/1GB pages, when to use them
- ASLR and its overhead
- Benchmarking TLB effects

References:
- [What Every Programmer Should Know About Memory](https://people.freebsd.org/~lstewart/articles/cpumemory.pdf) - Section 4
- [LWN - Huge pages](https://lwn.net/Articles/374424/)
- [Operating Systems: Three Easy Pieces](https://pages.cs.wisc.edu/~remzi/OSTEP/)

## 6. False sharing

- Cache line contention between threads
- `alignas(std::hardware_destructive_interference_size)` equivalent in Rust
- Benchmarking the impact
- Padding strategies

References:
- [Gallery of Processor Cache Effects](https://igoro.com/archive/gallery-of-processor-cache-effects/) - Example 6
- [What Every Programmer Should Know About Memory](https://people.freebsd.org/~lstewart/articles/cpumemory.pdf) - Section 6
- [The Lost Art of Structure Packing](https://www.catb.org/esr/structure-packing/)
- [C++ Design Patterns for Low-latency Applications](https://arxiv.org/abs/2309.04259)

## 7. Spin-lock optimization

- Test-and-set: the naive approach
- Test-and-test-and-set: reducing cache line bouncing
- Exponential backoff: handling contention
- Ticket locks: fairness guarantees
- `spin_loop()` hint and CPU behavior
- Benchmarking with criterion, profiling with perf
- Latency analysis: P50 vs P99 tradeoffs across implementations

References:
- [Mara Bos - Rust Atomics and Locks](https://marabos.nl/atomics/) - Chapter 4
- [Nick - Spinlocks](https://youtube.com/playlist?list=PLxNPSjHT5qvsZyes3ATYvhPhwkoY33L2v)
- [The Art of Multiprocessor Programming](https://www.amazon.com/Art-Multiprocessor-Programming-Maurice-Herlihy/dp/0124159508) - Chapter 7
- [C++ Design Patterns for Low-latency Applications](https://arxiv.org/abs/2309.04259)

## 8. Synchronization primitives

- Atomics: compare-and-swap, fetch-add
- Mutexes: OS-level vs userspace
- Futexes: fast userspace mutexes
- Condition variables and semaphores
- Performance comparison

References:
- [Mara Bos - Rust Atomics and Locks](https://marabos.nl/atomics/) - Chapters 4-9
- [LWN - Futexes Are Tricky](https://www.kernel.org/doc/ols/2002/ols2002-pages-479-495.pdf)
- [Operating Systems: Three Easy Pieces](https://pages.cs.wisc.edu/~remzi/OSTEP/)

## 9. LRU cache implementations

- Naive: HashMap + doubly-linked list
- Array-based for cache locality
- Clock approximation algorithm
- Comparison: LRU vs LFU vs ARC

References:
- [Design and Implementation of a High-Performance LRU Cache (USENIX ATC '21)](https://www.usenix.org/conference/atc21/presentation/yang-juncheng)
- [Handles vs Pointers](https://floooh.github.io/2018/06/17/handles-vs-pointers.html)

## 10. Ring buffers in Rust

Progressive implementation from non-thread-safe to thread-safe:
1. Basic single-threaded ring buffer
2. Mutex-based thread-safe version
3. Lock-free with atomics
4. Memory ordering optimizations (acquire/release)
5. Index caching to reduce cache coherency traffic

Benchmarks: latency p50/p99, throughput, cache misses. RISC-V assembly analysis.

References:
- [rigtorp.se/ringbuffer](https://rigtorp.se/ringbuffer/) - Erik Rigtorp's lock-free SPSC queue
- [ultra-low-latency-ring-buffer](https://github.com/cale-cmd/ultra-low-latency-ring-buffer)
- [1024cores.net](https://www.1024cores.net/home/lock-free-algorithms/queues) - Dmitry Vyukov's lock-free queues
- [Mara Bos - Rust Atomics and Locks](https://marabos.nl/atomics/)
- [C++ Design Patterns for Low-latency Applications](https://arxiv.org/abs/2309.04259)

---

## Future ideas

- Memory allocators: bump, arena, pool, slab
  - [Untangling Lifetimes: The Arena Allocator](https://www.rfleury.com/p/untangling-lifetimes-the-arena-allocator)
- SIMD in Rust: auto-vectorization vs explicit intrinsics
- Branch prediction and branchless programming
- Cache-oblivious algorithms
- Lock-free data structures beyond ring buffers
- Memory ordering deep dive (seq_cst, acquire, release, relaxed)
- Inline assembly in Rust
  - [FFmpeg ASM Lessons](https://github.com/FFmpeg/asm-lessons)
- Profile-guided optimization (PGO)
- Zero-copy parsing
- Custom allocators with the Allocator API
- Memory safety approaches
  - [Borrow checking, RC, GC, and the Eleven (!) Other Memory Safety Approaches](https://verdagon.dev/grimoire/grimoire)
- Bare metal / embedded programming
  - [From Blinky to Bootloader: Bare Metal Programming Series](https://www.youtube.com/playlist?list=PLP29wDx6QmW7HaCrRydOnxcy8QmW0SNdQ)
  - [Introduction to Embedded Development with Rust](https://kerkour.com/introduction-to-embedded-development-with-rust)
  - [NES Emulator in Rust](https://bugzmanov.github.io/nes_ebook/)
- Ray tracing
  - [Ray Tracing In One Weekend Series](https://raytracing.github.io/)
- Linux networking internals
  - [Linux IP Networking](https://www.cs.unh.edu/cnrg/people/gherrin/linux-net.html)
- Data-oriented design
  - [Data-Oriented Design Book](https://www.dataorienteddesign.com/dodbook/)
  - [A Practical Guide to Applying Data Oriented Design - Andrew Kelley](https://www.youtube.com/watch?v=IroPQ150F6c)
  - [Practical Optimizations - Arseny Kapoulkine](https://www.youtube.com/watch?v=NAVbI1HIzCE)
- Rust deep dives
  - [Four Limitations of Rust's Borrow Checker](https://blog.polybdenum.com/2024/12/21/four-limitations-of-rust-s-borrow-checker.html)
  - [Pointers and Bytes](https://www.ralfj.de/blog/2018/07/24/pointers-and-bytes.html)
  - [Why Rust is Difficult](https://vorner.github.io/difficult.html)
- Performance optimization
  - [Abseil Performance Hints](https://abseil.io/fast/hints.html)
- Learning resources
  - [Low Level Academy](https://lowlevel.academy/)
  - [CS Primer](https://csprimer.com/)
  - [Boot.dev](https://www.boot.dev/)
  - [CodeCrafters](https://codecrafters.io/)
  - [Operating System in 1000 Lines](https://operating-system-in-1000-lines.vercel.app/en)
  - [Build a Database in 3000 Lines](https://build-your-own.org/blog/20251015_db_in_3000/)
