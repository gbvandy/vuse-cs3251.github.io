---
layout: default
title: Style Guidelines
author: Samuel Lijin
---
# Why?

> The ratio of time spent reading code vs. writing code is well over 10 to 1.

We're not teaching you to be a code monkey. We're teaching you to be a software developer: someone who writes *good* code and appreciates the importance of doing so.

That's why we're asking you to follow this style guide. Now, to be fair, there is no such thing as a perfect style guide: there will always be exceptions. But by and large, this guide has been designed to encourage good style with reasonable restriction.

Keep in mind that with the exception of the "General Guidelines" section, this guide has been developed with C++ in mind, so many rules are not portable (even to C/Java/C#, which are in the same syntax family!).

# General Guidelines

In general (no matter what programming language), your code must be

* **consistent**: any convention you apply in your code should be applied consistently throughout your code;
* **well-structured**: add exactly as much complexity to your code as it needs - if a function should be broken up, break it up, and if you should have another layer of abstraction, add it - but be sure to stop and ask yourself if your code *should* be written that way;
* **self-documenting**: use informative names that tell us what purpose a variable serves, what a function returns, etc.;
* **commented meaningfully**: comments are a *supplement* to your code and should enhance the reader's understanding of your code - explaining yourself is good (particularly when the code at hand is very complex, see [this example in the illumos source code](https://github.com/illumos/illumos-gate/blob/f7877f5d39900cfd8b20dd673e5ccc1ef7cc7447/usr/src/uts/common/os/turnstile.c#L142-L160)), and it's even better if you don't have to; and
* **readable**: it should be easy to understand your code simply by reading it, c.f.:

  ~~~ c++
  // save stuff like this for code golf
  // also, yes, this compiles (at least under GCC 6.2.1, admittedly not with -Wall)
  #include <cstdio>
  char x[]="\0FizzBuzz\n";main(){while('e'>++*x){x[6]=*x%5?x[x[5]=10]:'u';printf(*x%3?*x%5?"%d\n":x+5:x+1,*x);x[5]='B';}}

  // a less extreme example of code with poor readability
  d = c++ - --e + f;

  // this is far more readable - no mental juggling of prefix and postfix!
  --e;
  d = c - e + f;
  ++c;
  ~~~

But remember: the purpose of these rules is to *enhance* your code, never to make it worse. We have designed these guidelines to be broadly applicable, but there are no rules without exceptions - there will always be judgment calls to make, and we expect you to be able to make the correct ones.

# Conventions

There are a *lot* of coding conventions out there, ranging from the absurd to the controversial. For the purposes of this course, we will be using a mix of conventions that can be enforced with the `clang-format` linter and in-house rules (where ambiguous, ask, but it is safe to assume that in-house rules take precedence).

## Using `clang-format`

You are expected to use `clang-format -style=webkit` to lint your code, which will automatically format your code according to the WebKit coding standard, which can be found [here](https://webkit.org/code-style-guidelines/).

Alternatively, you may maintain your own [clang-format specification](http://clang.llvm.org/docs/ClangFormatStyleOptions.html).

Note that

* some rules are specific to the WebKit project (e.g. the inclusion of `config.h`) and those may be ignored for the purposes of this course, and
* the rules about `using` in the WebKit standard should be ignored; we have included guidelines about their usage in the in-house rules.

## In-house Rules

* **Maximum line width** is 100 characters.
* Use **Javadoc-style comments** in the header file.
    * Such comments are important because they define your API and lay out behavior specifications.
    * This does **not** mean you should include every single comment field, but rather, it means your comments should be just extensive enough to allow someone to understand what a method does by reading the comments and looking at the method signature (e.g. `@details` is often unnecessary for simple methods/classes). Remember: comments are *supplements* to your code, c.f.

      ~~~ c++
      /**
       * @brief   Pretty prints the current state of the puzzle.
       */
      void pprint() const;
      ~~~
    * The Javadoc fields should all be vertically aligned, c.f.

      ~~~ c++
      /**
       * @brief   Sends an HTTP request and writes the response to responseFile.
       * @details Sends an HTTP request to targetUri (doing both DNS resolution and IP
       *          routing) with the specified request headers, doing an asynchronous
       *          write to responseFile.
       * @param   <targetUri> the URI to send the HTTP request to
       * @param   <headers> key-value pairs to send in the header
       * @param   <responseFile> the output file stream to write to
       * @return  the response code
       * @pre     Both targetUri and headers are properly formatted.
       * @post    If the envvar HTTP_REQUEST_LOGFILE is set, the targetUri, response
       *          code, and responseFile are logged with the system time to
       *          HTTP_REQUEST_LOGFILE.
       * @throws  std::errc::network_down - network is down
       * @throws  std::system_error - error when writing to responseFileName
       */
      int sendHttpRequest(std::string const targetUri,
                          std::map<std::string, std::string> const headers,
                          std::ofstream &responseFile) const;
      ~~~
    * Classes should also be commented, c.f.

      ~~~ c++
      /**
       * @brief   Monitors the state of a Plumbus, provides an interface to
       *          the NPD, and alerts owner if it is sweating.
       * @details Monitors the temperature, color tone, size, and whether or
       *          not the corresponding Plumbus is flashing/humming. Provides
       *          methods for communicating with the National Plumbus
       *          Database, and by default will alert the owner if the
       *          Plumbus starts sweating (alert service is disabled if the
       *          Squiggle Remote is straightened out).
       * @author  Ryan Ridley
       * @author  Plumbus & Kimble Co. (for owning the rights)
       */
      class PlumbusMonitor {
          ...
      };
      ~~~
* Namespace contamination is not acceptable. This means:
  * You are not allowed to use `using namespace std;`; nor are you allowed to import the contents of any namespace into the current namespace. You *may* import specific methods/variables with `using std::cout;`.
  * Global variables are banned.
  * Use of global functions is limited to `main()`, `friend` operator overloads, and where specified in an assignment.

# Structure

Classes must be split between two files: a `.h` containing only their declaration (the specification of the class's interface, fields, etc.) and a `.cpp`  containing only their definition (i.e. implementation of methods, etc.).

## Header and Code Files

The use of both `.h` and `.cpp` files for the same bodies of code means that there are a number of stylistic concerns that must be managed:

* No file should contain code for more than one class.
* The `.h` and `.cpp` for the same class should have matching filenames which also match the name of said class.
* The order of member fields and functions should be preserved between the `.h` and the `.cpp` files.
* Function signatures in `.h` files should include the same parameter names used in the `.cpp` file.
* The `.h` should be included in the `.cpp` file.
  * Note that in the case of template classes, you cannot `#include` the `.h` in the `.cpp`, since this is at odds with the process by which template classes are instantiated; as such, in these cases, the `.cpp` should be included at the end of the `.h` file.
* System headers should only be imported from the C++ standard library; it is *not* OK to import anything from the C standard library, particularly as everything in the C standard library has been ported to C++ (e.g. `<cstdlib>` replaces `<stdlib.h>`).

## Declaration Order

When defining a class or struct, you should use access modifiers as the primary order, the following list as the secondary order, and grouping by functionality as the ternary order:

* `using`, `typedef`, `enum`
* Constants (`static const` fields)
* Big 3 (or 5 in C++11)
* Operator methods
* Other methods (`static` included)
* Fields

Comments should also be used to distinguish logical groups of fields and/or functions.

Within functions, etc., variable declarations should be placed either at the start of a block (after the `{`) or at the beginning of the group of lines of code that use the variables, and in the case of the latter, should be preceded by a blank line.

## Access Modifiers

Never expose more of a class than should be exposed - in other words, if a member variable or function is not `private`, then there should be a good reason for it to be `public` or `protected`.


# Syntax and Semantics

C++ has been around for a long time, and also is a very powerful language. As a result, there are a number of things that you can do which - for the most part - you should generally be unable to do, or at the very least you should avoid doing. These rules are as follows:

* Pointers should be used only for dynamically allocated objects.
* The appropriate data type should be used for the appropriate data:
  * e.g. `bool` for true/false, `ptrdiff_t` for pointer arithmetic, `size_t` for values returned by `sizeof`.
  * Where appropriate, architecture-independent data types should be used, e.g. `uint32_t` for integers.

* Functions may be collapsed into one-liners if and only if they contain no executable statements, e.g. `void foo() {}`.
* Use base member initialization whenever possible.
* Use default parameters whenever possible.
* Think carefully about any overhead that might be involved in a seemingly innocuous operation.
  * When incrementing and decrementing non-primitive types, it is generally preferable to use the prefix version, both because of the overhead and semantics of postfix.
  * C++ has some [very complex initialization semantics](http://en.cppreference.com/w/cpp/language/initialization)! As a general rule of thumb, direct initialization is preferred to copy initialization.
* Modern compilers can do a **lot** of work for you that you might not actually want them to do! For fully deterministic behavior, you should use `explicit` constructors and operators when possible.
  * The [safe bool idiom](http://www.artima.com/cppsource/safebool.html) is one of the many practices that we were able to do away with [when `explicit` was introduced in C++11](http://stackoverflow.com/questions/6242768/).
  * Avoid using the [`inline`](http://en.cppreference.com/w/cpp/language/inline) keyword unless you understand the implications thereof for compiler optimization, static linking, and dynamic linking.
* Always use `nullptr` when referring to the null pointer, never `NULL` or `0`.
* Use `const` liberally *where it makes sense to do so*.
    * A method signature `void goodMethod(const Foo *param);` is very meaningful - it tells the caller that they can expect a `Foo` instance to be the same before and after calling `someMethod()` on it!!
    * By contrast, the `const` in `void badMethod(Foo *const var);` has absolutely no meaning whatsoever for the caller, and should be omitted.
* Use pass-by-reference *exactly where it is appropriate to do so*. The purpose of passing by reference is to reduce the overhead of a function call, not to make your code harder to read.

## Naming Fields and Functions

We expect `SCREAMING_SNAKE_CASE` on constants and `camelCase` on everything else; specifically, `UpperCamelCase` for classes and namespaces and `lowerCamelCase` for class fields, locals, and functions.

Names should also generally correspond to *what* a variable represents and to *what* a function does or returns. To that end, variable names should generally be nouns and function names should generally be adjectives and/or verbs (generally, "is" should prefix boolean accessors, "get" for other accessors, and "set" for mutators), e.g.

~~~ c++
uint32_t size
std::string errorMessage
bool isEmpty()
Property getProperty()
void setProperty()
~~~

Avoid names which might be ambiguous, e.g. `void setCounterZero()` and `void setCounterToZero()` are preferable to `void zeroCounter()`.

# Spacing

The usage of whitespace is *really* important and is absolutely huge for readability. Use blank lines liberally (but not excessively) to improve readability; it is far more useful to be able to distinguish logical groups of member functions at a glance than to have to scroll for them.

There are also a number of issues that you should be aware of:

* Trailing whitespace should be trimmed, because it screws with diffing (and by consequence, also messes up Git diffs and thereby can introduce severe complications to merging etc.). Unfortunately `clang-format` does not support this.
* In nested templates, prior to C++11, compilers were unable to interpret

  ~~~ c++
  typedef std::map<std::string, std::map<int, char>> FOO_MAP;
  ~~~

  and spaces were needed to help the compiler identify templates, c.f.

  ~~~ c++
  typedef std::map<std::string, std::map<int, char> > FOO_MAP;
  ~~~

  due to tokenization rules in the C++ language standard. The C++11 standard - which redefined tokenization rules so that the former template declaration was valid - was not fully supported until GCC 4.9 and Clang 3.3, however, so be aware that using the former can be problematic in legacy code. Use either as befits your preference.
