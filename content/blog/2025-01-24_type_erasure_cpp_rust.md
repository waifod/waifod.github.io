+++
title = "Polymorphism in C++ and Rust: type erasure"
date = 2025-01-24

[taxonomies]
tags = ["c++", "rust", "programming", "patterns"]
+++

In which I teach myself C++ type erasure, and discover why Rust's trait system makes most of it unnecessary.

<!-- more -->

## The problem: polymorphism and its limitations

Let's start with a simple problem: you want to calculate areas of different shapes. The traditional object-oriented solution uses inheritance and virtual functions.

### Classic dynamic polymorphism

C++ with virtual functions:

```cpp
#include <cmath>
#include <iostream>
#include <vector>
#include <memory>

class Shape {
public:
    virtual ~Shape() = default;
    virtual double area() const = 0;
};

class Circle : public Shape {
    double radius_;
public:
    Circle(double r) : radius_(r) {}
    double area() const override { return M_PI * radius_ * radius_; }
};

class Square : public Shape {
    double side_;
public:
    Square(double s) : side_(s) {}
    double area() const override { return side_ * side_; }
};

int main() {
    std::vector<std::unique_ptr<Shape>> shapes;
    shapes.emplace_back(std::make_unique<Circle>(5.0));
    shapes.emplace_back(std::make_unique<Square>(4.0));
    
    for (const auto& shape : shapes) {
        std::cout << "Area: " << shape->area() << "\n";
    }
}
```

Rust with trait objects:

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

This works, but it has limitations:

1. Pointer semantics: you must use pointers/references (`std::unique_ptr`, `Box`) in your API
2. Intrusive: types must inherit from the base class (C++) or implement the trait (Rust)

What if you want polymorphism with value semantics at the API level?

## External polymorphism

In C++, traditional polymorphism has a fundamental limitation: types must inherit from a base class. This is *intrusive*: you can't make existing types polymorphic without modifying them.

External polymorphism solves this by wrapping types in a polymorphic interface. This is also the first step toward type erasure:

```cpp
#include <cmath>
#include <iostream>
#include <memory>
#include <vector>

// Existing types - they don't inherit from anything
struct Circle {
    double radius;
    double area() const { return M_PI * radius * radius; }
};

struct Square {
    double side;
    double area() const { return side * side; }
};

// External polymorphism: wrap types in a polymorphic interface
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

int main() {
    std::vector<std::unique_ptr<ShapeWrapper>> shapes;
    shapes.emplace_back(std::make_unique<ShapeWrapperImpl<Circle>>(Circle{5.0}));
    shapes.emplace_back(std::make_unique<ShapeWrapperImpl<Square>>(Square{4.0}));
    
    for (const auto& shape : shapes) {
        std::cout << "Area: " << shape->area() << "\n";
    }
}
```

This pattern lets you add polymorphism to types you don't control. But it still exposes pointers in your API.

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

This is a key difference: C++ needs the external polymorphism pattern as a workaround, while Rust's trait system provides it naturally.

[^newtype]: If you need to implement a foreign trait for a foreign type, you can use the newtype pattern: wrap the foreign type in your own struct and implement the trait on that.

## Enter type erasure

Type erasure solves a specific problem: how do you get polymorphic behavior with value semantics at the API level?

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
- we pass by value, not by pointer
- each callable has a different type
- no inheritance required
- the concrete type is "erased" behind `std::function`

This is type erasure: hiding the concrete type behind a uniform interface while maintaining *value semantics*.

{% alert(type="note") %}
Type erasure gives you value semantics at the API level. The heap allocation still happens, but it's an implementation detail hidden from callers.
{% end %}

## Implementing type erasure

Now let's see how each language implements type erasure.

### C++: the manual approach

C++ doesn't provide type erasure as a language feature, and instead you implement it using the "external polymorphism" pattern (see Sean Parent's [*Inheritance Is The Base Class of Evil*](https://www.youtube.com/watch?v=2bLkxj6EVoM) talk and Klaus Iglberger's [*C++ Software Design*](https://www.oreilly.com/library/view/c-software-design/9781098113155/) for in-depth treatments):

```cpp
#include <cmath>
#include <memory>
#include <iostream>
#include <vector>

class Shape {
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

public:
    template<typename T>
    Shape(T obj) : object(std::make_unique<Model<T>>(std::move(obj))) {}
    
    double area() const { return object->area(); }
};

// Concrete types - no inheritance required
struct Circle {
    double radius;
    double area() const { return M_PI * radius * radius; }
};

struct Square {
    double side;
    double area() const { return side * side; }
};

int main() {
    std::vector<Shape> shapes;  // Value semantics!
    shapes.emplace_back(Circle{5.0});
    shapes.emplace_back(Square{4.0});
    
    for (const auto& shape : shapes) {
        std::cout << "Area: " << shape.area() << "\n";
    }
}
```

The pattern requires some boilerplate: a `Concept` base class, a templated `Model` that wraps concrete types, and a `unique_ptr` to hide the heap allocation. These names ("Concept" and "Model") are the standard terminology for this pattern. The template constructor accepts any type with the required methods, and type checking happens at template instantiation.

This is the same technique used internally by `std::function` and `std::any`.

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
- a cleaner public API
- the ability to add methods to `Shape` that aren't on the trait
- encapsulation: the `Box` is an implementation detail you could change later

The tradeoff is that you lose flexibility at call sites. With `Box<dyn Trait>` directly, callers can choose between `Box`, `Rc`, or references depending on their needs.

In practice, this C++-style wrapper pattern is rare in Rust. The trait system's flexibility and non-intrusiveness means `Box<dyn Trait>` is usually good enough, and the extra indirection of a wrapper struct doesn't buy you much.

## Conclusion

C++ and Rust both support type erasure, but the experience is different:

**C++**: You build it yourself using the Concept/Model pattern. It works, but requires understanding how to combine templates and inheritance. This reflects the language's history: type erasure emerged as a pattern the community discovered over decades to work around limitations in the inheritance model.

**Rust**: Trait objects are built into the language. The same trait works for both static (`impl Trait`) and dynamic (`dyn Trait`) dispatch. Traits were designed from the start with both use cases in mind.

Both languages give you the same level of control if you need it. Rust just makes the common case simpler.
