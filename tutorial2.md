title: Software Tutorial - More C & Basics of Git
output: tutorial2.html
controls: true
style: style.css

--

# User Defined Types & Pointers

--

### Array

* <span class="hidden">Apart from the simple data types we mentioned in the previous tutorial, we can have complex user defined types. </span>Array<span class="hidden">, which is the implementation of list data structure,</span> is one of the user defined data structure.

* Array stores *fixed size* elements consecutively in the memory<span class="hidden">, and we can find out the address of the element using simple arithmetic (*array location* + *element size* \* *element index*).</span>

* Declaration example:

  ```c
  int list[10];            // initialize a 10 element integer array with undefined values
  int list2[] = {1, 2, 3}; // initialize a 3 element integer array with values
  int list3[5] = {0};      // initialize all 5 elements with 0 (this is special syntax)
  ```
  
* We can access the element of the array by *name*\[*index*\]. The index starts with 0.   
  
  Example: `list[0]` get the first element of array `list`.
  
* <span class="hidden">If the index is larger than the size of the array, the element we access will be wrong and errors may occur (either logical or causing the program to crash). This is usually called</span> *index out of range*<span class="hidden">.</span> 

--

### String

* In general array does not contain size information. <span class="hidden">We would encode the size information either by passing a size explicitly or by terminate the array with a special element.</span>

* String is an array of characters, terminated by a *null character* which is a zero (`\0` in string). We use ASCII code to store our characters. 

* String literals are surrounded by double quotes: `"a string"`. Single quotes are used to represent a single character: `'a'`.

* Common escape sequence<span class="hidden">: `\"` representing a double quote character, `\\` representing a backslash character, `\n` representing linefeed (LF, which is Unix style newline) character, `\r` representing carriage return (CR, CRLF is the Windows style newline), `\t` tab, `\0` null character and a lot more...</span>

* Example:

  ```c
  char text[] = "abc";
  char text2[] = {'a', 'b', 'c', '\0'};
  ```

--

### Enumeration & typedef

* Enumerations are used to assign meaningful names to certain integer values.

* Example:

  ```c
  enum Direction {
      UP = 5,
      DOWN
  };
  enum Direction direction = UP;
  ```
  
* The numerical values assigned are consecutive and begins at 0 by default, but can be overridden. If overridden, it is programmer's responsibility to check for duplicate: `enum Test {A, B, C = 1};` The `B` and `C` are both 1.

* `typedef` could be used to assign meaningful names to types, for abstraction or better readability: `typedef <type> <name>`. Example: `typedef long long UUID`.

* This can also be used to avoid using `enum` in the type signature of variables: `typedef enum {A} Foo` and you can define a variable by `Foo a = A`. <span class="hidden">Technically this enum is anonymous. This also means that the declaration itself can be used as a type name. (see the example next section)</span>

--

### Structure

* Structures can be used to store multiple data in one type to represent more complex data types:
  ```c
  struct InterviewApplicant {
      char itsc[10];
      char name[20];
      enum {
          MECHANICAL,
          SOFTWARE,
          HARDWARE,
          MANAGEMENT
      } pos;
  };
  typedef struct InterviewApplicant InterviewApplicant_t;
  InterviewApplicant_t john;
  ```
  
* Similar to enumerations, `struct` keyword is required before the name of the struct type, we could use `typedef` to define a shorthand.

* We can access the fields of a struct through the dot  (`.`) operator. Example: `john.pos = SOFTWARE;`.

--

### Pointer

* Pointer is a variable *pointing* towards certain memory location. Usually used when we want to avoid copying the value.

* `&` is *address-of* operator<span class="hidden"> when used as a unary operator, means get a pointer towards a variable</span>. `*` denotes pointer type when used in type declaration, and *dereference* operator when used with a pointer <span class="hidden">(get back the variable it points towards)</span>.

  ```c
  int a = 0;
  int b = a;            // a is copied
  b += 1;               // this only affects b but not a
  printf("%d\n", a);    // a is not modified, still 0
  int *pointerToA = &a; // &a is the address of a, this is the pointer to a
  *pointerToA += 1;     // *pointerToA means dereference the pointer
  printf("%d\n", a);    // a is modified now
  ```

* Array variables are also pointer variables, pointing towards the first element of the array. The value of the array variable is also its address.

  ```c
  int a[] = {1, 2, 3};
  printf("%d", *a == a[0]);
  ```

--

### Pointer Arithmetic & Array

* Pointers and array variables are indeed the same. <span class="hidden">The only difference is that pointers can be changed but arrays are constant (pointer constant, not value constant).</span>  
  For an array of type `T []`, its pointer equivalent is `T *const`.

* For a pointer `p`, `p + c` where `c` is an integer, means `&(p[c])`. So `*(p + c)` means `p[c]`.

* For 2 pointers `p1` and `p2`, `p2 - p1` means the number of elements between the 2 pointers.

  ```c
  int array[5];
  int *p1 = array;
  int *p2 = array + 4;
  printf("%d", p2 - p1);  // 4
  ```

* However, pointer is still different from array. <span class="hidden">Address of array is the same as the array itself. However, pointer to a pointer is valid. Please refer to the last example in the next slide.</span>

--

### Fixed-Size Array, Multi-Dimensional Array & Pointer of Pointer

* Fixed-size array, which contains the number of elements in its type signature, is ***not*** the same as an ordinary array, and different size means different type.<span class="hidden">  
  This is because the type definition of fixed size array specifies the size of the entire array.</span>

* Multi-Dimensional array means having an array of arrays. <span class="hidden">Only fixed-size arrays can be used as the elements, as the size of the elements of an array has to be known. It is used similar to normal arrays. (the left-most size is the inner element)</span>

  ```c
  int matrix34[3][4];
  int (*ptr)[3] = matrix34;  // pointer to a fixed size array
  matrix34[0][0] = 0;
  ```

* We can create pointer to pointer by applying the asterisk twice: `int **a`. <span class="hidden">This is usually used to represent pointer to array. For example an array of string pointers:</span>

  ```c
  char foo[] = "foo", bar[] = "bar";
  char *_foo = foo, *_bar = bar;
  char **names[2];
  names[0] = &_foo;  // wrong when using &foo as foo is an array 
  names[1] = &_bar;  // wrong when using &bar as bar is an array
  for (int i = 0; i < 2; ++i) printf(*names[0]);
  ```

--

# Preprocessor

## Welcome to the world of metaprogramming

--

### What is a Preprocessor

* Compiler build programs, preprocessor generate code for compiler. However, we usually consider preprocessing as a part of the compiling process.
* Preprocessor programming is called *metaprogramming*, and is a powerful technique in C/C++ programming.

> Warning: Metaprogramming is powerful, but also hard and dangerous. Overusing it would make your code ***unreadable*** and ***unmaintainable***.

--

### Include Statement

* Include statement just copy the content of another file to this file. `#include "something.h"`

* The compiler searches through *include path* for the file.

* Source files (`*.c`) contains *definition* of variables and functions.

* Header files (`*.h`) usually contains:

  * Function *declarations*.

  * External variable (not creating a variable, just telling the compiler to find it in another source file).

    ```c
    extern int something;
    ```

  * Type definitions, such as `typedef`, `struct`, `enum`.

* Source and headers are usually used together. Source contains the *implementation* and header provides the *interface*.

--

### Macro

* Object-like Macro: just a shorthand for some expression. (indeed not necessarily an expression)

  ```c
  #define TWO (1 + 1)
  int a = TWO; // after preprocessing: int a = (1 + 1);
  ```

* Function-like Macro: takes argument and put them into the expression.

  ```c
  #define MUL(a, b) ((a) * (b))
  int a = MUL(2 + 2, 2); // after preprocessing: int a = ((2 + 2) * (2));
  ```

* Function-like Macro replace the arguments directly, without the extra brackets the above example will become this:

  ```C
  #define MUL(a, b) (a * b)
  int a = MUL(2 + 2, 2); // after preprocessing: int a = (2 + 2 * 2);
  ```

* Macro are usually defined in header files, it can improve code readability and maintainability if used wisely.

* There are a lot of techniques in macro, check [GCC Tutorial](https://gcc.gnu.org/onlinedocs/cpp/Macros.html) and [Tips and tricks](https://github.com/pfultz2/Cloak/wiki/C-Preprocessor-tricks,-tips,-and-idioms) if interested.

--

### Conditional Compilation Directives

* Part of the code can be compiled or not compiled depending on certain condition:

* `#ifdef` and `#ifndef` checks for if an identifier is defined or not defined, usually used with macro. <span class="hidden">(compile if a certain macro is defined, which can be passed from compiler options.)</span>

  ```c
  #ifdef PCA
  printf("PCA is defined");
  #endif
  ```
  
* `#if` checks if the expression evaluates to a non-zero value. Simple expressions can be used in the condition (with macros replaced).

* `#elif` (else if) and `#else` can also be used.

  ```c
  #if 1 + 1 == 2
  printf("1 + 1 = 2");
  #else
  printf("1 + 1 != 2");
  #endif
  ```

--

### Misc.

* Include guard: prevent header file content from imported multiple times.  
  It can be implemented with macro and conditional compilation:

  ```C
  #ifndef MODULE_NAME
  #define MODULE_NAME
  // module content
  #endif
  ```

  or it can also be implemented through special compiler directive (modern compilers usually support this):

  ```C
  #pragma once
  ```

* `\` at the end of line would concatenate the next line into the current line, usually used with macro. Example:

  ```C
  #define IF_BLOCK        \
      if (1 + 1 == 2)     \
          printf("true"); \
      else                \
          printf("false");
  ```

--

# Git

## Even better than Google Cloud

--

### What is Git

* A distributed version management tool for programmers.
* Used for version management and conflict handling.
* Multiple people editing the same thing is problematic. Git handles that by calculating diffs and join them.
* Git provides multiple branches for management. Different branch for different purposes, such as stable or feature branch, and provides operations for managing them.

![merge](./tutorial2.assets/merge_branch_005.png)

--

### Common Operations & Terminologies

* **Add**: Propose changes for later operations (usually used with commit).
* **Commit**: Save the changes to the current branch, with message describing the changes.
* **Push**: Send the current branch changes to remote branch.
* **Pull**: Get the changes from the remote branch to the current branch.
* **Merge**: Merge changes from another branch.
* **Conflict**: When diffs overlap with each other and git does not know how to resolve them. Requires programmer to *resolve* and commit.

![git merge conflict](./tutorial2.assets/merge-conflict.png)



