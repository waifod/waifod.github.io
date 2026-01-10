+++
title = "Learning Software Engineering"
date = 2025-01-04

[taxonomies]
tags = ["learning", "resources", "software-engineering"]

[extra]
toc = true
+++

A curated collection of resources for learning software engineering fundamentals and best practices.

<!-- more -->

> [!WARNING]
> I haven't used all of these resources. Some are listed based on recommendations I've received, for my own future reference.

---

## Books

### Fundamentals
- [Structure and Interpretation of Computer Programs](https://mitpress.mit.edu/sites/default/files/sicp/index.html) - Classic introduction to computational thinking
- [Code: The Hidden Language of Computer Hardware and Software](https://www.codehiddenlanguage.com/) - Understanding computers from first principles
- [Algorithms](https://algs4.cs.princeton.edu/) - Core algorithms and data structures
- [Types and Programming Languages](https://www.cis.upenn.edu/~bcpierce/tapl/) - Type systems and formal semantics

### Systems & Architecture

**Low-level Systems**
- [Operating Systems: Three Easy Pieces](https://pages.cs.wisc.edu/~remzi/OSTEP/) - OS fundamentals
- [What Every Programmer Should Know About Memory](https://people.freebsd.org/~lstewart/articles/cpumemory.pdf) - Memory hierarchies and performance
- [Data-Oriented Design](https://www.dataorienteddesign.com/dodbook/) - Cache locality and memory optimization
- [Database Internals](https://www.databass.dev/) - How databases work under the hood
- [Programming in C](https://www.pearson.com/en-us/subject-catalog/p/programming-in-c/P200000003471)
- [The Rust Programming Language](https://doc.rust-lang.org/book/) - Modern systems programming with memory safety
- [RISC-V ISA Specification](https://riscv.org/technical/specifications/)

**Distributed Systems & Design**
- [Designing Data-Intensive Applications](https://dataintensive.net/) - Distributed systems and data architecture
- [Game Engine Architecture](https://www.gameenginebook.com/)
- [Software Engineering at Google](https://abseil.io/resources/swe-book) - Engineering practices at scale
- [A Philosophy of Software Design](https://web.stanford.edu/~ouster/cgi-bin/book.php) - Principles for managing complexity

### Language Implementation
- [Compilers: Principles, Techniques, and Tools](https://suif.stanford.edu/dragonbook/) - The Dragon Book
- [Crafting Interpreters](https://craftinginterpreters.com/) - Building programming languages from scratch

### Operations
- [The Linux Command Line](https://linuxcommand.org/tlcl.php) - Shell scripting and command-line usage

---

## Videos & Talks

- [From Blinky to Bootloader: Bare Metal Programming Series](https://www.youtube.com/playlist?list=PLP29wDx6QmW7HaCrRydOnxcy8QmW0SNdQ) - ARM Cortex-M embedded development
- [Data-Oriented Design and C++ - Mike Acton (CppCon 2014)](https://www.youtube.com/watch?v=rX0ItVEVjHc)
- [CPU Caches and Why You Care - Scott Meyers (code::dive 2014)](https://www.youtube.com/watch?v=WDIkqP4JbkE)
- [A Practical Guide to Applying Data Oriented Design - Andrew Kelley](https://www.youtube.com/watch?v=IroPQ150F6c)
- [Practical Optimizations - Arseny Kapoulkine](https://www.youtube.com/watch?v=NAVbI1HIzCE)
- [The Big OOPs: Anatomy of a Thirty-five-year Mistake - Casey Muratori (BSC 2025)](https://www.youtube.com/watch?v=wo84LFzx5nI)
- [Designing a Physics Engine in 5 minutes](https://www.youtube.com/watch?v=-_IspRG548E)

---

## Articles & Papers

### Performance & Memory
- [Gallery of Processor Cache Effects](https://igoro.com/archive/gallery-of-processor-cache-effects/)
- [The Lost Art of Structure Packing](https://www.catb.org/esr/structure-packing/) - Memory layout optimization
- [Handles vs Pointers](https://floooh.github.io/2018/06/17/handles-vs-pointers.html) - Memory indirection strategies
- [Memory access is O(N^[1/3])](https://vitalik.eth.limo/general/2025/10/05/memory13.html) - Real-world memory performance
- [C++ Design Patterns for Low-latency Applications](https://arxiv.org/abs/2309.04259) - Performance-critical patterns

### Networking
- [Linux IP Networking](https://www.cs.unh.edu/cnrg/people/gherrin/linux-net.html) - Guide to the Linux protocol stack implementation

---

## Courses & Practice

- [Computer, Enhance!](https://www.computerenhance.com/) - Performance-aware programming course
- [Ray Tracing In One Weekend Series](https://raytracing.github.io/) - Build a ray tracer from scratch
- [Rustlings](https://github.com/rust-lang/rustlings) - Small exercises to learn Rust

---

## Tools

- [Compiler Explorer](https://godbolt.org/) - See what your code compiles to
- **gdb** - The GNU debugger
- **valgrind** - Memory debugging and profiling
- **clang-tidy** - Static analysis
- **clang-format** - Code formatting

---

## Blogs & Channels

### Blogs
- [Random ASCII](https://randomascii.wordpress.com/) - Debugging and performance deep dives
- [The Old New Thing](https://devblogs.microsoft.com/oldnewthing/) - Windows internals and backwards compatibility
- [purplesyringa](https://purplesyringa.moe/) - Systems programming and low-level optimization

### YouTube Channels
- [Bisqwit](https://www.youtube.com/@Bisqwit) - Retro programming and algorithms
- [CoffeeBeforeArch](https://www.youtube.com/@CoffeeBeforeArch) - Computer architecture and performance
- [javidx9 - One Lone Coder](https://www.youtube.com/@javidx9) - Game development and graphics programming
- [mCoding](https://www.youtube.com/@mCoding) - Programming concepts across languages
- [Tsoding](https://www.youtube.com/@Tsoding) - Low-level programming and language implementation
