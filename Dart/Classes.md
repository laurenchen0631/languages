## Classes
- Dart is an **object-oriented** language with classes and **mixin-based** inheritance.
  - Mixin-based inheritance means that although every class has exactly one superclass, **a class body can be reused in multiple class hierarchies**
- Dart provides `extension` methods to add functionality to a class.

- **Class members**
  - Class members consists of functions and data, *methods* and *instance variables*, respectively)
  - The method (`.` or `?.`) has access to that object’s functions and data.
    ```dart
    var p = Point(2, 2);

    // Get the value of y.
    assert(p.y == 2);

    // Invoke distanceTo() on p.
    double distance = p.distanceTo(Point(4, 4));
    ``` 

- **Constructors**
  - We create an object using a constructor.
  - Constructor names can be either `ClassName` or `ClassName.identifier`
    ```dart
    var p1 = Point(2, 2);
    var p2 = Point.fromJson({'x': 1, 'y': 2});
    ``` 
  - `new` keyword became optional in Dart 2.
    ```dart
    var p1 = new Point(2, 2);
    var p2 = new Point.fromJson({'x': 1, 'y': 2});
    ``` 

  - Some classes provide `constant constructors` to create a compile-time constant 
    - Constructing two identical compile-time constants results in a single, canonical instance.
    ```dart
    var p = const ImmutablePoint(2, 2);

    var a = const ImmutablePoint(1, 1);
    var b = const ImmutablePoint(1, 1);

    assert(identical(a, b)); // They are the same instance!
    ``` 

    - Within a *constant context*, you can omit the const before a constructor in Dart 2.
      ```dart
      const pointAndLine = const {
        'point': const [const ImmutablePoint(0, 0)],
        'line': const [const ImmutablePoint(1, 10), const ImmutablePoint(-2, 11)],
      };

      // Only one const, which establishes the constant context.
      const pointAndLines = {
        'point': [ImmutablePoint(0, 0)],
        'line': [ImmutablePoint(1, 10), ImmutablePoint(-2, 11)],
      };
      ``` 

- We can  get an object’s type at runtime using `Object` property `runtimeType` that is a `Type` object.
  ```dart
  print('The type of a is ${a.runtimeType}');
  ``` 

- Dart class can be called like a function by implementing the `call()` method.
  ```dart
  class WannabeFunction {
    String call(String a, String b, String c) => '$a $b $c!';
  }

  var wf = WannabeFunction();
  var out = wf('Hi', 'there,', 'gang');

  void main() => print(out);
  ```


## Instance variables

```dart
class Point {
  double? x; // Declare instance variable x, initially null.
  double? y; // Declare y, initially null.
  double z = 0; // Declare z, initially 0.
}
```

- All uninitialized instance variables have the value `null`.
- All instance variables generate an implicit `getter` method.
- Non-`final` instance variables and `late final` instance variables without initializers generate an implicit `setter` method.
  ```dart
  void main() {
    var point = Point();
    point.x = 4; // Use the setter method for x.
    assert(point.x == 4); // Use the getter method for x.
    assert(point.y == null); // Values default to null.
  }
  ```

- Initialize `final`, non-`late` instance variables at **declaration**, **constructor**, or **constructor’s initializer list**.
  ```dart
  class ProfileMark {
    final String name;
    final DateTime start = DateTime.now();

    ProfileMark(this.name);
    ProfileMark.unnamed() : name = '';
  }
  ```

- If you initialize a non-late instance variable, the value is set when the instance is created before the constructor and its initializer list execute.


## Constructors
- Constructor is a function with the same name as its class ((plus, optionally, an additional identifie)
  ```dart
  class Point {
    double x = 0;
    double y = 0;

    Point(double x, double y) {
      // There's a better way to do this, stay tuned.
      this.x = x;
      this.y = y;
    }
  }
  ``` 

- The `this` refers to the current instance, but it can be omitted if there is no name conflict.
- Dart has syntactic sugar to assign a constructor argument to an instance variable.
  ```dart
  class Point {
    double x = 0;
    double y = 0;

    // Syntactic sugar for setting x and y
    // before the constructor body runs.
    Point(this.x, this.y);
  }
  ``` 

- *Default constructors*: If you don’t declare a constructor, a default constructor is provided with no arguments.
- *Constructors aren’t inherited*: Subclasses don’t inherit constructors from their superclass and has only the default constructor.d
  - By default, a constructor in a subclass calls the superclass’s unnamed, no-argument constructor.
  - In summary, the order of execution is as follows:
     1. initializer list
     1. superclass’s no-arg constructor
     2. main class’s no-arg constructor
  - If the superclass doesn’t have a default constructor, then we must manually call one.
    ```dart
    class Person {
      String? firstName;

      Person.fromJson(Map data) {
        print('in Person');
      }
    }

    class Employee extends Person {
      // Person does not have a default constructor;
      // you must call super.fromJson(data).
      Employee.fromJson(Map data) : super.fromJson(data) {
        print('in Employee');
      }
    }
    ``` 
  - Because the arguments to the superclass constructor are evaluated before invoking the constructor, an argument can be an expression such as a function call.
    ```dart
    class Employee extends Person {
      Employee() : super.fromJson(fetchDefaultData());
      // ···
    }
    ```


- *Named constructors*: implement multiple constructors for a class or to provide extra clarity:
  ```dart
  const double xOrigin = 0;
  const double yOrigin = 0;

  class Point {
    double x = 0;
    double y = 0;

    Point(this.x, this.y);

    // Named constructor
    Point.origin()
        : x = xOrigin,
          y = yOrigin;
  }
  ```

- *Initializer list*
  ```dart
  // Initializer list sets instance variables before
  // the constructor body runs.
  Point.fromJson(Map<String, double> json)
      : x = json['x']!,
        y = json['y']! {
    print('In Point.fromJson(): ($x, $y)');
  }
  ``` 

  - During development, we can validate inputs by using assert in the initializer list.
    ```dart
    Point.withAssert(this.x, this.y) : assert(x >= 0) {
      print('In Point.withAssert(): ($x, $y)');
    }
    ``` 

  - Initializer lists are handy when **setting up `final` fields**
    ```dart
    import 'dart:math';

    class Point {
      final double x;
      final double y;
      final double distanceFromOrigin;

      Point(double x, double y)
          : x = x,
            y = y,
            distanceFromOrigin = sqrt(x * x + y * y);
    }

    void main() {
      var p = Point(2, 3);
      print(p.distanceFromOrigin);
    }
    ``` 

- *Redirecting constructors*

  ```dart
  class Point {
    double x, y;

    // The main constructor for this class.
    Point(this.x, this.y);

    // Delegates to the main constructor.
    Point.alongXAxis(double x) : this(x, 0);
  }
  ```

- *Constant constructors*
  - To do this, define a `const` constructor and make sure that **all instance** variables are `final`.

  ```dart
  class ImmutablePoint {
    static const ImmutablePoint origin = ImmutablePoint(0, 0);

    final double x, y;

    const ImmutablePoint(this.x, this.y);
  }
  ```

- *Factory constructors*
  - Use the `factory` keyword when implementing a constructor that doesn’t always create **a new instance** of its class
    - A factory constructor might return an instance from a cache
    - A factory constructor might return an instance of a subtype
    - Another use case for factory constructors is **initializing a final variable** using logic that **can’t be handled in the initializer list**.

  - Factory constructors have no access to `this`.

  ```dart
  class Logger {
    final String name;
    bool mute = false;

    // _cache is library-private, thanks to
    // the _ in front of its name.
    static final Map<String, Logger> _cache =
        <String, Logger>{};

    factory Logger(String name) {
      return _cache.putIfAbsent(
          name, () => Logger._internal(name));
    }

    factory Logger.fromJson(Map<String, Object> json) {
      return Logger(json['name'].toString());
    }

    Logger._internal(this.name);

    void log(String msg) {
      if (!mute) print(msg);
    }
  }

  var logger = Logger('UI');
  logger.log('Button clicked');

  var logMap = {'name': 'UI'};
  var loggerJson = Logger.fromJson(logMap);
  ```

## Methods
- Methods are functions that provide behavior for an object.

- *Instance methods*
  - Instance methods on objects can access instance variables and `this`.
  ```dart
  import 'dart:math';

  class Point {
    double x = 0;
    double y = 0;

    Point(this.x, this.y);

    double distanceTo(Point other) {
      var dx = x - other.x;
      var dy = y - other.y;
      return sqrt(dx * dx + dy * dy);
    }
  }
  ``` 

- *Operators*
  - Dart allows you to define operators with the following names
    - `<`
    - `+`
    - `|`
    - `[]`
    - `>`
    - `/`
    - `^`
    - `[]=`
    - `<=`
    - `~/`
    - `&`
    - `~`
    - `>=`
    - `*`
    - `<<`
    - `==`
    -	`%`
    - `>>`
  - Some operators, like `!=`, aren’t in the list of names since they are just syntactic sugar.
    - `e1 != e2` is syntactic sugar for `!(e1 == e2)`.

  - An operator declaration is identified using the built-in `identifier` operator.
    ```dart
    class Vector {
      final int x, y;

      Vector(this.x, this.y);

      Vector operator +(Vector v) => Vector(x + v.x, y + v.y);
      Vector operator -(Vector v) => Vector(x - v.x, y - v.y);

      // Operator == and hashCode not shown.
      // ···
    }

    void main() {
      final v = Vector(2, 3);
      final w = Vector(2, 2);

      assert(v + w == Vector(4, 5));
      assert(v - w == Vector(0, 1));
    }
    ``` 

- *Getters and setters*
  - Getters and setters are special methods that provide read and write access to an object’s properties.
  - You can create additional properties by implementing getters and setters, using the `get` and `set` keywords

  ```dart
  class Rectangle {
    double left, top, width, height;

    Rectangle(this.left, this.top, this.width, this.height);

    // Define two calculated properties: right and bottom.
    double get right => left + width;
    set right(double value) => left = value - width;
    double get bottom => top + height;
    set bottom(double value) => top = value - height;
  }

  void main() {
    var rect = Rectangle(3, 4, 20, 15);
    assert(rect.left == 3);
    rect.right = 12;
    assert(rect.left == -8);
  }
  ```

- *Abstract methods*
  - We can define an interface but leaving its implementation up to other classes, which is called abstract methods.
  - Instance, getter, and setter methods can be abstract.
  - To make a method abstract, use `;` instead of a method body.


  ```dart
  abstract class Doer {
    // Define instance variables and methods...

    void doSomething(); // Define an abstract method.
  }

  class EffectiveDoer extends Doer {
    void doSomething() {
      // Provide an implementation, so the method is not abstract here...
    }
  }
  ```

## Interface
- Use the `abstract` modifier to define an abstract class — a class that **can’t be instantiated**.
  ```dart
  // This class is declared abstract and thus
  // can't be instantiated.
  abstract class AbstractContainer {
    // Define constructors, fields, methods...

    void updateChildren(); // Abstract method.
  }
  ```

  - Abstract classes are useful for defining interfaces.
  - If you want your abstract class to appear to be instantiable, define a factory constructor.

- *Implicit interfaces*
  - Every class implicitly defines an interface containing **all** the instance members.
    - If you want to create a class A that supports class B’s API without inheriting B’s implementation, class A should implement the B interface.

  - A class implements one or more interfaces by declaring them in an `implements` clause
    ```dart
    // A person. The implicit interface contains greet().
    class Person {
      // In the interface, but visible only in this library.
      final String _name;

      // Not in the interface, since this is a constructor.
      Person(this._name);

      // In the interface.
      String greet(String who) => 'Hello, $who. I am $_name.';
    }

    // An implementation of the Person interface.
    class Impostor implements Person {
      String get _name => '';

      String greet(String who) => 'Hi $who. Do you know who I am?';
    }

    String greetBob(Person person) => person.greet('Bob');

    void main() {
      print(greetBob(Person('Kathy')));
      print(greetBob(Impostor()));
    }
    ``` 

    ```dart
    class Point implements Comparable, Location {...}
    ```

## Inheritance
- Use `extends` to create a subclass, and `super` to refer to the superclass.
  ```dart
  class Television {
    void turnOn() {
      _illuminateDisplay();
      _activateIrSensor();
    }
    // ···
  }

  class SmartTelevision extends Television {
    void turnOn() {
      super.turnOn();
      _bootNetworkInterface();
      _initializeMemory();
      _upgradeApps();
    }
    // ···
  }
  ``` 

- Use `@override` annotation to override a member

  ```dart
  class SmartTelevision extends Television {
    @override
    void turnOn() {...}
    // ···
  }
  ``` 

  - To narrow the type of a method parameter or instance variable, you can use the `covariant` keyword.
    - `covariant` tell the analyzer that we are tightening a type intentionally.
    - This **removes the static error** and instead checks for an invalid argument type at runtime.

    ```dart
    class Animal {
      void chase(Animal x) { ... }
    }

    class Mouse extends Animal { ... }

    class Cat extends Animal {
      @override
      void chase(covariant Mouse x) { ... }
    }
    ```

- Override `noSuchMethod()` to detect or react whenever code attempts to use a non-existent method.
  ```dart
  class A {
    // Unless you override noSuchMethod, using a
    // non-existent member results in a NoSuchMethodError.
    @override
    void noSuchMethod(Invocation invocation) {
      print('You tried to use a non-existent member: '
          '${invocation.memberName}');
    }
  }
  ```

## Extension
- When you’re using someone else’s API, it’s often impractical or impossible to change the API. But you might still want to add some functionality.
- `Extension` methods are a way to add functionality to existing libraries. 
- Extensions can define not just methods, but also other members such as getter, setters, and operators. 

```dart
extension NumberParsing on String {
  int parseInt() {
    return int.parse(this);
  }
  // ···
}

print(int.parse('42'));
print('42'.parseInt());
```

- To create a local extension, we can either
  - Omit the extension name
  - Give it a name that starts with `_`

- If an extension member conflicts, then we have a few options.
  1. Use `show` or `hide` to limit the exposed API
     ```dart
     // Defines the String extension method parseInt().
     import 'string_apis.dart';

     // Also defines parseInt(), but hiding NumberParsing2
     // hides that extension method.
     import 'string_apis_2.dart' hide NumberParsing2;

     // ···
     // Uses the parseInt() defined in 'string_apis.dart'.
     print('42'.parseInt());
     ``` 
  2. Apply the extension explicitly
     ```dart
     // Both libraries define extensions on String that contain parseInt(),
     // and the extensions have different names.
     import 'string_apis.dart'; // Contains NumberParsing extension.
     import 'string_apis_2.dart'; // Contains NumberParsing2 extension.
 
     // ···
     // print('42'.parseInt()); // Doesn't work.
     print(NumberParsing('42').parseInt());
     print(NumberParsing2('42').parseInt());
     ``` 

- Extensions can have generic type parameters.
  ```dart
  extension MyFancyList<T> on List<T> {
    int get doubleLength => length * 2;
    List<T> operator -() => reversed.toList();
    List<List<T>> split(int at) => [sublist(0, at), sublist(at)];
  }
  ``` 

## Enumerated types
- Enumerated types, often called *enumerations* or *enums*, are a special kind of class used to represent a fixed number of constant values.

- Declare an enumerated type using the `enum` keyword:
  ```dart
  enum Color { red, green, blue }
  ```

- Each value in an `enum` has an index
  ```dart
  assert(Color.red.index == 0);
  assert(Color.green.index == 1);
  assert(Color.blue.index == 2);
  ``` 

- To get all of the values in the enum, use the `values` constant.
  ```dart
  List<Color> colors = Color.values;
  assert(colors[2] == Color.blue);
  ``` 

- Enums in `switch` statements ’ll get a warning if we don’t handle all of the values
  ```dart
  var aColor = Color.blue;

  switch (aColor) {
    case Color.red:
      print('Red as roses!');
      break;
    case Color.green:
      print('Green as grass!');
      break;
    default: // Without this, you see a WARNING.
      print(aColor); // 'Color.blue'
  }
  ``` 

- Enumerated types have the following limits:
  - We can’t subclass, mix in, or implement an enum.
  - We can’t explicitly instantiate an enum.


## Mixins
- Mixins are a way of **reusing a class’s code** in multiple class hierarchies.
- To **use** a mixin, use the `with` keyword
  ```dart
  class Musician extends Performer with Musical {
    // ···
  }

  class Maestro extends Person
      with Musical, Aggressive, Demented {
    Maestro(String maestroName) {
      name = maestroName;
      canConduct = true;
    }
  }

  ```

- To implement a mixin, **create a class that extends Object** and declares no constructors.
  - If `mixin` to be usable as a regular class, use the `mixin` keyword instead of class.
    ```dart
    mixin Musical {
      bool canPlayPiano = false;
      bool canCompose = false;
      bool canConduct = false;

      void entertainMe() {
        if (canPlayPiano) {
          print('Playing piano');
        } else if (canConduct) {
          print('Waving hands');
        } else {
          print('Humming to self');
        }
      }
    }
    ```

- We can restrict the types that can use a mixin by using the `on` keyword:
  ```dart
  class Musician {
    // ...
  }

  mixin MusicalPerformer on Musician {
    // ...
  }

  class SingerDancer extends Musician with MusicalPerformer {
    // ...
  }
  ``` 

## Static
- *Static variables*
  ```dart
  class Queue {
    static const initialCapacity = 16;
    // ···
  }

  void main() {
    assert(Queue.initialCapacity == 16);
  }
  ```
  - static variables are useful for class-wide state and constants.
  - Static variables **aren’t** initialized until they’re used.

- *Static methods*
  - Static methods don’t operate on an instance, and thus **don’t** have access to `this`.
  - Static methods have access to static variables.
  - We can use static methods as compile-time constants.

  ```dart
  import 'dart:math';

  class Point {
    double x, y;
    Point(this.x, this.y);

    static double distanceBetween(Point a, Point b) {
      var dx = a.x - b.x;
      var dy = a.y - b.y;
      return sqrt(dx * dx + dy * dy);
    }
  }

  void main() {
    var a = Point(2, 2);
    var b = Point(4, 4);
    var distance = Point.distanceBetween(a, b);
    assert(2.8 < distance && distance < 2.9);
    print(distance);
  }
  ```

## Generics
- The `<…>` notation marks as a *generic* (or parameterized) type.
- By convention, most type variables have single-letter names, such as `E`, `T`, `S`, `K`, and `V`.

- Why use generics?
  - Properly specifying generic types results in better generated code.
  - Reduce code duplication.

  ```dart
  abstract class ObjectCache {
    Object getByKey(String key);
    void setByKey(String key, Object value);
  }

  abstract class StringCache {
    String getByKey(String key);
    void setByKey(String key, String value);
  }

  abstract class Cache<T> {
    T getByKey(String key);
    void setByKey(String key, T value);
  }
  ```

- List, set, and map literals can be parameterized.
  ```dart
  var names = <String>['Seth', 'Kathy', 'Lars'];
  var uniqueNames = <String>{'Seth', 'Kathy', 'Lars'};
  var pages = <String, String>{
    'index.html': 'Homepage',
    'robots.txt': 'Hints for web robots',
    'humans.txt': 'We are people, not machines'
  };
  ``` 

- Parameterized types constructors: `var views = Map<int, View>();`
- Dart generic types are *reified*, which means that they carry their **type information around at runtime**.
  ```dart
  var names = <String>[];
  names.addAll(['Seth', 'Kathy', 'Lars']);
  print(names is List<String>); // true
  ``` 
  - In contrast, generics in Java use *erasure*, which means that generic type parameters are removed at runtime. 
- **Restricting** the parameterized type: use `extends`
  ```dart
  class Foo<T extends SomeBaseClass> {
    // Implementation goes here...
    String toString() => "Instance of 'Foo<$T>'";
  }

  class Extender extends SomeBaseClass {...}

  var someBaseClassFoo = Foo<SomeBaseClass>();
  var extenderFoo = Foo<Extender>();

  // It’s also OK to specify no generic argument:
  var foo = Foo();

  // Specifying any non-SomeBaseClass type results in an error
  var foo = Foo<Object>();
  ``` 

- Generic methods: 
  ```dart
  T first<T>(List<T> ts) {
    // Do some initial work or error checking, then...
    T tmp = ts[0];
    // Do some additional checking or processing...
    return tmp;
  }
  ```
  - In the function’s return type (`T`).
  - In the type of an argument (`List<T>`).
  - In the type of a local variable (`T tmp`).


