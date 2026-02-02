+++
title = "Polymorphism in C++ and Rust: type erasure"
date = 2026-01-24

[taxonomies]
tags = ["c++", "rust", "programming", "patterns"]

[extra]
toc = true
+++

In which I teach myself C++ type erasure, and discover why Rust's trait system makes most of it unnecessary.

<!-- more -->

## The problem

Type erasure is one of those techniques that sounds like it solves a problem you don't have until you have it.

Say you want to implement a `Shape` class with an `area()` method. You have circles, squares, maybe triangles later. The obvious approach is inheritance: define a base class, override the virtual method, store pointers in a container. It works, but now your API is littered with `unique_ptr` and heap allocations are visible to callers. What if you want the same polymorphic behavior but with value semantics, where the memory management is an implementation detail, not part of your interface?

That's the problem type erasure solves. We'll start from the familiar virtual function approach, see where it falls short, and build up to a technique that gives you polymorphism without exposing the plumbing.

## Classic dynamic polymorphism

The traditional object-oriented solution uses inheritance and virtual functions. We start with an interface:

```cpp
struct Shape {
    virtual ~Shape() = default;
    virtual double area() const = 0;
};
```

Then add `Circle` and `Square`:

```cpp
struct Circle : Shape {
    double radius_;
    Circle(double r) : radius_(r) {}
    double area() const override { return M_PI * radius_ * radius_; }
};

struct Square : Shape {
    double side_;
    Square(double s) : side_(s) {}
    double area() const override { return side_ * side_; }
};
```

Now we can store different shapes in a container and use them generically:

```cpp
int main() {
    std::vector<std::unique_ptr<Shape>> shapes;
    shapes.emplace_back(std::make_unique<Circle>(5.0));
    shapes.emplace_back(std::make_unique<Square>(4.0));
    
    for (const auto& shape : shapes) {
        std::cout << "Area: " << shape->area() << "\n";
    }
}
```

The Rust version looks similar:

```rust
use std::f64::consts::PI;

trait Shape {
    fn area(&self) -> f64;
}

struct Circle { radius: f64 }

impl Shape for Circle {
    fn area(&self) -> f64 { PI * self.radius * self.radius }
}

struct Square { side: f64 }

impl Shape for Square {
    fn area(&self) -> f64 { self.side * self.side }
}

fn main() {
    let shapes: Vec<Box<dyn Shape>> = vec![
        Box::new(Circle { radius: 5.0 }),
        Box::new(Square { side: 4.0 }),
    ];
    
    for shape in &shapes {
        println!("Area: {}", shape.area());
    }
}
```

This works, but notice the `unique_ptr` and `Box` in the container types. The pointer semantics are baked into your API. There's also a second limitation in C++: types must inherit from the base class, so you can't make existing types polymorphic without modifying them.

## Template-based polymorphism

If types can't share a base class, you might reach for templates instead:

```cpp
void print_area(const auto& shape) {
    std::cout << "Area: " << shape.area() << "\n";
}
```

This works for any type with an `area()` method, no inheritance required. Templates generate code for each type you use, so if you pass something without `area()`, the compiler errors when it tries to instantiate the template. But it has two problems.

First, there's no common type. Each template instantiation is distinct, so you can't store a mix of `Circle` and `Square` in one container:

```cpp
std::vector<???> shapes;  // What type goes here?
```

Second, templates are viral. Any function that calls `print_area` must either know the concrete type or be a template itself. This spreads templates across the codebase, making it harder to read and increasing compile times and binary size.

## External polymorphism

External polymorphism addresses the intrusiveness problem. If you have types that don't share a base class, you can wrap them in a polymorphic interface and treat them uniformly. Say `Circle` and `Square` don't inherit from anything:

```cpp
struct Circle {
    double radius;
    double area() const { return M_PI * radius * radius; }
};

struct Square {
    double side;
    double area() const { return side * side; }
};
```

We define a wrapper interface and a templated implementation that forwards to the wrapped object:

```cpp
struct ShapeWrapper {
    virtual ~ShapeWrapper() = default;
    virtual double area() const = 0;
};

template<typename T>
struct ShapeWrapperImpl : ShapeWrapper {
    T shape_;
    ShapeWrapperImpl(T s) : shape_(std::move(s)) {}
    double area() const override { return shape_.area(); }
};
```

Now we can store `Circle` and `Square` in the same container:

```cpp
int main() {
    std::vector<std::unique_ptr<ShapeWrapper>> shapes;
    shapes.emplace_back(std::make_unique<ShapeWrapperImpl<Circle>>(Circle{5.0}));
    shapes.emplace_back(std::make_unique<ShapeWrapperImpl<Square>>(Square{4.0}));
    
    for (const auto& shape : shapes) {
        std::cout << "Area: " << shape->area() << "\n";
    }
}
```

This pattern lets you add polymorphism to types you don't control, but it still exposes pointers in your API.

Rust doesn't need this pattern because traits are *non-intrusive* by design. You can implement traits for types without modifying them[^newtype]:

```rust
use std::f64::consts::PI;

struct ThirdPartyCircle { radius: f64 }

trait Shape {
    fn area(&self) -> f64;
}

impl Shape for ThirdPartyCircle {
    fn area(&self) -> f64 { PI * self.radius * self.radius }
}
```

A key difference: C++ needs the external polymorphism pattern as a workaround, while Rust's trait system provides it naturally.

[^newtype]: If you need to implement a foreign trait for a foreign type, you can use the newtype pattern as a workaround for the [orphan rule](https://doc.rust-lang.org/reference/items/implementations.html#orphan-rules): wrap the foreign type in your own struct and implement the trait on that.

## Enter type erasure

We've solved intrusiveness, but pointers are still visible in the API. Type erasure hides them. The idea is: what if the heap allocation was an implementation detail, not something your callers had to think about?

Consider `std::function` in C++:

```cpp
#include <functional>
#include <iostream>

void print_result(std::function<int(int, int)> op) {
    std::cout << op(3, 4) << "\n";
}

int main() {
    // Different types, same interface
    print_result([](int a, int b) { return a + b; });     // Lambda
    print_result(std::multiplies<int>{});                 // Functor
    print_result([](int a, int b) { return a * b; });     // Different lambda
}
```

Notice:
- we pass by value, not by pointer;
- each callable has a different type;
- no inheritance required;
- the concrete type is "erased" behind `std::function`.

That's type erasure: the concrete type is hidden behind a uniform interface, and callers use value semantics.

{% alert(type="note") %}
The heap allocation still happens internally, but it's an implementation detail hidden from callers.
{% end %}

## Implementing type erasure

### C++: the manual approach

C++ doesn't provide type erasure as a language feature. You build it yourself, which is either empowering or exhausting depending on your disposition. The standard approach uses the "external polymorphism" pattern (see Sean Parent's [*Inheritance Is The Base Class of Evil*](https://www.youtube.com/watch?v=2bLkxj6EVoM) talk and Klaus Iglberger's [*C++ Software Design*](https://www.oreilly.com/library/view/c-software-design/9781098113155/) for in-depth treatments).

The idea is to nest the polymorphic machinery inside a value-semantic wrapper. First, define a `Concept` (the interface) and a templated `Model` (the wrapper):

```cpp
struct Shape {
    struct Concept {
        virtual ~Concept() = default;
        virtual double area() const = 0;
    };
    
    template<typename T>
    struct Model : Concept {
        T data;
        Model(T value) : data(std::move(value)) {}
        double area() const override { return data.area(); }
    };
    
    std::unique_ptr<Concept> object;

    template<typename T>
    Shape(T obj) : object(std::make_unique<Model<T>>(std::move(obj))) {}
    
    double area() const { return object->area(); }
};
```

`Circle` and `Square` don't need to inherit from anything:

```cpp
struct Circle {
    double radius;
    double area() const { return M_PI * radius * radius; }
};

struct Square {
    double side;
    double area() const { return side * side; }
};
```

And now we get value semantics at the API level:

```cpp
int main() {
    std::vector<Shape> shapes;  // No pointers!
    shapes.emplace_back(Circle{5.0});
    shapes.emplace_back(Square{4.0});
    
    for (const auto& shape : shapes) {
        std::cout << "Area: " << shape.area() << "\n";
    }
}
```

Look at `main()`: no `unique_ptr`, no template parameters at the call site, just `Shape`. The polymorphic machinery is entirely hidden.

The pattern requires some boilerplate: a `Concept` base class, a templated `Model` that wraps concrete types, and a `unique_ptr` to hide the heap allocation. These names ("Concept" and "Model") are the standard terminology for this pattern. The template constructor accepts any type with the required methods, and type checking happens at template instantiation.

The same technique powers `std::function` and `std::any`.

### Rust: first-class trait objects

Look back at the very first Rust example:

```rust
let shapes: Vec<Box<dyn Shape>> = vec![
    Box::new(Circle { radius: 5.0 }),
    Box::new(Square { side: 4.0 }),
];
```

That's already type erasure: Rust's `dyn Trait` is *built-in* type erasure. The `Box<dyn Shape>` erases the concrete type behind a trait object, giving you runtime polymorphism with the same trait you'd use for static dispatch.

The key difference from C++: *no boilerplate*. Because traits are non-intrusive, Rust doesn't need the Concept/Model workaround. The compiler generates the vtable automatically, trait bounds are checked at the point of use, and object safety rules are enforced at compile time.

### Rust: the C++ style

You can also use the C++ pattern in Rust if you want a cleaner API:

```rust
use std::f64::consts::PI;

trait ShapeTrait {
    fn area(&self) -> f64;
}

struct Circle { radius: f64 }

impl ShapeTrait for Circle {
    fn area(&self) -> f64 { PI * self.radius * self.radius }
}

struct Square { side: f64 }

impl ShapeTrait for Square {
    fn area(&self) -> f64 { self.side * self.side }
}

struct Shape {
    inner: Box<dyn ShapeTrait>,
}

impl Shape {
    fn new<T: ShapeTrait + 'static>(shape: T) -> Self {
        Shape { inner: Box::new(shape) }
    }
    
    fn area(&self) -> f64 { self.inner.area() }
}

fn main() {
    let shapes: Vec<Shape> = vec![
        Shape::new(Circle { radius: 5.0 }),
        Shape::new(Square { side: 4.0 }),
    ];
    
    for shape in &shapes {
        println!("Area: {}", shape.area());
    }
}
```

Now you have `Vec<Shape>` instead of `Vec<Box<dyn Shape>>`. This gives you:
- a cleaner public API;
- the ability to add methods to `Shape` that aren't on the trait;
- encapsulation: the `Box` is an implementation detail you could change later.

The tradeoff is that you lose flexibility at call sites: with `Box<dyn Trait>` directly, callers can choose between `Box`, `Rc`, or references depending on their needs.

In practice, this C++-style wrapper pattern is rare in Rust. The trait system's flexibility and non-intrusiveness means `Box<dyn Trait>` is usually good enough, and the extra indirection of a wrapper struct doesn't buy you much.

## Where C++ has the edge

Rust's built-in type erasure is convenient, but C++'s manual approach offers more flexibility in certain scenarios. Whether that flexibility is worth the boilerplate is a question I'll leave to you and your therapist.

1. **Small buffer optimization** - C++'s `std::function` stores small callables inline, avoiding heap allocation (see Raymond Chen's [explanation of how this works](https://devblogs.microsoft.com/oldnewthing/20200514-00/?p=103749)). Rust's `Box<dyn Trait>` always heap-allocates[^sbo].

2. **Custom storage** - C++'s manual approach gives you full control over how the erased type is stored. You can use arena allocation, custom allocators, or other memory layouts. Rust can do this too, but it's harder.

3. **More flexible interface definition** - Rust trait objects have restrictions: traits with generic methods, methods returning `Self`, or methods taking `self` by value aren't [object-safe](https://doc.rust-lang.org/reference/items/traits.html#object-safety). You also can't combine arbitrary traits - `dyn TraitA + TraitB` only works when `TraitB` is an auto trait like `Send` or `Sync`. C++ templates don't have these limitations since you control the `Concept` interface directly.

## Conclusion

C++ and Rust both support type erasure, but with different tradeoffs. Rust's `dyn Trait` makes the common case trivial: no boilerplate, non-intrusive traits, and the compiler handles the vtable. C++ requires manual implementation but offers more flexibility: no object safety restrictions, and `std::function` provides SBO out of the box for callables. For custom type erasure, both languages require extra effort if you want optimizations like SBO or custom storage.

Code samples available on [GitHub](https://github.com/waifod/code_samples/tree/main/2026-01-24_polymorphism_type_erasure).

This post was inspired by David Álvarez Rosa's [*Deriving Type Erasure*](https://beta.alvarezrosa.com/posts/deriving-type-erasure/).

[^sbo]: The custom type erasure implementation shown earlier in this post uses `unique_ptr`, which also heap-allocates. SBO is an optimization you'd have to implement yourself in either language. The difference is that C++ ships with it for the callable case via `std::function`, while Rust's standard library doesn't provide an equivalent.
