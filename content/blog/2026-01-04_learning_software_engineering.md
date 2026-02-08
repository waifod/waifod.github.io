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
- [Algorithms for Modern Hardware](https://en.algorithmica.org/hpc/) - cache-aware algorithms, free online
- [Database Internals](https://www.databass.dev/) - storage engines and distributed databases
- [RISC-V ISA Specification](https://riscv.org/technical/specifications/)

### Languages
- [The Rust Programming Language](https://doc.rust-lang.org/book/) - systems programming with memory safety
- [The Rustonomicon](https://doc.rust-lang.org/nomicon/) - unsafe Rust
- [Modern C](https://gustedt.gitlabpages.inria.fr/modern-c/) - Jens Gustedt, free online, covers C23
- [Ziglearn](https://ziglearn.org/) - Zig tutorial
- [Jai Primer](https://github.com/BSVino/JaiPrimer/blob/master/JaiPrimer.md) - community guide to Jai
- Execute Program TypeScript: [Basics](https://www.executeprogram.com/courses/typescript-basics), [Everyday](https://www.executeprogram.com/courses/everyday-typescript), [Advanced](https://www.executeprogram.com/courses/advanced-typescript) - interactive

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
- [MIT 6.172 Performance Engineering](https://www.youtube.com/playlist?list=PLUl4u3cNGP63VIBQVWguXxZZi0566y7Wf) - the MIT course on making code fast
- [Computer, Enhance!](https://www.computerenhance.com/) - performance-aware programming (Casey Muratori)
- [Linux Kernel Internals and Development (LFD420)](https://training.linuxfoundation.org/training/linux-kernel-internals-and-development/) - Linux Foundation
- [Embedded Linux Kernel and Driver Development](https://bootlin.com/training/kernel/) - kernel and driver development, free training materials
- [CS Primer](https://csprimer.com/) - CS fundamentals with a systems focus
- [From Blinky to Bootloader](https://www.youtube.com/playlist?list=PLP29wDx6QmW7HaCrRydOnxcy8QmW0SNdQ) - bare metal STM32, from blinking an LED to signed firmware updates

---

## Hands-on

### Build-your-own
- [CodeCrafters](https://codecrafters.io/) - build Redis, Git, SQLite from scratch
- [Writing an OS in Rust](https://os.phil-opp.com/) - the classic
- [Operating System in 1000 Lines](https://operating-system-in-1000-lines.vercel.app/en)
- [OS/161](http://os161.org/) - teaching operating system
- [Writing a JPEG Decoder in Rust](https://mht.wtf/post/jpeg-rust-1/) - surprisingly fun format to reverse-engineer
- [What's in a Linux Executable?](https://fasterthanli.me/series/making-our-own-executable-packer/part-1) - ELF deep dive (fasterthanlime)
- [Writing a Wayland Compositor](https://drewdevault.com/2018/02/17/Writing-a-Wayland-compositor-1.html) - Drew DeVault
- [FFmpeg Assembly Lessons](https://github.com/FFmpeg/asm-lessons/blob/main/lesson_01/index.md) - x86 assembly, from the FFmpeg project
- [Build a Database in 3000 Lines](https://build-your-own.org/blog/20251015_db_in_3000/)
- [NES Emulator in Rust](https://bugzmanov.github.io/nes_ebook/)
- [Ray Tracing In One Weekend](https://raytracing.github.io/)
- [Linux in a Pixel Shader](https://blog.pimaker.at/texts/rvc1/) - a RISC-V emulator that runs Linux inside VRChat, which is a sentence
- [Designing a Physics Engine in 5 minutes](https://www.youtube.com/watch?v=-_IspRG548E)

### Exercises
- [Rustlings](https://github.com/rust-lang/rustlings) - small exercises to learn Rust
- [8086 Assembler Tutorial](https://yassinebridi.github.io/asm-docs/asm_tutorial_01.html) - x86 assembly basics
- [git-katas](https://github.com/eficode-academy/git-katas) - deliberate practice for git
- [SQLBolt](https://sqlbolt.com/) - interactive SQL exercises

---

## Videos & talks

### Performance
- [CPU Caches and Why You Care](https://www.youtube.com/watch?v=WDIkqP4JbkE) - Scott Meyers
- [What Every Programmer Should Know about How CPUs Work](https://www.youtube.com/watch?v=-HNpim5x-IE) - Matt Godbolt
- [A Practical Guide to Applying Data Oriented Design](https://www.youtube.com/watch?v=IroPQ150F6c) - Andrew Kelley
- [Practical Optimizations](https://www.youtube.com/watch?v=NAVbI1HIzCE) - Arseny Kapoulkine
- [The Big OOPs: Anatomy of a Thirty-five-year Mistake](https://www.youtube.com/watch?v=wo84LFzx5nI) - Casey Muratori
- [Spinlocks](https://youtube.com/playlist?list=PLxNPSjHT5qvsZyes3ATYvhPhwkoY33L2v) - CoffeeBeforeArch

### Compilers
- [What Has My Compiler Done for Me Lately?](https://www.youtube.com/watch?v=bSkpMdDe4g4) - Matt Godbolt
- [The Bits Between the Bits: How We Get to main()](https://www.youtube.com/watch?v=dOfucXtyEsU) - Matt Godbolt
- [Advent of Compiler Optimisations 2025](https://www.youtube.com/playlist?list=PL2HVqYf7If8cY4wLk7JUQ2f0JXY_xMQm2) - Matt Godbolt

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
- [Pointers Are Complicated](https://www.ralfj.de/blog/2018/07/24/pointers-and-bytes.html) - Ralf Jung on pointer semantics
- [The RAM Myth](https://purplesyringa.moe/blog/the-ram-myth/) - memory is not what you think it is
- [abseil / Performance Hints](https://abseil.io/fast/hints.html) - practical optimization tips from Google

### Concurrency
- [Optimizing a Ring Buffer for Throughput](https://rigtorp.se/ringbuffer/) - lock-free SPSC queue
- [1024cores](https://sites.google.com/site/1024cores/home/lock-free-algorithms) - Dmitry Vyukov's lock-free algorithms
- [Basics of Futexes](https://eli.thegreenplace.net/2018/basics-of-futexes/) - Eli Bendersky
- [Futexes Are Tricky](https://www.kernel.org/doc/ols/2002/ols2002-pages-479-495.pdf) - Ulrich Drepper
- [Acquire-Release Semantics](https://davekilian.com/acquire-release.html) - memory ordering explained clearly
- [Linux-Kernel Memory Model](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2023/p0124r8.html) - the formal model
- [The Scoped Task Trilemma](https://without.boats/blog/the-scoped-task-trilemma/) - structured concurrency vs. borrowing vs. async cancellation

### Other
- [How Programs Get Run](https://lwn.net/Articles/630727/) - from fork to exec
- [Linux IP Networking](https://www.cs.unh.edu/cnrg/people/gherrin/linux-net.html) - Linux protocol stack
- [Compiler Engineering in Practice](https://chisophugis.github.io/2025/12/08/compiler-engineering-in-practice-part-1-what-is-a-compiler.html)
- [C++ Design Patterns for Low-Latency Applications](https://arxiv.org/pdf/2309.04259) - patterns from trading systems
- [Parse, Don't Validate](https://lexi-lambda.github.io/blog/2019/11/05/parse-don-t-validate/) - type-driven design
- [Three Ways Formally Verified Code Can Go Wrong](https://buttondown.com/hillelwayne/archive/three-ways-formally-verified-code-can-go-wrong-in/) - Hillel Wayne
- [Borrow checking, RC, GC, and the Eleven Other Memory Safety Approaches](https://verdagon.dev/grimoire/grimoire)
- [Four Limitations of Rust's Borrow Checker](https://blog.polybdenum.com/2024/12/21/four-limitations-of-rust-s-borrow-checker.html)
- [Why Is Rust Difficult?](https://vorner.github.io/difficult.html)
- [Rust Embedded Development](https://kerkour.com/introduction-to-embedded-development-with-rust) - getting started with embedded Rust
- [HVM](https://github.com/HigherOrderCO/HVM) - massively parallel functional runtime in Rust
- [A Defect-Tolerant Computer Architecture](https://fab.cba.mit.edu/classes/862.16/notes/computation/Heath-1998.pdf) - reconfigurable computing (Heath, 1998)
- [The Google File System](https://static.googleusercontent.com/media/research.google.com/en//archive/gfs-sosp2003.pdf) - the GFS paper (Ghemawat et al., 2003)
- [yarchive.net/comp](https://yarchive.net/comp/index.html) - archived Usenet posts from Linus Torvalds, Mitch Alsup, Terje Mathisen

---

## Tools

- [Compiler Explorer](https://godbolt.org/) - see what your code compiles to
- [Bootlin Elixir](https://elixir.bootlin.com/linux/v6.11.6/source) - Linux kernel source browser
- [CREATOR](https://creatorsim.github.io/) - RISC-V and MIPS assembly simulator
- [Vim Hero](https://www.vim-hero.com/) - interactive Vim lessons
- [awesome-shell](https://github.com/alebcay/awesome-shell) - curated list of CLI tools
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
- [Sheafification of G](https://www.youtube.com/@SheafificationOfG) - math and CS theory
