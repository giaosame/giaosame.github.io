---
title: "C++ Review Notes"
date: 2021-03-15
layout: single
---


##  1. ```std::optional```


`optional<T>` provides interfaces to determine if it contains a `T` and to query the stored value. You can initialize an `optional` with an actual `T` value, or default-initialize it (or initialize with `std::nullopt`) to put it in the “empty” state.


Two user cases:  

- A common use case for optional is the return value of a function that may fail. As opposed to other approaches, such as ```std::pair<T,bool>```, optional handles expensive-to-construct objects well and is more readable, as the intent is expressed explicitly. 


- Another case: the delayed initialization problem. Write a class with a member object whose initialization is delayed, i.e., optionally contains an object? For whatever reason, we don’t want to initialize this member in a constructor. (use pointer can solve this problem, but if the member is an ```int``` object, dynamic allocation is orders of magnitude more expensive than simply returning an integer.


  ```cpp
  #include <optional>
  class Contact
  {
      std::optional<std::string> home_phone;
      std::optional<std::string> work_phone;
      std::optional<std::string> mobile_phone;
  };
  ```


References:


- [https://en.cppreference.com/w/cpp/utility/optional](https://en.cppreference.com/w/cpp/utility/optional)
- [https://devblogs.microsoft.com/cppblog/stdoptional-how-when-and-why/#the-need-for-sometimes-a-thing](https://devblogs.microsoft.com/cppblog/stdoptional-how-when-and-why/#the-need-for-sometimes-a-thing)
- [https://stackoverflow.com/questions/44856701/what-to-use-stdoptional-or-stdunique-ptr](https://stackoverflow.com/questions/44856701/what-to-use-stdoptional-or-stdunique-ptr)  




## 2. `volatile`

`volatile` is a hint to the implementation to avoid aggressive optimization involving the object because the value of the object might be changed by means undetectable by an implementation.  

Programs that deal directly with hardware often have data elements whose value is controlled by processes outside the direct control of the program itself. For example, a program might contain a variable updated by the system clock. An object should be declared ```volatile``` when its value might be changed in ways outside the control or detection of the program. The ```volatile``` keyword is a directive to the compiler that it should not perform optimizations on such objects.  


References:

- [https://stackoverflow.com/questions/4437527/why-do-we-use-volatile-keyword](https://stackoverflow.com/questions/4437527/why-do-we-use-volatile-keyword)  



## 3. `alignof` & `alignas`

**Alignment is a restriction on which memory positions a value's first byte can be stored.** Alignment of 8 means that memory addresses that are a multiple of 8 are the only valid addresses, i.e, **Something with an alignment of 8 then will be placed on the next available address that is a multiple of 8** (there may be a implicit padding from last used address).  

The `alignof` operator returns the alignment in bytes of the specified type.


- The `alignof` value is the same as the value for  `alignof` for basic types. 


- The `alignof` value is the alignment requirement of the largest element in the structure.

```c++
struct S
{
    int a; 
    double b; 
};  
// alignof(S) == 8  

struct t1
{
    int i1;
    int i2;
    string s;
};

struct t2
{
    double d;
    string s;
};

cout << sizeof(string) << endl;   // 28 bytes
cout << alignof(string) << endl;  // 4 bytes
cout << alignof(t1) << endl;      // 4 bytes
cout << alignof(t2) << endl;      // 8 bytes
cout << sizeof(t1) << endl;       // 36 bytes
cout << sizeof(t2) << endl;       // 40 bytes   
```

`alignas` specifies the [alignment requirement](https://en.cppreference.com/w/cpp/language/object#Alignment) of a type or an object, and we can only align to powers of 2: 1, 2, 4, 8, 16, 32, 64, 128, ...... Also, alignment cannot be set to less than the default alignment.


```c++
alignas(4) double d; // error
struct alignas(8) S {};
struct alignas(1) U { S s; }; // error: alignment of U would have been 8 without alignas(1)
```


References:

- [https://www.geeksforgeeks.org/alignof-operator-in-c/](https://www.geeksforgeeks.org/alignof-operator-in-c/)




## 4. `const` Qualifier


- `const` reference: references are always `const`, in the sense that we can never reseat a reference to make it refer to a different object.  

- reference to `const`  

  ```c++
  const int& a = 100;
  ```

- pointer to `const`  

  ```c++
  const int* p = &a;
  ```

- `const` pointer  

  ```c++
  int b = 1; 
  int* const pp = &b;
  ```


References:  

- [https://giaosame.me/Cpp-Notes-Variable-and-Basic-Types/#4-const-qualifier](https://giaosame.me/Cpp-Notes-Variable-and-Basic-Types/#4-const-qualifier)  



## 5. Differences between `#define` and `const`


- The `#define` directive is a **preprocessor** directive; the preprocessor replaces those macros by their body before compilation begins. 


- A ```const``` variable declaration declares an actual variable, an unmodifiable variable. The big advantage of ```const``` over ```#define``` is type checking. 
- No performance difference.


References:

- [https://stackoverflow.com/questions/6442328/what-is-the-difference-between-define-and-const](https://stackoverflow.com/questions/6442328/what-is-the-difference-between-define-and-const)

- [https://stackoverflow.com/questions/6393776/what-is-the-difference-between-a-macro-and-a-const-in-c](https://stackoverflow.com/questions/6393776/what-is-the-difference-between-a-macro-and-a-const-in-c)



## 6. C++ Memory Management  

The following summarizes a C++ program's major distinct memory areas. 

- **Const Data**  

  The const data area stores string literals and other data whose values are known at compile time.  

  No objects of class type can exist in this area. All data in this area is available during the entire lifetime of the program.

  Further, all of this data is **read-only**, and the results of trying to modify it are undefined. This is in part because even the underlying storage format is subject to arbitrary optimization by the implementation.  For example, a particular compiler may store string literals in overlapping objects if it wants to.

- **Stack**  

  The stack stores **automatic variables** (an automatic variable is a local variable which is allocated and deallocated automatically when program flow enters and leaves the variable's scope, such as **function parameters** and **local variables**). **Typically allocation is much faster than dynamic storage** (heap or free store) because a memory allocation involves only pointer increment rather than more complex management. Objects are constructed immediately after memory is allocated and destroyed immediately before memory is deallocated, so there is no opportunity for programmers to directly manipulate allocated but uninitialized stack space.

- **Free Store**  

  The free store is one of the two dynamic memory areas, allocated/freed by new/delete. Object lifetime can be less than the time the storage is allocated; that is, free store objects can have memory allocated without being immediately initialized, and can be destroyed without the memory being immediately deallocated. During  the period when the storage is allocated but outside the object's lifetime, the storage may be accessed and manipulated through a `void*` but none of the proto-object's non`static` members or member functions may be accessed, have their addresses taken, or be otherwise manipulated.

- **Heap**  

  The heap is the other dynamic memory area, allocated/freed by malloc/free and their variants. Note that while the default global new and delete might be implemented in terms of malloc and free by a particular compiler, the heap is not the same as free store and memory allocated in one area cannot be safely deallocated in the other. Memory allocated from the heap can be used for objects of class type by placement-new construction and explicit destruction. If so used, the notes about free store object lifetime apply similarly here.  

- **Global/Static**  

  Global or static variables and objects have their storage allocated at program startup, but may not be initialized until after the program has begun executing. For instance, a static variable in a function is initialized only the first time program execution passes through its definition. The order of initialization of global variables across translation units is not defined, and special care is needed to manage dependencies between global objects (including class statics).  As always, uninitialized proto-objects' storage may be accessed and manipulated through a ```void*``` but no non```static``` members or member functions may be used or referenced outside the object's actual lifetime.

***Stack* VS *Heap***


|                             |                   STACK                    |                   HEAP                   |
| :-------------------------: | :----------------------------------------: | :--------------------------------------: |
|            Basic            | Memory is allocated in a contiguous block. | Memory is allocated in any random order. |
| Allocation and Deallocation |    Automatic by compiler instructions.     |          Manual by programmer.           |
|            Cost             |                    Less                    |                   More                   |
|         Access time         |                   Faster                   |                  Slower                  |
|         Main Issue          |             Shortage of memory             |           Memory fragmentation           |
|    Locality of reference    |                 Excellent                  |                 Adequate                 |
|         Flexibility         |                 Fixed size                 |           Resizing is possible           |
|     Data type structure     |                   Linear                   |               Hierarchical               |


References:

- [http://www.gotw.ca/gotw/009.htm](http://www.gotw.ca/gotw/009.htm)
- [*Stack* VS *Heap*](https://www.geeksforgeeks.org/stack-vs-heap-memory-allocation/)
- [*Free store* VS *Heap*](https://www.codesec.net/view/223702.html)  



## 7. malloc & free
`void* malloc(size_t size)` allocates a block of `size` bytes of memory, returning a pointer to the beginning of the block.
- The content of the newly allocated block of memory is not initialized, remaining with indeterminate values.  
- If size is zero, the return value depends on the particular library implementation (it may or may not be a _null pointer_), but the returned pointer shall not be dereferenced.

```c++
#include <cstdlib>

void main() {
	int *ptr;
	ptr = (int*)malloc(5 * sizeof(int));

	if(!ptr) {
		cout << "Memory Allocation Failed";
		return;
	}

	for (int i = 0; i < 5; i++) {
		ptr[i] = i * 2 + 1;
		cout << *(ptr + i) << endl;
	}

	free(ptr);
}
```

`malloc()` **VS** `new`
- **Calling Constructors:** `new` calls constructors, while `malloc()` does not.
- **operator vs function:** `new` is an operator, while `malloc()` is a function.
- **return type:** `new` returns exact data type, while `malloc()` returns `void *`.
- **Failure Condition:** On failure, `malloc()` returns `NULL` where as `new` throws `bad_alloc` exception.
- **Memory:** In case of `new`, memory is allocated from free store where as in `malloc()` memory allocation is done from heap.
- **Buffer Size:** `malloc()` allows to change the size of buffer using `realloc()` while `new` doesn’t, so the only way that would be beneficial to use `malloc` would be that **change the size of the data buffer**. The `new` keyword does not have an analogous way like `realloc`. The `realloc` function might be able to extend the size of a chunk of memory more efficiently.

`void free(void* ptr)`
A block of memory previously allocated by a call to  [malloc](http://www.cplusplus.com/malloc),  [calloc](http://www.cplusplus.com/calloc)  or  [realloc](http://www.cplusplus.com/realloc)  is deallocated.
- Passing a pointer to `free()` which is not returned by `malloc()` and its family is undefined behavior.
- If  `ptr`  is a  *null pointer*, the function does nothing.  
  

Notice that this function does not change the value of  `ptr`  itself, hence it still points to the same (now invalid) location.
**Cannot** use `free()` to free parts of memory, the only way to acchieve the similar effect is using ``realloc()``.

**How does `free()` know how much to free?**
When memory allocation is done, the actual heap space allocated is one word larger than the requested memory. The extra word is used to store the size of the allocation and is later used by `free( )`.

`void* realloc(void* ptr, size_t new_size)`
The reallocation is done by either:
- expanding or contracting the existing area pointed to by  `ptr`, if possible. The contents of the area remain unchanged up to the lesser of the new and old sizes. If the area is expanded, the contents of the new part of the array are undefined.
- allocating a new memory block of size  `new_size`  bytes, copying memory area with size equal the lesser of the new and the old sizes, and freeing the old block.

If `ptr` is a null pointer, the behavior is the same as calling `malloc(size)`.

References:
- [https://en.cppreference.com/w/cpp/memory/c/malloc](https://en.cppreference.com/w/cpp/memory/c/malloc)
- [https://www.geeksforgeeks.org/malloc-vs-new/](https://www.geeksforgeeks.org/malloc-vs-new/)
- [https://stackoverflow.com/questions/2479766/how-allocate-or-free-only-parts-of-an-array](https://stackoverflow.com/questions/2479766/how-allocate-or-free-only-parts-of-an-array)
- [https://en.cppreference.com/w/cpp/memory/c/realloc](https://en.cppreference.com/w/cpp/memory/c/realloc)  




## 8. RAII (Resource Acquisition Is Initialization)

RAII binds the life cycle of a resource that must be acquired before use (allocated heap memory, thread of execution, open socket, open file, locked mutex — anything that exists in limited supply) to the lifetime of an object.

RAII can be summarized as follows:  
- Acquiring all resources
- Using resources
- Releasing resources

Where  
- Resources are implemented as classes, and all pointers have class wrappers around them (making them smart pointers).
- Resources are acquired by invoking their constructors and released implicitly (in reverse order of acquiring) by invoking their destructors.  

**RAII examples**  

- Smart pointers like ```std::shared_ptr``` and ```std::unique_ptr``` are RAII classes.
- `std::fstream` has an RAII type of design. An input file stream is opened in the object's constructor, and it is closed upon destruction of the object. 
-  ```std::string```, ```std::vector```.
- ```std::lock_guard```, ```std::unique_lock```, ```std::shared_lock``` to manage mutexes.

References:  

- [https://en.cppreference.com/w/cpp/language/raii](https://en.cppreference.com/w/cpp/language/raii)  
- [https://stackoverflow.com/questions/2321511/what-is-meant-by-resource-acquisition-is-initialization-raii](https://stackoverflow.com/questions/2321511/what-is-meant-by-resource-acquisition-is-initialization-raii)  
- [https://en.wikibooks.org/wiki/C%2B%2B_Programming/RAII](https://en.wikibooks.org/wiki/C%2B%2B_Programming/RAII)  




## 9. Order of Constructor/Destructor Call

- Why the constructor of base class is called first to initialize all the inherited members?  

  The data members and member functions of base class comes automatically in derived class based on the access specifier, but the definition of these members exists in base class only. Thus, when we create an object of derived class, all of the members of derived class must be initialized, but the inherited members in derived class can only be initialized by the base class’s constructor as the definition of these members exists in base class only.

- Destructors are called in the opposite order of that of Constructors. Destructors automatically get executed when a class object goes out of scope.  

```c++
class Parent {
public:
    Parent() { cout << "Contruct Parent" << endl; }
    ~Parent() { cout << "Destruct Parent" << endl; }
};

class Child : public Parent {
public:
    Child() { cout << "Contruct Child" << endl; }
    ~Child() { cout << "Destruct Child" << endl; }
};

int main() {
    Child child = Child();
}
```

```bash
Contruct Parent
Contruct Child
Destruct Child
Destruct Parent
```

References:

- [https://www.geeksforgeeks.org/order-constructor-destructor-call-c/](https://www.geeksforgeeks.org/order-constructor-destructor-call-c/)  




## 10. `const` Function

A "`const` function", denoted with the keyword `const` after a function declaration, makes it a compiler error for this class function to change a member variable of the class. However, reading of a class variables is okay inside of the function, but writing inside of this function will generate a compiler error.

References:  

- [https://stackoverflow.com/questions/3141087/what-is-meant-with-const-at-end-of-function-declaration](https://stackoverflow.com/questions/3141087/what-is-meant-with-const-at-end-of-function-declaration)  



## 11. ```static```


- **Static variables in a Function**: When a variable is declared as static, space for the static variable is allocated only once and gets allocated for the lifetime of the program. The scope of static variables is through out the life time of program.


- **Static variables in a class**: the static variables in a class are shared by the objects, and they cannot be initialized using constructors.


  ```c++
  class Test {
  private:
      static int t;
  public:
      static int getT(){ return t; }
  };
  
  int Test::t = 666;  // ok
  int main() {
      Test test;
      cout << Test::t << endl;       // error
      cout << Test::getT() << endl;  // ok
  }
  ```

- **Static functions in a class**: shared by the objects like static members. **Static member functions are allowed to access only the static data members or other static member functions**, they can not access the non-static data members or member functions of the class.

References:

- [https://www.geeksforgeeks.org/static-keyword-cpp/](https://www.geeksforgeeks.org/static-keyword-cpp/)  




## 12. *lvalue* and *rvalue*

- *rvalue* includes literals and temporary values.

- A pointer is not an rvalue or an lvalue. A pointer is a *type*. The only thing that can be an rvalue or an lvalue is an expression.

  ```c++
  int* p;
  *p = 7;  // the dereferenced pointer is an lvalue.
  
  int arr[] = {1, 2};
  int* p = &arr[0];
  // A way to produce lvalue from rvalue
  *(p + 1) = 10;   // OK: p + 1 is an rvalue, but *(p + 1) is an lvalue
  ```


- Conversely, the unary address-of operator `&` takes an lvalue argument and produces an rvalue:


  ```c++
  int* addr = &var;           // OK: var is an lvalue
  &var = 40;                  // ERROR: lvalue required as left operand assignment
  ```


- All lvalues that aren't arrays, functions or of incomplete types can be converted to rvalues, while rvalues cannot be converted to lvalues.


**R-value references as function parameters**


```c++
void func(const int& lref) {
    cout << "l-value reference to const" << endl;
}


void func(int&& rref) {
    cout << "r-value reference" << endl;
}


int main() {
    int x = 5;
    func(x); // l-value argument calls l-value version of function
    func(5); // r-value argument calls r-value version of function
    
    int&& rref = 6;
    func(rref);  // rref is actually an l-value itself, because it's a named object
}
```


```bash
l-value reference to const
r-value reference
l-value reference to const
```

[**Is an Rvalue Reference an Rvalue?**](http://thbecker.net/articles/rvalue_references/section_05.html)  

Things that are declared as rvalue reference can be lvalues or rvalues. The distinguishing criterion is: _if it has a name_, then it is an lvalue. Otherwise, it is an rvalue.

```c++
void foo(T&& t) {
	T temp = t; // calls T(t const & rhs)
}

T&& goo();
T t = goo(); // calls X(X&& rhs) because the thing on the right hand side has no name
```

`std::move(x)`  "turns its argument into an rvalue even if it isn't," and it achieves that by "hiding the name". So it is declared as an rvalue reference and does not have a name. Hence, it is an rvalue. 

**Move constructor of derived class**  
```c++
Derived(Derived&& rhs) 
	: Base(std::move(rhs)) // good, calls Base(Base&& rhs)
	                       // if not use std::move, calls copy constructor Base(const Base& rhs)
	                       // rhs is lvalue, std::move(rhs) is rvalue
{ // Derived-specific stuff }
```

References:  

- [https://giaosame.me/Cpp-Notes-Variable-and-Basic-Types/#31-lvalue-vs-rvalue](https://giaosame.me/Cpp-Notes-Variable-and-Basic-Types/#31-lvalue-vs-rvalue)  
- [http://thbecker.net/articles/rvalue_references/section_01.html](http://thbecker.net/articles/rvalue_references/section_01.html)  
- [https://eli.thegreenplace.net/2011/12/15/understanding-lvalues-and-rvalues-in-c-and-c/](https://eli.thegreenplace.net/2011/12/15/understanding-lvalues-and-rvalues-in-c-and-c/)  
- [https://stackoverflow.com/questions/3601602/what-are-rvalues-lvalues-xvalues-glvalues-and-prvalues](https://stackoverflow.com/questions/3601602/what-are-rvalues-lvalues-xvalues-glvalues-and-prvalues)  




## 13. Move Semantics

Move semantics means the class will transfer ownership of the object rather than making a copy, which enables to transfer resources (such as dynamically allocated memory) from one object to another. 

Move semantics works because it enables resources to be transferred from temporary objects that cannot be referenced elsewhere in the program.

**Copy semantics recap**  

Copy constructors are used to initialize a class by making a copy of an object of the same class. Copy assignment is used to copy one class object to another existing class object. By default, C++ will provide a copy constructor and copy assignment operator if one is not explicitly provided. These compiler-provided functions do shallow copies, which may cause problems for classes that allocate dynamic memory. So classes that deal with dynamic memory should override these functions to do deep copies.

- Copy the temporary variable into the new variable using a deep-copy operation.
- Discard the temporary variable.

**Move constructors and move assignment**

The copy flavors of these functions take a const l-value reference parameter, **the move flavors of these functions use non-const r-value reference parameters.** 

If we construct an object or do an assignment where the argument is an r-value, then we know that r-value is just a temporary object of some kind. Instead of copying it (which can be expensive), we can simply transfer its resources (which is cheap) to the object we’re constructing or assigning. This is safe to do because the temporary will be destroyed at the end of the expression anyway, so we know it will never be used again!

**`std::move`**  

Allows the efficient transfer of resources from the given argument to another object, it returns an *rvalue reference* to the given argument.

```c++
std::string str = "Hello";
std::vector<std::string> v;
 
// uses the push_back(const T&) overload, which means 
// we'll incur the cost of copying str
v.push_back(str);
std::cout << "After copy, str is \"" << str << "\"\n";
 
// uses the rvalue reference push_back(T&&) overload, 
// which means no strings will be copied; instead, the contents
// of str will be moved into the vector.  This is less
// expensive, but also means str might now be empty.
v.push_back(std::move(str));
std::cout << "After move, str is \"" << str << "\"\n";
 
std::cout << "The contents of the vector are \"" << v[0]
                                         << "\", \"" << v[1] << "\"\n";
```

```
After copy, str is "Hello"
After move, str is ""
The contents of the vector are "Hello", "Hello"
```

`push_back` VS `emplace_back`


Instead of taking an object, `emplace_back` takes a list of arguments, so that means that it can perfectly forward the arguments and construct directly an object into a container without a temporary at all. `push_back` either copies or moves an existing object into the container.

**Move Semantics and Compiler Optimizations**
```c++
T foo() {
	T t;
	// ... 
	return std::move(t); // don't do this, worse then just return t
}
```
Any modern compiler will apply **_return value optimization_** to the original function definition. In other words, rather than constructing an `T` locally and then copying it out, the compiler would construct the `T` object directly at the location of the function's return value. Rather obviously, that's even better than move semantics.

References:  

- [https://www.ibm.com/support/knowledgecenter/SSGH3R_12.1.0/com.ibm.xlcpp121.aix.doc/proguide/rvaluereferences.html](https://www.ibm.com/support/knowledgecenter/SSGH3R_12.1.0/com.ibm.xlcpp121.aix.doc/proguide/rvaluereferences.html)  
- [https://www.learncpp.com/cpp-tutorial/15-1-intro-to-smart-pointers-move-semantics/](https://www.learncpp.com/cpp-tutorial/15-1-intro-to-smart-pointers-move-semantics/)  
- [https://www.learncpp.com/cpp-tutorial/15-3-move-constructors-and-move-assignment/](https://www.learncpp.com/cpp-tutorial/15-3-move-constructors-and-move-assignment/)
- [https://stackoverflow.com/questions/3106110/what-is-move-semantics](https://stackoverflow.com/questions/3106110/what-is-move-semantics)  
- [http://thbecker.net/articles/rvalue_references/section_06.html](http://thbecker.net/articles/rvalue_references/section_06.html)  




## 14. Smart Pointers


A smart pointer is a class that wraps a 'raw' C++ pointer, to manage the lifetime of the object being pointed to. 

`unique_ptr`
A [unique_ptr](https://docs.microsoft.com/en-us/cpp/standard-library/unique-ptr-class?view=msvc-160) doesn't share its pointer. It cannot be copied to another `unique_ptr`, passed by value to a function, it can only be moved. This means that the ownership of the memory resource is transferred to another `unique_ptr` and the original `unique_ptr` no longer owns it.
```c++
struct Vec3 {
    int x, y, z;
    Vec3(int x = 0, int y = 0, int z = 0) noexcept : x(x), y(y), z(z) {}
};

void main() {
	unique_ptr<Vec3> v1 = std::make_unique<Vec3>();  // use the default constructor
	unique_ptr<Vec3> v2 = std::make_unique<Vec3>(0, 1, 2);
	auto v3 = std::move(v2);
}
```

`shared_ptr`
More than one owner might share the ownership of a pointer and manage the lifetime of the object in memory.
```c++
// Use make_shared function when possible.  
auto sp1 = make_shared<Vec3>(3, 4, 5);
// Ok, but slightly less efficient.
shared_ptr<Vec3> sp2(new Vec3());
```

**Why to avoid ```auto_ptr``` ?**


First, because ```auto_ptr``` implements move semantics through the copy constructor and copy assignment operator, passing a ```auto_ptr``` by value to a function will cause your resource to get moved to the function parameter (and be destroyed at the end of the function when the function parameters go out of scope). Then when you go to access your ```auto_ptr``` argument from the caller (not realizing it was transferred and deleted), you’re suddenly dereferencing a null pointer. Crash!


Second, ```auto_ptr``` always deletes its contents using non-array delete. This means ```auto_ptr``` won’t work correctly with dynamically allocated arrays, because it uses the wrong kind of deallocation. Worse, it won’t prevent you from passing it a dynamic array, which it will then mismanage, leading to memory leaks.


```c++
template<class T>
class SmartPtr 
{
private:
    T* ptr;
public:
    SmartPtr(T* _ptr = nullptr): ptr(_ptr) {}

    ~SmartPtr() { delete ptr; }

    // Copy constructor: deep copy
    SmartPtr(const SmartPtr& sp) {
        ptr = new T();
        *ptr = *sp.ptr;
    }

    // Move constructor
    SmartPtr(SmartPtr&& sp) noexcept
        : ptr(sp.ptr) {
        sp.ptr = nullptr;
    }

    // Copy assignment operator: deep copy
    SmartPtr& operator=(const SmartPtr& sp) {
        if (&sp == this)
            return *this;

        delete ptr;
        ptr = new T();
        *ptr = *sp.ptr;
        return *this;
    }

    // Move assignment operator
    SmartPtr& operator=(SmartPtr&& sp) noexcept {
        if (&sp == this)
            return *this;
            
        delete ptr;
        ptr = sp.ptr;
        sp.ptr = nullptr;
        return *this;
    }
    
    T* operator->() { return ptr; }
    T& operator*() { return *ptr; }
    bool isNull() { return ptr == nullptr; }
};
```


**Why bother doing “cleanup” if parameter ```sp``` is going to be destroyed anyway? (set ```sp.ptr = nullptr```)**


When ```sp``` goes out of scope, ```sp```’s destructor will be called, and ```sp.ptr``` will be deleted. If at that point, ```sp.ptr``` is still pointing to the same object as ```ptr```, then ```ptr``` will be left as a dangling pointer (a pointer pointing to a memory location that has been deleted or freed). 


**Cyclic dependency issues with** ```shared_ptr```


A **Circular reference** (also called a **cyclical reference** or a **cycle**) is a series of references where each object references the next, and the last object references back to the first, causing a referential loop. Hence, `use_count` will never reach zero and they never get deleted.


```c++
class Resource {
public:
	std::shared_ptr<Resource> m_ptr;  // initially created empty
	Resource() { std::cout << "Resource acquired\n"; }
	~Resource() { std::cout << "Resource destroyed\n"; }
};
 
void main() {
	auto ptr1 = std::make_shared<Resource>();
	ptr1->m_ptr = ptr1;  // m_ptr is now sharing the Resource that contains it
}
```


`weak_ptr` was designed to break the reference cycles. `weak_ptr` points to a `shared_ptr` but does not increase its reference count. This means that the object can still be deleted even though there is a `weak_ptr` reference to it.


The downside of `weak_ptr` is that `weak_ptr` is not directly usable (they have no operator->). To use a `weak_ptr`, you must first convert it into a `shared_ptr` by calling the member function `lock()`.


```c++
void main () {
	std::shared_ptr<int> sp1 = std::make_shared<int>(18);
	std::weak_ptr<int> wp = sp1;
	std::shared_ptr<int> sp2 = wp.lock();             
}
```


References:

- [https://stackoverflow.com/questions/106508/what-is-a-smart-pointer-and-when-should-i-use-one](https://stackoverflow.com/questions/106508/what-is-a-smart-pointer-and-when-should-i-use-one)  
- [https://www.learncpp.com/cpp-tutorial/15-1-intro-to-smart-pointers-move-semantics/](https://www.learncpp.com/cpp-tutorial/15-1-intro-to-smart-pointers-move-semantics/)  
- Implement a Shared Pointer: [https://medium.com/analytics-vidhya/c-shared-ptr-and-how-to-write-your-own-d0d385c118adwww.geeksforgeeks.org/how-to-implement-user-defined-shared-pointers-in-c/](https://medium.com/analytics-vidhya/c-shared-ptr-and-how-to-write-your-own-d0d385c118adwww.geeksforgeeks.org/how-to-implement-user-defined-shared-pointers-in-c/)  
- [https://www.learncpp.com/cpp-tutorial/15-7-circular-dependency-issues-with-stdshared_ptr-and-stdweak_ptr/](https://www.learncpp.com/cpp-tutorial/15-7-circular-dependency-issues-with-stdshared_ptr-and-stdweak_ptr/)  




## 15. `decltype`  

References:  

- [https://giaosame.me/Cpp-Notes-Variable-and-Basic-Types/#5-the-decltype-type-specifier](https://giaosame.me/Cpp-Notes-Variable-and-Basic-Types/#5-the-decltype-type-specifier)




## 16. `sizeof`

```c++
double* d_ptr = new double(10);
Test* test_ptr = new Test();
int* t = new int[5];
int a[5] = {};

cout << sizeof(d_ptr) << endl;     // 4, pointer has 4 bytes
cout << sizeof(test_ptr) << endl;  // 4, pointer has 4 bytes
cout << sizeof(t) << endl;         // 4, pointer has 4 bytes, we cannot get the total size of a dynamic array by using sizeof
cout << sizeof(a) << endl;         // 5 * 4 = 20 bytes
cout << sizeof(a) / sizeof(a[0]) << endl;  // 5, the way to get the length of an array
```


References:

- [https://stackoverflow.com/questions/22008755/how-to-get-size-of-dynamic-array-in-c](https://stackoverflow.com/questions/22008755/how-to-get-size-of-dynamic-array-in-c)  




## 17. Rule of Three  

The rule of three (also known as the Law of The Big Three or The Big Three) is a rule of thumb in C++ (prior to C++11) that claims that if a class defines any of the following then it should probably explicitly define all three:  

- destructor
- copy constructor
- copy assignment operator

With the advent of C++11 the rule of three can be broadened to the *rule of five* as C++11 implements move semantics:
- move constructor
- move assignment operator

References:

- [https://en.wikipedia.org/wiki/Rule_of_three_%28C++_programming%29](https://en.wikipedia.org/wiki/Rule_of_three_%28C++_programming%29)




## 18. ```inline```

C++ provides an inline functions to reduce the function call overhead. Inline function is a function that is expanded in line when it is called.

When the inline function is called, the whole code of the inline function gets inserted or substituted at the point of inline function call. This substitution is performed by the C++ compiler at compile time. Inline function may increase efficiency if it is small.

- ```inline``` this function will be defined in multiple [translation units](https://en.wikipedia.org/wiki/Translation_unit_(programming)), don't worry about it. The linker needs to make sure all translation units use a single instance of the variable/function.

**When to use** ```inline```?  

The best candidates for inlining are small functions that are called frequently from a few places.

Only when the function's definition can show up in multiple translation units. It's a good idea to define small (as in one line) functions in the header file as it gives the compiler more information to work with while optimizing your code. It also increases compilation time.

**When the compiler doesn't perform inlining?**  

The function contains a loop / recursion / switch statement, etc.

**Advantages**  

- Function call overhead doesn’t occur.
- It also saves the overhead of push/pop variables on the stack when function is called.
- It also saves overhead of a return call from a function.
- When you inline a function, you may enable compiler to perform context specific optimization on the body of function. 

**[Translation unit](https://docs.microsoft.com/en-us/cpp/cpp/program-and-linkage-cpp?view=msvc-160)**

A translation unit is the basic unit of compilation in C++. It consists of the contents of a single source file, plus the contents of any header files directly or indirectly included by it, minus those lines that were ignored using conditional preprocessing statements. A single translation unit can be compiled into an object file, library, or executable program.

References:

- [https://stackoverflow.com/questions/1759300/when-should-i-write-the-keyword-inline-for-a-function-method](https://stackoverflow.com/questions/1759300/when-should-i-write-the-keyword-inline-for-a-function-method)  
- [https://www.geeksforgeeks.org/inline-functions-cpp/](https://www.geeksforgeeks.org/inline-functions-cpp/)  
- [https://stackoverflow.com/questions/1106149/what-is-a-translation-unit-in-c](https://stackoverflow.com/questions/1106149/what-is-a-translation-unit-in-c)  



## 19. `extern`

The ```extern``` keyword specifies that the symbol has external linkage.


```extern``` - use this variable/function name in this translation unit but don't complain if it isn't defined. The linker will sort it out and make sure all the code that tried to use some extern symbol has its address. 


- In a non-`const` global variable declaration, ```extern``` specifies that the variable or function is defined in another translation unit. The ```extern``` must be applied in all files except the one where the variable is defined.
- In a `const` variable declaration, it specifies that the variable has external linkage. The ```extern``` must be applied to all declarations in all files. (Global `const` variables have internal linkage by default.)
- **extern "C"** specifies that the function is defined elsewhere and uses the C-language calling convention. The extern "C" modifier may also be applied to multiple function declarations in a block.

References:

- https://docs.microsoft.com/en-us/cpp/cpp/extern-cpp?view=msvc-160




## 20. C++ Mutex

```c++
void print_block(int n, char c) {
	mtx.lock();
	for (int i = 0; i < n; i++) cout << c;
	cout << endl;
	mtx.unlock();
}

void main() {
	std::thread th1(print_block, 50, '*');
	std::thread th2(print_block, 50, '$');
	th1.join();
	th2.join();
}
```

`std::unique_lock`

When you want to lock a mutex, you create a local variable of type `unique_lock` passing the mutex as parameter. When the `unique_lock` is constructed it will lock the mutex, and when it gets destructed it will unlock the mutex. More importantly: If an exception is thrown, the `unique_lock` destructor will be called and so the mutex will be unlocked. 

```c++
#include<mutex>
std::mutex mtx;
int some_shared_var = 0;

int func() {
    int a = 3;
    { // Critical section
        std::unique_lock<std::mutex> lock(mtx);
        some_shared_var += a;
    } // End of critical section
      // Unlocked automatically on destruction of _lock_ because this variable goes out of the scope
}        
```

**Differences between** `std::unique_lock` **and** `std::lock_guard`

The programmer can unlock `unique_lock`, but cannot unlock `lock_guard`. The `lock_guard` class doesn’t have any other member function, it locks the mutex by invoking its constructor and unlocks the mutex when the destructor is invoked, while we can manually lock and unlock a `unique_lock` by

```c++
std::unique_lock<mutex> lk(mutex);
lk.lock();
// Critical section ...
lk.unlock();
```

**condition_variable** 

The `condition_variable` class is a synchronization primitive that can be used to block a thread, or multiple threads at the same time, until another thread both modifies a shared variable (the *condition*), and notifies the `condition_variable`.

It uses a `unique_lock` (over a `mutex`) to lock the thread when one of its *wait functions* is called. The thread remains blocked until woken up by another thread that calls a *notification function* on the same condition_variable object.

`condition_variable`s allow one to atomically release a held mutex and put the thread to sleep. Then, after being signaled, atomically re-acquire the mutex and wake up. For example, in the producer/consumer problem, a thread will deadlock if it goes to sleep while holding the mutex, but it could also deadlock if it releases the mutex before sleeping (by missing the signal to wake up).

References:

- [https://stackoverflow.com/questions/14709233/how-to-use-create-unique-lock-in-c](https://stackoverflow.com/questions/14709233/how-to-use-create-unique-lock-in-c)
- [https://stackoverflow.com/questions/20516773/stdunique-lockstdmutex-or-stdlock-guardstdmutex](https://stackoverflow.com/questions/20516773/stdunique-lockstdmutex-or-stdlock-guardstdmutex)
- [http://jakascorner.com/blog/2016/02/lock_guard-and-unique_lock.html](http://jakascorner.com/blog/2016/02/lock_guard-and-unique_lock.html)




## 21. Memory Leak

Memory leakage occurs in C++ when programmers allocates memory by using *new* keyword and forgets to deallocate the memory by using *delete* operator.

**Problem**: Memory leak reduces the performance of the computer by reducing the amount of available memory. Eventually, in the worst case, too much of the available memory may become allocated and all or part of the system or device stops working correctly, the application fails, or the system slows down vastly, or even the program crashes.

**How to avoid Memory Leak?**

- Use smart pointers instead of managing memory manually.
- Follow the RAII concept, anything that requires dynamic memory should be hidden inside an RAII object that releases the memory when it goes out of scope. 




## 22. 2D Array Declaration and Allocation

```c++
// Allocate
int** a = new int*[num_rows];
for (int i = 0; i < num_rows; ++i) {
    a[i] = new int[num_cols];
}
// Clean up
for (int i = 0; i < num_rows; ++i) {
    delete[] a[i];
}
delete[] a;

// C++ 11
auto a2 = new int[num_rows][num_cols];
delete[] a2;
```

**Smart Pointers and Dynamics Arrays**

```c++
unique_ptr<int []> up(new int[10]);  // up points to an array of 10 uninitialized ints
up.realease();                          // automatically uses delete[] to destroy its pointer

// from C++14
auto up1 = make_unique<int[]>(5);
// When a unique_ptr points to an array, we can use the subscript operator to access the elements
for (int i = 0; i < 510; i++) {
    up1[i] = i;
}

```

References:
- [https://stackoverflow.com/questions/59279670/is-it-true-that-unique-ptr-which-points-to-an-array-will-automatically-free-dyna](https://stackoverflow.com/questions/59279670/is-it-true-that-unique-ptr-which-points-to-an-array-will-automatically-free-dyna)




## 24. Lambda

A lambda is just an expression. A lambda expression is a convenient way of defining an anonymous function object.


Unlike a function, lambdas may be defined inside a function


**Lambdas VS Closures**


The runtime effect of a lambda expression is the generation of an object. Such objects are known as *closures*.


```c++
auto f = [&](int x, int y) { return fudgeFactor * (x + y); };
```

The expression to the right of the `=` is the lambda expression (i.e., "the lambda"), and the runtime object created by that expression is the closure. `f` isn't a closure, it is a *copy* of the closure. The process of copying the closure into `f` may be optimized into a move (whether it is depends on the types captured by the lambda), but that doesn't change the fact that `f` itself is not the closure. The actual closure object is a temporary that's typically destroyed at the end of the statement.

The distinction between a lambda and the corresponding closure is precisely equivalent to the distinction between a class and an instance of the class. A class exists only in source code; it doesn't exist at runtime. What exists at runtime are objects of the class type. Closures are to lambdas as objects are to classes. This should not be a surprise, because **each lambda expression causes a unique class to be generated (during compilation) and also causes an object of that class type--a closure--to be created (at runtime)**.

References:

- https://giaosame.me/Cpp-Notes-lambda/
- http://scottmeyers.blogspot.com/2013/05/lambdas-vs-closures.html




## 25. Deleted Functions


In C++ 11,  appending the `=delete;` specifier to the end of a member function declaration, **to disable the usage of this function.**

Using a deleted function will cause syntax error.	

```c++
class A { 
public: 
    // Delete the copy constructor 
    A(const A&) = delete;  
    // Delete the copy assignment operator 
    A& operator=(const A&) = delete;  
}; 
```




## 26. Parameter Pack 


A template parameter pack is a template parameter that accepts zero or more template arguments (non-types, types, or templates). A function parameter pack is a function parameter that accepts zero or more function arguments.


A template with at least one parameter pack is called a ***variadic template***.
```bash
template(typename arg, typename... args)
return_type function(arg var1, args... var2)
```

```c++
template <typename T, typename... Types>
void print(T var1, Types... var2) {
	cout << var1 << endl;
	print(var2...);
}

void print() { 
    cout << "Empty Print";
} 
```


References:


- https://en.cppreference.com/w/cpp/language/parameter_pack






## 27. Function Pointers -- Pointers to Functions


```c++
// fcnPtr is a pointer to a function that takes no arguments and returns an integer
int (*fcnPtr)();
// To make a const function pointer
int (*const fcnPtr)();
```


Assigning a function to a function pointer


```c++
int foo() { return 5; }
int goo() { return 6; }
void main() {
    int (*fcnPtr)(){ &foo };  // fcnPtr points to function foo
    fcnPtr = &goo;  // fcnPtr now points to function goo
}
```


Calling a function using a function pointer


The implicit dereference method looks just like a normal function call, since **normal function names are pointers to functions anyway**!


```c++
int foo(int x) { return x; }
void main() {
    int (*fcnPtr)(int){ &foo }; 
    // Call function foo(5) through fcnPtr.
    (*fcnPtr)(5); 
 	// Call function through implicit dereference
    fcnPtr(5);
}
```


**Passing functions as arguments to other functions**


Functions used as arguments to another function are sometimes called **callback functions**. Callers can make such functions behave differently by passing different callback functions. In C++ STL, [functors](https://www.geeksforgeeks.org/functors-in-cpp/) are also used for this purpose.


```c++
bool ascending(int x, int y) {
    return x > y; // swap if the first element is greater than the second
}
 
bool descending(int x, int y) {
    return x < y; // swap if the second element is greater than the first
}


// Set _ascending_ as the default parameter of our user-defined comparison parameter
void selectionSort(int *array, int size, bool (*comparisonFcn)(int, int) = ascending) {
    for (int i = 0; i < size - 1; i++) {
        // _best_idx_ is the index of the smallest/largest element we've encountered so far.
        int best_idx = i;
        
        // Look for smallest/largest element remaining in the array (starting at i + 1)
        for (int j = i + 1; j < size; j++) {
            if (comparisonFcn(array[best_idx], array[j])) // COMPARISON DONE HERE
                best_idx = j;
        }
        // Swap our start element with our smallest/largest element
        std::swap(array[i], array[best_idx]);
    }
}


void main() {
    int array[9]{ 3, 7, 9, 5, 6, 1, 8, 2, 4 };
    // Sort the array in descending order using the descending() function
    selectionSort(array, 9, descending);
    // ...
    // Sort the array in ascending order using the ascending() function
    selectionSort(array, 9, ascending);
    // ...
}
```


**Function Pointers VS Normal Pointers**


- Unlike normal pointers, a function pointer points to code, not data. Typically a function pointer stores the start of the executable code.
- Unlike normal pointers, we do not allocate and de-allocate memory when using function pointers.
- Like normal pointers, we can have an array of function pointers, and function pointer can be used in place of switch case.
- Many object oriented features in C++ are implemented using function pointers in C. For example, virtual functions. Class methods are another example implemented using function pointers.


References:


- https://www.learncpp.com/cpp-tutorial/78-function-pointers/
- https://www.geeksforgeeks.org/function-pointer-in-c/






## 28. Inheritance


The capability of a class to derive properties and characteristics from another class.

**Multiple Inheritance**
The constructors of inherited classes are called in the same order in which they are inherited. For example, in the following program, B’s constructor is called before A’s constructor.
```c++
class A {
public:
	A() { cout << "A's constructor" << endl; }
};
class B {
public:
	B() { cout << "B's constructor" << endl; }
};

class C : public B, public A {// Note the order 
public:
	C() { cout << "C's constructor" << endl; }
};

void main() {
	C c;
}
```
```
B's constructor
A's constructor
C's constructor
```

**The diamond problem**  
The diamond problem occurs when two superclasses of a class have a common base class. For example, in the following diagram, the TA class gets two copies of all attributes of Person class, this causes ambiguities.
![](https://media.geeksforgeeks.org/wp-content/uploads/diamondproblem.png)


**Virtual Inheritance**
Virtual inheritance is there to solve this problem. When you specify virtual when inheriting your classes, you're telling the compiler that you only want a single instance.
```c++
class A { public: void Foo() {} };
class B : public virtual A {};
class C : public virtual A {};
class D : public B, public C {};
```

**Virtual base classes**
To share a base class, simply insert the `virtual` keyword in the inheritance list of the derived class. This creates what is called a **virtual base class**, which means there is only one base object. The base object is shared between all objects in the inheritance tree and it is only constructed once.

**Member functions cannot be inherited**
- constructor
- destructor
- assignment operator function
- friend function

References:


- https://www.geeksforgeeks.org/inheritance-in-c/
- https://www.geeksforgeeks.org/multiple-inheritance-in-c/#:~:text=Multiple%20Inheritance%20is%20a%20feature,is%20called%20before%20A's%20constructor.
- https://www.learncpp.com/cpp-tutorial/virtual-base-classes/





## 29. Encapsulation


Encapsulation is defined as binding together the data and the functions that manipulates them, used to hide the values or state of data object inside a class, preventing unauthorized parties' direct access to them.


The process of implementing encapsulation:


- The data members should be labeled as private using the *private* access specifiers.
- The member function which manipulates the data members should be labeled as public using the *public* access specifier.


References:


- https://www.geeksforgeeks.org/encapsulation-in-c/






## 30. Polymorphism


The word polymorphism means having many forms. 


**In C++ polymorphism is mainly divided into two types:**


- Compile time Polymorphism


  - Function overloading


    There are multiple functions with same name but different parameters.


  - Operator overloading


    For example, we can make the operator (‘+’) for string class to concatenate two strings. We know that this is the addition operator whose task is to add two operands. So a single operator ‘+’ when placed between integer operands , adds them and when placed between string operands, concatenates them.


- Runtime Polymorphism (achieved by Function Overriding)


  **[Function overriding](https://www.geeksforgeeks.org/override-keyword-c/)** occurs when a derived class has a definition for one of the member functions of the base class. That base function is said to be *overridden*.


**How to simulate OO-style polymorphism in C?** 


Use function pointers: https://stackoverflow.com/questions/8194250/polymorphism-in-c.


Basically, the base class is a struct, and derived structs must include the base struct at the first position, so that a pointer to the "derived" struct will also be a valid pointer to the base struct.


```c
typedef struct {
   data member_x;
} Base;

typedef struct {
   struct base;
   data member_y;
} Derived;

void function_on_base(struct Base* b); // here we can pass both pointers to derived and to base
void function_on_derived(struct Derived* d); // here we must pass a pointer to the derived class
```


References:


- https://www.geeksforgeeks.org/polymorphism-in-c/
- https://stackoverflow.com/questions/1031273/what-is-polymorphism-what-is-it-for-and-how-is-it-used
- https://stackoverflow.com/questions/524033/how-can-i-simulate-oo-style-polymorphism-in-c






## 31. Virtual Function


A virtual function is a member function which is declared within a base class and is re-defined (**overridden**) by a derived class. 


- Virtual functions should be accessed using pointer or reference of base class type to achieve run time polymorphism.


  Virtual functions are called according to the type of the object instance pointed to or referenced, not according to the type of the pointer or reference. For example:

	```c++
	void main() {
	    Child child;
	    Parent *p = &child;
	}
	```
	Note that because `p` is a base pointer, it only points to the base class portion of `child`. However, also note that `*__vptr` is in the base class portion of the class, so `p` has access to this pointer. Finally, note that `p->__vptr` points to the `Child` virtual table! Consequently, even though `p` is of type `Parent`, it still has access to `Child`’s virtual table (through `__vptr`).

- If we have created a virtual function in the base class and it is being overridden in the derived class then we don’t need virtual keyword in the derived class, functions are automatically considered as virtual functions in the derived class.


**Early binding and Late binding**


Binding refers to the process of converting identifiers (such as variable and performance names) into addresses. Binding is done for each variable and functions. For functions, it means that matching the call with the right function definition by the compiler.


- Early Binding / Static Binding : (compile-time polymorphism)


  As the name indicates, compiler (or linker) directly associate an address to the function call. It replaces the call with a machine language instruction that tells the mainframe to leap to the address of the function.


- Late Binding / dynamic binding : (Run time polymorphism)


  The compiler adds code that identifies the kind of object at runtime then matches the call with the right function definition. This can be achieved by declaring a virtual function.


**How does the compiler perform runtime resolution?**
The compiler maintains two things for classes containing virtual functions:


- [***vtable:***](http://en.wikipedia.org/wiki/Virtual_method_table) A static array of function pointers, sets up at compile time, maintained per class.
  - Where each entry contains the address of each virtual function contained in that class, i.e., each entry is filled out with the most-derived function an object of that class type can call.
  - Every class that uses virtual functions (or is derived from a class that uses virtual functions) is given its own virtual table. 
- [***vptr:*** ](http://en.wikipedia.org/wiki/Virtual_method_table#Implementation) A pointer to vtable, maintained per object instance 
  - If an object of that class is created then a virtual pointer(vptr) is inserted as a data member of the class to point to vtable of that class. For each new object created, a new virtual pointer is inserted as a data member of that class. 
  - `*__vptr` is a real pointer. Consequently, **it makes each class object allocated bigger by the size of one pointer.** It also means that `*__vptr` is inherited by derived classes.


Calling a virtual function is slower than calling a non-virtual function for a couple of reasons: 


First, we have to use the `*__vptr` to get to the appropriate virtual table. Second, we have to index the virtual table to find the correct function to call. Only then can we call the function. As a result, we have to do 3 operations to find the function to call, as opposed to 2 operations for a normal indirect function call, or one operation for a direct function call. However, with modern computers, this added time is usually fairly insignificant.

**Pure Virtual Functions and Abstract Classes**  
- A class is abstract if it has at least one pure virtual function.
- We can have pointers and references of abstract class type.
- If we don't override the pure virtual function in derived class, then derived class also becomes abstract class. 

A pure virtual function (or abstract function) in C++ is a [virtual function](https://www.geeksforgeeks.org/virtual-functions-and-runtime-polymorphism-in-c-set-1-introduction/) for which we don’t have implementation, we only declare it, with `= 0` at the end of the declaration.
```c++
class Shape {    
public:
    virtual void draw() = 0; 
}; 
```
Sometimes implementation of all function cannot be provided in a base class because we don’t know the implementation. Such a class is called abstract class. For example, let Shape be a base class. We cannot provide implementation of function draw() in Shape, but we know every derived class must have implementation of draw().

**Virtual destructor**


Deleting a derived class object using a pointer of base class type that has a non-virtual destructor results in memroy leak, because only the base class destructor will be called (undefined behavior?). To correct this situation, the base class should be defined with a virtual destructor.
```c++
class Base {
public:
	~Base() { cout << "Destroy Base" << endl; }
};

class Derived: public Base {
public:
	~Derived() { cout << "Destroy Derived" << endl; }
};

void main() {
	Base* b = new Derived();
	delete b;
}
```
```
Destroy Base
```
Making base class destructor virtual guarantees that the object of derived class is destructed properly, i.e., **both base class and derived class destructors are called**. 

**Pure virtual destructor**
If a class contains a pure virtual destructor, **it must provide a *function body* for the pure virtual destructor**.
```c++
class Base { 
public: 
    virtual ~Base() = 0; // Pure virtual destructor 
}; 

Base::~Base() { 
    cout << "Pure virtual destructor is called" << endl; 
} 
```
The reason is because destructors (unlike other functions) are not actually ‘overridden’, rather they are always called in the reverse order of the class derivation. This means that a derived class’ destructor will be invoked first, then base class destructor will be called. If the definition of the pure virtual destructor is not provided, then what function body will be called during object destruction? Therefore the compiler and linker enforce the existence of a function body for pure virtual destructors. 

**[Why don't we have virtual constructors?](http://www.stroustrup.com/bs_faq2.html#virtual-ctor)**
A virtual call is a mechanism to get work done given partial information. In particular, "virtual" allows us to call a function knowing only any interfaces and not the exact type of the object. To create an object you need complete information. In particular, you need to know the exact type of what you want to create. Consequently, a "call to a constructor" cannot be virtual.

***虚函数的实现***
https://jacktang816.github.io/post/virtualfunction/


References:
- https://www.geeksforgeeks.org/early-binding-late-binding-c/
- https://www.learncpp.com/cpp-tutorial/125-the-virtual-table/
- https://www.geeksforgeeks.org/virtual-functions-and-runtime-polymorphism-in-c-set-1-introduction/
- https://www.geeksforgeeks.org/pure-virtual-destructor-c/






## 32. Macros vs Functions


|                           MACRO                           |                       FUNCTION                        |
| :-------------------------------------------------------: | :---------------------------------------------------: |
|                   Macro is Preprocessed                   |                 Function is Compiled                  |
|             No Type Checking is done in Macro             |           Type Checking is Done in Function           |
|         Speed of Execution using Macro is Faster          |      Speed of Execution using Function is Slower      |
| Before Compilation, macro name is replaced by macro value | During function call, transfer of control takes place |


References:


- https://www.geeksforgeeks.org/macros-vs-functions/






## 33. Iterator Invalidation


When we iterate over our container using iterators then it may happen that iterator gets invalidated. This may be due to change in the size of the container while iterating.


Invalidation of iterator does not always mean that dereferencing such an iterator causes a program to crash. It also includes the possibility that iterator does not point to an element which it is supposed to point.


References:


- https://www.geeksforgeeks.org/iterator-invalidation-cpp/






## 34. Differences between C and C++


C++ can be said a superset of C. Major added features in C++ are Object-Oriented Programming, *Exception Handling* and rich C++ Library. 


|                              C                               |                             C++                              |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| C does no support polymorphism, encapsulation, and inheritance which means that C does not support object oriented programming. | C++ supports polymorphism, encapsulation, and inheritance because it is an object oriented programming language. |
| **Data and functions are separated** in C because it is a procedural programming language. | Data and functions are encapsulated together in form of an object in C++. |
| C is a function driven language because C is a procedural programming language. | C++ is an object driven language because it is an object oriented programming. |
|      Functions in C are not defined inside structures.       |       Functions can be used inside a structure in C++.       |
|       Namespace features are not present inside the C.       | [Namespace](https://www.geeksforgeeks.org/namespace-in-c/) is used by C++, which avoid name collisions. |
|         Reference variables are not supported by C.          |          Reference variables are supported by C++.           |
|     Virtual and friend functions are not supported by C.     | [Virtual](https://www.geeksforgeeks.org/virtual-function-cpp/) and [friend functions](https://www.geeksforgeeks.org/friend-class-function-cpp/) are supported by C++. |
| Instead of focusing on data, C focuses on method or process. | C++ focuses on data instead of focusing on method or procedure. |
| C provides [malloc()](https://www.geeksforgeeks.org/dynamic-memory-allocation-in-c-using-malloc-calloc-free-and-realloc/) and [calloc()](https://www.geeksforgeeks.org/dynamic-memory-allocation-in-c-using-malloc-calloc-free-and-realloc/) functions for [dynamic memory allocation](https://www.geeksforgeeks.org/dynamic-memory-allocation-in-c-using-malloc-calloc-free-and-realloc/), and [free()](https://www.geeksforgeeks.org/dynamic-memory-allocation-in-c-using-malloc-calloc-free-and-realloc/) for memory de-allocation. | C++ provides [new operator](https://www.geeksforgeeks.org/new-and-delete-operators-in-cpp-for-dynamic-memory/) for memory allocation and [delete operator](https://www.geeksforgeeks.org/new-and-delete-operators-in-cpp-for-dynamic-memory/) for memory de-allocation. |
|          C structures don’t have access modifiers.           |            C ++ structures have access modifiers.            |






## 35. `union`


A `union` is a user-defined type in which all members share the same memory location. This definition means that at any given time, a union can contain no more than one object from its list of members. It also means that no matter how many members a union has, it always uses only enough memory to store the largest member.


**The purpose of union is to save memory by using the same memory region for storing different objects at different times.**


```c++
union RecordType {  // Declare a simple union type
    char   ch;
    int    i;
    long   l;
    float  f;
    double d;
};

void main() {
    RecordType t;
    cout << sizeof(RecordType) << endl;  // 8 bytes
    t.i = 5;    // t holds an int
    t.f = 7.25; // t now holds a float
}
```


References:


- https://docs.microsoft.com/en-us/cpp/cpp/unions?view=msvc-160




## 36. `friend`


- A non-member function can access the private and protected members of a class if it is declared a *friend* of that class. That is done by including a declaration of this external function within the class, and preceding it with the keyword `friend`.
- A friend class is a class whose members have access to the private or protected members of another class.


References:


- http://www.cplusplus.com/doc/tutorial/inheritance/


