# Blog Post Ideas

## Roadmap

1. **CRTP and type erasure** - Translating C++ idioms to Rust
2. **Static vs dynamic polymorphism** - vtables, devirtualization, CRTP
3. **The compilation process** - Translation units, LTO, incremental compilation
4. **CPU caches** - L1/L2/L3 hierarchy, cache lines, associativity
5. **TLB and virtual memory** - Page tables, TLB misses, huge pages
6. **False sharing** - Cache line contention, benchmarking
7. **Spin-lock optimization** - Test-and-set variants, backoff, ticket locks
8. **Synchronization primitives** - Atomics, mutexes, futexes
9. **LRU cache implementations** - Cache-aware data structure design
10. **Ring buffers** - Lock-free SPSC, memory ordering, cache awareness

---

## 1. CRTP and type erasure in Rust

- Implementing C++'s Curiously Recurring Template Pattern
- Type erasure pattern (concept + model)
- Building a minimal `std::any` equivalent
- Trade-offs vs trait objects

References:
- [Foonathan - Type Erasure](https://foonathan.net/2020/01/type-erasure/)
- [Arthur O'Dwyer - Back to Basics: Type Erasure (CppCon 2019)](https://www.youtube.com/watch?v=tbUCHifyT24)

## 2. Static vs dynamic polymorphism

- Virtual dispatch mechanics (vtable, vptr)
- Devirtualization: when compilers can optimize virtual calls
- CRTP for static polymorphism
- Assembly comparison (RISC-V)
- LTO and whole-program optimization effects

References:
- [Performance-Aware Programming - Casey Muratori](https://www.computerenhance.com/)
- [CppCon 2017 - Louis Dionne - Runtime Polymorphism](https://www.youtube.com/watch?v=gVGtNFg4ay0)

## 3. The compilation process

- Preprocessing, compilation, assembly, linking
- Translation units and their implications
- LTO and cross-TU optimization
- RISC-V assembly examples

References:
- [Compiler Explorer](https://godbolt.org/)
- [Matt Godbolt - What Has My Compiler Done for Me Lately? (CppCon 2017)](https://www.youtube.com/watch?v=bSkpMdDe4g4)
- [Diving into C Compilation Steps](https://www.youtube.com/watch?v=H6IAPC20Oqg)

## 4. False sharing

- Cache line contention between threads
- `alignas(std::hardware_destructive_interference_size)` equivalent in Rust
- Benchmarking the impact
- Padding strategies

References:
- [Gallery of Processor Cache Effects](https://igoro.com/archive/gallery-of-processor-cache-effects/) - Example 6
- [What Every Programmer Should Know About Memory](https://people.freebsd.org/~lstewart/articles/cpumemory.pdf) - Section 6

## 5. Synchronization primitives

- Spinlocks: implementation and when to use
- Atomics: compare-and-swap, fetch-add
- Mutexes: OS-level vs userspace
- Futexes: fast userspace mutexes
- Condition variables and semaphores
- Performance comparison

References:
- [Mara Bos - Rust Atomics and Locks](https://marabos.nl/atomics/) - Chapters 4-9
- [LWN - Futexes Are Tricky](https://www.kernel.org/doc/ols/2002/ols2002-pages-479-495.pdf)

## 6. LRU cache implementations

- Naive: HashMap + doubly-linked list
- Array-based for cache locality
- Clock approximation algorithm
- Comparison: LRU vs LFU vs ARC

References:
- [Design and Implementation of a High-Performance LRU Cache (USENIX ATC '21)](https://www.usenix.org/conference/atc21/presentation/yang-juncheng)

## 7. Ring buffers in Rust

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

---

## Ideas from david-alvarez-rosa/personal-website

- **Exploring CPU caches** - L1/L2/L3 hierarchy, cache lines, associativity, write policies
- **Translation Lookaside Buffer (TLB)** - Virtual memory, page tables, TLB misses, huge pages
- **Spin-lock optimization** - Test-and-set, test-and-test-and-set, exponential backoff, ticket locks
- **Incremental compilation** - How compilers avoid recompiling unchanged code

---

## Future ideas

- Memory allocators: bump, arena, pool, slab
- SIMD in Rust: auto-vectorization vs explicit intrinsics
- Branch prediction and branchless programming
- Cache-oblivious algorithms
- Lock-free data structures beyond ring buffers
- Memory ordering deep dive (seq_cst, acquire, release, relaxed)
- Inline assembly in Rust
- Profile-guided optimization (PGO)
- Zero-copy parsing
- Custom allocators with the Allocator API
