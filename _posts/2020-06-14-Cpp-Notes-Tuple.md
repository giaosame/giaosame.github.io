---
title: "C++ Notes: Tuple"
date: 2020-06-14
layout: single
---

I have known $tuple$ of Python for a long time, but I just found that C++ also provides $tuple$.

## 1. The $tuple$ Type

- A $tuple$ is a template that is similar to a $pair$.
- A $tuple$ has members whose types vary from one $tuple$  type to another, but a $tuple$ can have any number of members.

``` c++
tuple<type1, type2, type3> t1;
t1 = make_tuple("John Wick", "Male", 45);
tuple<size_t, int, char, bool> t2(1, 2, 'a', true); // ok
tuple<double, double, double> threeD{1, 2, 3};      // ok
```



## 2. Accessing the Members of a $tuple$

Access the members of a $tuple$ through a library function template named **get**. Pass a $tuple$ object to **get**, and it can return a reference to the specified member:

```c++
auto x = get<0>(threeD);  // returns the first member of threeD
auto y = get<1>(threeD);  // returns the second member of threeD
```

To know the number of members in a $tuple$ and the type of a specific member, we use two auxiliary class templates, $tuple\_size$  and $tuple\_element$.

```c++
size_t sz = tuple_size<threeD>::value;  // returns 3
typedef decltype(threeD) trans;         // trans is the type of threeD
tuple_element<2, trans>::type z = get<int>(threeD);  // z is a double
```



## 3. Relational and Equality Operators

We can compare two $tuple$s only if they have the same number of members, otherwise the program will report errors.



## 4. Using a $tuple$ to Return Multiple Values

A common use of $tuple$ is to return multiple values from a function. Just like Python, we can group several values together in the end of a function and return it as a tuple.



## 5. $tuple$ + Structured Binding

Structured Binding is a C++17 feature which provides a new way to allow a single definition to define multiple variables with different types. Like a reference, a structured binding is an alias to an existing object. Unlike a reference, the type of a structured binding does not have to be a reference type. We can use a structured binding with an array, data members of a class, or a tuple.

```c++
auto [a, b, c] = threeD;  // a == 1.0, b == 2.0, c == 3.0
```

Be careful! Structured binding only works if the structure is known at compile time, so it doesn't work for the $vector$.



## References

- Stanley B. Lippman. *C++ Primer (5th Edition)*

- https://en.cppreference.com/w/cpp/language/structured_binding
