+++
title = "Learning software engineering"
date = 2026-01-04

[taxonomies]
tags = ["learning", "resources", "software-engineering"]

[extra]
toc = true
+++

Resources I've bookmarked for learning software engineering. Some I've actually used.

<!-- more -->

> [!WARNING]
> I haven't used all of these resources. Some are listed based on recommendations I've received, for my own future reference.

---

## Books

### Fundamentals
- [Structure and Interpretation of Computer Programs](https://mitpress.mit.edu/sites/default/files/sicp/index.html) - computational thinking
- [Code: The Hidden Language of Computer Hardware and Software](https://www.codehiddenlanguage.com/) - computers from first principles
- [Algorithms](https://algs4.cs.princeton.edu/) - algorithms and data structures
- [Types and Programming Languages](https://www.cis.upenn.edu/~bcpierce/tapl/) - type systems and formal semantics
- [The Linux Command Line](https://linuxcommand.org/tlcl.php) - shell scripting

### Systems
- [Operating Systems: Three Easy Pieces](https://pages.cs.wisc.edu/~remzi/OSTEP/) - OS fundamentals
- [Computer Systems: A Programmer's Perspective](https://csapp.cs.cmu.edu/) - how architecture affects your code
- [Computer Architecture: A Quantitative Approach](https://www.elsevier.com/books/computer-architecture/hennessy/978-0-12-811905-1) - Hennessy & Patterson
- [What Every Programmer Should Know About Memory](https://people.freebsd.org/~lstewart/articles/cpumemory.pdf) - memory hierarchies
- [Data-Oriented Design](https://www.dataorienteddesign.com/dodbook/) - cache-aware programming
- [Database Internals](https://www.databass.dev/) - storage engines and distributed databases
- [RISC-V ISA Specification](https://riscv.org/technical/specifications/)

### Languages
- [The Rust Programming Language](https://doc.rust-lang.org/book/) - systems programming with memory safety
- [The Rustonomicon](https://doc.rust-lang.org/nomicon/) - unsafe Rust
- [Programming in C](https://www.pearson.com/en-us/subject-catalog/p/programming-in-c/P200000003471)
- [Ziglearn](https://ziglearn.org/) - Zig tutorial
- [Jai Primer](https://github.com/BSVino/JaiPrimer/blob/master/JaiPrimer.md) - community guide to Jai

### Concurrency
- [Rust Atomics and Locks](https://marabos.nl/atomics/) - concurrency primitives, free online
- [The Art of Multiprocessor Programming](https://www.amazon.com/Art-Multiprocessor-Programming-Maurice-Herlihy/dp/0124159508) - concurrent data structures

### Architecture & design
- [Designing Data-Intensive Applications](https://dataintensive.net/) - distributed systems
- [Software Engineering at Google](https://abseil.io/resources/swe-book) - engineering at scale
- [A Philosophy of Software Design](https://web.stanford.edu/~ouster/cgi-bin/book.php) - managing complexity
- [Game Engine Architecture](https://www.gameenginebook.com/)

### Language implementation
- [Compilers: Principles, Techniques, and Tools](https://suif.stanford.edu/dragonbook/) - the Dragon Book
- [Crafting Interpreters](https://craftinginterpreters.com/) - building languages from scratch

---

## Courses

### Degree programs
- [Georgia Tech OMSCS](https://omscs.gatech.edu/) - online MS in CS (~$7k total), strong systems track
- [CU Boulder MS-CS on Coursera](https://www.coursera.org/degrees/ms-computer-science-boulder) - flexible online MS

### Self-paced
- [Computer, Enhance!](https://www.computerenhance.com/) - performance-aware programming (Casey Muratori)
- [CS Primer](https://csprimer.com/) - CS fundamentals with a systems focus

---

## Hands-on

### Build-your-own
- [CodeCrafters](https://codecrafters.io/) - build Redis, Git, SQLite from scratch
- [Ray Tracing In One Weekend](https://raytracing.github.io/)
- [Operating System in 1000 Lines](https://operating-system-in-1000-lines.vercel.app/en)
- [Build a Database in 3000 Lines](https://build-your-own.org/blog/20251015_db_in_3000/)
- [NES Emulator in Rust](https://bugzmanov.github.io/nes_ebook/)
- [Designing a Physics Engine in 5 minutes](https://www.youtube.com/watch?v=-_IspRG548E)

### Exercises
- [Rustlings](https://github.com/rust-lang/rustlings) - small exercises to learn Rust

---

## Videos & talks

### Performance
- [CPU Caches and Why You Care](https://www.youtube.com/watch?v=WDIkqP4JbkE) - Scott Meyers
- [What Every Programmer Should Know about How CPUs Work](https://www.youtube.com/watch?v=-HNpim5x-IE) - Matt Godbolt
- [A Practical Guide to Applying Data Oriented Design](https://www.youtube.com/watch?v=IroPQ150F6c) - Andrew Kelley
- [Practical Optimizations](https://www.youtube.com/watch?v=NAVbI1HIzCE) - Arseny Kapoulkine
- [The Big OOPs: Anatomy of a Thirty-five-year Mistake](https://www.youtube.com/watch?v=wo84LFzx5nI) - Casey Muratori
- [Spinlocks](https://youtube.com/playlist?list=PLxNPSjHT5qvsZyes3ATYvhPhwkoY33L2v) - Nick

### Compilers
- [What Has My Compiler Done for Me Lately?](https://www.youtube.com/watch?v=bSkpMdDe4g4) - Matt Godbolt
- [The Bits Between the Bits: How We Get to main()](https://www.youtube.com/watch?v=dOfucXtyEsU) - Matt Godbolt

### Other
- [From Blinky to Bootloader](https://www.youtube.com/playlist?list=PLP29wDx6QmW7HaCrRydOnxcy8QmW0SNdQ) - bare metal ARM programming

---

## Articles

### Memory & performance
- [Gallery of Processor Cache Effects](https://igoro.com/archive/gallery-of-processor-cache-effects/)
- [Myths Programmers Believe about CPU Caches](https://software.rajivprab.com/2018/04/29/myths-programmers-believe-about-cpu-caches/)
- [The Lost Art of Structure Packing](https://www.catb.org/esr/structure-packing/)
- [Handles vs Pointers](https://floooh.github.io/2018/06/17/handles-vs-pointers.html)
- [Memory access is O(N^[1/3])](https://vitalik.eth.limo/general/2025/10/05/memory13.html)
- [Untangling Lifetimes: The Arena Allocator](https://www.rfleury.com/p/untangling-lifetimes-the-arena-allocator)
- [Static Search Trees](https://curiouscoding.nl/posts/static-search-tree/) - cache-aware data structures

### Concurrency
- [Optimizing a Ring Buffer for Throughput](https://rigtorp.se/ringbuffer/) - lock-free SPSC queue
- [1024cores](https://sites.google.com/site/1024cores/home/lock-free-algorithms) - Dmitry Vyukov's lock-free algorithms
- [Futexes Are Tricky](https://www.kernel.org/doc/ols/2002/ols2002-pages-479-495.pdf) - Ulrich Drepper

### Other
- [Borrow checking, RC, GC, and the Eleven Other Memory Safety Approaches](https://verdagon.dev/grimoire/grimoire)
- [Linux IP Networking](https://www.cs.unh.edu/cnrg/people/gherrin/linux-net.html) - Linux protocol stack
- [Compiler Engineering in Practice](https://chisophugis.github.io/2025/12/08/compiler-engineering-in-practice-part-1-what-is-a-compiler.html)

---

## Tools

- [Compiler Explorer](https://godbolt.org/) - see what your code compiles to
- **gdb** - the GNU debugger
- **perf** - Linux profiling
- **valgrind** - memory debugging and profiling

---

## Blogs & channels

### Blogs
- [Julia Evans](https://jvns.ca/) - systems explainers
- [Dan Luu](https://danluu.com/) - performance and systems deep dives
- [Fabian Giesen](https://fgiesen.wordpress.com/) - graphics and performance
- [Brendan Gregg](https://www.brendangregg.com/blog/) - performance engineering
- [Easyperf](https://easyperf.net/) - CPU performance analysis
- [Travis Downs](https://travisdowns.github.io/) - microarchitecture
- [null program](https://nullprogram.com/) - systems programming in C
- [Nelson Elhage](https://blog.nelhage.com/) - systems and debugging
- [fasterthanlime](https://fasterthanli.me/) - Rust and systems
- [Random ASCII](https://randomascii.wordpress.com/) - debugging and performance
- [The Old New Thing](https://devblogs.microsoft.com/oldnewthing/) - Windows internals
- [purplesyringa](https://purplesyringa.moe/) - systems programming

### YouTube
- [Ben Eater](https://www.youtube.com/@BenEater) - hardware from first principles
- [Low Byte Productions](https://www.youtube.com/@LowByteProductions) - embedded and low-level
- [stacksmashing](https://www.youtube.com/@stacksmashing) - hardware hacking and reverse engineering
- [CoffeeBeforeArch](https://www.youtube.com/@CoffeeBeforeArch) - computer architecture
- [Jon Gjengset](https://www.youtube.com/@jonhoo) - Rust deep dives
- [Jonathan Blow](https://www.youtube.com/@jblow888) - game dev and language design
- [javidx9](https://www.youtube.com/@javidx9) - game development
- [Bisqwit](https://www.youtube.com/@Bisqwit) - retro programming
- [Tsoding](https://www.youtube.com/@Tsoding) - low-level programming
- [Nir Lichtman](https://www.youtube.com/@nirlichtman) - low-level programming
- [mCoding](https://www.youtube.com/@mCoding) - programming concepts
- [Sheafification of G](https://www.youtube.com/@SheafificationOfG) - math and CS theory
