---
title: "The Volatile Keyword in C++: When NOT To Use It"
date: 2026-03-14T12:00:00Z
draft: false
mermaid: false
cpp_version: "C++14"
compiler_version: "Clang 16"
---

The `volatile` keyword in C++ is one of the most misunderstood modifiers in the language. Many believe it makes variables thread-safe or atomic. **It does neither.** 

`volatile` simply prevents the compiler from optimizing away memory accesses. This is only necessary when interacting with memory-mapped I/O (like reading from a hardware sensor register) or signal handlers. 

If you are trying to share variables between threads, use `std::atomic<T>` or a `std::mutex` instead. Never use `volatile` for inter-thread synchronization in C++. 
