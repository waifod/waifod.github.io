+++
title = "Polymorphism in C++ and Rust: Virtual, Final, and CRTP"
date = 2026-01-26

[taxonomies]
tags = ["c++", "rust", "programming", "patterns"]

[extra]
toc = true
+++

In which I teach myself about the performance costs of runtime polymorphism and the alternatives, from simple to arcane.

<!-- more -->

## The problem

Sometimes a base class needs to know its derived type at compile time. Fluent builders that return `WidgetBuilder&` instead of `Builder&`. Per-class static data. Calling derived methods without vtable overhead.

Virtual functions don't help with the first two (they change behavior, not types), and they're overkill for the third when you know the concrete type at compile time.

Let's look at the options. Spoiler: if you only care about performance, skip to `final`. CRTP is for when the base class actually needs to *know* the derived type.

## Virtual functions: the default

Virtual functions are C++'s standard polymorphism mechanism:

```cpp
struct Shape {
    virtual ~Shape() = default;
    virtual double getArea() const = 0;
};

struct Square : Shape {
    double side_;
    double getArea() const override { return side_ * side_; }
};
```

When the compiler generates code for a call to `getArea()` through a `Shape*`, it doesn't know which implementation to call. It emits code that loads the vtable pointer from the object, indexes into the vtable, and performs an indirect call. This works for any derived type, but it has costs:

- One pointer load (vtable pointer from object)
- One indexed load (function pointer from vtable)
- One indirect call (branch to unknown address)
- Potential cache miss on the vtable
- Branch misprediction if the target varies
- No inlining across the indirect call

For hot paths, this adds up. More importantly, inlining enables further optimizations (constant propagation, dead code elimination, loop unrolling) that don't work across indirect calls.

There's also a memory cost: virtual classes embed a vtable pointer in each object. A `Square` with just `double side_` goes from 8 to 16 bytes with virtuals. This affects cache utilization and can matter more than dispatch overhead for small objects in tight loops.

That said, 3x slower on a microbenchmark doesn't mean 3x slower application. If you're calling a virtual function once per user click, the overhead is nanoseconds. The performance argument applies to tight loops, not general architecture.

## Restoring performance: the `final` keyword

C++11's `final` prevents a class from being inherited or a virtual function from being overridden. When the compiler sees a call on a `final` class, it knows no derived class could override the method, so it can devirtualize and potentially inline.

```cpp
struct SquareFinal final : Shape {
    double side_;
    double getArea() const override { return side_ * side_; }
};
```

A `SquareFinal&` can only refer to a `SquareFinal`, never a subclass (they can't exist), so the compiler can replace virtual dispatch with a direct call.

You can also mark individual methods `final` while leaving the class open:

```cpp
struct Square : Shape {
    double side_;
    double getArea() const override final { return side_ * side_; }
};
```

`final` helps with concrete types:

```cpp
std::vector<SquareFinal> squares;
for (const auto& s : squares) {
    total += s.getArea();  // Devirtualized
}
```

### When `final` doesn't help

Through base pointers:

```cpp
std::vector<std::unique_ptr<Shape>> shapes;
for (const auto& shape : shapes) {
    total += shape->getArea();  // Still virtual
}
```

The compiler only sees `Shape*`. It can't know the pointee type, so it must emit virtual dispatch. `final` on the derived class doesn't help here.

### The benchmarks

I ran benchmarks comparing direct calls, virtual dispatch, and `final` classes, iterating over 10000 shapes calling `getArea()`. Code is on [GitHub](https://github.com/waifod/code_samples/tree/main/2026-01-26_polymorphism_crtp). Machine is a [Hetzner Cloud CAX11](/blog/my-metal/#hetzner-cloud-cax11) (ARM Neoverse-N1), compiled with clang 21.1.8 at `-O2`. Full `perf stat` output is in the [appendix](#appendix-raw-benchmark-output).

```sh
$ perf stat \
    -e cycles,instructions,cache-misses,branch-misses \
    ./bench_direct \
    --benchmark_repetitions=50 \
    --benchmark_report_aggregates_only=true
```

| Benchmark | Mean (ns) | Ratio | Branch miss/iter | Cache miss/iter | Notes |
|-----------|-----------|-------|------------------|-----------------|-------|
| bmDirect | 8783 | 1.0x | 1.0 | 92 | Plain struct, `vector<Square>` |
| bmVirtualDirect | 24675 | 2.8x | 0.4 | 55 | `vector<Square>` with virtual base |
| bmVirtualHomogeneous | 25992 | 3.0x | 0.4 | 153 | `vector<unique_ptr<Shape>>`, all squares |
| bmFinalDirect | 7108 | 0.8x | 1.3 | 177 | `vector<SquareFinal>` |
| bmFinalHomogeneous | 25744 | 2.9x | 0.4 | 144 | `vector<unique_ptr<Shape>>`, all final squares |

The non-virtual baseline is ~3x faster than virtual dispatch. No vtable pointer means smaller objects (8 vs 16 bytes), better cache utilization, and the compiler can inline everything.

Curiously, bmFinalDirect (7108 ns) beats bmDirect (8783 ns) by 19%. The [assembly section](#the-assembly) explains why: auto-vectorization. The point stands that `final` eliminates virtual dispatch cost, but the specific numbers here are misleading for reasons we'll see later. The cache and branch miss numbers are also suspect: bmVirtualDirect shows *fewer* misses than bmDirect despite virtual dispatch involving indirect branches. This is because Google Benchmark [runs faster benchmarks for more iterations](https://github.com/google/benchmark/blob/main/docs/user_guide.md#runtime-and-reporting-considerations) to get stable timing. Since `perf stat` counts total events across the process, the "per iteration" numbers don't normalize properly: faster benchmarks accumulate more total events simply by running longer.

Without `final`, even iterating over a homogeneous `vector<Square>` (bmVirtualDirect) pays virtual dispatch cost. The compiler conservatively emits virtual calls because a subclass *could* exist. `final` tells the compiler "no subclasses," enabling devirtualization.

Through base pointers (bmVirtualHomogeneous, bmFinalHomogeneous), `final` provides no benefit. The compiler can't see through `Shape*`.

## CRTP: when you need more

The Curiously Recurring Template Pattern is more complex. The name alone should tell you something: if your pattern needs "curiously" in the title, you're in for a ride. Use it when `final` isn't enough, typically when you need type-aware base class functionality, not just performance.

### The pattern

A class inherits from a template instantiated with itself:

```cpp
template<typename Derived>
struct Shape {
    double getArea() const {
        return static_cast<const Derived*>(this)->area();
    }
};

struct Square : Shape<Square> {
    double side_;
    double area() const { return side_ * side_; }
};

struct Triangle : Shape<Triangle> {
    double base_, height_;
    double area() const { return 0.5 * base_ * height_; }
};
```

`Square` inherits from `Shape<Square>`. The base class knows its derived type at compile time, so it can call `area()` without virtual dispatch. The `static_cast<const Derived*>(this)` is a downcast, safe *only if* the inheritance is set up correctly. The cast assumes `this` points to a `Derived`, which is true when `Shape<Derived>` is a base of `Derived`. But the compiler can't verify this; it trusts you. Inherit `Shape<Square>` from `Triangle`, and the cast compiles fine but produces undefined behavior. No runtime overhead (unlike `dynamic_cast`), but no checking either. Safety comes from following the pattern correctly, not from the language enforcing it.

### Performance

CRTP matches direct calls:

| Benchmark | Mean (ns) | Ratio | Branch miss/iter | Cache miss/iter |
|-----------|-----------|-------|------------------|-----------------|
| bmDirect | 8783 | 1.0x | 1.0 | 92 |
| bmCrtp | 8765 | 1.0x | 1.0 | 91 |

The template machinery disappears at compile time, and the compiler *can* inline everything, producing the same code as writing `getArea()` directly in each class. But inlining isn't guaranteed; it depends on optimization level, function complexity, and compiler heuristics. For trivial functions at `-O2`, it's reliable. For complex implementations, check the assembly.

### When CRTP is actually useful

If you only want to avoid virtual dispatch, use `final`. It's simpler and eliminates the dispatch overhead. CRTP (or no inheritance) also avoids the vtable pointer overhead, which matters for cache-sensitive code with many small objects. But the real strength of CRTP is letting the base class know its derived type at compile time:

**Per-class static data:**

```cpp
template<typename Derived>
struct Counted {
    static inline int count = 0;
    Counted() { ++count; }
    ~Counted() { --count; }
};

struct Widget : Counted<Widget> {};
struct Gadget : Counted<Gadget> {};
// Widget::count and Gadget::count are independent
```

**Fluent interfaces that preserve the concrete type:**

```cpp
template<typename Derived>
struct Builder {
    std::string name_;
    
    Derived& set_name(std::string n) {
        name_ = std::move(n);
        return static_cast<Derived&>(*this);
    }
};

struct WidgetBuilder : Builder<WidgetBuilder> {
    int size_ = 0;
    
    WidgetBuilder& set_size(int s) { size_ = s; return *this; }
};

auto b = WidgetBuilder{}.set_name("foo").set_size(42);  // Chaining works
```

Without CRTP, `set_name()` would return `Builder&`, and you'd lose access to `set_size()`.

**Mixin classes:**

```cpp
template<typename Derived>
struct Printable {
    void print() const {
        std::cout << static_cast<const Derived*>(this)->to_string() << '\n';
    }
};

template<typename Derived>
struct Comparable {
    bool operator!=(const Derived& other) const {
        return !(static_cast<const Derived&>(*this) == other);
    }
};

struct Widget : Printable<Widget>, Comparable<Widget> {
    std::string name_;
    std::string to_string() const { return name_; }
    bool operator==(const Widget& other) const { return name_ == other.name_; }
};
```

CRTP lets you compose functionality from multiple bases, each adding methods that depend on the derived type. `std::enable_shared_from_this` uses this pattern.

**Pre-C++20 interface enforcement:**

Before concepts, CRTP could enforce interfaces at compile time while providing default implementations using the derived type. Concepts can verify a type has certain methods, but can't *add* methods. CRTP does both: checks that `Derived` has `area()` (by calling it) and provides `getArea()` that uses it.

### The downsides

CRTP has rough edges.

1. **Confusing syntax**: `class Derived : Base<Derived>` takes a moment to parse.
2. **No heterogeneous collections**: each `Shape<Derived>` is a different type. You can't store `Shape<Square>` and `Shape<Triangle>` together without a common base, which brings back virtuals.
3. **Composition problems**: multiple CRTP bases can lead to diamond inheritance.
4. **Compile-time costs**: each instantiation generates a separate class. Many derived types means bloated compile times and binary size.
5. **Easy to misuse**: nothing stops you from writing `class Wrong : Shape<SomeOtherClass>`, which compiles but invokes UB when `getArea()` is called. A private constructor with friend access catches *some* mistakes:

```cpp
template<typename Derived>
struct Shape {
private:
    Shape() = default;
    friend Derived;
    
public:
    double getArea() const {
        return static_cast<const Derived*>(this)->area();
    }
};
```

Now only `Derived` can construct a `Shape<Derived>`. This catches `class Wrong : Shape<Right>` because `Wrong` can't access `Shape<Right>`'s constructor. It's not a complete solution though:

- It doesn't catch `class Wrong : Shape<Wrong>, Shape<Right>` (multiple inheritance)
- It doesn't help if `Derived` has a user-defined constructor that calls the base
- It adds complexity for partial protection

Still, it catches the most common copy-paste error:

```cpp
// crtp_safety_test.cpp
struct Right : Shape<Right> {
    double area() const { return 0.0; }
};

struct Wrong : Shape<Right> {  // Oops, meant Shape<Wrong>
    double area() const { return 0.0; }
};

int main() {
    Right r;   // OK
    Wrong w;   // Error
}
```

```sh
$ clang++ -std=c++20 crtp_safety_test.cpp
crtp_safety_test.cpp:27:11: error: call to implicitly-deleted default constructor of 'Wrong'
   27 |     Wrong w;
      |           ^
crtp_safety_test.cpp:21:16: note: default constructor of 'Wrong' is implicitly deleted because
                                 base class 'Shape<Right>' has an inaccessible default constructor
   21 | struct Wrong : Shape<Right> {
      |                ^
1 error generated.
```

## The assembly

To see what the compiler actually generates, compile with `-S`:

```sh
$ clang++ -O2 -std=c++20 -march=native -S bench_direct.cpp -o bench_direct.s
```

Here's the direct (non-polymorphic) hot loop from the benchmark (ARM64):

```asm
// from bench_direct.s
.LBB0_29:                               // =>This Inner Loop Header: Depth=2
        ldr     d1, [x10, #8]!          // Load side from vector, advance pointer
        cmp     x10, x22                // Check end
        fmul    d1, d1, d1              // side * side
        fadd    d0, d0, d1              // total += area
        str     d0, [sp, #40]           // Store for DoNotOptimize (!)
        b.ne    .LBB0_29                // Loop
```

Six instructions: load, compare, multiply, add, store, branch. The call to `getArea()` is fully inlined; there's no function call at all. Note the `str` instruction: that's Google Benchmark's `DoNotOptimize` forcing a store every iteration to prevent the compiler from optimizing away the accumulator. This extra store likely inhibits vectorization.

The CRTP version generates identical code (verified by comparing `bench_crtp.s`); the template machinery disappears entirely.

### Why bmFinalDirect is faster than bmDirect

Remember the anomaly from the benchmarks: `final` (7108 ns) beat the non-polymorphic baseline (8783 ns) by 19%. We're about to spend several paragraphs figuring out why. The assembly shows the answer: bmFinalDirect vectorized while bmDirect didn't.

Here's the bmFinalDirect hot loop:

```asm
// from bench_final.s
.LBB0_30:                                   // =>This Inner Loop Header: Depth=2
        sub     x15, x14, #32
        subs    x13, x13, #4
        ld2     { v1.2d, v2.2d }, [x15]     // Deinterleaved load: 4 doubles (2 objects)
        fmul    v1.2d, v1.2d, v1.2d         // SIMD multiply: 2 squares at once
        ld2     { v2.2d, v3.2d }, [x14]     // Load 4 more doubles (2 more objects)
        add     x14, x14, #64
        mov     d4, v1.d[1]
        fadd    d0, d0, d1                  // Accumulate
        fmul    v1.2d, v2.2d, v2.2d         // SIMD multiply
        fadd    d0, d0, d4
        mov     d2, v1.d[1]
        fadd    d0, d0, d1
        fadd    d0, d0, d2
        b.ne    .LBB0_30
// After the loop:
.LBB0_34:
        str     d0, [sp, #40]               // DoNotOptimize store happens ONCE, outside the loop
```

The `ld2` instruction does a deinterleaved SIMD load (separating the vtable pointers from the doubles), and `fmul v1.2d, v1.2d, v1.2d` multiplies two doubles in parallel. It's processing 4 elements per iteration. Crucially, the `DoNotOptimize` store was hoisted outside the loop.

Compare this to the bmDirect loop above: the `str` happens every iteration, which likely prevents vectorization. But why did the compiler hoist the store for `final` and not for `direct`? Both benchmarks use the same `DoNotOptimize(total)` pattern. My best guess is that the different object layouts (16-byte `SquareFinal` vs 8-byte `Square`) led the optimizer down different code paths. Compiler heuristics are fickle.

To confirm this is a benchmark artifact, let's remove the harness entirely:

```cpp
#include <cstddef>
#include <random>
#include <vector>

constexpr std::size_t kNumShapes = 10000;

struct Square {
    double side_;
    Square(double s) : side_(s) {}
    double getArea() const { return side_ * side_; }
};

// noinline prevents the compiler from inlining sumAreas into main,
// so we can inspect the loop in isolation in the assembly
__attribute__((noinline))
double sumAreas(const std::vector<Square>& squares) {
    double total = 0.0;
    for (const auto& s : squares) {
        total += s.getArea();
    }
    return total;
}

int main() {
    std::mt19937 rng(42);
    std::uniform_real_distribution<double> dist(1.0, 10.0);

    std::vector<Square> squares;
    for (std::size_t i = 0; i < kNumShapes; ++i) {
        squares.emplace_back(dist(rng));
    }

    // volatile prevents the compiler from optimizing away the call entirely
    volatile double result = sumAreas(squares);
}
```

Sure enough, the compiler vectorizes it:

```asm
// from direct_standalone.s
.LBB0_5:                                // =>This Inner Loop Header: Depth=1
        ldp     q1, q2, [x12, #-16]     // Load 4 doubles
        subs    x13, x13, #4            // Process 4 per iteration
        add     x12, x12, #32
        fmul    v1.2d, v1.2d, v1.2d     // SIMD square
        mov     d3, v1.d[1]
        fadd    d0, d0, d1
        fmul    v1.2d, v2.2d, v2.2d     // SIMD square
        fadd    d0, d0, d3
        mov     d2, v1.d[1]
        fadd    d0, d0, d1
        fadd    d0, d0, d2
        b.ne    .LBB0_5
```

Same vectorization pattern: `ldp` loads 4 doubles, `fmul v1.2d` squares two at a time. The 8-byte `Square` vectorizes fine without the benchmark framework. Note the simpler `ldp` (paired load) versus the `ld2` (deinterleaved load) in bmFinalDirect: with 8-byte objects the doubles are contiguous, so no deinterleaving is needed. This also means better cache efficiency: 8 doubles fit in a 64-byte cache line versus only 4 objects when each carries a vtable pointer.

The lesson: microbenchmarks can mislead in subtle ways. The performance difference here is a Google Benchmark artifact, not an inherent property of `final` vs non-polymorphic code. The qualitative conclusions (virtual dispatch costs ~3x, `final` eliminates that cost) still hold.

### Virtual dispatch through pointers

Here's what virtual dispatch looks like through `unique_ptr`:

```asm
// from bench_virtual.s
.LBB1_2:                                // =>This Inner Loop Header: Depth=1
        ldr     x8, [x21], #16          // Load unique_ptr (Shape*), advance by 16
        ldr     x8, [x8]                // Load vtable pointer from object
        blr     x8                      // Indirect call to first vtable entry
        cmp     x21, x20                // Check end
        mov     x0, x21
        b.ne    .LBB1_2                 // Loop
```

The `blr x8` is the performance killer: an indirect call to whatever address is in `x8`. The CPU can't know the target until it completes the load chain (pointer -> vtable pointer -> function pointer), so it speculates. Modern CPUs have indirect branch predictors that learn patterns, which is why the homogeneous case (~1 branch miss per iteration) isn't catastrophic: the predictor learns "it's always `Square::getArea`" and guesses correctly.

But look at the heterogeneous benchmark below: ~5000 branch misses per iteration, compared to ~1 for homogeneous. With a random 50/50 mix of squares and triangles, the predictor can't learn a pattern. Each misprediction flushes the pipeline and stalls execution until the correct target is resolved. That's why heterogeneous dispatch is 7x slower than the baseline, not 3x: the branch predictor penalty dominates.

The other cost is subtler: the CPU can't speculatively execute *past* the indirect call until it knows the target. With a direct call, the CPU knows where execution will continue and can fetch/decode those instructions in parallel. With `blr x8`, it's blocked until `x8` is resolved.

## Heterogeneous collections

Out of curiosity, I tested randomly mixing squares and triangles:

| Benchmark | Mean (ns) | Ratio | Branch miss/iter | Cache miss/iter |
|-----------|-----------|-------|------------------|-----------------|
| bmDirect | 8783 | 1.0x | 1.0 | 92 |
| bmVirtualHeterogeneous | 60911 | 6.9x | 4950 | 43 |
| bmFinalHeterogeneous | 61397 | 7.0x | 5014 | 46 |

~7x slower than the direct baseline, but the comparison is unfair. **You can't do this without virtual dispatch.** Each `Shape<Derived>` is a different type, so you can't mix them without a common base class, which brings back virtual functions. The 7x shows the cost of genuine runtime polymorphism when the branch predictor can't learn a pattern, not a static alternative you're ignoring.

Note that `final` provides no benefit here: the compiler can't see through `Shape*` to know the concrete type, so it emits virtual dispatch regardless.

Static polymorphism's fundamental limitation: you trade runtime flexibility for compile-time performance. If your design genuinely requires heterogeneous collections, virtual dispatch is the right tool. CRTP and `final` are optimizations for when you *don't* need that flexibility.

## Rust's approach

Rust doesn't have CRTP because it doesn't need it. Traits with default implementations provide the same functionality:

```rust
trait Shape {
    fn area(&self) -> f64;
    
    fn get_area(&self) -> f64 {
        self.area()
    }
}

struct Square { side: f64 }

impl Shape for Square {
    fn area(&self) -> f64 { self.side * self.side }
}
```

The trait defines `get_area()` with a default implementation that calls `area()`. Types implementing the trait only need to provide `area()`. Same result as CRTP, cleaner syntax.

Trait methods are statically dispatched unless you explicitly opt into dynamic dispatch with `dyn Trait`. When you call `s.get_area()` on a concrete type, the compiler knows the type and inlines the call. This static dispatch uses monomorphization: the compiler generates specialized code for each concrete type, just like C++ templates. The tradeoff is the same: zero runtime overhead, but increased compile times and binary size when you have many concrete types.

When you *do* need runtime polymorphism:

```rust
fn total_area(shapes: &[&dyn Shape]) -> f64 {
    shapes.iter().map(|s| s.get_area()).sum()  // Dynamic dispatch
}
```

This pays similar costs to C++ virtual dispatch.

### Memory layout differences

Rust's trait objects are "fat pointers": two pointers bundled together (one to the data, one to the vtable). C++ embeds the vtable pointer in the object itself. This has concrete implications:

```rust
use std::mem::size_of;

struct Square { side: f64 }

// Square itself: 8 bytes (just the f64)
// &Square: 8 bytes (one pointer)
// &dyn Shape: 16 bytes (data ptr + vtable ptr, both in the reference)
// Box<dyn Shape>: 16 bytes (same fat pointer layout)
```

The vtable pointer lives in the `Box<dyn Shape>`, not in the `Square`. The `Square` on the heap is still just 8 bytes.

Compare to C++:

```cpp
struct Square { double side_; };                    // 8 bytes
struct VirtualSquare : Shape { double side_; };     // 16 bytes (vtable ptr embedded in object)
Square* p;                                          // 8 bytes, points to 8-byte object
VirtualSquare* vp;                                  // 8 bytes, points to 16-byte object
```

In C++, the vtable pointer is inside the object, so `VirtualSquare` is 16 bytes even though it only has one `double` field.

This affects collections: Rust's `Vec<Box<dyn Shape>>` stores 16-byte fat pointers to 8-byte objects, while C++'s `vector<unique_ptr<Shape>>` stores 8-byte pointers to 16-byte objects. Same total memory (24 bytes per element), different cache behavior. When iterating, Rust loads 16 bytes per element from the vector, then chases one pointer to the 8-byte object. C++ loads 8 bytes per element, then chases one pointer to a 16-byte object. Rust's approach keeps objects smaller and potentially more cache-friendly when you're accessing the objects themselves; C++ keeps the pointer array denser, which matters when you're scanning pointers without dereferencing. In practice, the difference is usually negligible unless you're in a very tight loop. With C++ multiple inheritance, objects can have multiple vtable pointers, making the comparison more nuanced.

### Fluent builders without CRTP

The fluent builder pattern (where methods return the concrete type for chaining) doesn't require CRTP in Rust. The `Self` type handles it:

```rust
struct WidgetBuilder {
    name: String,
    size: i32,
}

impl WidgetBuilder {
    fn new() -> Self {
        Self { name: String::new(), size: 0 }
    }
    
    fn set_name(mut self, name: impl Into<String>) -> Self {
        self.name = name.into();
        self
    }
    
    fn set_size(mut self, size: i32) -> Self {
        self.size = size;
        self
    }
}

let builder = WidgetBuilder::new().set_name("foo").set_size(42);
```

If you want shared builder behavior across types, you can use a trait with `Self`:

```rust
trait BuilderExt: Sized {
    fn name_mut(&mut self) -> &mut String;
    
    fn set_name(mut self, name: impl Into<String>) -> Self {
        *self.name_mut() = name.into();
        self
    }
}
```

This isn't a perfect equivalent to CRTP (you can't easily share field access without additional machinery), but it covers the common cases without template recursion.

### Extension traits: more powerful than CRTP mixins

Unlike CRTP, Rust traits can be added to types you don't control, after the fact (though the [orphan rule](https://doc.rust-lang.org/reference/items/implementations.html#orphan-rules) sometimes requires workarounds):

```rust
// CRTP mixin in C++ requires inheritance at definition time:
// struct Widget : Printable<Widget> { ... };

// Rust extension trait:
trait Printable: std::fmt::Display {
    fn print(&self) {
        println!("{}", self);
    }
}

// Blanket impl: ANY type implementing Display gets print() for free
impl<T: std::fmt::Display> Printable for T {}

// No inheritance required: String, i32, your custom types all get print()
fn demo() {
    "hello".print();
    42.print();
}
```

Extension traits are genuinely more powerful. You can extend the standard library, third-party crates, or your own types uniformly. With CRTP, you'd have to modify every type's inheritance hierarchy.

The key difference: Rust makes static dispatch the default and dynamic dispatch opt-in. C++ virtual functions are dynamic by default.

## Conclusion

Virtual functions are the default for good reason: they're flexible and the overhead rarely matters. When performance matters and you have concrete types, `final` is the first thing to try. It's boring, it works, and you can explain it to your coworkers.

CRTP is for when you need the base class to know the derived type. It's not primarily a performance optimization; it's a pattern for type-aware base classes that happens to avoid virtual dispatch. If you find yourself reaching for it just to avoid vtable overhead, you've probably overcomplicated things.

Rust's traits provide all of this more cleanly: default implementations, static dispatch by default, extensibility without inheritance. The CRTP pattern simply isn't needed.

## Appendix: raw benchmark output

For nerds who don't trust summary tables.

Machine: [Hetzner Cloud CAX11](/blog/my-metal/#hetzner-cloud-cax11) (ARM Neoverse-N1, 2 vCPUs @ 2.5GHz), Fedora 43, clang 21.1.8.

### Direct

```sh
$ perf stat \
    -e cycles,instructions,cache-misses,branch-misses \
    ./bench_direct \
    --benchmark_repetitions=50 \
    --benchmark_report_aggregates_only=true
------------------------------------------------------------
Benchmark                  Time             CPU   Iterations
------------------------------------------------------------
bmDirect_mean           8783 ns         8753 ns           50
bmDirect_median         8773 ns         8744 ns           50
bmDirect_stddev         32.5 ns         31.0 ns           50
bmDirect_cv             0.37 %          0.35 %            50

 Performance counter stats for './bench_direct [...]':

   104,064,362,763      cycles:u
   240,684,872,065      instructions:u            #    2.31  insn per cycle
       376,541,888      cache-misses:u
         4,081,565      branch-misses:u

      35.236795742 seconds time elapsed
```

### Virtual

```sh
$ perf stat \
    -e cycles,instructions,cache-misses,branch-misses \
    ./bench_virtual \
    --benchmark_repetitions=50 \
    --benchmark_report_aggregates_only=true \
    --benchmark_filter=bmVirtualDirect
-----------------------------------------------------------------
Benchmark                       Time             CPU   Iterations
-----------------------------------------------------------------
bmVirtualDirect_mean        24675 ns        24591 ns           50
bmVirtualDirect_median      24678 ns        24592 ns           50
bmVirtualDirect_stddev       29.7 ns         27.2 ns           50
bmVirtualDirect_cv           0.12 %          0.11 %            50

 Performance counter stats for './bench_virtual [...]':

   104,903,758,723      cycles:u
   186,846,046,795      instructions:u            #    1.78  insn per cycle
       225,974,315      cache-misses:u
         1,512,763      branch-misses:u

      35.496489852 seconds time elapsed
```

```sh
$ perf stat \
    -e cycles,instructions,cache-misses,branch-misses \
    ./bench_virtual \
    --benchmark_repetitions=50 \
    --benchmark_report_aggregates_only=true \
    --benchmark_filter=bmVirtualHomogeneous
----------------------------------------------------------------------
Benchmark                            Time             CPU   Iterations
----------------------------------------------------------------------
bmVirtualHomogeneous_mean        25992 ns        25879 ns           50
bmVirtualHomogeneous_median      25987 ns        25866 ns           50
bmVirtualHomogeneous_stddev       44.2 ns         40.5 ns           50
bmVirtualHomogeneous_cv           0.17 %          0.16 %            50

 Performance counter stats for './bench_virtual [...]':

   104,791,163,245      cycles:u
   164,210,116,554      instructions:u            #    1.57  insn per cycle
       629,148,412      cache-misses:u
         1,458,236      branch-misses:u

      35.548482083 seconds time elapsed
```

```sh
$ perf stat \
    -e cycles,instructions,cache-misses,branch-misses \
    ./bench_virtual \
    --benchmark_repetitions=50 \
    --benchmark_report_aggregates_only=true \
    --benchmark_filter=bmVirtualHeterogeneous
------------------------------------------------------------------------
Benchmark                              Time             CPU   Iterations
------------------------------------------------------------------------
bmVirtualHeterogeneous_mean        60911 ns        60617 ns           50
bmVirtualHeterogeneous_median      60868 ns        60578 ns           50
bmVirtualHeterogeneous_stddev        247 ns          237 ns           50
bmVirtualHeterogeneous_cv           0.41 %          0.39 %            50

 Performance counter stats for './bench_virtual [...]':

   103,195,442,604      cycles:u
    75,236,253,650      instructions:u            #    0.73  insn per cycle
       249,445,342      cache-misses:u
     2,846,025,503      branch-misses:u

      35.154729549 seconds time elapsed
```

### Final

```sh
$ perf stat \
    -e cycles,instructions,cache-misses,branch-misses \
    ./bench_final \
    --benchmark_repetitions=50 \
    --benchmark_report_aggregates_only=true \
    --benchmark_filter=bmFinalDirect
---------------------------------------------------------------
Benchmark                     Time             CPU   Iterations
---------------------------------------------------------------
bmFinalDirect_mean         7108 ns         7084 ns           50
bmFinalDirect_median       7102 ns         7078 ns           50
bmFinalDirect_stddev       20.4 ns         19.7 ns           50
bmFinalDirect_cv           0.29 %          0.28 %            50

 Performance counter stats for './bench_final [...]':

   102,930,322,685      cycles:u
   171,631,602,734      instructions:u            #    1.67  insn per cycle
       726,534,697      cache-misses:u
         5,517,593      branch-misses:u

      34.847815132 seconds time elapsed
```

```sh
$ perf stat \
    -e cycles,instructions,cache-misses,branch-misses \
    ./bench_final \
    --benchmark_repetitions=50 \
    --benchmark_report_aggregates_only=true \
    --benchmark_filter=bmFinalHomogeneous
--------------------------------------------------------------------
Benchmark                          Time             CPU   Iterations
--------------------------------------------------------------------
bmFinalHomogeneous_mean        25744 ns        25642 ns           50
bmFinalHomogeneous_median      25751 ns        25650 ns           50
bmFinalHomogeneous_stddev       45.4 ns         43.1 ns           50
bmFinalHomogeneous_cv           0.18 %          0.17 %            50

 Performance counter stats for './bench_final [...]':

   104,235,049,545      cycles:u
   164,972,163,246      instructions:u            #    1.58  insn per cycle
       592,234,866      cache-misses:u
         1,461,272      branch-misses:u

      35.375672852 seconds time elapsed
```

```sh
$ perf stat \
    -e cycles,instructions,cache-misses,branch-misses \
    ./bench_final \
    --benchmark_repetitions=50 \
    --benchmark_report_aggregates_only=true \
    --benchmark_filter=bmFinalHeterogeneous
----------------------------------------------------------------------
Benchmark                            Time             CPU   Iterations
----------------------------------------------------------------------
bmFinalHeterogeneous_mean        61397 ns        61117 ns           50
bmFinalHeterogeneous_median      61328 ns        61055 ns           50
bmFinalHeterogeneous_stddev        194 ns          177 ns           50
bmFinalHeterogeneous_cv           0.32 %          0.29 %            50

 Performance counter stats for './bench_final [...]':

   104,225,776,685      cycles:u
    75,197,253,469      instructions:u            #    0.72  insn per cycle
       260,211,222      cache-misses:u
     2,858,405,370      branch-misses:u

      35.415437967 seconds time elapsed
```

### CRTP

```sh
$ perf stat \
    -e cycles,instructions,cache-misses,branch-misses \
    ./bench_crtp \
    --benchmark_repetitions=50 \
    --benchmark_report_aggregates_only=true
------------------------------------------------------------
Benchmark                  Time             CPU   Iterations
------------------------------------------------------------
bmCrtp_mean             8765 ns         8737 ns           50
bmCrtp_median           8762 ns         8734 ns           50
bmCrtp_stddev           12.6 ns         11.7 ns           50
bmCrtp_cv               0.14 %          0.13 %            50

 Performance counter stats for './bench_crtp [...]':

   104,364,572,397      cycles:u
   241,380,965,818      instructions:u            #    2.31  insn per cycle
       371,367,469      cache-misses:u
         4,091,475      branch-misses:u

      35.266632976 seconds time elapsed
```
