---
title: "Demystifying CRTP: The Curiously Recurring Template Pattern"
date: 2026-03-14T12:05:00Z
draft: false
mermaid: true
ShowToc: true
TocOpen: true
tags:
  - "cpp"
  - "templates"
  - "design-patterns"
categories:
  - "deep-dive"
---

## Introduction

The **Curiously Recurring Template Pattern (CRTP)** is an advanced C++ idiom where a class `Derived` derives from a class template `Base`, and `Base` takes `Derived` as a template parameter. 

This pattern allows for static (compile-time) polymorphism, avoiding the runtime cost of virtual dispatch.

## The Basic Structure

Here is how CRTP looks in code:

```cpp
template <typename T>
class Base {
public:
    void interface() {
        // Cast this pointer to the derived class type
        static_cast<T*>(this)->implementation();
    }
};

class Derived : public Base<Derived> {
public:
    void implementation() {
        std::cout << "Derived implementation!" << std::endl;
    }
};
```

## How It Works (Visualized)

Because `Base<Derived>` is instantiated with the `Derived` type, the base class inherently "knows" what the derived type is. This allows us to use `static_cast` instead of a virtual function table lookup.

Below is a Mermaid diagram explaining the inheritance and template resolution flow:

{{< mermaid >}}
classDiagram
    class Base~Derived~ {
        +interface()
    }
    class Derived {
        +implementation()
    }
    
    Base~Derived~ <|-- Derived : Inherits
    Derived ..> Base~Derived~ : Provides self as type parameter

    note for Base~Derived~ "Calls static_cast<Derived*>(this)->implementation()"
{{< /mermaid >}}

## Use Cases

1. **Static Polymorphism**: When you need polymorphic behavior in tight loops where `vtable` lookups (virtual functions) are too slow.
2. **Method Chaining**: Implementing methods in a base class that return references to the derived class object (like the Fluent Interface pattern).
3. **Object Counters**: Tracking the number of instances created for specific derived classes.

If you don't need runtime dispatch, CRTP is one of the most powerful tools in modern C++.
