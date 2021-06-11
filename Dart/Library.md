## Libraries and visibility
- The `import` and `library` directives create a modular and shareable code base.
  - Every Dart app is a library, even if it doesn’t use a `library` directive.
  - Private identifiers are visible only inside the library. 

- **Using libraries**
  - Use `import` to specify how a namespace from one library is used
    - The only required argument to **import is a URI** specifying the library.
    ```dart
    import 'dart:html';
    ``` 
  - For **built-in libraries**, the URI has the special `dart:` scheme.
  - For other libraries, we use a file system path or the `package:` scheme.
    ```dart
    import 'package:test/test.dart';
    ``` 

  - We can specify a prefix libraries, and it is useful for conflicting identifiers.
    ```dart
    import 'package:lib1/lib1.dart';
    import 'package:lib2/lib2.dart' as lib2;

    // Uses Element from lib1.
    Element element1 = Element();

    // Uses Element from lib2.
    lib2.Element element2 = lib2.Element();
    ``` 

  - We can use only part of a library
    ```dart
    // Import only foo.
    import 'package:lib1/lib1.dart' show foo;

    // Import all names EXCEPT foo.
    import 'package:lib2/lib2.dart' hide foo;
    ```

- Lazily loading a library
  - *Deferred loading* (also called lazy loading) allows a **web app** to load a library on demand.
    - To reduce a web app’s initial startup time.
    - To perform A/B testing.
    - To load rarely used functionality.
  - To lazily load a library, import it using `deferred as` and invoke `loadLibrary()` when it is needed.
    ```dart
    import 'package:greetings/hello.dart' deferred as hello;

    Future<void> greet() async {
      await hello.loadLibrary();
      hello.printGreeting();
    }
    ``` 
  - `loadLibrary()` can be invoked multiple times without problems. The library is loaded only once.
  - Keep in mind that:
    - A deferred library’s constants aren’t constants in the importing file
    - You **can’t use types from a deferred library** in the importing file.
    - The `loadLibrary()` function returns a `Future`.
