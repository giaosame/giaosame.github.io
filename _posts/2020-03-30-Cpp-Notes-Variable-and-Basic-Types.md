---
title: "C++ Notes: Variables and Basic Types "
date: 2020-03-30
layout: single
author_profile: false
---

### 1. Primitive Built-in Types

$$
\text{primitive type} =
\begin {cases}
\text{arithmetic type}
	\begin {cases}
		\text{integral type (including character and boolean types)} \newline
		\newline
		\text{floating-point type}
	\end {cases}
\newline
void
\end{cases}
$$

#### 1.1 Character Types

A ```char``` is guaranteed to be big enough to hold numeric values corresponding to the characters in the machine's basic character set. ```wchar_t```,  ```char16_t``` , ```char32_t``` are used for extended character sets. The ```wchar_t``` type, meaning "wide character" is guaranteed to be large enough to hold any character in the machine's largest extended character set. The types ```char16_t``` , ```char32_t``` are intended for Unicode characters.

#### 1.2 Signed and Unsigned Characters

There are three distinct basic character types: ```char```, ```signed char```, and ```unsigned char```. In particular, ```char``` is not the same type as ```signed char```, and actually there are only two representations: signed and unsigned. The plain ```char``` type uses one of these representations depending on the compiler.

#### 1.3 Single-precision Floating-point VS Double-precision Floating-point

Use ```double``` for floating-point computations; ```float``` usually does not have enough precision, and the cost of double-precision calculations versus single-precision is negligible. In fact, in some machines, double-precision operations are faster than single.

#### 1.4 Signed and Unsigned Type Conversions

Signed values are automatically converted to unsigned, when expressions are mixed signed and unsigned values.

- If we assign an out-of-range value to an unsigned type object, the result is the remainder of the value modulo the number of values the target type can hold.

  ```c++
  unsigned char uc = -1;  // assuming 8-bit chars, uc has value 255
                          // 8-bit char can hold 256 values from 0 to 255 inclusively
                          // so uc = (-1 % 256) = 255;
  ```

- If we assign an out-of-range value to an signed type object, the result is undefined. The program might appear to work, it might crash or produce garbage values.

#### 1.5 Literals

- There are no literals of type ```short```.

- Floating-point literals have type ```double```.

- The compiler appends a null character ($\text{'\0'}$) to every string literal.

- The value of a decimal literal is never a negative number. If we write what appears to be a negative decimal literal, for example, -36, the minus sign is **not** part of the literal. The minus sign is an operator that negates the values of its literal operand.




### 2. Variables

#### 2.1 Variable VS Object

- Variable is a named storage with a certain type.
- Object is a region of memory that has a type, it can be seen as a variable of class types.

#### 2.2 Default Initialization

The value of a built-in type object that is not explicitly initialized depends on where it is defined. Variables defined outside any function body are initialized zero.



### 3. Compound Types

A compound type is a type that is defined in terms of another type, for example, references and pointers.

- A reference defines an alternative name for an object, itself is not an object.
- A pointer is a compound type that "points to" another type and hold the address of another object.
- A pointer to reference is illegal in C++, because references are not objects, they don't have addresses.

#### 3.1 Lvalue VS Rvalue

Every expression is either an rvalue or an lvalue. An rvalue refers to an object's value (its content) while an lvalue refers to an object's identity (its location in memory).

- In general, lvalues could stand on the left-hand side of an assignment whereas rvalues could not.
- A variable is an lvalue.
- An lvalue expression yields an object or a function, but some lvalues, such as ```const``` objects, may not be the left-hand operand of an assignment. Besides, some expressions yield objects but return them as rvalue.
- We can use an lvalue when an rvalue is required, but not vice versa.
- Lvalues have persistent state, whereas rvalues are either literals or temporary object created in the course of evaluating expression.

#### 3.2 Lvalue Reference VS Rvalue Reference

Technically speaking, when using the term "reference", it means "lvalue reference". An rvalue reference is a reference that must be bound to an rvalue, and it can be obtained by using $\&\&$.

``` c++
int i = 32;
int& r = i;
int&& rr = i;           // error: cannot bind an rvalue reference to an lvalue
int& r2 = i * 3;        // error: i * 3 is an rvalue
const int& r3 = i * 3;  // ok
int&& rr2 = i * 3;      // ok
```

- Lvalue reference:

  We cannot bind lvalue references to expressions that require a conversion, to literals, or to expression that returns rvalue.

- Rvalue reference:

  We can bind  an rvalue to the above expressions, but we cannot directly bind an rvalue reference to an lvalue. Rvalue references refer to objects that are about to be destroyed, then new standard introduced it to support move operations.

#### 3.3 Null Pointer

A null pointer does not point to any object.

``` c++
int *p1 = nullptr;
int *p2 = 0;  
int *p3 = NULL;  // NULL is a preprocessor variable, which the cstdlib header defines as 0.
```

It is illegal to assign an ```int``` variable to a pointer, even if the variable's value happens to be 0.

#### 3.4 ```void*``` Pointers

The type ```void*``` is a special pointer type that can hold the address of any object.  

- We can compare ```void*``` to another pointer, pass it or return it from a function, and we can assign it to another ```void*``` pointer.
- We cannot use a ```void*``` to operate the object it addresses -- we don't know that object's type, and the type determines what operations we can perform on the object.



### 4. ```const``` Qualifier

#### 4.1 References to ```const```

A reference to ```const``` is a reference that refers to a ```const``` type, so it cannot be used to change the object to which the reference is bound.

- We can bind a reference to ```const``` to a non-```const``` object, a literal, or a more general expression:

  ``` c++
  const int& r1 = 666;     // ok, while "int& r1 = 666;" is error
  const int& r2 = r1 * 3;  // ok
  r2 = 0;                  // error
  ```

#### 4.2 Pointers to ```const```

A pointer to ```const``` may not be used to change the object to which the pointer points. We may store the address of a ```const``` object only in a pointer to ```const```:

``` c++
const int pi = 666;   
int* ptr = &pi;          // error
const int* cptr = &pi;   // ok
*cptr = 888;             // error
int pj = 2; cptr = &pj;  // ok
```

#### 4.3 ```const``` Pointers

- Like any other ```const``` object, a ```const``` pointer must be initialized, and once initialized, its value (i.e.,  the address that it holds) may not be changed.

- Put the ```const``` after the * to indicate the pointer is ```const```

  ``` c++
  int i = 0;
  int *const cptr = &i;
  *cptr = 666;                // ok
  const int *const pip = &i;  // ok, pip is a const pointer to a const object.
  ```

#### 4.4 ```constexpr``` and Constant Expressions

A constant expression is an expression whose value cannot change and that can be evaluated at compile time. A literal is a constant expression. A ```const``` object that is initialized from a constant expression is also a constant expression.

``` c++
const int i = 1;           // i is a constant expression
const int j = i + 1;       // j is a constant expression
const int k = get_size();  // k is NOT a constant expression,
                           // because its initializer's value is not known until run time.            
```

Variables declared as ```constexpr``` are implicitly ```const``` and must be initialized by constant expressions:

``` c++
constexpr int ci = i + 2;       // ok
constexpr int sz = get_size();  // ok only if get_size() is a constexpr function
```

#### 4.5 ```const``` VS ```constexpr```

The primary difference between ```const``` and ```constexpr``` variables is that the initialization of a ```const``` variable can be deferred until run time. A ```constexpr``` variable must be initialized at compile time. All ```constexpr``` variables are ```const```.



### 5. The ```decltype``` Type Specifier

```decltype``` returns the "declared type" of an expression. The compiler analyzes the expression to determine its type but does not evaluate the expression:

```c++
decltype(func()) sum = x;  // sum has whatever type func returns but does not call func.
```

#### 5.1 When to use ```decltype```?

Sometimes we want to define a variable with a type that the compiler deduces from an expression but do not want to use the expression to initialize the variable.

#### 5.2 ```decltype``` and References

If an expression is an rvalue,  ```decltype(expression)``` is the type of the expression. If the expression is an lvalue,  ```decltype(expression)``` is an lvalue reference to the type of *expression*.

```c++
const int&& func();
const int ci = 0, &cj = ci;
decltype(ci) x = 0;      // const int
decltype(cj) y = x;      // const int&
decltype(func()) z = 0;  // const int&&
```

Two important rules:

- The dereference operator is an example of an expression for which ```decltype``` returns a reference!
- Wrapping the variable's name in one or more sets of parentheses cause ```decltype``` returns a references.

```c++
int i = 42, *p = &i;
decltype(*p) c;   // error: c is int& and must be initialized
decltype((i)) d;  // error: d is int& and must be initialized
```

#### 5.3 ```decltype``` VS ```auto```

```decltype``` returns the exact type of an expression including top-level ```const``` and references, while we need to use  ```const auto``` or ```auto&``` to get what ```decltype``` does.



### 6. Preprocessor and Preprocessor Variables

The preprocessor is a program that runs before the compiler and changes the source text of our programs.

- When the preprocessor see a ```#include```, it replaces the ```#include``` with the contents of the specified header.
- Use the preprocessor to make it safe to include a header multiple times.
- C++ programs also use the preprocessor to define header guards. Header guards rely on preprocessor variables, which  hae one of two possible states: defined or not defined. The ```#define``` directive takes a name and defines that name as preprocessor variable. Another two directives test whether a given preprocessor variable has or has not been defined: ```#ifdef``` and ```#ifndef```. If the test is true, then everything following the ```#ifdef``` or ```#ifndef``` is processed up to the matching ```#endif```.



### References

- Stanley B. Lippman. *C++ Primer (5th Edition)*

- https://docs.microsoft.com/en-us/cpp/cpp/constexpr-cpp?view=vs-2019

- https://stackoverflow.com/questions/12084040/decltype-vs-auto
