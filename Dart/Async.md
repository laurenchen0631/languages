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

## Future
- A future represents the result of an asynchronous operation, and can have two states: `uncompleted` or `completed`.
  - *Uncompleted*: when an asynchronous function is called, it returns an uncompleted future. That future is waiting for the function’s asynchronous operation to finish or to throw an error.
  - *Completed*: if the asynchronous operation succeeds, the future completes with a value. Otherwise it completes with an error.

- A future of type `Future<T>` completes with a value of type `T`.
  - A future with type `Future<String>` produces a string value.
  - If a future doesn’t produce a usable value, then the future’s type is `Future<void>`.
  - If the asynchronous operation performed by the function fails for any reason, the future completes with an error.

- Code that uses `await` expressions can be easier to understand than code that uses the Future API.

  ```dart
  void runUsingFuture() {
    // ...
    findEntryPoint().then((entryPoint) {
      return runExecutable(entryPoint, args);
    }).then(flushThenExit);
  }
  ```

  ```dart
  Future<void> runUsingAsyncAwait() async {
    // ...
    var entryPoint = await findEntryPoint();
    var exitCode = await runExecutable(entryPoint, args);
    await flushThenExit(exitCode);
  }
  ```

  - The first function uses Future’s `then()` method to execute three asynchronous functions.
  - The second function looks more like synchronous code.

- An `async` function can catch exceptions from `Futures`.

  ```dart
  var entryPoint = await findEntryPoint();
  try {
    var exitCode = await runExecutable(entryPoint, args);
    await flushThenExit(exitCode);
  } catch (e) {
    // Handle the error...
  }
  ```

- You can use `then()` to schedule code that runs when the future completes.

  ```dart
  HttpRequest.getString(url).then((String result) {
    print(result);
  });
  ```

  - Use `catchError()` to handle any errors or exceptions that a Future object might throw.

  ```dart
  HttpRequest.getString(url).then((String result) {
    print(result);
  }).catchError((e) {
    // Handle or ignore the error.
  });
  ```

  - If the callback registered with `then()` returns a `Future`, `then()` returns an equivalent `Future`.
  - If the callback returns a value of any other type, `then()` **creates a new `Future`** that completes with the value.

  ```dart
  Future result = costlyQuery(url);
  result
      .then((value) => expensiveWork(value))
      .then((_) => lengthyComputation())
      .then((_) => print('Done!'))
      .catchError((exception) {
    /* Handle exception... */
  });
  ```

## Stream
- `Stream` objects represents sequences of data.
  - For example, HTML events such as button clicks are delivered using streams.

- There are two kinds of streams.
  1. Single subscription streams
     - This is the kind of stream you get when you read a file or receive a web request.
     - Such a stream can only **be listened to once**.
  2. Broadcast streams
     - The stream is intended for individual messages that can be handled one at a time. 
     - This kind of stream can be used for mouse events in a browser.
     - More than one listener can listen at the same time, and we can listen again later after canceling a previous subscription.

- The following methods on `Stream<T>` **process** the stream and return a result:
  
  ```dart
  Future<T> get first;
  Future<bool> get isEmpty;
  Future<T> get last;
  Future<int> get length;
  Future<T> get single;
  Future<bool> any(bool Function(T element) test);
  Future<bool> contains(Object? needle);
  Future<E> drain<E>([E? futureValue]);
  Future<T> elementAt(int index);
  Future<bool> every(bool Function(T element) test);
  Future<T> firstWhere(bool Function(T element) test, {T Function()? orElse});
  Future<S> fold<S>(S initialValue, S Function(S previous, T element) combine);
  Future forEach(void Function(T element) action);
  Future<String> join([String separator = '']);
  Future<T> lastWhere(bool Function(T element) test, {T Function()? orElse});
  Future pipe(StreamConsumer<T> streamConsumer);
  Future<T> reduce(T Function(T previous, T element) combine);
  Future<T> singleWhere(bool Function(T element) test, {T Function()? orElse});
  Future<List<T>> toList();
  Future<Set<T>> toSet();
  ```

  - Each one can be written easily by using an async function with an await for loop. For example:
    
    ```dart
    Future<bool> contains(Object? needle) async {
      await for (var event in this) {
        if (event == needle) return true;
      }
      return false;
    }

    Future forEach(void Function(T element) action) async {
      await for (var event in this) {
        action(event);
      }
    }

    Future<List<T>> toList() async {
      final result = <T>[];
      await forEach(result.add);
      return result;
    }

    Future<String> join([String separator = '']) async =>
        (await toList()).join(separator);
    ```

- The following methods on Stream return a new stream based on the original stream.

  ```dart
  Stream<R> cast<R>();
  Stream<S> expand<S>(Iterable<S> Function(T element) convert);
  Stream<S> map<S>(S Function(T event) convert);
  Stream<T> skip(int count);
  Stream<T> skipWhile(bool Function(T element) test);
  Stream<T> take(int count);
  Stream<T> takeWhile(bool Function(T element) test);
  Stream<T> where(bool Function(T event) test);
  ```

  - Each methods **waits until someone listens** on the new stream before listening on the original.

- **Using an asynchronous for loop**
  ```dart
  void main(List<String> arguments) {
    // ...
    FileSystemEntity.isDirectory(searchPath).then((isDir) {
      if (isDir) {
        final startingDir = Directory(searchPath);
        startingDir.list().listen((entity) {
          if (entity is File) {
            searchFile(entity, searchTerms);
          }
        });
      } else {
        searchFile(File(searchPath), searchTerms);
      }
    });
  }
  ```

  ```dart
  Future<void> main(List<String> arguments) async {
    // ...
    if (await FileSystemEntity.isDirectory(searchPath)) {
      final startingDir = Directory(searchPath);
      await for (var entity in startingDir.list()) {
        if (entity is File) {
          searchFile(entity, searchTerms);
        }
      }
    } else {
      searchFile(File(searchPath), searchTerms);
    }
  }
  ```
  - The first function uses Stream’s `listen()` method to subscribe to a list of files.
  - We can use an asynchronous for loop (`await for`) instead of using the Stream API.


- **Listening for stream data**
  - To get each value as it arrives, either use `await for` or subscribe to the stream using the `listen()` method.

  ```dart
  // Add an event handler to a button.
  submitButton.onClick.listen((e) {
    // When the button is clicked, it runs this code.
    submitData();
  });
  ```
  - In this example, the `onClick` property is a `Stream` object provided by the submit button.
  - We can get one event using a property such as `first`, `last`, or `single`.
  -  To test the event before handling it, use a method such as `firstWhere()`, `lastWhere()`, or `singleWhere()`.
  - If we want a subset of events, use methods such as `skip()`, `skipWhile()`, `take()`, `takeWhile()`, and `where()`.

- **Transforming stream data**
  - Often, we need to change the format of a stream’s data before using it. Use the `transform()` method to produce a stream with a different type of data.

  ```dart
  var lines = inputStream
    .transform(utf8.decoder)
    .transform(LineSplitter());
  ```

  - A normal map requires one value for each incoming event. However, especially for I/O streams, **it might take several incoming events to produce an output**. A `StreamTransformer` can work with that.

    ```dart
    import 'dart:convert';
    import 'dart:io';

    Future<void> main(List<String> args) async {
      var file = File(args[0]);
      var lines = utf8.decoder
          .bind(file.openRead())
          .transform(LineSplitter());
      await for (var line in lines) {
        if (!line.startsWith('#')) print(line);
      }
    }
    ```
    - `Utf8Decoder` are a transformer.
    - A transformer requires only one function: `bind()`

- **Handling errors and completion**
  - If use an asynchronous `for` loop, then use try-catch to handle errors.

    ```dart
    Future<void> readFileAwaitFor() async {
      var config = File('config.txt');
      Stream<List<int>> inputStream = config.openRead();

      var lines = inputStream
          .transform(utf8.decoder)
          .transform(LineSplitter());
      try {
        await for (var line in lines) {
          print('Got ${line.length} characters from stream');
        }
        print('file is now closed');
      } catch (e) {
        print(e);
      }
    }
    ```
  - If use the `Stream` API, then handle errors by registering an `onError` listener.

    ```dart
    var config = File('config.txt');
      Stream<List<int>> inputStream = config.openRead();

      inputStream
          .transform(utf8.decoder)
          .transform(LineSplitter())
          .listen((String line) {
        print('Got ${line.length} characters from stream');
      }, onDone: () {
        print('file is now closed');
      }, onError: (e) {
        print(e);
    });
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

