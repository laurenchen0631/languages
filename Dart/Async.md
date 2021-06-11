## Asynchrony 
- Dart libraries are full of functions that return `Future` or `Stream` objects
- The `async` and `await` keywords support asynchronous programming

- Handling **Futures**
  - When we need the result of a completed Future, we can either 
    - Use `async` and `await`.
    - Use the Future API, as described in the library tour.
  ```dart
  Future<void> checkVersion() async {
    var version = await lookUpVersion();
    // Do something with version
  }
  ```

  - The `async` function executes only until it encounters its first `await` expression. Then it returns a `Future` object, resuming execution only after the `await` expression completes.
  - In `await` expression, the value of expression is usually a `Future`; if it isn’t, then the value is automatically wrapped in a `Future`.

  - The body of `main()` must be marked as `async`:
    ```dart
    Future<void> main() async {
      checkVersion();
      print('In main: version is ${await lookUpVersion()}');
    }
    ```

- Declaring async functions
  - An `async` function is a function whose body is marked with the `async` modifier.
  - Adding the `async` keyword to a function makes it return a `Future` **automatically**. 
    ```dart
    Future<String> lookUpVersion() async => '1.0.0';
    ```

- Handling **Streams**
  - When we need to get values from a Stream, we can either 
    1. Use `async` and an asynchronous `for` loop (`await for`). 
    2. Use the Stream API.

  - Asynchronous for loop
    ```dart
    await for (varOrType identifier in expression) {
      // Executes each time the stream emits a value.
    }
    ``` 
    - The value of expression **must** have type `Stream`.
    - Execution proceeds as follows:
      1. Wait until the stream emits a value.
      2. Execute the body of the for loop, with the variable set to that emitted value.
      3. Repeat 1 and 2 until the stream is closed.
    - To stop listening to the stream, use `break` or `return` statement 

    ```dart
    Future<void> main() async {
      // ...
      await for (var request in requestServer) {
        handleRequest(request);
      }
      // ...
    }
    ```

## Generators
- Generator function **lazily** produce a **sequence** of values.
- Dart has built-in support for two kinds of generator functions:
  - *Synchronous* generator: Returns an Iterable object.
  - *Asynchronous* generator: Returns a Stream object.

- Implement a synchronous generator: mark the function body as `sync*` and use `yield` statements.
  ```dart
  Iterable<int> naturalsTo(int n) sync* {
    int k = 0;
    while (k < n) yield k++;
  }
  ``` 

- To implement an asynchronous generator: mark the function body as `async*`, and use `yield` statements
  ```dart
  Stream<int> asynchronousNaturalsTo(int n) async* {
    int k = 0;
    while (k < n) yield k++;
  }
  ``` 

- If the generator is **recursive**, you can improve its performance by using `yield*`
  ```dart
  Iterable<int> naturalsDownFrom(int n) sync* {
    if (n > 0) {
      yield n;
      yield* naturalsDownFrom(n - 1);
    }
  }
  ```