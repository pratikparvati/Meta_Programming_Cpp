# C++ Examples

## Slide 3


Ex. 1

```C++
// Function template with multiple parameter
#include <iostream>

template <typename RT, typename T1, typename T2>
RT max(T1 a, T2 b)
{
    return (a > b) ? a : b;
}


int main()
{
    double x;
    // note that return type cannot be deduced, so must always be specified
    x = max<double, double, int>(23.9, 45);  // double max<double, int>
    x = max<double>(15, 11.5);               // double max<int, double>
    x = max<double>(15.5, 11);               // double max<double, int>
    return EXIT_SUCCESS;
}

/*
// We can also use decltype to deduce the return type
template<typename T1, typename T2>
auto max(T1 a, T2 b) -> decltype((a > b) ? a : b)
{
    return (a > b) ? a : b;
}
*/
```

Ex.2

```C++
// Class template
#include <iostream>

template <class T>
class MyClass
{
    T a, b;

public:
    MyClass(T first, T second)
    {
        a = first;
        b = second;
    }
    T getMaxval();
};

template <class T>
T MyClass<T>::getMaxval()
{
    return (a > b) ? a : b;
}

int main()
{
    MyClass<int> Obj1(50, 150);
    std::cout << "Maximum of 50 and 150 = " << Obj1.getMaxval() << std::endl;

    MyClass<char> Obj2('A', 'a');
    std::cout << "Maximum of 'A' and 'a' = " << Obj2.getMaxval() << std::endl;

    return 0;
}

```

Ex 3
```C++

// Example fpr Non type parameter
template<int N>; 	// N is a constant expression determined at compile time
template<size_t N, size_t M>;	// N, M is a constant expression determined at compile time

template<typename T, size_t n>
void printValues(T (&arr)[n])
{
    for(size_t i = 0; i < n; ++i)
    { 
        std::cout << arr[i] << ' ';
    } 
    std::cout << std::endl;
}
```

## Slide 4

Ex 1

```C++
// This template function doesn't give correct result for char *
template <typename T>
T* max(T* a, T* b)
{
    return (*a > *b) ? a : b;
}

// Explicit specialization of max function for const char *
template<>
const char* max(const char* a, const char* b)
{
    return strcmp(a, b) > 0 ? a : b;
}
```

Ex 2

```C++
/**
 * The below template class doesn't give correct result for below piece of code
 * FindMax<const char*> Max
 * const char* str1 = "Good"
 * const char* str2 -= "Bye"
 * const char * res = Max.getMax(str1, str2);
 */
template<typename T>
class FindMax
{
public:
    T getMax(T a, T b)
    {
        return (a > b)? a : b;
    }
};

// Hence, explicitly specialized class defined for const char *
template<>
class FindMax<const char*>
{
public:
    const char* getMax(const char* a, const char* b)
    {
        return (strcmp(a,b) > 0)? a : b;
    }
};
```

Ex 3

```C++
// Partial Specialization
template <class T1, class T2>
class MyClass
{
public:
    MyClass()
    {
        std::cout << "MyClass<T1, T2>::MyClass() --> regular constructor\n";
    }
    T1 _a;
    T2 _b;
};

// Partial specialization class to int as second template parameter
template <class T>
class MyClass<T, int>
{
public:
    MyClass()
    {
        std::cout << "MyClass<T, int>::MyClass() --> partial specializations\n";
    }

    T _a;
    int _b;
};

int main()
{
    MyClass<double, double> a;
    MyClass<double, int> b; // partial specialization
    return 0;
}

/*OUTPUT
MyClass<T1, T2>::MyClass() --> regular constructor
MyClass<T, int>::MyClass() --> partial specializations
*/
```

## Slide 5

Ex 1
```C++
// Dependent Names
#include <iostream>

template <typename T>
struct MyStruct {
  typedef T value_type;
  void print() {
    std::cout << "The type of this struct is " << typeid(value_type).name() << std::endl;
  }
};

int main() {
  MyStruct<int> s;
  s.print();
  return 0;
}

/* Explaination
* When using templates there is a distinction between the point of definition of the template and the point of instantiation (where templates are used). Names that depend on a template don't get bound until the point of instantiation
* member typedef value_type that is used to store the template parameter T
* When we instantiate MyStruct<int>, value_type is replaced with int, so the output of the print() function will be "The type of this struct is int".
* The name value_type is dependent on the template parameter T, as it is only defined when the template is instantiated with a specific type */
```
Ex 2

```C++
// Dependent name compile time checking
#include <iostream>
#include <typeinfo>

template <typename T>
struct MyStruct {
  typedef T value_type;
  void print() {
    std::cout << "The type of this struct is " << typeid(value_type).name() << std::endl;
  }
  void foo() {
     value_type::bar();
  }

};

class OtherClass {
public:
    static void bar()
    {
        std::cout << "Hello from OtherClass::bar!" << std::endl;
    }
};

int main() {
  MyStruct<int> s;
  s.print();

  MyStruct<OtherClass> oc;
  oc.foo();

  //s.foo(); this will throw compile timeerror
  return 0;
}
```


Ex 3

```C++
// Template Template Parameter
#include <iostream>
#include <vector>

// Define a template class that takes a type T and an allocator type as its arguments
template <typename T, typename Allocator = std::allocator<T>>
class MyVector {
public:
    // Define some member functions
    void push_back(const T& value) {
        vec_.push_back(value);
    }

    std::size_t size() const {
        return vec_.size();
    }

private:
    std::vector<T, Allocator> vec_;
};

// Define a template template parameter that takes a template argument
template <template <typename, typename> class Container>
class MyContainer {
public:
    // Define some member functions that use the template argument
    template <typename T>
    void push_back(const T& value) {
        container_.push_back(value);
    }

    std::size_t size() const {
        return container_.size();
    }

private:
    Container<int, std::allocator<int>> container_;
};

int main() {
    // Instantiate the MyVector template with int and std::allocator<int> as its arguments
    MyVector<int, std::allocator<int>> my_vector;

    // Instantiate the MyContainer template with MyVector as its template argument
    MyContainer<MyVector> my_container;

    // Add some elements to the MyVector container
    my_vector.push_back(1);
    my_vector.push_back(2);
    my_vector.push_back(3);

    // Add some elements to the MyContainer container
    my_container.push_back(4);
    my_container.push_back(5);
    my_container.push_back(6);

    // Print the sizes of both containers
    std::cout << "Size of MyVector container: " << my_vector.size() << std::endl;
    std::cout << "Size of MyContainer container: " << my_container.size() << std::endl;

    return 0;
}

/* Explaination
* template class MyVector that takes a type T and an allocator type as its arguments
* template template parameter MyContainer that takes a template argument
* In main(), we instantiate MyVector with int and std::allocator<int> as its arguments, and we instantiate MyContainer with MyVector as its template argument.
```

Ex 4

```C++
// Variadic function templates
#include <iostream>

template <typename... Args>
void foo(Args... args)
{
    std::cout << sizeof...(Args) << " arguments were passed." << std::endl;
}

int main()
{
    foo();                // prints "0 arguments were passed."
    foo(1);               // prints "1 argument was passed."
    foo(1, "hello", 3.14); // prints "3 arguments were passed."
    return 0;
}
```

Ex 5 

```C++
// Variadic class template
#include <iostream>

template <typename... Args>
class Foo
{
public:
    void bar()
    {
        std::cout << sizeof...(Args) << " arguments were passed." << std::endl;
    }
};

int main()
{
    Foo<> f0;
    f0.bar();                 // prints "0 arguments were passed."
    
    Foo<int> f1;
    f1.bar();                 // prints "1 argument was passed."
    
    Foo<int, std::string, double> f2;
    f2.bar();                 // prints "3 arguments were passed."
    
    return 0;
}
```

## Slide 8

Ex 1

```C++
#include <iostream>

template<int N>
struct Fibonacci {
    static const int value = Fibonacci<N-1>::value + Fibonacci<N-2>::value;
};

template<>
struct Fibonacci<0> { // Explicit specialization
    static const int value = 0;
};

template<>
struct Fibonacci<1> { // Explicit specialization
    static const int value = 1;
};

static_assert(Fibonacci<0>::value == 0);
static_assert(Fibonacci<1>::value == 1);
static_assert(Fibonacci<2>::value == 1);
static_assert(Fibonacci<3>::value == 2);
static_assert(Fibonacci<4>::value == 3);
static_assert(Fibonacci<5>::value == 5);

template<int N, int Prev = 0, int Curr = 1>
struct PrintFibonacci {
    static_assert(N >= 0, "N must be non-negative");
    static_assert(Prev <= Curr, "The previous Fibonacci number must be less than or equal to the current one");
    static_assert(N == 0 || Prev + Curr > Prev, "Overflow detected");
    
    static void print() {
        std::cout << Curr << " ";
        PrintFibonacci<N-1, Curr, Prev+Curr>::print();
    }
};

template<int Prev, int Curr> // partial specialization
struct PrintFibonacci<0, Prev, Curr> { 
    static void print() {}
};

int main() {
    std::cout << "Fibonacci sequence up to 55:" << std::endl;
    PrintFibonacci<10>::print();
    std::cout << std::endl;
    return 0;
}
```

## Slide 9

Ex 1

```C++
#include <iostream>
#include <type_traits>

template<typename T>
void print_type_traits() {
    std::cout << "is_integral: " << std::is_integral<T>::value << std::endl;
    std::cout << "is_floating_point: " << std::is_floating_point<T>::value << std::endl;
    std::cout << "is_arithmetic: " << std::is_arithmetic<T>::value << std::endl;
    std::cout << "is_pointer: " << std::is_pointer<T>::value << std::endl;
    std::cout << "is_reference: " << std::is_reference<T>::value << std::endl;
    std::cout << "is_array: " << std::is_array<T>::value << std::endl;
    std::cout << "is_class: " << std::is_class<T>::value << std::endl;
    std::cout << "is_function: " << std::is_function<T>::value << std::endl;
}

int main() {
    std::cout << "int:" << std::endl;
    print_type_traits<int>();
    std::cout << std::endl;

    std::cout << "float:" << std::endl;
    print_type_traits<float>();
    std::cout << std::endl;

    std::cout << "double*:" << std::endl;
    print_type_traits<double*>();
    std::cout << std::endl;

    std::cout << "const char&:" << std::endl;
    print_type_traits<const char&>();
    std::cout << std::endl;

    return 0;
}
```

Ex 2

```C++
#include <iostream>
#include <type_traits>
#include <cmath>

template <typename T>
typename std::enable_if<std::is_floating_point<T>::value, bool>::type
is_negative(T x)
{
    return x < 0;
}

template <typename T>
typename std::enable_if<!std::is_floating_point<T>::value, bool>::type
is_negative(T x)
{
    return false;
}

int main()
{
    std::cout << std::boolalpha;
    std::cout << is_negative(-10.5) << std::endl; // prints true
    std::cout << is_negative(0) << std::endl; // prints false
    std::cout << is_negative(10) << std::endl; // prints false
    return 0;
}

/*Explaination
* The first template specialization is enabled only if T is a floating-point type (std::is_floating_point<T>::value == true). It returns true if the argument is negative, and false otherwise.
* The second template specialization is enabled only if T is not a floating-point type (std::is_floating_point<T>::value == false). It always returns false.
```

## Slide 10

Ex 1

```C++
#include <iostream>
#include <type_traits>

template <typename T>
struct add_const_ref {
    using type = const T&;
};

int main() {
    using my_type = add_const_ref<int>::type; // my_type = int&
    static_assert(std::is_same_v<my_type, const int&>, "Error: types are not the same");

    std::cout << "Type after adding const and reference: " << typeid(my_type).name() << std::endl;

    return 0;
}
```

Ex 2

```C++
#include <iostream>
#include <type_traits>

// Template metafunction that returns the type of the input argument with const qualifier
template <typename T>
struct AddConst {
    using type = const T;
};

// Specialization for pointers to void type
template <>
struct AddConst<void*> { // this metafunction is specialized for certain types to provide custom behavior
    using type = const void*;
};

int main() {
    // Test with int
    using IntType = int;
    using IntTypeWithConst = AddConst<IntType>::type;
    std::cout << "IntTypeWithConst is " << std::boolalpha << std::is_same_v<const int, IntTypeWithConst> << std::endl;
    
    // Test with pointer to double
    using DoublePtrType = double*;
    using DoublePtrTypeWithConst = AddConst<DoublePtrType>::type;
    std::cout << "DoublePtrTypeWithConst is " << std::boolalpha << std::is_same_v<const double*, DoublePtrTypeWithConst> << std::endl;
    
    // Test with pointer to void
    using VoidPtrType = void*;
    using VoidPtrTypeWithConst = AddConst<VoidPtrType>::type;
    std::cout << "VoidPtrTypeWithConst is " << std::boolalpha << std::is_same_v<const void*, VoidPtrTypeWithConst> << std::endl;

    return 0;
}
```

Ex 3

```C++
#include <iostream>
#include <vector>

template<typename T>
struct Addable {
    static const bool value = false;
};

template<typename T>
struct Addable<std::vector<T>> {
    static const bool value = true;
};

template<typename T>
typename std::enable_if<Addable<T>::value, T>::type
sum(const T& v) {
    typename T::value_type s = typename T::value_type();
    for (auto& x : v) {
        s += x;
    }
    return s;
}

int main() {
    std::vector<int> v{1, 2, 3, 4, 5};
    std::cout << "sum(v) = " << sum(v) << std::endl;

    // int type is not addable
    //std::cout << "sum(10) = " << sum(10) << std::endl;

    return 0;
}
/*Explaination
* define a template metafunction Addable that checks if a type is addable or not
* we specialize this metafunction for std::vector<T> as we know that vectors can be added element-wise.
* We then define a function sum that uses template argument deduction. The template argument deduction is performed based on the function argument v.
* If Addable<T>::value is true, then the function returns the sum of the elements of v. Otherwise, a compilation error is generated as the function cannot be instantiated for non-addable types. */
```

## Slide 14

Ex 1

```C++
// constant expression in C++
#include <iostream>

// Define a constexpr function that calculates the factorial of an integer
constexpr int factorial(int n) {
    return n <= 1 ? 1 : n * factorial(n - 1);
}

int main() {
    // Declare a variable with const expression using a constexpr function
    const int max_size = factorial(5);
    // This variable is assigned a value at compile-time and its value cannot be modified during run-time
    
    int arr[max_size]; // Use the variable to define the size of an array
    
    // Output the value of the const expression
    std::cout << "Maximum array size is " << max_size << std::endl;
    
    // Attempt to modify the value of the const expression, this will result in a compiler error
    // max_size = 5; // Uncommenting this line will result in a compiler error
    
    return 0;
}
```