# CPP_NOTES

## This file contains the notes are taken along side re-learning C++. 

- A good rule of thumb is that after reading a value with std::cin, remove the newline from the stream. This can be done using the following:
  `std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');`
  
- About const keyword:
  ```
  const double gravity { 9.8 }; // preferred use of const before type
  int const sidesInSquare { 4 }; // okay, but not preferred
  ```

- Floating point equality
  * The equality operators (== and !=) are much more troublesome.
    Consider operator==, which returns true only if its operands are exactly equal.
    Because even the smallest rounding error will cause two floating point numbers
    to not be equal, operator== is at high risk for returning false when a true might be expected.
    Operator!= has the same kind of problem.
    
  * Donald Knuth, a famous computer scientist, suggested the following method
    in his book `“The Art of Computer Programming, Volume II: Seminumerical Algorithms (Addison-Wesley, 1969)”`:

    ```
    // return true if the difference between a and b is within epsilon percent of the larger of a and b
    bool approximatelyEqual(double a, double b, double epsilon) {
        return (std::abs(a - b) <= (std::max(std::abs(a), std::abs(b)) * epsilon));
    }  
    ```

    
  C++ provides 6 bit manipulation operators, often called bitwise operators:
  
  |   Operator    |     Symbol	  |    Form	   |            Operation                 |
  | ------------- | ------------- | ---------- | -------------------------------------|
  |  left shift   |      <<       |.  x << y   |  all bits in x shifted left y bits   |
  |  right shift  |      <<       |.  x >> y.  |. all bits in x shifted right y bits  |
  |  bitwise NOT  |      ~	      |   ~x	   |  all bits in x flipped               |
  |  bitwise AND  |      &	      |   x & y	   |  each bit in x AND each bit in y     |
  |  bitwise OR	  |      \|	      |   x \| y	   |  each bit in x OR each bit in y      |
  |  bitwise XOR  |      ^	      |   x ^ y	   |  each bit in x XOR each bit in y     |
  -------------------------------------------------------------------------------------
    
  * Internal global variables definitions:
    ```
    static int g_x;          // defines non-initialized internal global variable (zero initialized by default)
    static int g_x{ 1 };     // defines initialized internal global variable 
    const int g_y { 2 };     // defines initialized internal global const variable 
    constexpr int g_y { 3 }; // defines initialized internal global constexpr variable 
    static int foo() {};     // defines internal function
    ```

* If you need global constants and your compiler is C++17 capable, prefer defining inline constexpr global variables in a header file.
```
namespace constants {
    inline constexpr double pi { 3.14159 }; // note: now inline constexpr
    inline constexpr double avogadro { 6.0221413e23 };
    inline constexpr double my_gravity { 9.2 }; // m/s^2 -- gravity is light on this planet
}
```


* Using declarations
    - The using declaration of using std::cout; tells the compiler that we’re going to be using the object cout from the std namespace. 
      So whenever it sees cout, it will assume that we mean std::cout.
    - If there’s a naming conflict between std::cout and some other use of cout, std::cout will be preferred.


  `using std::cout; // this using declaration tells the compiler that cout should resolve to std::cout`


* The using directive
    - The using directive using namespace std; tells the compiler that we want to use everything in the std namespace. 
      So if the compiler finds a name it doesn’t recognize, it will check the std namespace. 
      Consequently, when the compiler encounters cout (which it won’t recognize), it’ll look in the std namespace and find it there.
    - If there’s a naming conflict between std::cout and some other use of cout, the compiler will flag it as an error (rather than preferring one instance over the other).


  `using namespace std; // this using directive tells the compiler that we're using everything in the std namespace!`

* Typedefs and type aliases
    - Given the following typedef:
        - ` typedef double distance_t; // define distance_t as an alias for type double `
    
    - This can be declared as the following type alias:
        - ` using distance_t = double; // define distance_t as an alias for type double`
    + The two are functionally equivalent.

* Const member functions
  - Const class objects can only explicitly call const member functions.
  - A const member function is a member function that guarantees it will not modify the object or call any non-const member functions (as they may modify      the object).
  - Const member functions can also be called by non-const objects.
  - ` Best practice Make any member function that does not modify the state of the class object const, so that it can be called by const objects.`
  - Const members can not return non-const references to members
  - Because passing objects by const reference is common, your classes should be const-friendly. That means making any member function that does not          modify the state of the class object const!
* Static keyword
  - static variables keep their values and are not destroyed even after they go out of scope when declared in functions.
  - The static keyword has another meaning when applied to global variables -- it gives them internal linkage (which restricts them from being seen/used     outside of the file they are defined in).
  - Static members are not associated with class objects.
  - it is better to think of static members as belonging to the class itself, not to the objects of the class.
  - it can be accessed directly using the class name and the scope resolution operator (Class::static_member).
  - When we declare a static member variable inside a class, we’re telling the compiler about the existence of a static member variable, but not             actually defining it (much like a forward declaration).
  - Because static member variables are not part of the individual class objects (they are treated similarly to global variables, and get initialized       when the program starts), you must explicitly define the static member outside of the class, in the global scope.
  - If the static member variable is a const int, no explicit definition line is needed.
 
* Static member functions
  - Static member functions have no *this pointer.
  - static member functions can directly access other static members (variables or functions), but not non-static members. This is because non-static members         must belong to a class object, and static member functions have no class object to work with!
* Friend functions and classes
  - A friend function is a function that can access the private members of a class as though it were a member of that class. In all other regards, the friend         function is just like a normal function. A friend function may be either a normal function, or a member function of another class. To declare a friend           function, simply use the friend keyword in front of the prototype of the function you wish to be a friend of the class. It does not matter whether you           declare the friend function in the private or public section of the class.
  - Note that we have to pass an Class object to friend func(). This is because func() is not a member function. It does not have a *this pointer, nor does it       have a Class object to work with, unless given one.
  - A function can be a friend of more than one class at the same time.
  - It is also possible to make an entire class a friend of another class. This gives all of the members of the friend class access to the private members of the     other class.
  - A few additional notes on friend classes. First, even though Display is a friend of Storage, Display has no direct access to the *this pointer of Storage         objects. Second, just because Display is a friend of Storage, that does not mean Storage is also a friend of Display. If you want two classes to be friends       of each other, both must declare the other as a friend. Finally, if class A is a friend of B, and B is a friend of C, that does not mean A is a friend of C.
  - You can make a single member function a friend. But u musy move the definition of class with member function to be friend before the definition of class that declares the friend member function.
  - Friending is commonly used when defining overloaded operators (which we’ll cover in a later chapter), or less commonly, when two or more classes need to work together in an intimate way.

  - Note that making a specific member function a friend requires the full definition for the class of the member function to have been seen first.

* Dynamic memory allocation with new and delete
  - A pointer that is pointing to deallocated memory is called a dangling pointer. Indirection through- or deleting a dangling pointer will lead to undefined         behavior.
  - Set deleted pointers to 0 (or nullptr in C++11) unless they are going out of scope immediately afterward.
  - ` int* value { new (std::nothrow) int }; // value will be set to a null pointer if the integer allocation fails `
  - Using delete -> If ptr is non-null, the dynamically allocated variable will be deleted. If it is null, nothing will happen.

* Pointers and const
  - Pointer to const value
    - A pointer to a const value is a (non-const) pointer that points to a constant value.
    - To declare a pointer to a const value, use the const keyword before the data type:
    ``` 
    const int value{ 5 };
    const int* ptr{ &value };
    *ptr = 6;
    ```
    - A pointer to a constant variable can point to a non-constant variable. Think of it this way: a pointer to a constant variable treats the variable     as constant when it is accessed through the pointer, regardless of whether the variable was initially defined as const or not.
    - Because a pointer to a const value is not const itself (it just points to a const value), the pointer can be redirected to point at other values.
  - Const pointers
    - A const pointer is a pointer whose value can not be changed after initialization.
    - Because the value being pointed to is still non-const, it is possible to change the value being pointed to indrectly through the const pointer.
  - Const pointer to a const value
    - It is possible to declare a const pointer to a const value by using the const keyword both before the type and before the variable name
    - A const pointer to a const value can not be set to point to another address, nor can the value it is pointing to be changed through the pointer.
  - Recapping:
    ```
    A non-const pointer can be redirected to point to other addresses.
    A const pointer always points to the same address, and this address can not be changed. 

    A pointer to a non-const value can change the value it is pointing to. These can not point to a const value.
    A pointer to a const value treats the value as const (even if it is not), and thus can not change the value it is pointing to. 
    ``` 
* Reference to const value
  - Unlike references to non-const values, which can only be initialized with non-const l-values, references to const values can be initialized with         non-const l-values, const l-values, and r-values. 
  ```
  int x{ 5 };
  const int& ref1{ x }; // okay, x is a non-const l-value
 
  const int y{ 7 };
  const int& ref2{ y }; // okay, y is a const l-value
 
  const int& ref3{ 6 }; // okay, 6 is an r-value
  ```
  - References to r-values extend the lifetime of the referenced value
    - Normally r-values have expression scope, meaning the values are destroyed at the end of the expression in which they are created.
    - When a reference to a const value is initialized with an r-value, the lifetime of the r-value is extended to match the lifetime of the reference  
* Virtual functions and polymorphism
  - A virtual function is a special type of function that, when called, resolves to the most-derived version of the function that exists between the        base and derived class. This capability is known as polymorphism.
  - A derived function is considered a match if it has the same signature (name, parameter types, and whether it is const) and return type as the base      version of the function. Such functions are called overrides. 
