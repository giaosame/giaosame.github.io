---
title: "C++ Notes: Lambda Expressions"
date: 2020-06-06
layout: single
author_profile: false
---

### 1. Introducing Lambda

A lambda expression represents a callable unit of code. It is a convenient way of defining an anonymous function object (a *closure*) right at the location where it is invoked or passed as an argument to a function.

A lambda expression has the form

$$[capture \ list ] \ (parameter \ list) \quad \text{->} \quad return \ type \ \{ \ function \ body\ \} $$

where $capture\ list$ is an (often empty) list of local variables defined in the enclosing function.  

```c++
auto lambda = [] {return 666;};
cout << lambda() << endl;  // print 666
```

#### 1.1 Callables

If $e$ is a callable expression, we can write $e (args) $ where args is a comma-separated list of zero or more arguments.

Examples of callables:

- functions
- function pointers
- classes that overload the function-call operator:  ```int(1.f); ```
- **lambda expressions**

#### 1.2 Lambda VS Function

- Like any function, a lambda can be called, has a return type, a parameter list and a function body.
- Unlike a function, lambdas may be defined inside a function and they must use a trailing return to specify the return type.



### 2. Capture List of a Lambda

- A lambda may use a variable local to its surrounding function only if the lambda captures that variable in its capture list.
- The capture list is used for local non$static$ variables only; lambdas can use local $static$s and variables declared outside the function directly.

#### 2.1 Capture by Value

Unlike parameters, the value of a captured variable is copied when the lambda is created, not when it is called.

```c++
void main()
{
    int v1 = 666;   // local variable
    auto f = [v1] { return v1; };
    v1 = 0;
    cout << f() << endl;  // print 666
}
```

#### 2.2 Capture by Reference

The $\&$ before the variable $v2$ in the capture list indicates $v2$ should be captured as a reference.

```c++
void main()
{
    int v2 = 666;   // local variable
    auto g = [&v2] { return v2; };
    v2 = 0;
    cout << g() << endl;  // print 0
}
```

#### 2.3 Implicit Captures

Rather than explicitly listing the variables we want to use from the enclosing function, we can let the compiler infer which variables we use from the code in the lambda's body.

- The $\&$ tells the complier to capture by reference.
- The $=$ says the values are captured by value.

```c++
void main()
{
    int v3 = 888;
    auto f = [&] { return v3; };
    auto g = [=] { return v3; };
    v3 = 666;
    cout << f() << ", " << g();  // print "666, 888"
}
```

If we want to capture some variables by value and others by reference, we can mix implicit and explicit captures. Note that when mixing implicit and explicit captures, the first item in the capture list must be an $\&$ or $=$, which sets the default capture mode.

```c++
void main()
{
    int v4 = 999;
    string s = "666";
    auto f = [&, s] {return s + to_string(v4); };
    auto g = [=, &s] {return s + to_string(v4); };
    v4 = 888;
    s = "888";
    cout << f() << endl;  // print 666888
    cout << g() << endl;  // print 888999
}
```



### 3. Specifying the Lambda Return Type

We must use  a trailing return type to define a return type for a lambda.

If we omit the return type, the lambda has an inferred return type that depends on the code in the function body. If the function body is just a return statement, the return type is inferred from the type of the expression that is returned. Otherwise, the return type is $void$.



### 4. Lambdas are Function Objects

When we define a lambda, the compiler generates a unnamed class type that corresponds to that lambda. The classes generated from a lambda contain an overloaded function-call operator.

```c++
sort(words.begin(), words.end(), [](const string& a, const string& b){
    return a > b;
});
// The lambda passed as the last argument to sort() acts like an unnamed object of the following class:
class Compare {
public:
    bool operator() (const string& a, const string& b) const
    { return a > b; }
};
```

We can rewrite the call to $sort$ to use this class instead of the lambda expression:

```c++
sort(words.begin(), words.end(), Compare());
```

**Classes representing lambdas with captures**

Classes generated from lambdas that capture variables by value have data members corresponding to each such variable. These classes also have a constructor to initialize these data members fro the value of the captured variables.



### References

- Stanley B. Lippman. *C++ Primer (5th Edition)*

- https://docs.microsoft.com/en-us/cpp/cpp/lambda-expressions-in-cpp?view=vs-2019
