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

## dart:core

#### Printing to the console
- The top-level `print()` method takes a single argument (any `Object`) and displays that object’s string value (as returned by `toString()`) in the console.

```dart
print(anObject);
print('I drink $tea.');
```

#### Numbers
- The `dart:core` library defines the num, int, and double classes.
- You can convert a string into an integer or double with the `parse()` methods.

  ```dart
  assert(int.parse('42') == 42);
  assert(int.parse('0x42') == 66);
  assert(double.parse('0.50') == 0.5);

  assert(num.parse('42') is int);
  assert(num.parse('0x42') is int);
  assert(num.parse('0.50') is double);
  ``` 

  - To specify the base of an integer, add a `radix` parameter: `assert(int.parse('42', radix: 16) == 66);`

- To specify the number of digits to the right of the decimal, use `toStringAsFixed()`.

  ```dart
  // Convert an int to a string.
  assert(42.toString() == '42');

  // Convert a double to a string.
  assert(123.456.toString() == '123.456');

  // Specify the number of digits after the decimal.
  assert(123.456.toStringAsFixed(2) == '123.46');
  ```

- To specify the number of significant digits in the string, use `toStringAsPrecision()`

  ```dart
  // Specify the number of significant figures.
  assert(123.456.toStringAsPrecision(2) == '1.2e+2');
  assert(double.parse('1.2e+2') == 120.0);
  ```

#### Strings and regular expressions
- A string in Dart is an **immutable** sequence of **UTF-16** code units.
- You can use regular expressions (`RegExp` objects) to search within strings and to replace parts of strings.

- Searching inside a string

  ```dart
  // Check whether a string contains another string.
  assert('Never odd or even'.contains('odd'));

  // Does a string start with another string?
  assert('Never odd or even'.startsWith('Never'));

  // Does a string end with another string?
  assert('Never odd or even'.endsWith('even'));

  // Find the location of a string inside a string.
  assert('Never odd or even'.indexOf('odd') == 6);
  ```

- Extracting data from a string

  ```dart
  // Grab a substring.
  assert('Never odd or even'.substring(6, 9) == 'odd');

  // Split a string using a string pattern.
  var parts = 'structured web apps'.split(' ');
  assert(parts.length == 3);
  assert(parts[0] == 'structured');

  // Get a UTF-16 code unit (as a string) by index.
  assert('Never odd or even'[0] == 'N');

  // Use split() with an empty string parameter to get
  // a list of all characters (as Strings); good for
  // iterating.
  for (var char in 'hello'.split('')) {
    print(char);
  }

  // Get all the UTF-16 code units in the string.
  var codeUnitList =
      'Never odd or even'.codeUnits.toList();
  assert(codeUnitList[0] == 78);
  ```

  - In many cases, you want to work with Unicode grapheme clusters as opposed to pure code units. For this, the Dart team provides the `characters` package.

- Converting to uppercase or lowercase

  ```dart
  // Convert to uppercase.
  assert('structured web apps'.toUpperCase() ==
      'STRUCTURED WEB APPS');

  // Convert to lowercase.
  assert('STRUCTURED WEB APPS'.toLowerCase() ==
      'structured web apps');
  ```

- Trimming and empty strings
  
  ```dart
  // Trim a string.
  assert('  hello  '.trim() == 'hello');

  // Check whether a string is empty.
  assert(''.isEmpty);

  // Strings with only white space are not empty.
  assert('  '.isNotEmpty);
  ```

- Replacing part of a string
  - Strings are immutable objects, which means **you can create them but you can’t change them**.

  ```dart
  var greetingTemplate = 'Hello, NAME!';
  var greeting =
      greetingTemplate.replaceAll(RegExp('NAME'), 'Bob');

  // greetingTemplate didn't change.
  assert(greeting != greetingTemplate);
  ```

- Building a string
  - To programmatically generate a string, you can use `StringBuffer`. A `StringBuffer` doesn’t generate a new `String` object **until `toString()` is called**.

  ```dart
  var sb = StringBuffer();
  sb
    ..write('Use a StringBuffer for ')
    ..writeAll(['efficient', 'string', 'creation'], ' ')
    ..write('.');

  var fullString = sb.toString();

  assert(fullString ==
      'Use a StringBuffer for efficient string creation.');
  ```

- Regular expressions

  ```dart
  // Here's a regular expression for one or more digits.
  var numbers = RegExp(r'\d+');

  var allCharacters = 'llamas live fifteen to twenty years';
  var someDigits = 'llamas live 15 to 20 years';

  // contains() can use a regular expression.
  assert(!allCharacters.contains(numbers));
  assert(someDigits.contains(numbers));

  // Replace every match with another string.
  var exedOut = someDigits.replaceAll(numbers, 'XX');
  assert(exedOut == 'llamas live XX to XX years');
  ```

  - The Match class provides access to a regular expression match.
    
    ```dart
    var numbers = RegExp(r'\d+');
    var someDigits = 'llamas live 15 to 20 years';

    // Check whether the reg exp has a match in a string.
    assert(numbers.hasMatch(someDigits));

    // Loop through all matches.
    for (var match in numbers.allMatches(someDigits)) {
      print(match.group(0)); // 15, then 20
    }
    ```

#### Collections

- Lists
  - The `List` class also defines several methods for adding items to and removing items from lists.
    ```dart
    // Create an empty list of strings.
    var grains = <String>[];
    assert(grains.isEmpty);

    // Create a list using a list literal.
    var fruits = ['apples', 'oranges'];

    // Add to a list.
    fruits.add('kiwis');

    // Add multiple items to a list.
    fruits.addAll(['grapes', 'bananas']);

    // Get the list length.
    assert(fruits.length == 5);

    // Remove a single item.
    var appleIndex = fruits.indexOf('apples');
    fruits.removeAt(appleIndex);
    assert(fruits.length == 4);

    // Remove all elements from a list.
    fruits.clear();
    assert(fruits.isEmpty);

    // You can also create a List using one of the constructors.
    var vegetables = List.filled(99, 'broccoli');
    assert(vegetables.every((v) => v == 'broccoli'));
    ``` 

  - Use `indexOf()` to find the index of an object in a list

    ```dart
    var fruits = ['apples', 'oranges'];

    // Access a list item by index.
    assert(fruits[0] == 'apples');

    // Find an item in a list.
    assert(fruits.indexOf('apples') == 0);
    ``` 

  - Sort a list using the `sort()` method.
    - You can provide a sorting function that compares two objects. This sorting function must return < 0 for smaller, 0 for the same, and > 0 for bigger. 

    ```dart
    var fruits = ['bananas', 'apples', 'oranges'];

    // Sort a list.
    fruits.sort((a, b) => a.compareTo(b));
    assert(fruits[0] == 'apples');
    ```

- Sets
  - A set in Dart is an **unordered** collection of unique items

    ```dart
    // Create an empty set of strings.
    var ingredients = <String>{};

    // Add new items to it.
    ingredients.addAll(['gold', 'titanium', 'xenon']);
    assert(ingredients.length == 3);

    // Adding a duplicate item has no effect.
    ingredients.add('gold');
    assert(ingredients.length == 3);

    // Remove an item from a set.
    ingredients.remove('gold');
    assert(ingredients.length == 2);

    // You can also create sets using
    // one of the constructors.
    var atomicNumbers = Set.from([79, 22, 54]);
    ``` 

  - Use `contains()` and `containsAll()` to check whether one or more objects are in a set

    ```dart
    var ingredients = Set<String>();
    ingredients.addAll(['gold', 'titanium', 'xenon']);

    // Check whether an item is in the set.
    assert(ingredients.contains('titanium'));

    // Check whether all the items are in the set.
    assert(ingredients.containsAll(['titanium', 'xenon']));
    ```

  - An intersection is a set whose items are in two other sets.

    ```dart
    var ingredients = Set<String>();
    ingredients.addAll(['gold', 'titanium', 'xenon']);

    // Create the intersection of two sets.
    var nobleGases = Set.from(['xenon', 'argon']);
    var intersection = ingredients.intersection(nobleGases);
    assert(intersection.length == 1);
    assert(intersection.contains('xenon'));
    ```

- Maps
  - A map, commonly known as a *dictionary* or *hash*, is an unordered collection of key-value pairs. 
  - We can declare a map using a terse literal syntax, or we can use a traditional constructor.
    ```dart
    // Maps often use strings as keys.
    var hawaiianBeaches = {
      'Oahu': ['Waikiki', 'Kailua', 'Waimanalo'],
      'Big Island': ['Wailea Bay', 'Pololu Beach'],
      'Kauai': ['Hanalei', 'Poipu']
    };

    // Maps can be built from a constructor.
    var searchTerms = Map();

    // Maps are parameterized types; you can specify what
    // types the key and value should be.
    var nobleGases = Map<int, String>();
    ``` 

  - We add, get, and set map items using the bracket syntax. Use `remove()` to remove a key and its value from a map.

    ```dart
    var nobleGases = {54: 'xenon'};

    // Retrieve a value with a key.
    assert(nobleGases[54] == 'xenon');

    // Check whether a map contains a key.
    assert(nobleGases.containsKey(54));

    // Remove a key and its value.
    nobleGases.remove(54);
    assert(!nobleGases.containsKey(54));
    ``` 

  - We can retrieve all the values or all the keys from a map:

    ```dart
    var hawaiianBeaches = {
      'Oahu': ['Waikiki', 'Kailua', 'Waimanalo'],
      'Big Island': ['Wailea Bay', 'Pololu Beach'],
      'Kauai': ['Hanalei', 'Poipu']
    };

    // Get all the keys as an unordered collection
    // (an Iterable).
    var keys = hawaiianBeaches.keys;

    assert(keys.length == 3);
    assert(Set.from(keys).contains('Oahu'));

    // Get all the values as an unordered collection
    // (an Iterable of Lists).
    var values = hawaiianBeaches.values;
    assert(values.length == 3);
    assert(values.any((v) => v.contains('Waikiki')));
    ```

  - To check whether a map contains a key, use `containsKey()`. Because map values can be `null`, **we cannot rely on simply getting the value for the key and checking for null**.

    ```dart
    var hawaiianBeaches = {
      'Oahu': ['Waikiki', 'Kailua', 'Waimanalo'],
      'Big Island': ['Wailea Bay', 'Pololu Beach'],
      'Kauai': ['Hanalei', 'Poipu']
    };

    assert(hawaiianBeaches.containsKey('Oahu'));
    assert(!hawaiianBeaches.containsKey('Florida'));
    ```

  - Use the `putIfAbsent()` method when you want to assign a value to a key if and only if the key does not already exist.

    ```dart
    var teamAssignments = <String, String>{};
    teamAssignments.putIfAbsent('Catcher', () => pickToughestKid());
    assert(teamAssignments['Catcher'] != null);
    ``` 

- Common collection methods
  - List, Set, and Map share common functionality found in many collections.
  - Use `isEmpty` or `isNotEmpty` to check whether a list, set, or map has items:
    ```dart
    var coffees = <String>[];
    var teas = ['green', 'black', 'chamomile', 'earl grey'];
    assert(coffees.isEmpty);
    assert(teas.isNotEmpty);
    ``` 

  - To apply a function to each item in a list, set, or map, use `forEach()`:

    ```dart
    var teas = ['green', 'black', 'chamomile', 'earl grey'];

    teas.forEach((tea) => print('I drink $tea'));
    ```

    - When we invoke `forEach()` on a **map**, the function must take two arguments (the key and value):

      ```dart
      hawaiianBeaches.forEach((k, v) {
        print('I want to visit $k and swim at $v');
        // I want to visit Oahu and swim at
        // [Waikiki, Kailua, Waimanalo], etc.
      });
      ```

  - Iterables provide the `map()` method, which gives all the results in a single object.

    ```dart
    var teas = ['green', 'black', 'chamomile', 'earl grey'];

    var loudTeas = teas.map((tea) => tea.toUpperCase());
    loudTeas.forEach(print);
    ```

    - The object returned by `map()` is an Iterable that’s **lazily evaluated**. To force your function to be called immediately on each item, use `map().toList()` or `map().toSet()`.

      ```dart
      var loudTeas =
      teas.map((tea) => tea.toUpperCase()).toList();
      ```

  - Use Iterable’s `where()` method to get all the items that match a condition. Use Iterable’s `any()` and `every()` methods to check whether some or all items match a condition.

    ```dart
    var teas = ['green', 'black', 'chamomile', 'earl grey'];

    // Chamomile is not caffeinated.
    bool isDecaffeinated(String teaName) =>
        teaName == 'chamomile';

    // Use where() to find only the items that return true
    // from the provided function.
    var decaffeinatedTeas =
        teas.where((tea) => isDecaffeinated(tea));
    // or teas.where(isDecaffeinated)

    // Use any() to check whether at least one item in the
    // collection satisfies a condition.
    assert(teas.any(isDecaffeinated));

    // Use every() to check whether all the items in a
    // collection satisfy a condition.
    assert(!teas.every(isDecaffeinated));
    ```

#### URIs
- The `Uri` class provides functions to encode and decode strings for use in URIs.

- Encoding and decoding fully qualified URIs

  - To encode and decode characters **except** those with special meaning in a URI (such as `/`, `:`, `&`, `#`), use the `encodeFull()` and `decodeFull()` methods.
  ```dart
  var uri = 'https://example.org/api?foo=some message';

  var encoded = Uri.encodeFull(uri);
  assert(encoded ==
      'https://example.org/api?foo=some%20message');

  var decoded = Uri.decodeFull(encoded);
  assert(uri == decoded);
  ```

- Encoding and decoding URI components
  - To encode and decode all of a string’s characters that have special meaning in a URI, use the `encodeComponent()` and `decodeComponent()`.

  ```dart
  var uri = 'https://example.org/api?foo=some message';

  var encoded = Uri.encodeComponent(uri);
  assert(encoded ==
      'https%3A%2F%2Fexample.org%2Fapi%3Ffoo%3Dsome%20message');

  var decoded = Uri.decodeComponent(encoded);
  assert(uri == decoded);
  ```

- Parsing URIs

  ```dart
  var uri =
    Uri.parse('https://example.org:8080/foo/bar#frag');

  assert(uri.scheme == 'https');
  assert(uri.host == 'example.org');
  assert(uri.path == '/foo/bar');
  assert(uri.fragment == 'frag');
  assert(uri.origin == 'https://example.org:8080');
  ```

- Building URIs

  ```dart
  var uri = Uri(
      scheme: 'https',
      host: 'example.org',
      path: '/foo/bar',
      fragment: 'frag');
  assert(
      uri.toString() == 'https://example.org/foo/bar#frag');
  ```

#### Dates and times
- The time zone is either UTC or the local time zone.


```dart
// Get the current date and time.
var now = DateTime.now();

// Create a new DateTime with the local time zone.
var y2k = DateTime(2000); // January 1, 2000

// Specify the month and day.
y2k = DateTime(2000, 1, 2); // January 2, 2000

// Specify the date as a UTC time.
y2k = DateTime.utc(2000); // 1/1/2000, UTC

// Specify a date and time in ms since the Unix epoch.
y2k = DateTime.fromMillisecondsSinceEpoch(946684800000,
    isUtc: true);

// Parse an ISO 8601 date.
y2k = DateTime.parse('2000-01-01T00:00:00Z');
```

- Use the `Duration` class to calculate the difference between two dates and to shift a date forward or backward

  > Using a `Duration` to shift a DateTime by days can be problematic, due to clock shifts. **Use `UTC` dates if you must shift days**. 

```dart
var y2k = DateTime.utc(2000);

// Add one year.
var y2001 = y2k.add(const Duration(days: 366));
assert(y2001.year == 2001);

// Subtract 30 days.
var december2000 =
    y2001.subtract(const Duration(days: 30));
assert(december2000.year == 2000);
assert(december2000.month == 12);

// Calculate the difference between two dates.
// Returns a Duration object.
var duration = y2001.difference(y2k);
assert(duration.inDays == 366); // y2k was a leap year.
```

#### Utility classes
- The core library contains various utility classes, useful for sorting, mapping values, and iterating.

- Comparing objects
  - Implement the `Comparable` interface to indicate that an object can be compared to another object.
  - The `compareTo()` method returns `< 0` for smaller, `0` for the same, and `> 0` for bigger.
  
  ```dart
  class Line implements Comparable<Line> {
    final int length;
    const Line(this.length);

    @override
    int compareTo(Line other) => length - other.length;
  }

  void main() {
    var short = const Line(1);
    var long = const Line(100);
    assert(short.compareTo(long) < 0);
  }
  ```

- Implementing map keys
  - Each object in Dart **automatically provides an integer hash code**, and thus can be used as a key in a map.
  - You can override the `hashCode` getter to generate a custom hash code. If you do, you might also want to override the `==` operator. 
    - A hash code doesn’t have to be unique, but it should be well distributed.

  ```dart
  class Person {
    final String firstName, lastName;

    Person(this.firstName, this.lastName);

    // Override hashCode using strategy from Effective Java,
    // Chapter 11.
    @override
    int get hashCode {
      int result = 17;
      result = 37 * result + firstName.hashCode;
      result = 37 * result + lastName.hashCode;
      return result;
    }

    // You should generally implement operator == if you
    // override hashCode.
    @override
    bool operator ==(dynamic other) {
      return other is Person &&
          other.firstName == firstName &&
          other.lastName == lastName;
    }
  }

  void main() {
    var p1 = Person('Bob', 'Smith');
    var p2 = Person('Bob', 'Smith');
    var p3 = 'not a person';
    assert(p1.hashCode == p2.hashCode);
    assert(p1 == p2);
    assert(p1 != p3);
  }
  ```

- Iteration
  - The `Iterable` and `Iterator` classes support `for`-`in` loops. 
  - Implement `Iterable` whenever you create a class that can provide Iterators for use in for-in loops.
  - Implement `Iterator` to define the actual iteration ability.

  ```dart
  class Process {
    // Represents a process...
  }

  class ProcessIterator implements Iterator<Process> {
    @override
    Process get current => ...
    @override
    bool moveNext() => ...
  }

  // A mythical class that lets you iterate through all
  // processes. Extends a subclass of [Iterable].
  class Processes extends IterableBase<Process> {
    @override
    final Iterator<Process> iterator = ProcessIterator();
  }

  void main() {
    // Iterable objects can be used with for-in.
    for (var process in Processes()) {
      // Do something with the process.
    }
  }
  ```

#### Exceptions

- A couple of the most common errors are:
  - [NoSuchMethodError](https://api.dart.dev/stable/2.13.4/dart-core/NoSuchMethodError-class.html): Thrown when a receiving object (which might be null) does not implement a method.
  - [ArgumentError](https://api.dart.dev/stable/2.13.4/dart-core/ArgumentError-class.html): Can be thrown by a method that encounters an unexpected argument.

## dart:async
- Asynchronous programming often uses callback functions, but Dart provides alternatives: `Future` and `Stream` objects

- The `dart:async` library works in both web apps and command-line apps. To use it:
  1. As of **Dart 2.1**, you don’t need to import `dart:async` to use the Future and Stream APIs.
  2. import `dart:async`

## dart:math
- The `dart:math` library provides common functionality such as `sine` and `cosine`, `maximum` and `minimum`, and constants such as `pi` and `e`. 

#### Trigonometry

```dart
// Cosine
assert(cos(pi) == -1.0);

// Sine
var degrees = 30;
var radians = degrees * (pi / 180);
// radians is now 0.52359.
var sinOf30degrees = sin(radians);
// sin 30° = 0.5
assert((sinOf30degrees - 0.5).abs() < 0.01);
```

- The functions use **radians**, not degrees.

#### Maximum and minimum

```dart
assert(max(1, 1000) == 1000);
assert(min(1, -1000) == -1000);
```

#### Math constants

```dart
// See the Math library for additional constants.
print(e); // 2.718281828459045
print(pi); // 3.141592653589793
print(sqrt2); // 1.4142135623730951
```

#### Random numbers
- Generate random numbers with the `Random` class.
  - You can optionally provide a seed to the Random constructor.

```dart
var random = Random();
random.nextDouble(); // Between 0.0 and 1.0: [0, 1)
random.nextInt(10); // Between 0 and 9.
random.nextBool(); // true or false
```

## dart:convert 

#### Decoding and encoding JSON

- Decode
  ```dart
  // NOTE: Be sure to use double quotes ("),
  // not single quotes ('), inside the JSON string.
  // This string is JSON, not Dart.
  var jsonString = '''
    [
      {"score": 40},
      {"score": 80}
    ]
  ''';

  var scores = jsonDecode(jsonString);
  assert(scores is List);

  var firstScore = scores[0];
  assert(firstScore is Map);
  assert(firstScore['score'] == 40);
  ```

- Encode

  ```dart
  var scores = [
    {'score': 40},
    {'score': 80},
    {'score': 100, 'overtime': true, 'special_guest': null}
  ];

  var jsonText = jsonEncode(scores);
  assert(jsonText ==
      '[{"score":40},{"score":80},'
          '{"score":100,"overtime":true,'
          '"special_guest":null}]');
  ``` 

- Only objects of type `int`, `double`, `String`, `bool`, `null`, `List`, or `Map` are directly encodable into JSON.
- We have two options for encoding objects that aren’t directly encodable.
  1. `jsonEncode()` with a second argument: a function that returns an object that is directly encodable.
  2. Omit the second argument and the encoder calls the object’s `toJson()` method

#### Decoding and encoding UTF-8 characters

- Decode

  ```dart
  List<int> utf8Bytes = [
    0xc3, 0x8e, 0xc3, 0xb1, 0xc5, 0xa3, 0xc3, 0xa9,
    0x72, 0xc3, 0xb1, 0xc3, 0xa5, 0xc5, 0xa3, 0xc3,
    0xae, 0xc3, 0xb6, 0xc3, 0xb1, 0xc3, 0xa5, 0xc4,
    0xbc, 0xc3, 0xae, 0xc5, 0xbe, 0xc3, 0xa5, 0xc5,
    0xa3, 0xc3, 0xae, 0xe1, 0xbb, 0x9d, 0xc3, 0xb1
  ];

  var funnyWord = utf8.decode(utf8Bytes);

  assert(funnyWord == 'Îñţérñåţîöñåļîžåţîờñ');
  ```

  - To convert a stream of UTF-8 characters, specify `utf8.decoder` to the Stream `transform()`

  ```dart
  var lines = utf8.decoder.bind(inputStream).transform(LineSplitter());
  try {
    await for (var line in lines) {
      print('Got ${line.length} characters from stream');
    }
    print('file is now closed');
  } catch (e) {
    print(e);
  }
  ```

- Encode

  ```dart
  List<int> encoded = utf8.encode('Îñţérñåţîöñåļîžåţîờñ');

  assert(encoded.length == utf8Bytes.length);
  for (int i = 0; i < encoded.length; i++) {
    assert(encoded[i] == utf8Bytes[i]);
  }
  ```

## dart:io
- The dart:io library provides APIs to deal with files, directories, processes, sockets, WebSockets, and HTTP clients and servers.
- In general, the dart:io library implements and promotes an **asynchronous API**.
  - The few synchronous methods in the library are clearly marked with a `Sync` suffix on the name.

#### Files and directories

- Read
  - There are two choices for reading: *all at once*, or *streaming*.
  - Reading a file all at once requires enough memory to store all the contents.
  - When reading a text file encoded using UTF-8.
    ```dart
    Future<void> main() async {
      var config = File('config.txt');

      // Put the whole file in a single string.
      var stringContents = await config.readAsString();
      print(
          'The file is ${stringContents.length} characters long.');

      // Put each line of the file into its own string.
      var lines = await config.readAsLines();
      print('The file is ${lines.length} lines long.');
    }
    ```

    - We can read the entire file contents with `readAsString()`.
    - When the individual lines are important, we can use `readAsLines()`.
    - Both cases return a `Future` object providing the contents of the file as one or more strings.

  - When reading an entire file as bytes into a list of ints

    ```dart
    Future<void> main() async {
      var config = File('config.txt');

      var contents = await config.readAsBytes();
      print('The file is ${contents.length} bytes long.');
    }
    ```

  - Streaming file contents

    ```dart
    import 'dart:io';
    import 'dart:convert';

    Future<void> main() async {
      var config = File('config.txt');
      Stream<List<int>> inputStream = config.openRead();

      var lines =
          utf8.decoder.bind(inputStream).transform(LineSplitter());
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

- Write
  - Use the `File` `openWrite()` method to get an `IOSink` that we can write to. The default mode, `FileMode.write`, **completely overwrites** existing data in the file.

    ```dart
    var logFile = File('log.txt');
    var sink = logFile.openWrite();
    sink.write('FILE ACCESSED ${DateTime.now()}\n');
    await sink.flush();
    await sink.close();
    ```

  - To add to the end of the file, use the optional `mode` parameter.

    ```dart
    var sink = logFile.openWrite(mode: FileMode.append);
    ```

  - To write binary data, use `add(List<int> data)`.

- Listing files in a directory

  ```dart
  Future<void> main() async {
    var dir = Directory('tmp');

    try {
      var dirList = dir.list();
      await for (FileSystemEntity f in dirList) {
        if (f is File) {
          print('Found file ${f.path}');
        } else if (f is Directory) {
          print('Found dir ${f.path}');
        }
      }
    } catch (e) {
      print(e.toString());
    }
  }
  ```

#### HTTP server
- The HttpServer class provides the low-level functionality for building web servers.

```dart
Future<void> main() async {
  final requests = await HttpServer.bind('localhost', 8888);
  await for (var request in requests) {
    processRequest(request);
  }
}

void processRequest(HttpRequest request) {
  print('Got request for ${request.uri.path}');
  final response = request.response;
  if (request.uri.path == '/dart') {
    response
      ..headers.contentType = ContentType(
        'text',
        'plain',
      )
      ..write('Hello from the server');
  } else {
    response.statusCode = HttpStatus.notFound;
  }
  response.close();
}
```


#### HTTP client
- The `HttpClient` class helps you connect to HTTP resources from your Dart **command-line** or **server-side** application. 

```dart
Future<void> main() async {
  var url = Uri.parse('http://localhost:8888/dart');
  var httpClient = HttpClient();
  var request = await httpClient.getUrl(url);
  var response = await request.close();
  var data = await utf8.decoder.bind(response).toList();
  print('Response ${response.statusCode}: $data');
  httpClient.close();
}
```