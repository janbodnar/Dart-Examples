# Essential 25 Dart Examples

This collection presents 25 concise Dart code examples that together cover  
approximately 80% of the language's core features. Each example is  
self-contained, runnable in DartPad, and demonstrates idiomatic Dart syntax  
with clear explanations.  

These examples focus on pure Dart language features without Flutter-specific  
APIs, making them valuable for understanding Dart fundamentals whether you're  
building command-line tools, server applications, or Flutter apps. The  
progression moves from basic concepts to more advanced features, building a  
solid foundation in modern Dart programming.  

## Hello there and Basic Print

The foundation of any programming language starts with output and string  
manipulation. This example demonstrates basic print statements and string  
interpolation.  

```dart
// Title: Hello there and Basic Print
// Description: Basic output and string interpolation in Dart

void main() {
  // Simple print statement
  print('Hello there!');
  
  // String interpolation with variables
  var name = 'Dart';
  var version = 3.9;
  print('Welcome to $name $version');
  
  // Expression interpolation
  var x = 10;
  var y = 20;
  print('The sum of $x and $y is ${x + y}');
  
  // Multiline strings
  print('''
    This is a
    multiline string
    in Dart
  ''');
}
```

This example introduces Dart's print function for output, variable  
declarations with the `var` keyword, and string interpolation using the `$`  
syntax. Expression interpolation requires curly braces `${}` for complex  
expressions. Multiline strings use triple quotes for readability.  

## Variables and Type Inference

Dart supports both type inference and explicit type declarations, providing  
flexibility while maintaining strong type safety.  

```dart
// Title: Variables and Type Inference
// Description: Using var for type inference and explicit type declarations

void main() {
  // Type inference with var
  var message = 'Hello there!';
  var count = 42;
  var price = 19.99;
  var isActive = true;
  
  // Explicit type declarations
  String language = 'Dart';
  int year = 2024;
  double temperature = 23.5;
  bool isComplete = false;
  
  print('Message: $message (inferred as String)');
  print('Count: $count (inferred as int)');
  print('Price: \$$price (inferred as double)');
  print('Active: $isActive (inferred as bool)');
  print('Language: $language, Year: $year');
  print('Temperature: ${temperature}°C, Complete: $isComplete');
}
```

The `var` keyword allows Dart to automatically infer types from assigned  
values, reducing verbosity while maintaining type safety. Explicit type  
declarations enhance code readability and are particularly useful for public  
APIs and when the type isn't obvious from context.  

## Final vs Const vs Var

Understanding the differences between `var`, `final`, and `const` is crucial  
for proper variable declaration and immutability in Dart.  

```dart
// Title: Final vs Const vs Var
// Description: Differences between var, final, and const declarations

void main() {
  // var - mutable variable
  var mutableValue = 10;
  print('Initial mutable value: $mutableValue');
  mutableValue = 20;
  print('Modified mutable value: $mutableValue');
  
  // final - runtime constant (immutable reference)
  final currentTime = DateTime.now();
  print('Current time (final): $currentTime');
  // currentTime = DateTime.now(); // Error: cannot reassign
  
  // const - compile-time constant
  const pi = 3.14159;
  const maxUsers = 100;
  print('Pi (const): $pi, Max users: $maxUsers');
  // pi = 3.14; // Error: cannot reassign
  
  // const collections
  const colors = ['red', 'green', 'blue'];
  print('Colors: $colors');
  // colors.add('yellow'); // Error: cannot modify const collection
}
```

The `var` keyword creates mutable variables that can be reassigned. The  
`final` keyword creates runtime constants whose values are set once during  
execution. The `const` keyword creates compile-time constants with values  
known at compile time, offering better performance and immutability  
guarantees.  

## Basic Arithmetic and Operators

Dart provides a comprehensive set of arithmetic, comparison, and logical  
operators for performing calculations and making decisions.  

```dart
// Title: Basic Arithmetic and Operators
// Description: Arithmetic operations, comparisons, and logical operators

void main() {
  // Arithmetic operators
  var a = 10;
  var b = 3;
  print('Addition: $a + $b = ${a + b}');
  print('Subtraction: $a - $b = ${a - b}');
  print('Multiplication: $a * $b = ${a * b}');
  print('Division: $a / $b = ${a / b}');
  print('Integer division: $a ~/ $b = ${a ~/ b}');
  print('Modulo: $a % $b = ${a % b}');
  
  // Comparison operators
  print('\nComparisons:');
  print('$a == $b: ${a == b}');
  print('$a != $b: ${a != b}');
  print('$a > $b: ${a > b}');
  print('$a < $b: ${a < b}');
  
  // Logical operators
  var x = true;
  var y = false;
  print('\nLogical operations:');
  print('$x && $y = ${x && y}');
  print('$x || $y = ${x || y}');
  print('!$x = ${!x}');
}
```

Dart supports standard arithmetic operators including the integer division  
operator `~/` which returns an integer result. Comparison operators return  
boolean values for decision making. Logical operators `&&` (AND), `||` (OR),  
and `!` (NOT) enable complex boolean expressions for control flow.  

## Conditional Statements (if, else, switch)

Conditional statements control program flow based on boolean expressions,  
allowing different code paths based on runtime conditions.  

```dart
// Title: Conditional Statements (if, else, switch)
// Description: Using if-else and switch for conditional logic

void main() {
  // Basic if-else
  var age = 25;
  if (age >= 18) {
    print('Adult');
  } else {
    print('Minor');
  }
  
  // if-else if-else chain
  var score = 85;
  if (score >= 90) {
    print('Grade: A');
  } else if (score >= 80) {
    print('Grade: B');
  } else if (score >= 70) {
    print('Grade: C');
  } else {
    print('Grade: F');
  }
  
  // Switch statement (Dart 3 pattern matching)
  var day = 'Monday';
  switch (day) {
    case 'Monday' || 'Tuesday' || 'Wednesday' || 'Thursday' || 'Friday':
      print('Weekday');
    case 'Saturday' || 'Sunday':
      print('Weekend');
    default:
      print('Invalid day');
  }
  
  // Switch expression (Dart 3)
  var grade = switch (score) {
    >= 90 => 'A',
    >= 80 => 'B',
    >= 70 => 'C',
    >= 60 => 'D',
    _ => 'F'
  };
  print('Grade using switch expression: $grade');
}
```

The if-else statement evaluates conditions sequentially, executing the first  
matching block. Modern Dart 3 introduces pattern matching in switch  
statements with logical OR patterns and switch expressions that return  
values directly, making code more concise and expressive.  

## Loops: for, while, do-while

Loops enable repeated execution of code blocks, essential for processing  
collections and implementing algorithms.  

```dart
// Title: Loops: for, while, do-while
// Description: Various loop constructs for iteration

void main() {
  // Traditional for loop
  print('Traditional for loop:');
  for (var i = 1; i <= 5; i++) {
    print('  $i');
  }
  
  // for-in loop for collections
  print('\nFor-in loop:');
  var fruits = ['apple', 'banana', 'orange'];
  for (var fruit in fruits) {
    print('  $fruit');
  }
  
  // While loop
  print('\nWhile loop:');
  var count = 3;
  while (count > 0) {
    print('  Countdown: $count');
    count--;
  }
  
  // Do-while loop (executes at least once)
  print('\nDo-while loop:');
  var number = 0;
  do {
    print('  Number: $number');
    number++;
  } while (number < 3);
  
  // Break and continue
  print('\nBreak and continue:');
  for (var i = 1; i <= 10; i++) {
    if (i == 5) continue; // Skip 5
    if (i == 8) break;    // Stop at 8
    print('  $i');
  }
}
```

The traditional for loop uses an index variable for counted iterations. The  
for-in loop simplifies iteration over collections. While loops check  
conditions before executing, whereas do-while loops execute at least once  
before checking. Break exits loops early, and continue skips to the next  
iteration.  

## Functions with Optional and Named Parameters

Dart functions support flexible parameter definitions including required,  
optional positional, and named parameters for clean API design.  

```dart
// Title: Functions with Optional and Named Parameters
// Description: Defining functions with flexible parameter options

void main() {
  // Required parameters only
  greet('Alice');
  
  // Optional positional parameters
  describe('Bob', 30);
  describe('Charlie');
  
  // Named parameters
  createUser(name: 'Diana', email: 'diana@example.com');
  createUser(name: 'Eve', age: 28);
  
  // Mixed parameters
  calculate(10, 5, operation: 'multiply');
  calculate(15, 3);
}

// Basic function with required parameter
void greet(String name) {
  print('Hello there, $name!');
}

// Function with optional positional parameter
void describe(String name, [int? age]) {
  if (age != null) {
    print('$name is $age years old');
  } else {
    print('$name (age unknown)');
  }
}

// Function with named parameters
void createUser({required String name, String? email, int? age}) {
  print('User: $name, Email: ${email ?? 'N/A'}, Age: ${age ?? 'N/A'}');
}

// Function with mixed parameters
double calculate(double a, double b, {String operation = 'add'}) {
  var result = switch (operation) {
    'add' => a + b,
    'subtract' => a - b,
    'multiply' => a * b,
    'divide' => a / b,
    _ => 0.0
  };
  print('$a $operation $b = $result');
  return result;
}
```

Functions can have required parameters, optional positional parameters in  
square brackets, and named parameters in curly braces. Named parameters can  
be marked as `required` or have default values. This flexibility enables  
intuitive APIs with self-documenting code.  

## Null Safety and Nullable Types

Dart's sound null safety prevents null reference errors at compile time by  
distinguishing nullable and non-nullable types explicitly.  

```dart
// Title: Null Safety and Nullable Types
// Description: Working with nullable types and null safety operators

void main() {
  // Non-nullable variables (cannot be null)
  String name = 'Alice';
  int age = 30;
  // name = null; // Compilation error
  
  // Nullable variables (can be null)
  String? nickname;
  int? score;
  print('Nickname: $nickname'); // Prints: null
  
  // Null-aware operators
  String displayName = nickname ?? 'Anonymous';
  print('Display name: $displayName');
  
  // Null-aware assignment
  score ??= 0; // Assigns only if null
  print('Score: $score');
  
  // Null assertion operator (use carefully!)
  String? maybeString = 'Hello there!';
  print('Length: ${maybeString!.length}');
  
  // Safe navigation operator
  String? text;
  print('Uppercase: ${text?.toUpperCase()}'); // Prints: null
  
  text = 'dart';
  print('Uppercase: ${text?.toUpperCase()}'); // Prints: DART
  
  // Null-aware cascade
  String? builder;
  builder = builder?.toUpperCase() ?? 'default';
  print('Builder: $builder');
}
```

Non-nullable types guarantee variables always contain values, while nullable  
types use the `?` suffix to allow null. The `??` operator provides default  
values, `??=` assigns only when null, and `?.` safely accesses members. The  
`!` operator asserts non-null but should be used sparingly as it can throw  
runtime exceptions.  

## Type Casting and Type Checking

Dart provides operators for checking types at runtime and safely converting  
between types when necessary.  

```dart
// Title: Type Casting and Type Checking
// Description: Type checking with 'is' and type casting with 'as'

void main() {
  // Type checking with 'is'
  var value = 42;
  
  if (value is int) {
    print('$value is an integer');
  }
  
  if (value is! String) {
    print('$value is not a string');
  }
  
  // Type casting with 'as'
  dynamic dynamicValue = 'Hello there!';
  String stringValue = dynamicValue as String;
  print('Casted string: $stringValue');
  
  // Safe casting with null safety
  Object? obj = 123;
  
  if (obj is int) {
    // Type promotion - obj is treated as int here
    print('Integer value: ${obj + 10}');
  }
  
  // Pattern matching for type checking (Dart 3)
  processValue(42);
  processValue('text');
  processValue(3.14);
  processValue([1, 2, 3]);
}

void processValue(Object value) {
  switch (value) {
    case int n when n > 0:
      print('Positive integer: $n');
    case String s:
      print('String: $s (length: ${s.length})');
    case double d:
      print('Double: $d');
    case List list:
      print('List with ${list.length} elements');
    default:
      print('Unknown type');
  }
}
```

The `is` operator checks if a value is of a specific type, enabling type  
promotion where the compiler treats the variable as that type in the  
conditional block. The `as` operator explicitly casts values but throws an  
exception if the cast fails. Modern Dart 3 pattern matching provides elegant  
type checking with additional conditions.  

## Exception Handling with try-catch-finally

Exception handling provides structured error management, allowing programs to  
recover from errors gracefully rather than crashing.  

```dart
// Title: Exception Handling with try-catch-finally
// Description: Handling errors with try-catch-finally blocks

void main() {
  // Basic try-catch
  try {
    var result = 10 ~/ 0; // Integer division by zero
    print('Result: $result');
  } catch (e) {
    print('Error caught: $e');
  }
  
  // Catching specific exceptions
  try {
    var number = int.parse('not a number');
    print('Parsed: $number');
  } on FormatException catch (e) {
    print('Format error: ${e.message}');
  }
  
  // Multiple catch blocks
  try {
    riskyOperation(null);
  } on ArgumentError catch (e) {
    print('Invalid argument: ${e.message}');
  } on StateError catch (e) {
    print('Invalid state: ${e.message}');
  } catch (e) {
    print('Unexpected error: $e');
  }
  
  // try-catch-finally
  try {
    print('Opening resource...');
    performOperation();
  } catch (e) {
    print('Operation failed: $e');
  } finally {
    print('Cleaning up resources...');
  }
  
  // Rethrowing exceptions
  try {
    validateInput(-5);
  } catch (e) {
    print('Validation failed: $e');
  }
}

void riskyOperation(String? value) {
  if (value == null) {
    throw ArgumentError('Value cannot be null');
  }
}

void performOperation() {
  throw Exception('Operation not implemented');
}

void validateInput(int value) {
  try {
    if (value < 0) {
      throw ArgumentError('Value must be positive');
    }
  } catch (e) {
    print('Validation error caught: $e');
    rethrow; // Propagate to caller
  }
}
```

The try-catch block catches exceptions, preventing program crashes. Specific  
exception types can be caught with `on` clauses for targeted error handling.  
The finally block always executes, making it ideal for cleanup operations.  
The `rethrow` keyword re-throws caught exceptions to callers.  

## Defining Classes and Constructors

Classes are blueprints for creating objects with properties and behaviors,  
forming the foundation of object-oriented programming in Dart.  

```dart
// Title: Defining Classes and Constructors
// Description: Creating classes with various constructor patterns

void main() {
  // Using default constructor
  var person1 = Person('Alice', 30);
  person1.introduce();
  
  // Using named constructor
  var person2 = Person.withDefaults();
  person2.introduce();
  
  // Using factory constructor
  var person3 = Person.fromJson({'name': 'Charlie', 'age': 35});
  person3.introduce();
  
  // Using const constructor
  const point1 = Point(3, 4);
  const point2 = Point(3, 4);
  print('Points are identical: ${identical(point1, point2)}');
}

class Person {
  String name;
  int age;
  
  // Default constructor with positional parameters
  Person(this.name, this.age);
  
  // Named constructor
  Person.withDefaults() : name = 'Guest', age = 0;
  
  // Factory constructor
  factory Person.fromJson(Map<String, dynamic> json) {
    return Person(json['name'] as String, json['age'] as int);
  }
  
  void introduce() {
    print('Hello there! I am $name, $age years old.');
  }
}

class Point {
  final int x;
  final int y;
  
  // Const constructor for immutable objects
  const Point(this.x, this.y);
  
  @override
  String toString() => 'Point($x, $y)';
}
```

Classes define properties and methods for objects. The default constructor  
uses shorthand syntax `this.name` for automatic field assignment. Named  
constructors provide alternative initialization patterns. Factory  
constructors can return cached instances or subtypes. Const constructors  
create compile-time constant objects.  

## Inheritance and Method Overriding

Inheritance allows classes to inherit properties and methods from parent  
classes, promoting code reuse and establishing type relationships.  

```dart
// Title: Inheritance and Method Overriding
// Description: Using inheritance and overriding methods

void main() {
  var dog = Dog('Buddy', 'Golden Retriever');
  dog.describe();
  dog.makeSound();
  dog.fetch();
  
  print('');
  
  var cat = Cat('Whiskers', 'Siamese');
  cat.describe();
  cat.makeSound();
  cat.scratch();
  
  // Polymorphism
  print('\nPolymorphism:');
  List<Animal> animals = [
    Dog('Max', 'Labrador'),
    Cat('Luna', 'Persian'),
    Dog('Rocky', 'Bulldog')
  ];
  
  for (var animal in animals) {
    animal.makeSound();
  }
}

class Animal {
  String name;
  
  Animal(this.name);
  
  void describe() {
    print('This is $name');
  }
  
  void makeSound() {
    print('$name makes a sound');
  }
}

class Dog extends Animal {
  String breed;
  
  Dog(super.name, this.breed);
  
  @override
  void describe() {
    super.describe();
    print('Breed: $breed');
  }
  
  @override
  void makeSound() {
    print('$name barks: Woof! Woof!');
  }
  
  void fetch() {
    print('$name fetches the ball');
  }
}

class Cat extends Animal {
  String breed;
  
  Cat(super.name, this.breed);
  
  @override
  void makeSound() {
    print('$name meows: Meow!');
  }
  
  void scratch() {
    print('$name scratches the furniture');
  }
}
```

Classes extend parent classes using the `extends` keyword, inheriting all  
properties and methods. The `@override` annotation indicates method  
overriding, replacing parent implementations. The `super` keyword accesses  
parent class members. Inheritance enables polymorphism where subtype  
instances can be used wherever parent types are expected.  

## Abstract Classes and Interfaces

Abstract classes define contracts that subclasses must implement, while  
every class implicitly defines an interface that other classes can implement.  

```dart
// Title: Abstract Classes and Interfaces
// Description: Using abstract classes and implicit interfaces

void main() {
  // Abstract classes cannot be instantiated
  // var shape = Shape(); // Error
  
  var circle = Circle(5.0);
  print('Circle area: ${circle.area()}');
  print('Circle perimeter: ${circle.perimeter()}');
  circle.draw();
  
  print('');
  
  var rectangle = Rectangle(4.0, 6.0);
  print('Rectangle area: ${rectangle.area()}');
  print('Rectangle perimeter: ${rectangle.perimeter()}');
  rectangle.draw();
  
  // Using implicit interfaces
  print('\nUsing interfaces:');
  var printer = ConsolePrinter();
  printer.printMessage('Hello there!');
  
  var logger = FileLogger();
  logger.printMessage('Log entry');
}

// Abstract class with abstract and concrete methods
abstract class Shape {
  // Abstract methods (no implementation)
  double area();
  double perimeter();
  
  // Concrete method
  void draw() {
    print('Drawing a shape with area: ${area()}');
  }
}

class Circle extends Shape {
  double radius;
  
  Circle(this.radius);
  
  @override
  double area() => 3.14159 * radius * radius;
  
  @override
  double perimeter() => 2 * 3.14159 * radius;
}

class Rectangle extends Shape {
  double width;
  double height;
  
  Rectangle(this.width, this.height);
  
  @override
  double area() => width * height;
  
  @override
  double perimeter() => 2 * (width + height);
}

// Implicit interface
class Printable {
  void printMessage(String message) {
    print('Printing: $message');
  }
}

// Implementing an implicit interface
class ConsolePrinter implements Printable {
  @override
  void printMessage(String message) {
    print('[Console] $message');
  }
}

class FileLogger implements Printable {
  @override
  void printMessage(String message) {
    print('[File Log] $message');
  }
}
```

Abstract classes use the `abstract` keyword and can contain both abstract  
methods (no implementation) and concrete methods. Subclasses must implement  
all abstract methods. Every Dart class implicitly defines an interface with  
all its members, allowing other classes to implement it using the  
`implements` keyword without inheritance.  

## Mixins and the `with` Keyword

Mixins provide a way to reuse code across multiple class hierarchies without  
traditional inheritance, promoting composition over inheritance.  

```dart
// Title: Mixins and the `with` Keyword
// Description: Using mixins for code reuse across classes

void main() {
  var swimmer = Duck();
  swimmer.swim();
  swimmer.fly();
  swimmer.quack();
  
  print('');
  
  var flyer = Sparrow();
  flyer.fly();
  flyer.chirp();
  
  print('');
  
  var amphibian = Frog();
  amphibian.swim();
  amphibian.hop();
}

// Mixins for reusable behaviors
mixin Swimming {
  void swim() {
    print('Swimming in water');
  }
}

mixin Flying {
  void fly() {
    print('Flying in the sky');
  }
}

mixin Walking {
  void walk() {
    print('Walking on ground');
  }
}

// Base classes
class Bird {
  void breathe() {
    print('Breathing air');
  }
}

class Amphibian {
  void breathe() {
    print('Breathing through skin');
  }
}

// Using mixins with classes
class Duck extends Bird with Swimming, Flying {
  void quack() {
    print('Quack! Quack!');
  }
}

class Sparrow extends Bird with Flying {
  void chirp() {
    print('Chirp! Chirp!');
  }
}

class Frog extends Amphibian with Swimming, Walking {
  void hop() {
    print('Hopping around');
  }
}
```

Mixins are defined with the `mixin` keyword and applied using `with`. A  
class can use multiple mixins, gaining their methods without inheritance.  
Mixins enable horizontal code reuse across unrelated class hierarchies,  
perfect for sharing behaviors like logging, validation, or capabilities.  

## Getters, Setters, and Encapsulation

Getters and setters provide controlled access to object properties,  
implementing encapsulation for data validation and computed values.  

```dart
// Title: Getters, Setters, and Encapsulation
// Description: Using getters and setters for controlled property access

void main() {
  var person = Person('Alice', 30);
  
  // Using getters
  print('Name: ${person.name}');
  print('Age: ${person.age}');
  print('Is adult: ${person.isAdult}');
  print('Description: ${person.description}');
  
  // Using setters
  person.age = 31;
  print('Updated age: ${person.age}');
  
  // Setter with validation
  try {
    person.age = -5; // Invalid age
  } catch (e) {
    print('Error: $e');
  }
  
  print('');
  
  var circle = Circle(5.0);
  print('Radius: ${circle.radius}');
  print('Area: ${circle.area}');
  print('Circumference: ${circle.circumference}');
  
  circle.radius = 7.0;
  print('Updated area: ${circle.area}');
}

class Person {
  String name;
  int _age; // Private field (by convention with _)
  
  Person(this.name, this._age);
  
  // Getter for computed property
  bool get isAdult => _age >= 18;
  
  // Getter for private field
  int get age => _age;
  
  // Setter with validation
  set age(int value) {
    if (value < 0) {
      throw ArgumentError('Age cannot be negative');
    }
    _age = value;
  }
  
  // Getter with string interpolation
  String get description => '$name, $_age years old';
}

class Circle {
  double _radius;
  
  Circle(this._radius);
  
  // Getter
  double get radius => _radius;
  
  // Setter
  set radius(double value) {
    if (value <= 0) {
      throw ArgumentError('Radius must be positive');
    }
    _radius = value;
  }
  
  // Computed properties with getters
  double get area => 3.14159 * _radius * _radius;
  double get circumference => 2 * 3.14159 * _radius;
}
```

Getters use the `get` keyword to define computed properties or controlled  
access to private fields. Setters use the `set` keyword for validation and  
side effects during assignment. Fields prefixed with `_` are private by  
convention. This encapsulation pattern protects internal state while  
providing clean APIs.  

## Static Methods and Class Members

Static members belong to the class itself rather than instances, useful for  
utility functions, constants, and factory patterns.  

```dart
// Title: Static Methods and Class Members
// Description: Using static methods and fields

void main() {
  // Accessing static members without creating instances
  print('Circle area: ${MathUtils.circleArea(5.0)}');
  print('Rectangle area: ${MathUtils.rectangleArea(4.0, 6.0)}');
  print('Pi constant: ${MathUtils.pi}');
  
  print('');
  
  // Using static factory methods
  var config1 = Configuration.development();
  var config2 = Configuration.production();
  
  config1.display();
  config2.display();
  
  print('');
  
  // Static counter example
  print('Created users: ${User.userCount}');
  var user1 = User('Alice');
  var user2 = User('Bob');
  var user3 = User('Charlie');
  print('Created users: ${User.userCount}');
  
  User.resetCounter();
  print('After reset: ${User.userCount}');
}

class MathUtils {
  // Static constant
  static const double pi = 3.14159;
  
  // Static methods
  static double circleArea(double radius) {
    return pi * radius * radius;
  }
  
  static double rectangleArea(double width, double height) {
    return width * height;
  }
  
  static double triangleArea(double base, double height) {
    return 0.5 * base * height;
  }
}

class Configuration {
  String environment;
  String apiUrl;
  bool debugMode;
  
  Configuration(this.environment, this.apiUrl, this.debugMode);
  
  // Static factory methods
  static Configuration development() {
    return Configuration('dev', 'http://localhost:8080', true);
  }
  
  static Configuration production() {
    return Configuration('prod', 'https://api.example.com', false);
  }
  
  void display() {
    print('Environment: $environment');
    print('API URL: $apiUrl');
    print('Debug mode: $debugMode');
  }
}

class User {
  String name;
  static int userCount = 0;
  
  User(this.name) {
    userCount++;
  }
  
  static void resetCounter() {
    userCount = 0;
  }
}
```

Static members are accessed through the class name rather than instances.  
Static methods can't access instance members but are useful for utility  
functions and factory methods. Static fields maintain state across all  
instances, useful for counters, caches, and shared configuration.  

## Lists, Maps, and Sets

Dart provides three main collection types: Lists for ordered sequences, Maps  
for key-value pairs, and Sets for unique unordered elements.  

```dart
// Title: Lists, Maps, and Sets
// Description: Working with Dart's core collection types

void main() {
  // Lists - ordered collections
  var fruits = ['apple', 'banana', 'orange'];
  print('Fruits: $fruits');
  print('First fruit: ${fruits[0]}');
  print('Length: ${fruits.length}');
  
  fruits.add('mango');
  fruits.remove('banana');
  print('Modified fruits: $fruits');
  
  // Maps - key-value pairs
  var person = {
    'name': 'Alice',
    'age': 30,
    'city': 'New York'
  };
  print('\nPerson: $person');
  print('Name: ${person['name']}');
  print('Age: ${person['age']}');
  
  person['email'] = 'alice@example.com';
  person.remove('city');
  print('Modified person: $person');
  
  // Sets - unique elements
  var numbers = {1, 2, 3, 4, 5};
  print('\nNumbers: $numbers');
  
  numbers.add(6);
  numbers.add(3); // Duplicate, won't be added
  print('Modified numbers: $numbers');
  
  var evenNumbers = {2, 4, 6, 8};
  print('Union: ${numbers.union(evenNumbers)}');
  print('Intersection: ${numbers.intersection(evenNumbers)}');
  print('Difference: ${numbers.difference(evenNumbers)}');
  
  // Type-specific collections
  List<int> integers = [1, 2, 3, 4, 5];
  Map<String, double> prices = {'apple': 1.50, 'banana': 0.80};
  Set<String> tags = {'dart', 'flutter', 'mobile'};
  
  print('\nTyped collections:');
  print('Integers: $integers');
  print('Prices: $prices');
  print('Tags: $tags');
}
```

Lists maintain insertion order and allow duplicates, accessed by index. Maps  
store key-value pairs for fast lookups. Sets contain unique elements with no  
guaranteed order. All collections support type parameters for type safety.  
Sets provide mathematical operations like union, intersection, and  
difference.  

## Collection If, For, and Spread Operators

Dart offers powerful collection literals with conditional elements, loops,  
and spread operators for concise collection creation.  

```dart
// Title: Collection If, For, and Spread Operators
// Description: Advanced collection literal features

void main() {
  // Collection if
  var isLoggedIn = true;
  var menu = [
    'Home',
    'About',
    if (isLoggedIn) 'Profile',
    if (isLoggedIn) 'Logout' else 'Login',
  ];
  print('Menu: $menu');
  
  // Collection for
  var numbers = [1, 2, 3];
  var doubled = [
    for (var n in numbers) n * 2
  ];
  print('Doubled: $doubled');
  
  // Nested collection for
  var matrix = [
    for (var i = 1; i <= 3; i++)
      for (var j = 1; j <= 3; j++)
        '$i,$j'
  ];
  print('Matrix: $matrix');
  
  // Spread operator
  var list1 = [1, 2, 3];
  var list2 = [4, 5, 6];
  var combined = [...list1, ...list2];
  print('Combined: $combined');
  
  // Null-aware spread
  List<int>? nullableList;
  var safe = [0, ...?nullableList, 7, 8];
  print('Safe spread: $safe');
  
  // Combining features
  var evenNumbers = [2, 4, 6];
  var oddNumbers = [1, 3, 5];
  var includeEvens = true;
  
  var allNumbers = [
    ...oddNumbers,
    if (includeEvens) ...evenNumbers,
    for (var i = 7; i <= 9; i++) i,
  ];
  print('All numbers: $allNumbers');
  
  // Map collection features
  var baseConfig = {'debug': false, 'timeout': 30};
  var extendedConfig = {
    ...baseConfig,
    'apiUrl': 'https://api.example.com',
    if (isLoggedIn) 'token': 'abc123',
  };
  print('Extended config: $extendedConfig');
}
```

Collection if allows conditional elements based on boolean expressions.  
Collection for generates elements through iteration, supporting nested loops.  
The spread operator `...` unpacks collection elements, while `...?` safely  
handles null collections. These features enable concise, expressive  
collection creation without temporary variables.  

## Iterables and the `forEach` Method

Iterables represent sequences of elements that can be traversed, providing a  
unified interface for various collection types.  

```dart
// Title: Iterables and the `forEach` Method
// Description: Working with iterables and the forEach method

void main() {
  // Basic forEach usage
  var fruits = ['apple', 'banana', 'orange'];
  print('Fruits:');
  fruits.forEach((fruit) {
    print('  - $fruit');
  });
  
  // forEach with index using indexed iteration
  print('\nFruits with index:');
  var index = 0;
  fruits.forEach((fruit) {
    print('  ${index++}. $fruit');
  });
  
  // Using asMap for index access
  print('\nUsing asMap:');
  fruits.asMap().forEach((i, fruit) {
    print('  [$i] $fruit');
  });
  
  // Iterable operations
  var numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
  
  print('\nEven numbers:');
  numbers.where((n) => n % 2 == 0).forEach((n) => print('  $n'));
  
  print('\nFirst 5 numbers:');
  numbers.take(5).forEach(print);
  
  print('\nSkip first 5, take next 3:');
  numbers.skip(5).take(3).forEach(print);
  
  // Iterable properties
  print('\nIterable properties:');
  print('First: ${numbers.first}');
  print('Last: ${numbers.last}');
  print('Length: ${numbers.length}');
  print('Is empty: ${numbers.isEmpty}');
  print('Is not empty: ${numbers.isNotEmpty}');
  
  // Custom iterable processing
  var words = ['dart', 'is', 'awesome'];
  print('\nProcessing words:');
  words.forEach((word) {
    var capitalized = word[0].toUpperCase() + word.substring(1);
    print('  $word -> $capitalized');
  });
}
```

The forEach method executes a function for each element in an iterable.  
Iterables provide methods like where for filtering, take for limiting, and  
skip for skipping elements. Properties like first, last, and length offer  
convenient access to common operations. All collection types implement  
Iterable, providing a consistent interface.  

## Higher-Order Functions and Lambdas

Higher-order functions accept or return functions, while lambdas provide  
concise anonymous function syntax for functional programming patterns.  

```dart
// Title: Higher-Order Functions and Lambdas
// Description: Using functions as first-class values

void main() {
  // Lambda expressions (arrow functions)
  var add = (int a, int b) => a + b;
  var multiply = (int a, int b) => a * b;
  
  print('Add: ${add(5, 3)}');
  print('Multiply: ${multiply(5, 3)}');
  
  // Higher-order functions
  print('\nUsing calculate:');
  print('Result: ${calculate(10, 5, add)}');
  print('Result: ${calculate(10, 5, multiply)}');
  print('Result: ${calculate(10, 5, (a, b) => a - b)}');
  
  // Function that returns a function
  var multiplier = makeMultiplier(3);
  print('\nMultiplier results:');
  print('3 * 4 = ${multiplier(4)}');
  print('3 * 7 = ${multiplier(7)}');
  
  // Passing functions to methods
  var numbers = [1, 2, 3, 4, 5];
  
  print('\nApplying operations:');
  applyOperation(numbers, (n) => n * 2);
  applyOperation(numbers, (n) => n * n);
  
  // Functions as variables
  var operations = [
    (int n) => n + 10,
    (int n) => n * 2,
    (int n) => n - 5,
  ];
  
  print('\nApplying multiple operations to 5:');
  var value = 5;
  for (var operation in operations) {
    value = operation(value);
    print('  Result: $value');
  }
}

// Higher-order function accepting function parameter
int calculate(int a, int b, int Function(int, int) operation) {
  return operation(a, b);
}

// Function returning a function (closure)
Function makeMultiplier(int factor) {
  return (int value) => value * factor;
}

// Function accepting callback
void applyOperation(List<int> numbers, int Function(int) operation) {
  print('  ${numbers.map(operation).toList()}');
}
```

Functions are first-class citizens in Dart, assignable to variables and  
passable as arguments. Lambda expressions use arrow syntax `=>` for concise  
single-expression functions. Higher-order functions enable powerful  
abstractions by accepting functions as parameters or returning them, forming  
the foundation of functional programming patterns.  

## Using `map`, `filter`, and `reduce`

Functional programming operations transform collections using map, filter  
with where, and reduce for aggregation.  

```dart
// Title: Using `map`, `filter`, and `reduce`
// Description: Functional operations on collections

void main() {
  var numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
  
  // Map - transform each element
  var squared = numbers.map((n) => n * n).toList();
  print('Squared: $squared');
  
  var doubled = numbers.map((n) => n * 2).toList();
  print('Doubled: $doubled');
  
  // Filter with where - select elements matching condition
  var evens = numbers.where((n) => n % 2 == 0).toList();
  print('\nEven numbers: $evens');
  
  var greaterThanFive = numbers.where((n) => n > 5).toList();
  print('Greater than 5: $greaterThanFive');
  
  // Reduce - aggregate to single value
  var sum = numbers.reduce((a, b) => a + b);
  print('\nSum: $sum');
  
  var product = numbers.reduce((a, b) => a * b);
  print('Product: $product');
  
  var max = numbers.reduce((a, b) => a > b ? a : b);
  print('Max: $max');
  
  // Fold - reduce with initial value
  var sumWithInitial = numbers.fold(100, (prev, element) => prev + element);
  print('\nFold with initial 100: $sumWithInitial');
  
  // Chaining operations
  var result = numbers
      .where((n) => n % 2 == 0)     // Keep evens
      .map((n) => n * n)             // Square them
      .where((n) => n > 20)          // Keep > 20
      .toList();
  print('\nChained result: $result');
  
  // Practical example
  var words = ['dart', 'flutter', 'mobile', 'web'];
  var lengths = words.map((w) => w.length).toList();
  var totalLength = words.map((w) => w.length).reduce((a, b) => a + b);
  var longest = words.reduce((a, b) => a.length > b.length ? a : b);
  
  print('\nWord analysis:');
  print('Words: $words');
  print('Lengths: $lengths');
  print('Total length: $totalLength');
  print('Longest: $longest');
}
```

The map method transforms each element, returning a new iterable. The where  
method (Dart's filter) selects elements matching a condition. The reduce  
method aggregates elements into a single value using an accumulator. The  
fold method is similar but accepts an initial value. These operations chain  
elegantly for complex transformations.  

## Futures and `async`/`await`

Asynchronous programming with Futures and async/await enables non-blocking  
operations while maintaining readable, sequential-looking code.  

```dart
// Title: Futures and `async`/`await`
// Description: Asynchronous programming with futures

void main() async {
  print('Starting async operations...');
  
  // Basic async/await
  var message = await fetchData();
  print('Received: $message');
  
  // Sequential async operations
  print('\nSequential operations:');
  var data1 = await fetchWithDelay('Data 1', 1);
  print('Got: $data1');
  
  var data2 = await fetchWithDelay('Data 2', 1);
  print('Got: $data2');
  
  // Parallel async operations with Future.wait
  print('\nParallel operations:');
  var startTime = DateTime.now();
  var results = await Future.wait([
    fetchWithDelay('Task A', 2),
    fetchWithDelay('Task B', 2),
    fetchWithDelay('Task C', 2),
  ]);
  var elapsed = DateTime.now().difference(startTime).inSeconds;
  print('Results: $results (took ${elapsed}s)');
  
  // Error handling with async/await
  try {
    var data = await fetchWithError();
    print('Data: $data');
  } catch (e) {
    print('Error caught: $e');
  }
  
  // Future methods
  print('\nFuture chaining:');
  await Future.delayed(Duration(seconds: 1))
      .then((_) => print('Step 1 complete'))
      .then((_) => Future.delayed(Duration(seconds: 1)))
      .then((_) => print('Step 2 complete'));
  
  print('All operations complete!');
}

Future<String> fetchData() async {
  await Future.delayed(Duration(seconds: 1));
  return 'Hello there from async!';
}

Future<String> fetchWithDelay(String data, int seconds) async {
  await Future.delayed(Duration(seconds: seconds));
  return data;
}

Future<String> fetchWithError() async {
  await Future.delayed(Duration(seconds: 1));
  throw Exception('Network error');
}
```

The async keyword marks functions that return Futures, while await pauses  
execution until a Future completes. Sequential awaits execute operations one  
after another, while Future.wait runs multiple Futures concurrently. Error  
handling uses standard try-catch blocks. This approach keeps asynchronous  
code readable and maintainable.  

## Stream Basics and Listening

Streams represent sequences of asynchronous events, perfect for handling  
continuous data flows like user input or network responses.  

```dart
// Title: Stream Basics and Listening
// Description: Working with streams for asynchronous event sequences

void main() async {
  print('Stream examples:\n');
  
  // Creating and listening to a stream
  print('1. Basic stream:');
  var stream = Stream.periodic(Duration(seconds: 1), (count) => count);
  var subscription = stream.take(5).listen(
    (data) => print('  Received: $data'),
    onDone: () => print('  Stream complete!'),
  );
  
  await Future.delayed(Duration(seconds: 6));
  
  // Stream from iterable
  print('\n2. Stream from iterable:');
  var numberStream = Stream.fromIterable([1, 2, 3, 4, 5]);
  await numberStream.forEach((number) {
    print('  Number: $number');
  });
  
  // Stream from futures
  print('\n3. Stream from futures:');
  var futureStream = Stream.fromFutures([
    Future.delayed(Duration(seconds: 1), () => 'First'),
    Future.delayed(Duration(seconds: 1), () => 'Second'),
    Future.delayed(Duration(seconds: 1), () => 'Third'),
  ]);
  
  await for (var value in futureStream) {
    print('  $value');
  }
  
  // Custom async generator stream
  print('\n4. Custom stream:');
  await for (var value in countDown(5)) {
    print('  Countdown: $value');
  }
  
  // Stream transformation
  print('\n5. Stream transformation:');
  var dataStream = Stream.fromIterable([1, 2, 3, 4, 5, 6, 7, 8, 9, 10]);
  var transformed = dataStream
      .where((n) => n % 2 == 0)
      .map((n) => n * n);
  
  await for (var value in transformed) {
    print('  Even squared: $value');
  }
  
  print('\nAll streams processed!');
}

Stream<int> countDown(int start) async* {
  for (var i = start; i > 0; i--) {
    await Future.delayed(Duration(milliseconds: 500));
    yield i;
  }
  yield 0;
}
```

Streams emit multiple values over time, unlike Futures which complete once.  
The listen method subscribes to streams with callbacks. The await for loop  
consumes stream values sequentially. Streams support transformation methods  
like map, where, and take. The async* and yield keywords create custom  
streams.  

## Using `yield*` and Generators

Generator functions use yield to produce sequences lazily, while yield*  
delegates to other generators for powerful composition.  

```dart
// Title: Using `yield*` and Generators
// Description: Generator functions with yield and yield*

void main() async {
  // Synchronous generator (Iterable)
  print('1. Synchronous generator:');
  for (var number in generateNumbers(5)) {
    print('  $number');
  }
  
  print('\n2. Even numbers only:');
  for (var number in generateEvenNumbers(10)) {
    print('  $number');
  }
  
  // Asynchronous generator (Stream)
  print('\n3. Asynchronous generator:');
  await for (var number in generateAsync(5)) {
    print('  $number');
  }
  
  // Using yield* for delegation
  print('\n4. Generator delegation:');
  for (var item in generateAll()) {
    print('  $item');
  }
  
  // Recursive generator
  print('\n5. Recursive generator:');
  for (var number in generateRecursive(5)) {
    print('  $number');
  }
  
  // Fibonacci sequence
  print('\n6. Fibonacci sequence:');
  for (var fib in fibonacci(10)) {
    print('  $fib');
  }
  
  // Async stream with yield*
  print('\n7. Async delegation:');
  await for (var value in generateAsyncAll()) {
    print('  $value');
  }
}

// Synchronous generator returning Iterable
Iterable<int> generateNumbers(int n) sync* {
  for (var i = 1; i <= n; i++) {
    yield i;
  }
}

Iterable<int> generateEvenNumbers(int max) sync* {
  for (var i = 2; i <= max; i += 2) {
    yield i;
  }
}

// Asynchronous generator returning Stream
Stream<int> generateAsync(int n) async* {
  for (var i = 1; i <= n; i++) {
    await Future.delayed(Duration(milliseconds: 500));
    yield i;
  }
}

// Using yield* to delegate to another generator
Iterable<String> generateAll() sync* {
  yield 'Start';
  yield* ['A', 'B', 'C']; // Delegate to iterable
  yield 'Middle';
  yield* generateLabels(3); // Delegate to generator
  yield 'End';
}

Iterable<String> generateLabels(int count) sync* {
  for (var i = 1; i <= count; i++) {
    yield 'Label $i';
  }
}

// Recursive generator
Iterable<int> generateRecursive(int n) sync* {
  if (n > 0) {
    yield n;
    yield* generateRecursive(n - 1);
  }
}

// Fibonacci generator
Iterable<int> fibonacci(int n) sync* {
  var a = 0, b = 1;
  for (var i = 0; i < n; i++) {
    yield a;
    var temp = a + b;
    a = b;
    b = temp;
  }
}

// Async generator delegation
Stream<String> generateAsyncAll() async* {
  yield 'Async Start';
  yield* Stream.fromIterable(['X', 'Y', 'Z']);
  yield 'Async End';
}
```

Generators use sync* for synchronous sequences (Iterable) and async* for  
asynchronous sequences (Stream). The yield keyword produces individual  
values lazily, computing them only when requested. The yield* keyword  
delegates to another generator or iterable/stream, enabling composition.  
Generators are memory-efficient for large or infinite sequences.  

## Extension Methods and Custom Operators

Extension methods add functionality to existing types without modification,  
while operator overloading provides intuitive syntax for custom types.  

```dart
// Title: Extension Methods and Custom Operators
// Description: Extending types and overloading operators

void main() {
  // Using string extensions
  var text = 'hello there';
  print('Original: $text');
  print('Capitalized: ${text.capitalize()}');
  print('Reversed: ${text.reversed}');
  print('Is palindrome: ${text.isPalindrome()}');
  
  var palindrome = 'racecar';
  print('\n"$palindrome" is palindrome: ${palindrome.isPalindrome()}');
  
  // Using int extensions
  var number = 5;
  print('\n$number factorial: ${number.factorial()}');
  print('$number squared: ${number.squared}');
  print('$number is even: ${number.isEvenNumber}');
  
  // Using list extensions
  var numbers = [1, 2, 3, 4, 5];
  print('\nList: $numbers');
  print('Sum: ${numbers.sum()}');
  print('Average: ${numbers.average()}');
  
  // Custom operators
  print('\nVector operations:');
  var v1 = Vector(3, 4);
  var v2 = Vector(1, 2);
  
  print('v1: $v1');
  print('v2: $v2');
  print('v1 + v2: ${v1 + v2}');
  print('v1 - v2: ${v1 - v2}');
  print('v1 * 2: ${v1 * 2}');
  print('v1 == Vector(3, 4): ${v1 == Vector(3, 4)}');
  
  // Money type with operators
  print('\nMoney operations:');
  var price1 = Money(50);
  var price2 = Money(30);
  
  print('Price 1: $price1');
  print('Price 2: $price2');
  print('Total: ${price1 + price2}');
  print('Difference: ${price1 - price2}');
  print('Price 1 > Price 2: ${price1 > price2}');
}

// Extension methods on String
extension StringExtensions on String {
  String capitalize() {
    if (isEmpty) return this;
    return this[0].toUpperCase() + substring(1);
  }
  
  String get reversed => split('').reversed.join('');
  
  bool isPalindrome() {
    var cleaned = toLowerCase().replaceAll(' ', '');
    return cleaned == cleaned.split('').reversed.join('');
  }
}

// Extension methods on int
extension IntExtensions on int {
  int squared() => this * this;
  
  int get squared => this * this;
  
  bool get isEvenNumber => this % 2 == 0;
  
  int factorial() {
    if (this <= 1) return 1;
    return this * (this - 1).factorial();
  }
}

// Extension methods on List<int>
extension ListIntExtensions on List<int> {
  int sum() => isEmpty ? 0 : reduce((a, b) => a + b);
  
  double average() => isEmpty ? 0 : sum() / length;
}

// Custom class with operator overloading
class Vector {
  final double x;
  final double y;
  
  Vector(this.x, this.y);
  
  // Arithmetic operators
  Vector operator +(Vector other) => Vector(x + other.x, y + other.y);
  Vector operator -(Vector other) => Vector(x - other.x, y - other.y);
  Vector operator *(double scalar) => Vector(x * scalar, y * scalar);
  
  // Equality operator
  @override
  bool operator ==(Object other) =>
      other is Vector && x == other.x && y == other.y;
  
  @override
  int get hashCode => Object.hash(x, y);
  
  @override
  String toString() => 'Vector($x, $y)';
}

// Money class with comparison operators
class Money implements Comparable<Money> {
  final int cents;
  
  Money(this.cents);
  
  Money operator +(Money other) => Money(cents + other.cents);
  Money operator -(Money other) => Money(cents - other.cents);
  
  bool operator >(Money other) => cents > other.cents;
  bool operator <(Money other) => cents < other.cents;
  
  @override
  int compareTo(Money other) => cents.compareTo(other.cents);
  
  @override
  String toString() => '\$${(cents / 100).toStringAsFixed(2)}';
}
```

Extension methods use the `extension` keyword to add methods to existing  
types without modifying them or using inheritance. They provide clean syntax  
for domain-specific operations. Operator overloading enables natural syntax  
for custom types by implementing operator methods. Common operators include  
arithmetic (+, -, *, /), comparison (==, <, >), and indexing ([]).  
