---
title: "Global Compile Time Strings Must Be Short"
date: 2026-03-15T13:48:22+01:00
draft: false
mermaid: true
description: "Static duration constexpr std::string has a hidden length limit..."
tags:
  - "C++"
  - "SSO"
  - "Strings"
  - "Compile Time"
  - "Memory Management"
categories:
  - "Whimsiness: Remarkable"
  - "Obscurity: Modest"
cpp_version: "C++20"
compiler_version: "gcc 15.2 x86-64"
---

## Intent
Adding an immutable, compile time, static storage duration string to the codebase.

## Approach
Simply adding the constant to the `Constants.hpp` file.

```cpp
//file: .../Constants.hpp
constexpr std::string s_other_constant1 = "some_const1";    
constexpr std::string s_other_constant2 = "some_const2";    
constexpr std::string s_my_constant = "17characterstring";  //our new constant
```

## Observation
Compilation error with the following text:
```cpp
 error: 'std::__cxx11::basic_string<char>(((const char*)"17characterstring"), std::allocator<char>())' is not a constant expression because it refers to a result of 'operator new'
  200 |             return static_cast<_Tp*>(::operator new(__n));
      |                                      ~~~~~~~~~~~~~~^~~~~
```

Operator `new` is the issue?

{{< clavis 1 >}}
Calling operator `new` in compile time is only allowed if the allocated memory is also deallocated in compile time.
{{< /clavis >}}

In our case, we want our constant's lifetime to be the entire program duration. This means we cannot deallocate in compile time.

But then, why does it work for the other constants?

They must have dynamically allocated memory as well, right?

## The "Whimsy"

No, the other constants did not dynamically allocate memory. They used the "Small String Optimization (SSO)" buffer.

{{< clavis 2>}}
`std::string` objects contain a small statically allocated character buffer. When the content is short enough, the buffer is used. This is called "Small String Optimization (SSO)". It avoids expensive heap allocations for short strings.
{{< /clavis >}}

Since SSO holds the string content in stack and does not invoke any `new` calls, it allows creating `constexpr std::string` objects until a certain length which can remain alive for the entire program duration.

In our case, the compiler allows strings of length 15 at most.

We can easily see this by running the following code:

{{< godbolt height="400px" src="https://godbolt.org/e#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:2,endLineNumber:8,positionColumn:2,positionLineNumber:8,selectionStartColumn:2,selectionStartLineNumber:8,startColumn:2,startLineNumber:8),source:'%23include+%3Cstring%3E%0A%23include+%3Ciostream%3E%0A%0Aint+main()%0A%7B%0A++++std::string+s%7B%7D%3B%0A++++std::cout+%3C%3C+%22Capacity:+%22+%3C%3C+s.capacity()%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:100,l:'4',m:50,n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g152,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-std%3Dc%2B%2B20',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+15.2+(C%2B%2B,+Editor+%231)',t:'0')),header:(),l:'4',m:50,n:'0',o:'',s:0,t:'0')),l:'3',n:'0',o:'',t:'0')),version:4">}}

This length is an implementation detail.

## Solution

The solution lies in using static memory.

We want strings; therefore, we can use a character array. Good options are:
1. `constexpr std::string_view s_my_const`
2. `constexpr char s_my_const[SIZE]`

Both expressions create a character array stored in read-only static memory which will stay alive during the entire program duration.

Option 1 is probably more preferable in most cases since it provides many helpful member functions.

### Hence, global compile time strings must be short!
__(Actually, they should not exist at all...)__