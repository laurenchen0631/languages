## Gist
- **Be consistent**
  - When it comes to things like formatting, and casing, arguments about which is better are subjective and impossible to resolve.
  - When **a bit of code stands out** and catches your eye, it should do so for a useful reason.

- **Be brief**
  - Dart was designed to be familiar, so it inherits many of the same statements and expressions as C, Java, JavaScript and other languages.
  - If there are multiple ways to say something, we should generally **pick the most concise one**.

## Static Analysis
- Static analysis make us find problems before executing a single line of code. With the help of the analyzer, you can find simple typos.
- In the Dart ecosystem, the *Dart Analysis Server* and other tools use the analyzer package to perform static analysis.
- Dart tools such as the Dart compiler (`dart compile`), `dart analyze`, and `flutter analyze` use the analyzer package to evaluate the code.

#### The analysis options file
- Place the analysis options file, `analysis_options.yaml`, at the root of the package.
  - YAML is sensitive to whitespace. Don't use tabs in a YAML file, and **use 2 spaces**.

  ```yaml
  include: package:lints/recommended.yaml

  analyzer:
    exclude: [build/**]
    language:
      strict-raw-types: true
    strong-mode:
      implicit-casts: false

  linter:
    rules:
      - cancel_subscriptions
  ``` 

  - Use `include:` url to bring in options from the specified URL — in this case, from a file in the lints package.
  - Use the `analyzer:` entry to customize static analysis
  - Use the `linter:` entry to configure linter rules.

#### Stricter type checks
  ```yaml
  analyzer:
    strong-mode:
      implicit-casts: false
      implicit-dynamic: false
  ```

  - Use the `implicit-casts` and `implicit-dynamic` flags. Both default to `true`.
  - `implicit-casts: false` ensures that the type inference engine never implicitly casts from `dynamic` to a more specific type.
  - `implicit-dynamic: false` ensures that the type inference engine never chooses the `dynamic` type when it can't determine a static type.

#### Linter Rules
- Linters tend to be nondenominational—rules don't have to agree with each other.
- Note that linter rules can have **false positives**, unlike static analysis.
- The Dart team provides two sets of recommended linter rules
  1. Core rules: identify critical issues that are likely to lead to problems when running or consuming Dart code.
  2. Recommended rules: identify additional issues that may lead to problems when running or consuming Dart code, and enforce a single, idiomatic style and format.

- If you're working on Flutter code, then instead of using the lints package, use `flutter_lints`.
- Edit `analysis_options.yaml` file to include the preferred rule set:
  ```yaml
  include: package:lints/<RULE_SET>.yaml
  ```

- Enabling individual rules
  ```yaml
  linter:
    rules:
      - always_declare_return_types
      - cancel_subscriptions
      - close_sinks
      - comment_references
      - one_member_abstracts
      - only_throw_errors
      - package_api_docs
      - prefer_single_quotes
      - sort_child_properties_last
  ```

  - Add `linter:` to the analysis options file as a top-level key, followed by `rules:` as a second-level key. 

- Disabling individual rules

  ```yaml
  include: package:lints/recommended.yaml

  linter:
    rules:
      avoid_shadowing_type_parameters: false
      await_only_futures: true
  ```

  - Disabling individual rules is similar to enabling them, but requires the use of a map rather than a list.

#### Excluding code from analysis
- Sometimes it's OK for some code to fail analysis.
- You have a few ways to exclude code from analysis:
  - Exclude **entire files** from analysis.
  - Stop specific non-error rules from being applied to **individual files**.
  - Stop specific non-error rules from being applied to **individual lines** of code.

- Excluding files: use the `exclude:` analyzer option.
  ```yaml
  analyzer:
    exclude:
      - lib/client.dart
      - lib/server/*.g.dart
      - test/_data/**
  ```

  - You can list individual files, or use `glob` syntax.

- Suppressing rules for a file: add an `ignore_for_file` comment to the file

  ```dart
  // ignore_for_file: unused_import, unused_local_variable
  ```

- Suppressing rules for a line of code: put an `ignore` comment above the line of code

  ```dart
  // ignore: invalid_assignment
  int x = '';

  // ignore: invalid_assignment, const_initialized_with_non_constant_value
  const x = y;

  int x = ''; // ignore: invalid_assignment
  ```

#### Customizing analysis rules
- Each analyzer error code and linter rule has a default severity.
- The analyzer supports three severity levels:
  - info
  - warning
  - error: An error that causes analysis to fail.

- Ignore specific analyzer error codes and linter rules by using the `errors:` field.

  ```yaml
  analyzer:
    errors:
      todo: ignore
  ``` 

- Changing the severity of rules

  ```yaml
  analyzer:
    errors:
      invalid_assignment: warning
      missing_return: error
      dead_code: info
  ```

## Style Guide

#### Identifiers
- Identifiers come in three flavors in Dart.
  1. `UpperCamelCase`
  2. `lowerCamelCase` 
  3. `lowercase_with_underscores`

- DO name *types* using `UpperCamelCase`: classes, enum types, typedefs, and type parameters
- DO name *extensions* using `UpperCamelCase`.
- DO name *libraries, packages, directories, and source files* using `lowercase_with_underscores`.
- DO name *import prefixes* using `lowercase_with_underscores`.
- DO name other identifiers using `lowerCamelCase`: class members, top-level definitions, variables, parameters, and named parameters.

- PREFER using `lowerCamelCase` for *constant names*, including *enum values*.

- DO capitalize acronyms and abbreviations longer than two letters like words.
  ```dart
  // class HTTPConnection {}
  class HttpConnection {}
  // class DbIoPort {}
  class DBIOPort {}
  // class TvVcr {}
  class TVVcr {}
  // class MRRogers {}
  class MrRogers {}

  var httpRequest = ...
  var uiHandler = ...
  Id id;
  ```

- PREFER using `_`, `__`, etc. for *unused callback parameters*.


- DON'T use a leading underscore for identifiers that *aren't private*.


### Ordering
- DO place `'dart:'` imports before other imports.
- DO place `'package:'` imports before relative imports.
- DO specify *exports* in a separate section after all imports.
- DO sort sections **alphabetically**.

#### Formatting
- DO format your code using `dart format`.
- CONSIDER changing the code to make it more formatter-friendly.
  - Consider shortening a local variable name or hoisting out an expression into a new local variable. 
  - Think of dart format as a partnership where you work together, sometimes iteratively, to produce beautiful code.
- AVOID lines longer than 80 characters.
  - Exception: When a URI or file path occurs in a comment or string, it may remain whole;
  - Exception: Multi-line strings can contain lines longer than 80 characters

- DO use **curly braces for all flow control** statements.
  - Exception: Whenan `if` statement with no `else` clause, we can omit the braces.

## Documentation guide
- A concise, accurate comment only takes a few seconds to write but can save one of those people hours of time.

#### Comments
- DO format comments like sentences.
  - Capitalize the first word unless it's a case-sensitive identifier.
  - End it with a period

- DON'T use block comments (`/* ... */`) for documentation.
  - Use `/* ... *` to temporarily comment out a section of code, but all other comments should use `//`.
  - A doc comment appears before a declaration and uses `///` that dartdoc looks for.

#### Doc Comments
- Doc comments are especially handy because `dartdoc` parses them and generates beautiful doc pages.
- DO use `///` doc comments to document members and types.
- DO start doc comments with a single-sentence summary.
- DO separate the first sentence of a doc comment into its own paragraph.
  - Add a blank line after the first sentence to split it out into its own paragraph

  ```dart
  /// Deletes the file at [path].
  ///
  /// Throws an [IOError] if the file could not be found. Throws a
  /// [PermissionError] if the file is present but could not be deleted.
  void delete(String path) {
    ...
  }
  ```
- AVOID redundancy with the surrounding context.
  - The reader can clearly see the name of the class, what interfaces it implements, etc.
  - Focus on explaining what the reader doesn't already know.

- PREFER starting *function or method* comments with *third-person verbs*.
  ```dart
  /// Returns `true` if every element satisfies the [predicate].
  bool all(bool predicate(T element)) => ...
  ```
  
- PREFER starting *variable, getter, or setter* comments with *noun phrases*
  ```dart
  /// The current day of the week, where `0` is Sunday.
  int weekday;
  ```

- PREFER starting *library or type* comments with *noun phrases*.

  ```dart
  /// A chunk of non-breaking output text terminated by a hard or soft newline.
  ///
  /// ...
  class Chunk { ... }
  ```

- DO use **square brackets** in doc comments to refer to *in-scope identifiers*.
  ```dart
  /// Throws a [StateError] if ...
  /// similar to [anotherMethod()], but ...
  ```

- DO put doc comments before metadata annotations.


- PREFER writing doc comments for public APIs.
- CONSIDER writing a library-level doc comment.
  - Consider including:
    - A single-sentence summary of what the library is for.
    - Explanations of terminology used throughout the library.
    - A couple of complete code samples that walk through using the API.
    - Links to the most important or most commonly used classes and functions.
    - Links to external references on the domain the library is concerned with.
  - Document a library by placing a doc comment right above the `library` directive at the start of the file.
- CONSIDER writing doc comments for private APIs.
- CONSIDER including code samples in doc comments.

  ```dart
  /// Returns the lesser of two numbers.
  ///
  /// ```dart
  /// min(5, 3) == 3
  /// ```
  num min(num a, num b) => ...
  ```

#### Markdown

- Dart can use markdown formatting in doc comments and `dartdoc` will process it accordingly.
- AVOID using markdown excessively.
  - Words are what matter.
- PREFER backtick fences for code blocks.

#### Writing
- As for any programming language, it's worth putting effort into improving your proficiency.

- PREFER brevity.
- AVOID abbreviations and acronyms unless they are obvious.
- PREFER using "this" instead of "the" to refer to a member's instance.

## Usage Guide

#### Libraries

- DO use strings in `part of` directives.
  - If we do choose to use `part` to **split part of a library out into another file**, Dart requires the other file to in turn indicate which library it’s a part of.

  - The preferred, modern syntax is to **use a URI string** that points directly to the library file.

  ```dart
  // part of my_library;
  part of '../../my_library.dart';
  ```

- DON’T import libraries that are inside the `src` directory of another package.
- DON’T allow an import path to reach into or out of lib.
  - Don’t use `/lib/` in import paths.
  - Don’t use `../` to escape the lib directory.
  - When you need to reach into a package’s lib directory, use a `package:` import.

  ```
  my_package
  └─ lib
    └─ api.dart
    test
    └─ api_test.dart
  ```

  ```dart
  // api_test.dart
  import 'package:my_package/api.dart';
  ```

- PREFER relative import paths.
  - When an import does **not reach across** `lib`, prefer using relative imports.

  ```dart
  import 'src/stuff.dart';
  import 'src/utils.dart';
  import '../api.dart';
  ```

#### Null
- DON’T explicitly initialize *variables* to `null`.
  -  If the variable is nullable, then it is implicitly initialized to `null`.
- DON’T use an *explicit default value* of `null`.
- PREFER using `??` to convert `null` to a boolean value

  ```dart
  if (optionalThing?.isEnabled ?? false) {
    print('Have enabled thing.');
  }

  // if (optionalThing?.isEnabled != false) {
  //   print('Have enabled thing or nothing.');
  // }
  ```

- AVOID `late` variables if you need to check whether they are initialized.
- CONSIDER copying a nullable field to a local variable to enable type promotion.
  - Checking that a nullable variable is not equal to null promotes the variable to a non-nullable type.
  - Unfortunately, **promotion is only sound for local variables and parameters**, so fields and top-level variables aren’t promoted.
  - One pattern to work around this is to copy the field’s value to a local variable.

#### Strings
- DO use **adjacent strings to concatenate** string literals.
- PREFER using interpolation to compose strings and values.
- AVOID using curly braces in interpolation when not needed.

#### Collections
- DO use collection literals when possible.

  ```dart
  var points = <Point>[];
  // var addresses = Map<String, Address>();
  var addresses = <String, Address>{};
  // var counts = Set<int>();
  var counts = <int>{};
  ```
  - This guideline doesn’t apply to the named constructors for those classes, such as `List.from()` and `Map.fromIterable()`

- DON’T use `.length` to see if a collection is empty.
  - There are faster and more readable getters: `.isEmpty` and `.isNotEmpty`.
- **AVOID** using `Iterable.forEach()` with a function literal.
  - If you want to invoke some already existing function on each element, `forEach()` is fine.

- **DON’T** use `List.from()` unless you intend to change the type of the result.
  ```dart
  // Creates a List<int>:
  var iterable = [1, 2, 3];

  // Prints "List<int>":
  print(iterable.toList().runtimeType);

  // Prints "List<dynamic>":
  print(List.from(iterable).runtimeType);
  ```

- DO use `whereType()` to filter a collection by type.

  ```dart
  var objects = [1, 'a', 2, 'b', 3];
  // var ints = objects.where((e) => e is int);
  var ints = objects.whereType<int>();
  ```

- DON’T use `cast()` when a nearby operation will do.
- AVOID using `cast()`.
  - Sometimes there is no nearby operation you can use to fix the type of some object.
  - The `cast()` method returns a **lazy collection** that checks the element type **on every operatio**n. 
  - Prefer any of these options instead:
    - Create it with the right type
    - Cast the elements on access: use `as` inside the iteration.
    - Eagerly cast using `List<T>.from(iterable)`.

#### Functions
- DO use a function declaration to bind a function to a name.

  ```dart
  void localFunction() {
    ...
  }

  // var localFunction = () {
  //  ...
  // };
  ```

- DON’T create a lambda when a tear-off will do.
- DO use `=` to separate a named parameter from its default value.
  - For legacy reasons, Dart allows both `:` and `=` as the default value separator.

#### Variables

- DO follow a consistent rule for var and final on local variables.
  - There are two rules in wide use for when to use one or the other:
    1. Use `final` for local variables that are not reassigned and `var` for those that are.
    2. Use `var` **for all local variables**, even ones that aren’t reassigned.
  - Either rule is acceptable, but pick one and apply it consistently.

- AVOID storing what you can calculate.
  - In some cases, you may need to cache the result of a slow calculation, but **only do that after you know you have a performance problem.**


### Members
- DON’T wrap a field in a getter and setter unnecessarily.
  - In Dart, fields and getters/setters are completely indistinguishable.
  - We can expose a field in a class and later wrap it in a getter and setter without having to touch any code that uses that field.
  
- PREFER using a `final` field to make a read-only property
- CONSIDER using `=>` for simple members.
  - This style is a good fit for simple members that just calculate and return a value.

    ```dart
    double get area => (right - left) * (bottom - top);

    String capitalize(String name) =>
        '${name[0].toUpperCase()}${name.substring(1)}';
    ```

  - We can also use `=>` on members that don’t return a value

    ```dart
    num get x => center.x;
    set x(num value) => center = Point(value, center.y);
    ```

- DON’T use `this.` except to redirect to a *named constructor* or to avoid shadowing.

  - The time to use `this.` is when redirecting to a named constructor:
    ```dart
    class ShadeOfGray {
      final int brightness;

      ShadeOfGray(int val) : brightness = val;

      ShadeOfGray.black() : this(0);

      // But now it will!
      ShadeOfGray.alsoBlack() : this.black();
    }
    ```

- DO **initialize fields at their declaration** when possible.
  - Some fields can’t be initialized at their declarations because they need to reference this.

#### Constructors
- DO use initializing formals when possible.
  - `this.` syntax before a constructor parameter is called an “initializing formal”. 
  ```dart
  class Point {
    double x, y;

    // Point(double x, double y)
    //   : x = x,
    //     y = y;
        
    Point(this.x, this.y);
  }
  ``` 

- DON’T use late when a constructor initializer list will do.
  - Sound null safety requires Dart to ensure that a non-nullable field is initialized before it can be read.
  - You can make this error go away by marking the field `late`. That turns the compile-time error into a runtime error if accessing the field before it is initialized.

- DO use `;` instead of `{}` for empty constructor bodies.
- DON’T use `new`.
- DON’T use const redundantly.
  - In contexts where an expression must be constant, the `const` keyword is implicit.
  - Those contexts are any expression inside:
    - A const collection literal.
    - A const constructor call
    - A metadata annotation.
    - The initializer for a const variable declaration.
    - A switch case expression — the part right after case before the :, not the body of the case.


#### Error handling
- AVOID catches without on clauses.
- DON’T discard errors from catches without on clauses.
- DO throw objects that implement `Error` only for programmatic errors.
  - If the exception is some kind of runtime failure that doesn’t indicate a bug in the code, then throwing an `Error` is misleading. Instead, throw one of the core Exception classes or some other type.

- DON’T explicitly catch Error or types that implement it.
  - Instead of adding error-handling code to deal with this exception after the fact, go back and fix the code that is causing it to be thrown in the first place.

- DO use `rethrow` to rethrow a caught exception.


#### Asynchrony
- PREFER `async`/`await` over using raw futures.
- DON’T use `async` when it has no useful effect.
  - If you can omit the `async` without changing the behavior of the function, do so.
  - Cases where async is useful include:
    - Using `await`.
    - Returning an error asynchronously. `async` and then `throw` is shorter than return `Future.error(...)`.
    - Returning a value and you want it implicitly wrapped in a future

- CONSIDER using higher-order methods to transform a stream.
- AVOID using `Completer` directly.
- DO test for `Future<T>` when disambiguating a `FutureOr<T>` whose type argument could be `Object`.
  - Before you can do anything useful with a `FutureOr<T>`, you typically need to do an `is` check  to see if you have a `Future<T>` or a bare `T`.

  ```dart
  Future<T> logValue<T>(FutureOr<T> value) async {
    if (value is Future<T>) {
      var result = await value;
      print(result);
      return result;
    } else {
      print(value);
      return value;
    }
  }
  ```

## Design Guide

#### Names
- DO use terms consistently.
  - The goal is to take advantage of what the user already knows.

- AVOID abbreviations.
- PREFER putting the most **descriptive** noun last.
  - You can prefix it with other words, such as adjectives, to further describe the thing.
- CONSIDER making the code read like a sentence.
- PREFER a noun phrase for a *non-boolean property* or variable.
- PREFER a *non-imperative* verb phrase for a *boolean* property or variable.

  ```dart
  if (window.closeable) ...  // Adjective.
  if (window.canClose) ...   // Verb.
  ```
  
  - Good names tend to start with one of a few kinds of verbs:
    - a form of “to be”: `isEnabled`, `wasShown`, `willFire`.
    - an auxiliary verb: `hasElements`, `canClose`, `shouldConsume`, `mustSave`.
    - an active verb: `ignoresInput`, `wroteFile`. These are rare because they are usually ambiguous.

- CONSIDER omitting the verb for a named boolean parameter.
  - For named parameters that are boolean, the name is often just as clear without the verb.
- PREFER the “positive” name for a boolean property or variable.
- PREFER an imperative verb phrase for a function or method whose main purpose is a side effect.
- PREFER a noun phrase or non-imperative verb phrase for a function or method if returning a value is its primary purpose.
- CONSIDER an imperative verb phrase for a function or method if you want to draw attention to the work it performs.
- **AVOID** starting a method name with `get`.
- PREFER naming a method `to___()` if it copies the object’s state to a new object.
- PREFER naming a method `as___()` if it returns a different representation backed by the original object.

- AVOID describing the parameters in the function’s or method’s name.
- DO follow existing mnemonic conventions when naming type parameters.
  - `E` for the element type in a collection.
  - `K` and `V` for the key and value types in an associative collection
  - `R` for a type used as the return type of a function or a class’s methods.
  - Otherwise, use `T`, `S`, and `U` for generics.
  - If none of the above cases are a good fit, then either another single-letter mnemonic name or a descriptive name is fine.

#### Libraries
- PREFER making declarations private.
- CONSIDER declaring multiple classes in the same library.
  - Placing multiple classes together in one library can enable some useful patterns.
  - Since privacy in Dart works at the library level, not the class level, this is a way to define “friend” classes


#### Classes and mixins
- AVOID defining a one-member abstract class when a simple function will do.+
- **AVOID** defining a class that contains **only static** members.
  - If a function or variable isn’t logically tied to a class, put it at the top level. If you’re worried about name collisions, give it a more precise name or move it to a separate library.

  - With constants and enum-like types, it may be natural to group them in a class.

    ```dart
    class Color {
      static const red = '#f00';
      static const green = '#0f0';
      static const blue = '#00f';
      static const black = '#000';
      static const white = '#fff';
    }
    ``` 

- DO document if your class supports being extended.
  - If you want to allow subclasses of your class, state that.
  - Suffix the class name with `Base`, or mention it in the class’s doc comment.

- DO document if your class supports being used as an interface.
- DO use mixin to define a mixin type.
  - Any class that met certain restrictions  could be used as a mixin. This was confusing.
  - Dart 2.1.0 added a `mixin` keyword for explicitly declaring a mixin.

#### Constructors
- CONSIDER making your constructor const if the class supports it.
  - If you have a class where **all the fields are** `final`, and the constructor does nothing but initialize them, you can make that constructor `const`.

#### Members
- PREFER making fields and top-level variables `final`.
- DO use getters for operations that conceptually access properties.
- DO use setters for operations that conceptually change properties.
- DON’T define a setter without a corresponding getter.
- AVOID using runtime type tests to fake overloading.
  - Dart doesn’t have overloading. You can define an API that looks like overloading by defining a single method and then using is type tests.
  - However, faking overloading this way turns a compile time method selection into a choice that happens at runtime.

- AVOID **public** `late final` fields without initializers.
  - Fields are usually marked `late` so that they can be initialized **internally**.
  - - It’s better to pick one of the following solutions:
    - Don’t use `late`.
    - Use `late`, but initialize the late field at its declaration.
    - Use `late`, but make the late field **private** and define a **public getter** for it.

- AVOID returning nullable `Future`, `Stream`, and collection types.
- AVOID returning `this` from methods just to enable a fluent interface.
  - Method cascades are a better solution for chaining method calls.

#### Types
- DO type annotate variables without initializers.
  - If there is no initializer, inference fails.
- DO type annotate fields and top-level variables if the type isn’t obvious.
  - “Obvious” isn’t precisely defined, but these are all good candidates:
    - Literals.
    - Constructor invocations.
    - References to other constants that are explicitly typed.
    - Simple expressions on numbers and strings.
    - Factory methods like `int.parse()`, `Future.wait()`, etc. that readers are expected to be familiar with.

  - If you think the initializer expression is sufficiently clear, then you may omit the annotation.

- DO **annotate return types** on function declarations.
- DO **annotate parameter types** on function declarations.
- DON’T annotate inferred parameter types on function expressions.
  ```dart
  var names = people.map((person) => person.name);
  ```
- DON’T type annotate initializing formals.
  ```dart
  class Point {
    double x, y;
    Point(this.x, this.y);
  }
  ```

- DO write type arguments on generic invocations that aren’t inferred.
- DON’T write type arguments on generic invocations that are inferred.
- AVOID writing incomplete generic types.
- DO annotate with `dynamic` instead of letting inference fail.
  - When inference doesn’t fill in a type, it usually defaults to `dynamic`. 
  - When dynamic is the type you want, write that explicitly to make your intent clear and highlight that this code has less static safety.

  ```dart
  dynamic mergeJson(dynamic original, dynamic changes) => ...

  // mergeJson(original, changes) => ...
  ```
- AVOID using dynamic unless you want to disable static checking.

- PREFER signatures in function type annotations.
  - The identifier `Function` by itself **without any return type or parameter** signature refers to the special `Function` type.

  ```dart
  // bool isValid(String value, Function test) => ...
  bool isValid(String value, bool Function(String) test) => ...
  ```
  - **Exception**
    - Sometimes, you want a type that represents the union of multiple different function types.
    - Dart doesn’t have union types, there’s no way to precisely type that and you’d normally have to use `dynamic`.

    ```dart
    void handleError(void Function() operation, Function errorHandler) {
      try {
        operation();
      } catch (err, stack) {
        if (errorHandler is Function(Object)) {
          errorHandler(err);
        } else if (errorHandler is Function(Object, StackTrace)) {
          errorHandler(err, stack);
        } else {
          throw ArgumentError('errorHandler has wrong signature.');
        }
      }
    }
    ```

- DON’T specify a return type for a setter.
  - Setters always return `void` in Dart.

- PREFER using function type syntax for parameters.
  - Sort of like in C, we surround the parameter’s name with the function’s return type and parameter signature.

  ```dart
  Iterable<T> where(bool Function(T) predicate) => ...
  ```

- DO use `Future<void>` as the return type of asynchronous members that do not produce values.
- AVOID using `FutureOr<T>` as a return type.
  - If we return a `FutureOr<int`>, users need to check before they can do anything useful.
  - Just return a `Future<int>`, it’s cleaner

#### Parameters
- AVOID positional boolean parameters.
  - It can make callsites unreadable if it isn’t clear what the boolean represents.
  - Prefer using named arguments, named constructors, or named constants to clarify

  ```dart
  Task.oneShot();
  Task.repeating();
  ListBox(scroll: true, showScrollbars: true);
  Button(ButtonState.enabled);
  ```

- AVOID optional positional parameters if the user may want to omit earlier parameters.
  - Optional positional parameters should have a logical progression such that earlier parameters are passed more often.
  - Users should almost never need to explicitly pass a “hole” to omit an earlier positional argument to pass later one.
  -  You’re better off using named arguments for that.

- AVOID mandatory parameters that accept a special “no argument” value.
- DO use **inclusive start** and **exclusive end** parameters to accept a range.

  ```dart
  [0, 1, 2, 3].sublist(1, 3) // [1, 2]
  'abcd'.substring(1, 3) // 'bc'
  ```

#### Equality
- DO override `hashCode` if you override `==`.
  - **Any two objects that are equal must have the same hash code.**

- DO make your `==` operator obey the mathematical rules of equality.
  - *Reflexive*: `a == a` should always return true.
  - *Symmetric*: `a == b` should return the same thing as `b == a`.
  - *Transitive*: If `a == b` and `b == c` both return true, then `a == c` should too.

- AVOID defining custom equality for **mutable classes**.
- DON’T make the parameter to `==` nullable.