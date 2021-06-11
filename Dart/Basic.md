## Overview
```dart
// Define a function.
void printInteger(int aNumber) {
  print('The number is $aNumber.'); // Print to console.
}

// This is where the app starts executing.
void main() {
  var number = 42; // Declare and initialize a variable.
  printInteger(number); // Call a function.
}
```

- Playground [DartPad](https://dartpad.dev/)
- Everything you can place in a variable is an **object**. All objects, including numbers and functions, inherit from the Object except for `null`.
  - In Dart, the `new` keyword is optional.
- Dart is strongly typed, and it can infer types.
  - If you enable `null safety`, variables can’t contain null unless you say they can.
    - Make a variable nullable by putting `?` at the end of its type.
    - Add `!` to assert that it isn’t null.
  - Use the type `Object?` to allow any type

- Dart supports generic types.
- Dart supports top-level functions, `static` and instance methods, and functions within functions.
- Dart supports top-level variables.
- Dart doesn’t have the keywords `public`, `protected`, and `private`. If an identifier starts with `_`, it’s **private** to its library. 
- Dart has both *expressions* (which have runtime values) and *statements* (which don’t). 
  - The conditional expression condition `? expr1 : expr2` has a value of `expr1` or `expr2`. Compare that to an `if-else` statement, which has no value. 
  - A statement often contains one or more expressions, but an expression can’t directly contain a statement.

- Keywords

  |  |  |  |  |
  |-|-|-|-|
  | abstract 2 | else | import 2 | show 1 |
  | as 2 | enum | in | static 2 |
  | assert | export 2 | interface 2 | super |
  | async 1 | extends | is | switch |
  | await | extension 2 | late 2 | sync 1 |
  | break | external 2 | library 2 | this |
  | case | factory 2 | mixin 2 | throw |
  | catch | false | new | true |
  | class | final | null | try |
  | const | finally | on 1 | typedef 2 |
  | continue | for | operator 2 | var |
  | covariant 2 | Function 2 | part 2 | void |
  | default | get 2 | required 2 | while |
  | deferred 2 | hide 1 | rethrow | with |
  | do | if | return | yield |
  | dynamic 2 | implements 2 | set 2 |   |
  
  1. 1 are contextual keywords, which have meaning only in specific places. They’re valid identifiers everywhere.
  2. 2 are built-in identifiers. To simplify the task of **porting JavaScript code to Dart**, these keywords are valid identifiers in most places, but they can’t be used as class or type names, or as import prefixes
  3. All other words in the table are reserved words, which can’t be identifiers.

## Variables
- Variables store **references**. 
  - `var name = 'Bob';`:  is inferred to be `String`
  - `Object name = 'Bob';`: we change that type by specifying as `Object`.
  - `String name = 'Bob'`: explicitly declare the type that would be inferred

- *Uninitialized* variables
  - Non-null safety: all initial values are `null` since every variable has a nullable type.

    ```dart
    int? lineCount;
    // Production code ignores the assert() call.
    assert(lineCount == null);
    ``` 
  - null safety: we must initialize the values of non-nullable variables **before using** them.
    - We **don’t** have to initialize a local variable **where it’s declared**, but you do need to assign it a value **before it’s used**.

    ```dart
    int lineCount;

    if (weLikeToCount) {
      lineCount = countLines();
    } else {
      lineCount = 0;
    }

    print(lineCount);
    ```

- Late variables
  - Declares a non-nullable variable that’s **initialized after its declaration**
    - Sometimes detection of non-initialized used failed.
     
      ```dart
      late String description;

      void main() {
        description = 'Feijoada!';
        print(description);
      }
      ```

  - **Lazily initializing** a variable.
    - When a late variable initializes at its declaration, then the initializer runs the first time the variable is used.
    - Useful cases
      - The variable might not be needed, and initializing it is costly.
      - You’re initializing an instance variable, and its initializer needs access to thi

    ```dart
    late String temperature = _readThermometer(); // Lazily initialized.
    ```

- Final and const
- `final` variable can be set only once. 
  - A final top-level or class variable is initialized the first time it’s used.

  ```dart
  final name = 'Bob'; // Without a type annotation
  final String nickname = 'Bobby';

  name = 'Alice'; // Error: a final variable can only be set once.
  ```

- `const` variable is a compile-time constant.
  - If the const variable is at the class level, mark it `static const`.

  ```dart
  const bar = 1000000; // Unit of pressure (dynes/cm2)
  const double atm = 1.01325 * bar; // Standard atmosphere
  ```

  - We can define constants that use type checks and casts (`is` and `as`), collection `if`, and spread operators (`...` and `...?`):

  ```dart
  const Object i = 3; // Where i is a const Object with an int value...
  const list = [i as int]; // Use a typecast.
  const map = {if (i is int) i: 'int'}; // Use is and collection if.
  const set = {if (list is List<int>) ...list}; // ...and a spread.
  ```

- The `const` keyword can also be used to create constant values as well as to declare constructors that create constant values

  ```dart
  var foo = const [];
  final bar = const [];
  const baz = []; // Equivalent to `const []`

  baz = [42]; // Error: Constant variables can't be assigned a value.
  ``` 

- Although a `final` object cannot be modified, its **fields can be changed**. In comparison, a `const` object and its fields cannot be changed: they’re **immutable**.

## Built-in types
- Dart built-in types includes
  - Numbers (`int`, `double`)
  - Strings (`String`)
  - Booleans (`bool`)
  - Lists (`List`, also known as arrays)
  - Sets (`Set`)
  - Maps (`Map`)
  - Runes (`Runes`; often replaced by the `characters` API)
  - Symbols (`Symbol`)
  - `Null`

- Some other types also have special roles
  - `Object`: The superclass of all Dart classes except `Null`.
  - `Future` and `Stream`: Used in asynchrony support.
  - `Iterable`: Used in for-in loops and in synchronous generator functions.
  - `Never`: Indicates that an expression can never successfully finish evaluating. Most often used for functions that always throw an exception.
  - `dynamic`: Indicates that you want to **disable static checking**. Usually you should use `Object` or `Object?` instead.
  - `void`: Indicates that a **value is never used**. Often used as a return type.

- Because **every variable in Dart refers to an object**, you can usually use constructors to initialize variables.

- Numbers
  - `int`: Integer values no larger than 64 bits,
    - On native platforms, values can be from $-2^{63}$ to $2^{63} - 1$
    - On the web, values can be from $-2^{53}$ to $2^{53} - 1$

    ```dart
    var x = 1;
    var hex = 0xDEADBEEF;
    var exponent = 8e5;
    ```

    - **Only** int type specifies bitwise operations: `(<<,` `>>),` AND (`&`), and OR (`|`) operators.

      ```dart
      assert((3 << 1) == 6); // 0011 << 1 == 0110
      assert((3 >> 1) == 1); // 0011 >> 1 == 0001
      assert((3 | 4) == 7); // 0011 | 0100 == 0111
      ``` 

  - `double`: 64-bit (double-precision) floating-point numbers
    - Integer literals are automatically converted to doubles when necessary.

    ```dart
    var y = 1.1;
    var exponents = 1.42e5;
    double z = 1; // Equivalent to double z = 1.0.
    ``` 

  - Both int and double are subtypes of `num`. The `n`um type includes
    - Basic operators such as +, -, /, and *.
    - `abs()`, `ceil()`, `floor()` and other methods.
    - If `num` and its subtypes don’t have what you’re looking for, the `dart:math` library might.

    ```dart
    num x = 1; // x can have both int and double values
    x += 2.5;
    ```

  - We can turn a string into a number, and vice versa

    ```dart
    // String -> int
    var one = int.parse('1');
    assert(one == 1);

    // String -> double
    var onePointOne = double.parse('1.1');
    assert(onePointOne == 1.1);

    // int -> String
    String oneAsString = 1.toString();
    assert(oneAsString == '1');

    // double -> String
    String piAsString = 3.14159.toStringAsFixed(2);
    assert(piAsString == '3.14');
    ``` 

  - Many arithmetic expressions are  compile-time constants, as long as their operands are compile-time constants.

    ```dart
    const msPerSecond = 1000;
    const secondsUntilRetry = 5;
    const msUntilRetry = secondsUntilRetry * msPerSecond;
    ``` 

- Strings
  - `String` object holds a sequence of **UTF-16** code units.
  - Use *single or double* quotes to create a string:
    ```dart
    var s1 = 'Single quotes work well for string literals.';
    var s2 = "Double quotes work just as well.";
    var s3 = 'It\'s easy to escape the string delimiter.';
    var s4 = "It's even easier to use the other delimiter.";
    ``` 
  - Use triple quote to create a multi-line string
    ```dart
    var s1 = '''
    You can create
    multi-line strings like this one.
    ''';

    var s2 = """This is also a
    multi-line string.""";
    ``` 

  - We can put the value inside a string by using `${expression}`
    - If the expression is an identifier, `{}` can be omitted.
    - Dart calls the object’s `toString()` method.

    ```dart
    var s = 'string interpolation';

    assert('Dart has $s, which is very handy.' ==
        'Dart has string interpolation, ' +
            'which is very handy.');
    assert('That deserves all caps. ' +
            '${s.toUpperCase()} is very handy!' ==
        'That deserves all caps. ' +
            'STRING INTERPOLATION is very handy!');
    ``` 

  - Use adjacent string literals or `+` to concatenate strings.

    ```dart
    var s1 = 'String '
        'concatenation'
        " works even over line breaks.";
    assert(s1 ==
        'String concatenation works even over '
            'line breaks.');

    var s2 = 'The + operator ' + 'works, as well.';
    assert(s2 == 'The + operator works, as well.');
    ``` 

  - Prefix `r` to create a **raw string**:
    ```dart
    var s = r'In a raw string, not even \n gets special treatment.';
    ``` 

  - Literal strings are compile-time constants, as long as any interpolated expression is a compile-time constant
    ```dart
    // These work in a const string.
    const aConstNum = 0;
    const aConstBool = true;
    const aConstString = 'a constant string';

    // These do NOT work in a const string.
    var aNum = 0;
    var aBool = true;
    var aString = 'a string';
    const aConstList = [1, 2, 3];

    const validConstString = '$aConstNum $aConstBool $aConstString';
    // const invalidConstString = '$aNum $aBool $aString $aConstList';
    ``` 

- Booleans
  - Only two objects have type `bool`: the boolean literals `true` and `false`.
  - We **can’t** use code like `if (nonbooleanValue)` or `assert (nonbooleanValue)`.

  ```dart
  // Check for an empty string.
  var fullName = '';
  assert(fullName.isEmpty);

  // Check for zero.
  var hitPoints = 0;
  assert(hitPoints <= 0);

  // Check for null.
  var unicorn;
  assert(unicorn == null);

  // Check for NaN.
  var iMeantToDoThis = 0 / 0;
  assert(iMeantToDoThis.isNaN);
  ``` 

  - `true` and `false` are both compile-time constants.
  
- Lists
  - In Dart, arrays are `List` objects.
  - Dart can infers `list` type
    ```dart
    var list = [1, 2, 3];
    // List<int> list = [1, 2, 3]
    ``` 
  - Lists use zero-based indexing, where `0` is the index of the first value
    ```dart
    var list = [1, 2, 3];
    assert(list.length == 3);
    assert(list[1] == 2); 

    list[1] = 1;
    assert(list[1] == 1);
    ``` 

  - This trailing comma doesn’t affect the collection literal

    ```dart
    var list = [
      'Car',
      'Boat',
      'Plane',
    ];
    ``` 

  - To create a list that’s a compile-time constant, add `const` **before the list literal**

    ```dart
    var constantList = const [1, 2, 3];
    // constantList[1] = 1; // This line will cause an error.
    ``` 

  - **Spread operator** (`...`) and **null-aware spread operator** (`...?`)
    - They provide a concise way to insert multiple values into a collection.

      ```dart
      var list = [1, 2, 3];
      var list2 = [0, ...list];
      assert(list2.length == 4);
      ```

    - If the expression to the right of the spread operator might be `null`, you can avoid exceptions by using a null-aware spread operator

      ```dart
      var list;
      var list2 = [0, ...?list];
      assert(list2.length == 1);
      ``` 

  - Dart offers **collection if** and **collection for**

    ```dart
    var nav = [
      'Home',
      'Furniture',
      'Plants',
      if (promoActive) 'Outlet'
    ];
    ```

    ```dart
    var listOfInts = [1, 2, 3];
    var listOfStrings = [
      '#0',
      for (var i in listOfInts) '#$i'
    ];
    assert(listOfStrings[1] == '#1');
    ```

- Sets
  - `Set` is an **unordered** collection of unique items
  - `Set` can be created using a set literal `{v1, v2, ...}` that infers the type.
    ```dart
    var halogens = {'fluorine', 'chlorine', 'bromine', 'iodine', 'astatine'};
    // Set<String> halogens = {...};
    ``` 
    
    - To create an empty set, use `{}` preceded by a type argument
      ```dart
      var names = <String>{};
      // Set<String> names = {}; // This works, too.
      // var names = {}; // Creates a map, not a set.
      ```

  - Use `add()` or `addAll()` to add item(s):

    ```dart
    var elements = <String>{};
    elements.add('fluorine');
    elements.addAll(halogens);
    ``` 

  - `length`:  get the number of items

    ```dart
    var elements = <String>{};
    elements.add('fluorine');
    elements.addAll(halogens);
    assert(elements.length == 5);
    ``` 

  - To create a set that’s a compile-time constant, add `const` before the set literal:
    ```dart
    final constantSet = const {
      'fluorine',
      'chlorine',
      'bromine',
      'iodine',
      'astatine',
    };
    ```  
  
  - Sets support **spread operators** and **collection if and for**, just like lists do.

- Maps
  - In general, a map is an object that associates keys and values. Both keys and values can be any type of object.
  - To create using map literals

    ```dart
    //  Map<String, String> 
    var gifts = {
      // Key:    Value
      'first': 'partridge',
      'second': 'turtledoves',
      'fifth': 'golden rings'
    };

    // Map<int, String>
    var nobleGases = {
      2: 'helium',
      10: 'neon',
      18: 'argon',
    };
    ```
  
  - To create using a Map constructor:

    ```dart
    var gifts = Map<String, String>();
    gifts['first'] = 'partridge';
    gifts['second'] = 'turtledoves';
    gifts['fifth'] = 'golden rings';

    var nobleGases = Map<int, String>();
    nobleGases[2] = 'helium';
    nobleGases[10] = 'neon';
    nobleGases[18] = 'argon';
    ``` 

  - To add a new key-value pair

    ```dart
    var gifts = {'first': 'partridge'};
    gifts['fourth'] = 'calling birds'; // Add a key-value pair
    ``` 

  - To retrieve a value from a map

    ```dart
    var gifts = {'first': 'partridge'};
    assert(gifts['first'] == 'partridge');
    ```

    - **If the key isn’t in a map**, `null` is returned.

    ```dart
    var gifts = {'first': 'partridge'};
    assert(gifts['fifth'] == null);
    ``` 

  - `.length`: get the number of key-value pairs

    ```dart
    var gifts = {'first': 'partridge'};
    gifts['fourth'] = 'calling birds';
    assert(gifts.length == 2);
    ```

  - To create a map that’s a compile-time constant, add `const` before the map literal:

    ```dart
    final constantMap = const {
      2: 'helium',
      10: 'neon',
      18: 'argon',
    };

    // constantMap[2] = 'Helium'; // This line will cause an error.
    ```

  - Maps also support **spread operators** and **collection if and for**.

- Runes and grapheme clusters
  - In Dart, `Runes` expose the Unicode code points of a string. We can use the `characters package` to view or manipulate user-perceived characters, also known as [Unicode (extended) grapheme clusters](https://unicode.org/reports/tr29/#Grapheme_Cluster_Boundaries)

  - Because a Dart string is a sequence of UTF-16 code units, expressing Unicode code points within a string requires special syntax.
    - The usual way to express a Unicode code point is `\uXXXX`.
      - The heart character (♥) is `\u2665`
    - To specify more or less than 4 hex digits, place the value in curly brackets
      - For example, the laughing emoji (😆) is `\u{1f606}`.

  - If you **need to manipulate individual** Unicode characters, use the `characters` package.

    ```dart
    import 'package:characters/characters.dart';
    var hi = 'Hi 🇩🇰';
    print(hi);
    print('The end of the string: ${hi.substring(hi.length - 1)}');
    print('The last character: ${hi.characters.last}\n');
    ``` 

- Symbols
  - `Symbol` object represents an **operator** or **identifier** declared in a Dart program.
  - To get the symbol for an identifier, use a symbol literal `#`

    ```dart
    #radix
    #bar
    ``` 
  - Symbol literals are compile-time constants.

## Functions
- Functions in Dart are objects and have a type: `Function`.
  -  You can also call an instance of a Dart class if it is `Callable classes`.
- All functions return a value. **If no return value** is specified, the statement return `null`.
- The function still works if the types are omitted although **type annotations for public APIs** is recommended.

  ```dart
  bool isNoble(int atomicNumber) {
    return _nobleGases[atomicNumber] != null;
  }

  isNoble2(atomicNumber) {
    return _nobleGases[atomicNumber] != null;
  }
  ``` 

- For functions that contain just one expression, you can use the shorthand syntax.
  ```dart
  bool isNoble(int atomicNumber) => _nobleGases[atomicNumber] != null;
  ``` 
  - `=> expr` syntax is a shorthand for `{ return expr; }`.
  - Only an expression — not a statement — can appear between `=>` and `;`. For example, you can’t put an if statement there, but you can use a conditional expression.

- **Parameters**
  - A function can have any number of *required positional* parameters, followed either by *named* parameters or by *optional positional* parameters (but not both)
    - We can use trailing commas when passing arguments to a function.

  - *Named parameters*
    - When calling a function, use `paramName: value`.
      ```dart
      enableFlags(bold: true, hidden: false);
      ```
    - When defining a function, use `{param1, param2, …}`
      ```dart
      /// Sets the [bold] and [hidden] flags ...
      void enableFlags({bool? bold, bool? hidden}) {...}
      ```

    - Named parameters are **optional** unless they’re specifically marked as `required`.
      ```dart
      const Scrollbar({Key? key, required Widget child})
      ``` 

  - *Optional positional parameters*
    - When defining a function, wrap function parameters in `[]`
      ```dart
      String say(String from, String msg, [String? device]) {
        var result = '$from says $msg';
        if (device != null) {
          result = '$result with a $device';
        }
        return result;
      }
      ``` 
    - When calling a function
      ```dart
      assert(say('Bob', 'Howdy') == 'Bob says Howdy');
      assert(say('Bob', 'Howdy', 'smoke signal') == 'Bob says Howdy with a smoke signal');
      ``` 
  
  - *Default parameter values*
    - We use `=` to define default values for both *named* and *positional* parameters.
      - The default values **must be compile-time constants**.
      - If no default value is provided, the default value is `null`.
      - Old code might use `:` instead of `=` to set default values of *named* parameters

      ```dart
      /// Sets the [bold] and [hidden] flags ...
      void enableFlags({bool bold = false, bool hidden = false}) {...}

      // bold will be true; hidden will be false.
      enableFlags(bold: true);

      String say(String from, String msg, [String device = 'carrier pigeon']) {
        var result = '$from says $msg with a $device';
        return result;
      }
      ```

- **First-class objects**
  - We can pass a function as a parameter
    ```dart
    void printElement(int element) {
      print(element);
    }

    var list = [1, 2, 3];

    // Pass printElement as a parameter.
    list.forEach(printElement);
    ``` 
  - We can assign a function to a variable
    ```dart
    var loudify = (msg) => '!!! ${msg.toUpperCase()} !!!';
    assert(loudify('hello') == '!!! HELLO !!!');
    ``` 

- **Anonymous functions**
  - Anonymous function is sometimes called a *lambda* or *closure*.
  - An anonymous function looks similar to a named function— zero or more parameters, separated by commas and optional type annotations, between parentheses.
    ```
    ([[Type] param1[, …]]) {
      codeBlock;
    };
    ```
  
    ```dart
    const list = ['apples', 'bananas', 'oranges'];
    list.forEach((item) {
      print('${list.indexOf(item)}: $item');
    });
    ```
  - We can shorten it using arrow notation if it contains only a single expression or return statement.
    ```dart
    list.forEach(
      (item) => print('${list.indexOf(item)}: $item'));
    ``` 

- **Lexical scope**
  - Dart is a lexically scoped language, which means that the scope of variables is determined statically.
  - We can “follow the curly braces outwards” to see if a variable is in scope.

  ```dart
  bool topLevel = true;

  void main() {
    var insideMain = true;

    void myFunction() {
      var insideFunction = true;

      void nestedFunction() {
        var insideNestedFunction = true;

        assert(topLevel);
        assert(insideMain);
        assert(insideFunction);
        assert(insideNestedFunction);
      }
    }
  }
  ```

- **Lexical closures**
  - A **closure** is a function object that has access to variables in its lexical scope, even when the function is used outside of its original scope.

    ```dart
    /// Returns a function that adds [addBy] to the
    /// function's argument.
    Function makeAdder(int addBy) {
      return (int i) => addBy + i;
    }

    void main() {
      // Create a function that adds 2.
      var add2 = makeAdder(2);

      // Create a function that adds 4.
      var add4 = makeAdder(4);

      assert(add2(3) == 5);
      assert(add4(3) == 7);
    }
    ``` 

## Operators

- When using operators, we create **expressions**. 
- In the operator table, each operator has higher precedence than the operators in the rows that follow it
  | Description | Operator |
  |-|-|
  | unary postfix | `expr++`    `expr--`    `()`    `[]`    `.`    `?.` |
  | unary prefix | `-expr`    `!expr`    `~expr`    `++expr`    `--expr`      `await expr`    |
  | multiplicative | `*`    `/`    `%`  `~/` |
  | additive | `+`    `-` |
  | shift | `<<`    `>>`    `>>>` |
  | bitwise AND | `&` |
  | bitwise XOR | `^` |
  | bitwise OR | `|` |
  | relational | `>=`    `>`    `<=`    `<`    `as`    `is`    `is!` |
  | equality | `==`    `!=`    |
  | logical AND | `&&` |
  | logical OR | `||` |
  | if null | `??` |
  | conditional | `expr1 ? expr2 : expr3` |
  | cascade | `..`    `?..` |
  | assignment | `=`    `*=`    `/=`   `+=`   `-=`   `&=`   `^=`   etc. |

- For operators that take two operands, the leftmost operand determines which method is used.

- *Arithmetic operators*

  | Operator | Meaning |
  |-|-|
  | `+` | Add |
  | `–` | Subtract |
  | `-expr` | Unary minus, also known as negation |
  | `*` | Multiply |
  | `/` | Divide |
  | `~/` | Divide, **returning an integer result** |
  | `%` | modulo | 
  | `++var` | var = var + 1 (expression value is var + 1) |
  | `var++` | var = var + 1 (expression value is var) |
  | `--var` | var = var – 1 (expression value is var – 1) |
  | `var--` | var = var – 1 (expression value is var) |

- *Type test operators*

  | Operator | Meaning |
  |-|-|
  | `as` | Typecast |
  | `is` | True if the object has the specified type |
  | `is!` | True if the object doesn’t have the specified type |

  - The result of `obj is T` is true if `obj` implements the interface specified by `T`. For example, `obj` is `Object?` is always `true`.
  - `as` operator cast an object to a particular type iff the object is of that type.
    ```dart
    //  If employee is null or not a Person, it throws an exception.
    (employee as Person).firstName = 'Bob';


    // It won't throw exception
    if (employee is Person) {
       // Type check
      employee.firstName = 'Bob';
    }
    ``` 

- *Assignment operators*
  - To assign only if the assigned-to variable is null, use the `??=` operator.
    ```dart
    // Assign value to a
    a = value;
    // Assign value to b if b is null; otherwise, b stays the same
    b ??= value;
    ``` 

- *Conditional expressions*
  - `condition ? expr1 : expr2`: If condition is `true`, evaluates `expr1` (and returns its value); otherwise, evaluates and returns the value of `expr2`.
    ```dart
    var visibility = isPublic ? 'public' : 'private';
    ```

  - `expr1 ?? expr2`: If expr1 is non-`null`, returns its value; otherwise, evaluates and returns the value of expr2.
    ```dart
    String playerName(String? name) => name ?? 'Guest';
    ```

    ```dart
    // Slightly longer version uses ?: operator.
    String playerName(String? name) => name != null ? name : 'Guest';

    // Very long version uses if-else statement.
    String playerName(String? name) {
      if (name != null) {
        return name;
      } else {
        return 'Guest';
      }
    }
    ```

- **Cascade notation**
  - Cascades allow you to make a sequence of operations on the same object. This often **saves the step of creating a temporary variable** and allows you to write more fluid code.
  - Cascade must work cascade on a function that **returns an actual object**.

  - `..`
    ```dart
    var paint = Paint()
      ..color = Colors.black
      ..strokeCap = StrokeCap.round
      ..strokeWidth = 5.0;
    ```
    - The constructor, `Paint()`, returns a `Paint` object. The code that follows the cascade notation operates on this object, **ignoring any values that might be returned**.

    - The example is equivalent to:
      ```dart
      var paint = Paint();
      paint.color = Colors.black;
      paint.strokeCap = StrokeCap.round;
      paint.strokeWidth = 5.0;
      ``` 

  - `..?`: use null-shorting cascade if the object that the cascade operates on can be `null`
    ```dart
    querySelector('#confirm') // Get an object.
      ?..text = 'Confirm' // Use its members.
      ..classes.add('important')
      ..onClick.listen((e) => window.alert('Confirmed!'));
    ``` 
    - The code is equivalent to:
      ```dart
      var button = querySelector('#confirm');
      button?.text = 'Confirm';
      button?.classes.add('important');
      button?.onClick.listen((e) => window.alert('Confirmed!'));
      ```

  - We can nest cascade:
    ```dart
    final addressBook = (AddressBookBuilder()
        ..name = 'jenny'
        ..email = 'jenny@example.com'
        ..phone = (PhoneNumberBuilder()
              ..number = '415-555-0100'
              ..label = 'home')
            .build())
      .build();
    ``` 

- Other operators

  | Operator | Name | Meaning |
  |-|-|-|
  | `()` | Function application | Represents a function call |
  | `[]` | List access | Refers to the value at the specified index in the list |
  | `.` | Member access | Refers to a property of an expression |
  | `?.` | Conditional member access | Like ., but the leftmost operand can be null. |


## Control flow statements
- In Dart, control flow includes
  - `if` and `else`
  - `for` loops
  - `while` and `do`-`while` loops
  - `break` and `continue`
  - `switch` and `case`
  - `assert`

- **If and else**
  ```dart
  if (isRaining()) {
    you.bringRainCoat();
  } else if (isSnowing()) {
    you.wearJacket();
  } else {
    car.putTopDown();
  }
  ``` 
  - Unlike JavaScript, conditions **must** use `boolean` values.

- **For loops**
  ```dart
  var message = StringBuffer('Dart is fun');
  for (var i = 0; i < 5; i++) {
    message.write('!');
  }
  ``` 

  - Closures inside of Dart’s for **loops capture the value of the index**, avoiding a common pitfall found in JavaScript.
    ```dart
    var callbacks = [];
    for (var i = 0; i < 2; i++) {
      callbacks.add(() => print(i));
    }
    callbacks.forEach((c) => c());
    ``` 
    - The output is `0` and then `1`, as expected. In contrast, the example would print `2` and then `2` in JavaScript.

  - If the object is an `Iterable`, we can use the `for`-`in`
    ```dart
    for (var candidate in candidates) {
      candidate.interview();
    }
    ``` 

  - `Iterable` classes also have a `forEach()` method.
    ```dart
    var collection = [1, 2, 3];
    collection.forEach(print); // 1 2 3
    ``` 

- **While and do-while**
  - `while` loop evaluates the condition before the loop:
    ```dart
    while (!isDone()) {
      doSomething();
    }
    ``` 
  - `do`-`while` loop evaluates the condition after the loop
    ```dart
    do {
      printLine();
    } while (!atEndOfPage());
    ```

- **Break and continue**
  - Use `break` to stop looping
    ```dart
    while (true) {
      if (shutDownRequested()) break;
      processIncomingRequests();
    }
    ``` 

  - Use `continue` to skip to the next loop iteration:
    ```dart
    for (int i = 0; i < candidates.length; i++) {
      var candidate = candidates[i];
      if (candidate.yearsExperience < 5) {
        continue;
      }
      candidate.interview();
    }
    ```

- **Switch and case**
  - `switch` statements compare value using `==`
    - The compared objects must all be instances of the same class.
    - The class must not override `==`
    - `Enumerated` types also work in switch statements.

  ```dart
  var command = 'OPEN';
  switch (command) {
    case 'CLOSED':
      executeClosed();
      break;
    case 'PENDING':
      executePending();
      break;
    case 'APPROVED':
      executeApproved();
      break;
    case 'DENIED':
      executeDenied();
      break;
    case 'OPEN':
      executeOpen();
      break;
    default:
      executeUnknown();
  }
  ```
  - Each non-empty case clause must ends with a `break`, `continue`, `throw`, or `return`.
  - However, Dart does support empty case clauses, allowing a form of *fall-through*
    ```dart
    var command = 'CLOSED';
    switch (command) {
      case 'CLOSED': // Empty case falls through.
      case 'NOW_CLOSED':
        // Runs for both CLOSED and NOW_CLOSED.
        executeNowClosed();
        break;
    }
    ```

  - It also supports label and continue to do a fallthrough.
    ```dart
    var command = 'CLOSED';
    switch (command) {
      case 'CLOSED':
        executeClosed();
        continue nowClosed;
      // Continues executing at the nowClosed label.

      nowClosed:
      case 'NOW_CLOSED':
        // Runs for both CLOSED and NOW_CLOSED.
        executeNowClosed();
        break;
    }

    ``` 

- Assert
  ```dart
  // Make sure the variable has a non-null value.
  assert(text != null);

  // Make sure the value is less than 100.
  assert(number < 100);

  // Make sure this is an https URL.
  assert(urlString.startsWith('https'));
  ```

  - `assert(condition, optionalMessage);` disrupt normal execution if a boolean condition is `false`.

  - We can attach a message to an assertion by adding a string as the second argument: `assert(urlString.startsWith('https'), 'URL ($urlString) should start with "https".');`

  - **In production**, assertions are ignored, and the arguments to assert aren’t evaluated.