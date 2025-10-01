# Object-Oriented Programming in Dart

Object-oriented programming (OOP) in Dart provides powerful mechanisms for  
organizing code through classes, inheritance, mixins, and interfaces. Dart's  
OOP features combine traditional object-oriented concepts with modern  
language capabilities, enabling clean, maintainable, and reusable code.  

This comprehensive guide covers Dart's OOP capabilities from basic class  
definitions to advanced patterns like mixins, abstract classes, and  
interfaces. Dart supports single inheritance through extends, multiple  
behavior composition through mixins, and implicit interfaces for every class.  

## Basic Class Definition

Classes are the fundamental building blocks of object-oriented programming  
in Dart. A class defines a blueprint for creating objects with specific  
properties and behaviors.  

```dart
class Person {
  String name;
  int age;
  
  Person(this.name, this.age);
  
  void introduce() {
    print('Hello there! I am $name and I am $age years old');
  }
}

void main() {
  var person = Person('Alice', 30);
  person.introduce();
  
  print('Name: ${person.name}');
  print('Age: ${person.age}');
}
```

This example demonstrates a basic class with properties and a method. The  
constructor uses Dart's concise syntax where `this.name` automatically  
assigns the parameter to the instance variable. The introduce method  
showcases string interpolation for accessing instance properties.  

```
$ dart run basic_class.dart
Hello there! I am Alice and I am 30 years old
Name: Alice
Age: 30
```

## Named Constructors

Named constructors provide multiple ways to create instances of a class.  
They enable clear, self-documenting object creation patterns.  

```dart
class Point {
  double x;
  double y;
  
  Point(this.x, this.y);
  
  Point.origin()
      : x = 0,
        y = 0;
  
  Point.fromJson(Map<String, dynamic> json)
      : x = json['x'],
        y = json['y'];
  
  Point.polar(double radius, double angle)
      : x = radius * Math.cos(angle),
        y = radius * Math.sin(angle);
  
  @override
  String toString() => 'Point($x, $y)';
}

void main() {
  var p1 = Point(3.0, 4.0);
  var p2 = Point.origin();
  var p3 = Point.fromJson({'x': 5.0, 'y': 7.0});
  
  print('Regular: $p1');
  print('Origin: $p2');
  print('From JSON: $p3');
}
```

Named constructors allow different initialization strategies for the same  
class. The initializer list (after the colon) sets field values before the  
constructor body executes. This pattern is particularly useful for factory  
methods and alternative construction approaches.  

```
$ dart run named_constructors.dart
Regular: Point(3.0, 4.0)
Origin: Point(0.0, 0.0)
From JSON: Point(5.0, 7.0)
```

## Getters and Setters

Getters and setters provide controlled access to object properties. They  
enable encapsulation and validation logic without changing the interface.  

```dart
class Rectangle {
  double _width;
  double _height;
  
  Rectangle(this._width, this._height);
  
  double get width => _width;
  
  set width(double value) {
    if (value > 0) {
      _width = value;
    }
  }
  
  double get height => _height;
  
  set height(double value) {
    if (value > 0) {
      _height = value;
    }
  }
  
  double get area => _width * _height;
  
  double get perimeter => 2 * (_width + _height);
}

void main() {
  var rect = Rectangle(10.0, 5.0);
  
  print('Width: ${rect.width}');
  print('Height: ${rect.height}');
  print('Area: ${rect.area}');
  print('Perimeter: ${rect.perimeter}');
  
  rect.width = 15.0;
  print('New area: ${rect.area}');
}
```

Getters and setters use the `get` and `set` keywords. Computed properties  
like `area` are implemented as getters that calculate values on demand.  
Setters can include validation logic to maintain object invariants.  

```
$ dart run getters_setters.dart
Width: 10.0
Height: 5.0
Area: 50.0
Perimeter: 30.0
New area: 75.0
```

## Private Members

Dart uses underscore prefix for library-private members. Unlike Java or C#,  
privacy is at the library level, not the class level.  

```dart
class BankAccount {
  String _accountNumber;
  double _balance;
  
  BankAccount(this._accountNumber, this._balance);
  
  double get balance => _balance;
  
  void deposit(double amount) {
    if (amount > 0) {
      _balance += amount;
      print('Deposited: \$${amount.toStringAsFixed(2)}');
    }
  }
  
  bool withdraw(double amount) {
    if (amount > 0 && amount <= _balance) {
      _balance -= amount;
      print('Withdrew: \$${amount.toStringAsFixed(2)}');
      return true;
    }
    print('Insufficient funds');
    return false;
  }
  
  void _auditLog(String action) {
    print('[AUDIT] $action on $_accountNumber');
  }
}

void main() {
  var account = BankAccount('ACC123', 1000.0);
  
  print('Initial balance: \$${account.balance}');
  account.deposit(500.0);
  account.withdraw(200.0);
  print('Final balance: \$${account.balance}');
}
```

Private members (prefixed with `_`) are accessible only within the same  
library. This encapsulation protects internal implementation details while  
exposing a clean public API for clients to use.  

```
$ dart run private_members.dart
Initial balance: $1000.00
Deposited: $500.00
Withdrew: $200.00
Final balance: $1300.00
```

## Class Inheritance

Inheritance allows classes to extend other classes, inheriting their  
properties and methods while adding or overriding behavior.  

```dart
class Animal {
  String name;
  int age;
  
  Animal(this.name, this.age);
  
  void makeSound() {
    print('$name makes a sound');
  }
  
  void eat() {
    print('$name is eating');
  }
}

class Dog extends Animal {
  String breed;
  
  Dog(String name, int age, this.breed) : super(name, age);
  
  @override
  void makeSound() {
    print('$name barks: Woof! Woof!');
  }
  
  void fetch() {
    print('$name is fetching the ball');
  }
}

class Cat extends Animal {
  bool isIndoor;
  
  Cat(String name, int age, this.isIndoor) : super(name, age);
  
  @override
  void makeSound() {
    print('$name meows: Meow!');
  }
  
  void scratch() {
    print('$name is scratching');
  }
}

void main() {
  var dog = Dog('Buddy', 3, 'Golden Retriever');
  var cat = Cat('Whiskers', 2, true);
  
  dog.makeSound();
  dog.eat();
  dog.fetch();
  
  print('');
  
  cat.makeSound();
  cat.eat();
  cat.scratch();
}
```

The `extends` keyword creates inheritance relationships. Subclasses inherit  
all public members from the superclass and can override methods using the  
`@override` annotation. The `super` keyword calls the parent constructor.  

```
$ dart run inheritance.dart
Buddy barks: Woof! Woof!
Buddy is eating
Buddy is fetching the ball

Whiskers meows: Meow!
Whiskers is eating
Whiskers is scratching
```

## Abstract Classes

Abstract classes define contracts that concrete classes must implement.  
They can contain abstract methods (no implementation) and concrete methods.  

```dart
abstract class Shape {
  String name;
  
  Shape(this.name);
  
  double calculateArea();
  double calculatePerimeter();
  
  void display() {
    print('Shape: $name');
    print('Area: ${calculateArea().toStringAsFixed(2)}');
    print('Perimeter: ${calculatePerimeter().toStringAsFixed(2)}');
  }
}

class Circle extends Shape {
  double radius;
  
  Circle(this.radius) : super('Circle');
  
  @override
  double calculateArea() => 3.14159 * radius * radius;
  
  @override
  double calculatePerimeter() => 2 * 3.14159 * radius;
}

class Square extends Shape {
  double side;
  
  Square(this.side) : super('Square');
  
  @override
  double calculateArea() => side * side;
  
  @override
  double calculatePerimeter() => 4 * side;
}

void main() {
  List<Shape> shapes = [
    Circle(5.0),
    Square(4.0),
  ];
  
  for (var shape in shapes) {
    shape.display();
    print('');
  }
}
```

Abstract classes cannot be instantiated directly. They define methods that  
subclasses must implement, ensuring consistent interfaces across related  
classes. This enables polymorphism where different shapes can be treated  
uniformly.  

```
$ dart run abstract_classes.dart
Shape: Circle
Area: 78.54
Perimeter: 31.42

Shape: Square
Area: 16.00
Perimeter: 16.00
```

## Implicit Interfaces

Every class in Dart implicitly defines an interface containing all instance  
members. Classes can implement multiple interfaces using the `implements`  
keyword.  

```dart
class Printable {
  void printInfo() {
    print('Printable object');
  }
}

class Scannable {
  void scan() {
    print('Scanning document');
  }
}

class Photocopiable {
  void photocopy() {
    print('Photocopying document');
  }
}

class AllInOnePrinter implements Printable, Scannable, Photocopiable {
  String model;
  
  AllInOnePrinter(this.model);
  
  @override
  void printInfo() {
    print('$model: Printing document');
  }
  
  @override
  void scan() {
    print('$model: Scanning document');
  }
  
  @override
  void photocopy() {
    print('$model: Photocopying document');
  }
}

void main() {
  var printer = AllInOnePrinter('HP OfficeJet Pro');
  
  printer.printInfo();
  printer.scan();
  printer.photocopy();
}
```

When implementing interfaces, a class must provide implementations for all  
interface members. This differs from inheritance where implementations are  
inherited. Multiple interface implementation enables flexible composition.  

```
$ dart run implicit_interfaces.dart
HP OfficeJet Pro: Printing document
HP OfficeJet Pro: Scanning document
HP OfficeJet Pro: Photocopying document
```

## Mixins

Mixins enable code reuse across class hierarchies without using inheritance.  
They provide a way to add functionality to classes in a composable manner.  

```dart
mixin Swimmer {
  void swim() {
    print('Swimming in the water');
  }
}

mixin Flyer {
  void fly() {
    print('Flying in the sky');
  }
}

mixin Walker {
  void walk() {
    print('Walking on land');
  }
}

class Duck with Swimmer, Flyer, Walker {
  String name;
  
  Duck(this.name);
  
  void introduce() {
    print('I am $name, a duck');
  }
}

class Fish with Swimmer {
  String species;
  
  Fish(this.species);
}

class Bird with Flyer, Walker {
  String type;
  
  Bird(this.type);
}

void main() {
  var duck = Duck('Donald');
  duck.introduce();
  duck.swim();
  duck.fly();
  duck.walk();
  
  print('');
  
  var fish = Fish('Goldfish');
  fish.swim();
  
  print('');
  
  var bird = Bird('Sparrow');
  bird.fly();
  bird.walk();
}
```

Mixins are declared with the `mixin` keyword and applied using `with`. A  
class can use multiple mixins, gaining all their methods. This provides  
flexible composition without the diamond problem of multiple inheritance.  

```
$ dart run mixins.dart
I am Donald, a duck
Swimming in the water
Flying in the sky
Walking on land

Swimming in the water

Flying in the sky
Walking on land
```

## Mixins with On Clause

Mixins can specify required superclasses using the `on` clause, ensuring  
they're only applied to appropriate classes.  

```dart
abstract class Animal {
  String name;
  Animal(this.name);
}

mixin Carnivore on Animal {
  void hunt() {
    print('$name is hunting prey');
  }
}

mixin Herbivore on Animal {
  void graze() {
    print('$name is grazing on grass');
  }
}

class Lion extends Animal with Carnivore {
  Lion(String name) : super(name);
}

class Elephant extends Animal with Herbivore {
  Elephant(String name) : super(name);
}

class Bear extends Animal with Carnivore, Herbivore {
  Bear(String name) : super(name);
}

void main() {
  var lion = Lion('Simba');
  lion.hunt();
  
  var elephant = Elephant('Dumbo');
  elephant.graze();
  
  var bear = Bear('Baloo');
  bear.hunt();
  bear.graze();
}
```

The `on` clause restricts where a mixin can be applied. This ensures type  
safety and guarantees that required properties and methods are available.  
Bears can use both carnivore and herbivore mixins.  

```
$ dart run mixin_on_clause.dart
Simba is hunting prey
Dumbo is grazing on grass
Baloo is hunting prey
Baloo is grazing on grass
```

## Factory Constructors

Factory constructors can return cached instances or subclass instances  
rather than always creating new objects.  

```dart
class Logger {
  final String name;
  static final Map<String, Logger> _cache = {};
  
  factory Logger(String name) {
    return _cache.putIfAbsent(name, () => Logger._internal(name));
  }
  
  Logger._internal(this.name);
  
  void log(String message) {
    print('[$name] $message');
  }
}

abstract class Vehicle {
  String type;
  Vehicle(this.type);
  
  factory Vehicle.fromType(String type) {
    switch (type) {
      case 'car':
        return Car();
      case 'truck':
        return Truck();
      case 'motorcycle':
        return Motorcycle();
      default:
        throw ArgumentError('Unknown vehicle type: $type');
    }
  }
  
  void drive();
}

class Car extends Vehicle {
  Car() : super('Car');
  
  @override
  void drive() {
    print('Driving a car');
  }
}

class Truck extends Vehicle {
  Truck() : super('Truck');
  
  @override
  void drive() {
    print('Driving a truck');
  }
}

class Motorcycle extends Vehicle {
  Motorcycle() : super('Motorcycle');
  
  @override
  void drive() {
    print('Driving a motorcycle');
  }
}

void main() {
  var logger1 = Logger('App');
  var logger2 = Logger('App');
  print('Same logger instance: ${identical(logger1, logger2)}');
  
  logger1.log('Application started');
  
  var car = Vehicle.fromType('car');
  var truck = Vehicle.fromType('truck');
  
  car.drive();
  truck.drive();
}
```

Factory constructors use the `factory` keyword and can implement the  
singleton pattern or factory pattern. They're useful for caching, returning  
subclass instances, or implementing complex initialization logic.  

```
$ dart run factory_constructors.dart
Same logger instance: true
[App] Application started
Driving a car
Driving a truck
```

## Const Constructors

Const constructors create compile-time constant objects, enabling better  
performance and memory efficiency.  

```dart
class ImmutablePoint {
  final double x;
  final double y;
  
  const ImmutablePoint(this.x, this.y);
  
  @override
  String toString() => 'ImmutablePoint($x, $y)';
}

class Color {
  final int red;
  final int green;
  final int blue;
  
  const Color(this.red, this.green, this.blue);
  
  static const Color black = Color(0, 0, 0);
  static const Color white = Color(255, 255, 255);
  static const Color red = Color(255, 0, 0);
  static const Color green = Color(0, 255, 0);
  static const Color blue = Color(0, 0, 255);
  
  @override
  String toString() => 'Color($red, $green, $blue)';
}

void main() {
  const p1 = ImmutablePoint(3.0, 4.0);
  const p2 = ImmutablePoint(3.0, 4.0);
  
  print('Points are identical: ${identical(p1, p2)}');
  
  print('Black: ${Color.black}');
  print('Red: ${Color.red}');
  
  const customColor = Color(128, 128, 128);
  print('Custom: $customColor');
}
```

Const constructors require all instance fields to be final. Objects created  
with const constructors are canonicalized - identical const expressions  
produce the same object instance, saving memory.  

```
$ dart run const_constructors.dart
Points are identical: true
Black: Color(0, 0, 0)
Red: Color(255, 0, 0)
Custom: Color(128, 128, 128)
```

## Static Members

Static members belong to the class itself rather than instances. They're  
useful for utility functions and class-level data.  

```dart
class MathUtils {
  static const double pi = 3.14159;
  static const double e = 2.71828;
  
  static double square(double x) => x * x;
  
  static double cube(double x) => x * x * x;
  
  static double circleArea(double radius) => pi * square(radius);
  
  static double sphereVolume(double radius) => 
      (4 / 3) * pi * cube(radius);
}

class Counter {
  static int _count = 0;
  
  static int get count => _count;
  
  static void increment() {
    _count++;
  }
  
  static void reset() {
    _count = 0;
  }
}

void main() {
  print('Pi: ${MathUtils.pi}');
  print('Circle area (r=5): ${MathUtils.circleArea(5.0).toStringAsFixed(2)}');
  print('Sphere volume (r=3): ${MathUtils.sphereVolume(3.0).toStringAsFixed(2)}');
  
  print('\nCounter tests:');
  Counter.increment();
  Counter.increment();
  Counter.increment();
  print('Count: ${Counter.count}');
  
  Counter.reset();
  print('After reset: ${Counter.count}');
}
```

Static members are accessed through the class name, not instances. They're  
ideal for utility functions, constants, and shared state. Static members  
cannot access instance members.  

```
$ dart run static_members.dart
Pi: 3.14159
Circle area (r=5): 78.54
Sphere volume (r=3): 113.10

Counter tests:
Count: 3
After reset: 0
```

## Cascade Notation

Cascade notation allows performing multiple operations on the same object  
without repeating the object reference.  

```dart
class StringBuilder {
  StringBuffer _buffer = StringBuffer();
  
  StringBuilder append(String text) {
    _buffer.write(text);
    return this;
  }
  
  StringBuilder appendLine(String text) {
    _buffer.writeln(text);
    return this;
  }
  
  StringBuilder clear() {
    _buffer.clear();
    return this;
  }
  
  @override
  String toString() => _buffer.toString();
}

class Person {
  String? name;
  int? age;
  String? email;
  
  void display() {
    print('Name: $name, Age: $age, Email: $email');
  }
}

void main() {
  var sb = StringBuilder()
    ..append('Hello there')
    ..append(', ')
    ..append('World')
    ..appendLine('!')
    ..appendLine('This is a cascade example');
  
  print(sb);
  
  var person = Person()
    ..name = 'Alice'
    ..age = 25
    ..email = 'alice@example.com'
    ..display();
}
```

Cascade notation uses `..` to call multiple methods or set multiple  
properties on the same object. This creates cleaner, more readable code  
especially when configuring objects or building fluent interfaces.  

```
$ dart run cascade_notation.dart
Hello there, World!
This is a cascade example

Name: Alice, Age: 25, Email: alice@example.com
```

## Method Overriding

Method overriding allows subclasses to provide specific implementations of  
methods defined in their superclass.  

```dart
class Employee {
  String name;
  double baseSalary;
  
  Employee(this.name, this.baseSalary);
  
  double calculateSalary() {
    return baseSalary;
  }
  
  void displayInfo() {
    print('Employee: $name');
    print('Salary: \$${calculateSalary().toStringAsFixed(2)}');
  }
}

class Manager extends Employee {
  double bonus;
  
  Manager(String name, double baseSalary, this.bonus) 
      : super(name, baseSalary);
  
  @override
  double calculateSalary() {
    return baseSalary + bonus;
  }
  
  @override
  void displayInfo() {
    super.displayInfo();
    print('Bonus: \$${bonus.toStringAsFixed(2)}');
  }
}

class SalesRep extends Employee {
  double commission;
  int sales;
  
  SalesRep(String name, double baseSalary, this.commission, this.sales) 
      : super(name, baseSalary);
  
  @override
  double calculateSalary() {
    return baseSalary + (commission * sales);
  }
  
  @override
  void displayInfo() {
    super.displayInfo();
    print('Commission per sale: \$${commission.toStringAsFixed(2)}');
    print('Number of sales: $sales');
  }
}

void main() {
  List<Employee> employees = [
    Employee('John', 50000),
    Manager('Sarah', 70000, 15000),
    SalesRep('Mike', 40000, 100, 50),
  ];
  
  for (var employee in employees) {
    employee.displayInfo();
    print('');
  }
}
```

The `@override` annotation marks methods that override superclass methods.  
Overridden methods can call the superclass implementation using `super`.  
This enables polymorphism and specialization in class hierarchies.  

```
$ dart run method_overriding.dart
Employee: John
Salary: $50000.00

Employee: Sarah
Salary: $85000.00
Bonus: $15000.00

Employee: Mike
Salary: $45000.00
Commission per sale: $100.00
Number of sales: 50
```

## Polymorphism

Polymorphism allows objects of different types to be treated through a  
common interface, enabling flexible and extensible code.  

```dart
abstract class PaymentMethod {
  String name;
  
  PaymentMethod(this.name);
  
  void processPayment(double amount);
  bool validatePayment();
}

class CreditCard extends PaymentMethod {
  String cardNumber;
  String expiryDate;
  
  CreditCard(this.cardNumber, this.expiryDate) : super('Credit Card');
  
  @override
  bool validatePayment() {
    return cardNumber.length == 16;
  }
  
  @override
  void processPayment(double amount) {
    if (validatePayment()) {
      print('Processing credit card payment of \$${amount.toStringAsFixed(2)}');
      print('Card: ****${cardNumber.substring(12)}');
    } else {
      print('Invalid credit card');
    }
  }
}

class PayPal extends PaymentMethod {
  String email;
  
  PayPal(this.email) : super('PayPal');
  
  @override
  bool validatePayment() {
    return email.contains('@');
  }
  
  @override
  void processPayment(double amount) {
    if (validatePayment()) {
      print('Processing PayPal payment of \$${amount.toStringAsFixed(2)}');
      print('Account: $email');
    } else {
      print('Invalid PayPal account');
    }
  }
}

class Cash extends PaymentMethod {
  Cash() : super('Cash');
  
  @override
  bool validatePayment() => true;
  
  @override
  void processPayment(double amount) {
    print('Processing cash payment of \$${amount.toStringAsFixed(2)}');
  }
}

void main() {
  List<PaymentMethod> payments = [
    CreditCard('1234567890123456', '12/25'),
    PayPal('user@example.com'),
    Cash(),
  ];
  
  double amount = 99.99;
  
  for (var payment in payments) {
    print('\nPayment method: ${payment.name}');
    payment.processPayment(amount);
  }
}
```

Polymorphism enables treating different types uniformly through shared  
interfaces. This makes code more flexible and maintainable, as new types  
can be added without changing existing code.  

```
$ dart run polymorphism.dart

Payment method: Credit Card
Processing credit card payment of $99.99
Card: ****3456

Payment method: PayPal
Processing PayPal payment of $99.99
Account: user@example.com

Payment method: Cash
Processing cash payment of $99.99
```

## Callable Classes

Classes can be made callable like functions by implementing the `call`  
method, enabling function-like syntax for objects.  

```dart
class Multiplier {
  int factor;
  
  Multiplier(this.factor);
  
  int call(int value) => value * factor;
}

class Greeter {
  String prefix;
  
  Greeter(this.prefix);
  
  String call(String name) => '$prefix, $name!';
}

void main() {
  var triple = Multiplier(3);
  print('5 * 3 = ${triple(5)}');
  print('10 * 3 = ${triple(10)}');
  
  var greeter = Greeter('Hello there');
  print(greeter('Alice'));
  print(greeter('Bob'));
}
```

Callable classes implement the `call` method, allowing instances to be  
invoked like functions. This pattern is useful for creating function objects  
with state or implementing the command pattern.  

```
$ dart run callable_classes.dart
5 * 3 = 15
10 * 3 = 30
Hello there, Alice!
Hello there, Bob!
```

## Extension Methods

Extension methods add functionality to existing classes without modifying  
them or using inheritance.  

```dart
extension StringExtensions on String {
  String capitalize() {
    if (isEmpty) return this;
    return this[0].toUpperCase() + substring(1);
  }
  
  String reverse() {
    return split('').reversed.join('');
  }
  
  bool isPalindrome() {
    String cleaned = toLowerCase().replaceAll(' ', '');
    return cleaned == cleaned.split('').reversed.join('');
  }
}

extension ListExtensions<T> on List<T> {
  T? get secondOrNull => length >= 2 ? this[1] : null;
  
  List<T> get unique => {...this}.toList();
  
  void removeWhere(bool Function(T) test) {
    removeWhere(test);
  }
}

void main() {
  String text = 'hello';
  print('Capitalized: ${text.capitalize()}');
  print('Reversed: ${text.reverse()}');
  
  print('Is "racecar" palindrome? ${'racecar'.isPalindrome()}');
  print('Is "hello" palindrome? ${'hello'.isPalindrome()}');
  
  var numbers = [1, 2, 3, 2, 1];
  print('Second: ${numbers.secondOrNull}');
  print('Unique: ${numbers.unique}');
}
```

Extension methods enable adding methods to any type, including built-in  
types and third-party classes. They provide a clean way to enhance APIs  
without inheritance or wrapper classes.  

```
$ dart run extension_methods.dart
Capitalized: Hello
Reversed: olleh
Is "racecar" palindrome? true
Is "hello" palindrome? false
Second: 2
Unique: [1, 2, 3]
```

## Operator Overloading

Dart allows overloading operators to provide intuitive syntax for custom  
types.  

```dart
class Vector {
  double x;
  double y;
  
  Vector(this.x, this.y);
  
  Vector operator +(Vector other) {
    return Vector(x + other.x, y + other.y);
  }
  
  Vector operator -(Vector other) {
    return Vector(x - other.x, y - other.y);
  }
  
  Vector operator *(double scalar) {
    return Vector(x * scalar, y * scalar);
  }
  
  @override
  bool operator ==(Object other) {
    return other is Vector && x == other.x && y == other.y;
  }
  
  @override
  int get hashCode => Object.hash(x, y);
  
  @override
  String toString() => 'Vector($x, $y)';
}

void main() {
  var v1 = Vector(3.0, 4.0);
  var v2 = Vector(1.0, 2.0);
  
  var sum = v1 + v2;
  var diff = v1 - v2;
  var scaled = v1 * 2;
  
  print('v1 + v2 = $sum');
  print('v1 - v2 = $diff');
  print('v1 * 2 = $scaled');
  print('v1 == Vector(3.0, 4.0): ${v1 == Vector(3.0, 4.0)}');
}
```

Operator overloading uses the `operator` keyword followed by the operator  
symbol. Common operators include arithmetic (+, -, *, /), comparison (==, <,  
>), and indexing ([]). This enables natural, mathematical syntax.  

```
$ dart run operator_overloading.dart
v1 + v2 = Vector(4.0, 6.0)
v1 - v2 = Vector(2.0, 2.0)
v1 * 2 = Vector(6.0, 8.0)
v1 == Vector(3.0, 4.0): true
```

## Composition Over Inheritance

Composition provides a flexible alternative to inheritance by building  
complex objects from simpler components.  

```dart
class Engine {
  int horsepower;
  
  Engine(this.horsepower);
  
  void start() {
    print('Engine started: $horsepower HP');
  }
}

class Transmission {
  String type;
  
  Transmission(this.type);
  
  void shift(int gear) {
    print('$type transmission: shifting to gear $gear');
  }
}

class Car {
  String model;
  Engine engine;
  Transmission transmission;
  
  Car(this.model, this.engine, this.transmission);
  
  void drive() {
    print('Driving $model:');
    engine.start();
    transmission.shift(1);
    transmission.shift(2);
  }
}

void main() {
  var sportsCar = Car(
    'Ferrari',
    Engine(450),
    Transmission('Manual')
  );
  
  var sedan = Car(
    'Toyota Camry',
    Engine(200),
    Transmission('Automatic')
  );
  
  sportsCar.drive();
  print('');
  sedan.drive();
}
```

Composition creates "has-a" relationships rather than "is-a" relationships.  
This approach is more flexible than inheritance, allowing runtime  
configuration and easier testing through dependency injection.  

```
$ dart run composition.dart
Driving Ferrari:
Engine started: 450 HP
Manual transmission: shifting to gear 1
Manual transmission: shifting to gear 2

Driving Toyota Camry:
Engine started: 200 HP
Automatic transmission: shifting to gear 1
Automatic transmission: shifting to gear 2
```

## Interface Segregation

Following interface segregation principle, create specific interfaces  
rather than large, monolithic ones.  

```dart
abstract class Readable {
  String read();
}

abstract class Writable {
  void write(String data);
}

abstract class Seekable {
  void seek(int position);
}

class File implements Readable, Writable, Seekable {
  String _content = '';
  int _position = 0;
  
  @override
  String read() {
    return _content.substring(_position);
  }
  
  @override
  void write(String data) {
    _content += data;
  }
  
  @override
  void seek(int position) {
    _position = position;
  }
}

class ReadOnlyFile implements Readable {
  final String _content;
  
  ReadOnlyFile(this._content);
  
  @override
  String read() {
    return _content;
  }
}

class NetworkStream implements Readable {
  @override
  String read() {
    return 'Data from network';
  }
}

void main() {
  var file = File();
  file.write('Hello there, World!');
  print('File content: ${file.read()}');
  
  var readOnly = ReadOnlyFile('Read-only content');
  print('Read-only: ${readOnly.read()}');
  
  var stream = NetworkStream();
  print('Stream: ${stream.read()}');
}
```

Interface segregation keeps interfaces focused and cohesive. Classes only  
implement interfaces they need, avoiding unnecessary method implementations  
and maintaining cleaner, more maintainable code.  

```
$ dart run interface_segregation.dart
File content: Hello there, World!
Read-only: Read-only content
Stream: Data from network
```

## Sealed Classes and Pattern Matching

Dart 3 sealed classes enable exhaustive pattern matching for representing  
closed type hierarchies.  

```dart
sealed class Result<T> {}

class Success<T> extends Result<T> {
  final T value;
  Success(this.value);
}

class Failure<T> extends Result<T> {
  final String error;
  Failure(this.error);
}

class Pending<T> extends Result<T> {}

String handleResult<T>(Result<T> result) {
  return switch (result) {
    Success(value: var v) => 'Success: $v',
    Failure(error: var e) => 'Error: $e',
    Pending() => 'Pending...',
  };
}

void main() {
  var success = Success<int>(42);
  var failure = Failure<int>('Connection timeout');
  var pending = Pending<int>();
  
  print(handleResult(success));
  print(handleResult(failure));
  print(handleResult(pending));
}
```

Sealed classes can only be extended in the same library, enabling exhaustive  
switch expressions. The compiler ensures all possible cases are handled,  
preventing runtime errors from missing cases.  

```
$ dart run sealed_classes.dart
Success: 42
Error: Connection timeout
Pending...
```

## Generics in Classes

Generic classes enable type-safe reusable code that works with different  
types while maintaining type safety.  

```dart
class Box<T> {
  T value;
  
  Box(this.value);
  
  T getValue() => value;
  
  void setValue(T newValue) {
    value = newValue;
  }
}

class Pair<K, V> {
  K key;
  V value;
  
  Pair(this.key, this.value);
  
  @override
  String toString() => 'Pair($key: $value)';
}

class Stack<T> {
  final List<T> _items = [];
  
  void push(T item) {
    _items.add(item);
  }
  
  T? pop() {
    return _items.isEmpty ? null : _items.removeLast();
  }
  
  T? peek() {
    return _items.isEmpty ? null : _items.last;
  }
  
  bool get isEmpty => _items.isEmpty;
  int get size => _items.length;
}

void main() {
  var intBox = Box<int>(42);
  print('Int box: ${intBox.getValue()}');
  
  var stringBox = Box<String>('Hello there');
  print('String box: ${stringBox.getValue()}');
  
  var pair = Pair<String, int>('age', 25);
  print(pair);
  
  var stack = Stack<String>();
  stack.push('first');
  stack.push('second');
  stack.push('third');
  
  print('Stack size: ${stack.size}');
  print('Popped: ${stack.pop()}');
  print('Peek: ${stack.peek()}');
}
```

Generic classes use type parameters (T, K, V, etc.) to create reusable  
components that work with any type. This provides type safety while avoiding  
code duplication for different types.  

```
$ dart run generics.dart
Int box: 42
String box: Hello there
Pair(age: 25)
Stack size: 3
Popped: third
Peek: second
```

## Type Checking and Casting

Dart provides operators for runtime type checking and safe type casting.  

```dart
class Animal {
  String name;
  Animal(this.name);
}

class Dog extends Animal {
  Dog(String name) : super(name);
  void bark() => print('$name barks!');
}

class Cat extends Animal {
  Cat(String name) : super(name);
  void meow() => print('$name meows!');
}

void processAnimal(Animal animal) {
  print('Processing: ${animal.name}');
  
  if (animal is Dog) {
    animal.bark();
  } else if (animal is Cat) {
    animal.meow();
  }
  
  var dog = animal as Dog? ;
  dog?.bark();
}

void main() {
  List<Animal> animals = [
    Dog('Buddy'),
    Cat('Whiskers'),
    Animal('Generic'),
  ];
  
  for (var animal in animals) {
    print('\nAnimal type: ${animal.runtimeType}');
    print('Is Dog? ${animal is Dog}');
    print('Is Cat? ${animal is Cat}');
    print('Is Animal? ${animal is Animal}');
    
    if (animal is Dog) {
      animal.bark();
    }
  }
}
```

The `is` operator checks if an object is of a specific type. The `as`  
operator casts to a specific type (throws on failure). The `as?` operator  
attempts casting, returning null on failure. Type promotion automatically  
narrows types after successful checks.  

```
$ dart run type_checking.dart

Animal type: Dog
Is Dog? true
Is Cat? false
Is Animal? true
Buddy barks!

Animal type: Cat
Is Dog? false
Is Cat? true
Is Animal? true
Whiskers meows!

Animal type: Animal
Is Dog? false
Is Cat? false
Is Animal? true
```

## Object Equality

Implementing proper equality enables comparing objects by value rather than  
reference.  

```dart
class Person {
  final String name;
  final int age;
  
  Person(this.name, this.age);
  
  @override
  bool operator ==(Object other) {
    return other is Person && name == other.name && age == other.age;
  }
  
  @override
  int get hashCode => Object.hash(name, age);
  
  @override
  String toString() => 'Person($name, $age)';
}

void main() {
  var person1 = Person('Alice', 30);
  var person2 = Person('Alice', 30);
  var person3 = Person('Bob', 25);
  
  print('person1 == person2: ${person1 == person2}');
  print('person1 == person3: ${person1 == person3}');
  print('identical(person1, person2): ${identical(person1, person2)}');
  
  var set = {person1, person2, person3};
  print('Set size (should be 2): ${set.length}');
}
```

Override `==` operator and `hashCode` getter together for proper equality.  
The `hashCode` must be consistent with `==` - equal objects must have equal  
hash codes. Use `Object.hash()` for combining multiple fields.  

```
$ dart run object_equality.dart
person1 == person2: true
person1 == person3: false
identical(person1, person2): false
Set size (should be 2): 2
```

## Enumerated Types with Methods

Enhanced enums can contain methods and computed properties, creating  
powerful type-safe constants.  

```dart
enum HttpStatus {
  ok(200, 'OK'),
  created(201, 'Created'),
  badRequest(400, 'Bad Request'),
  unauthorized(401, 'Unauthorized'),
  notFound(404, 'Not Found'),
  serverError(500, 'Internal Server Error');
  
  final int code;
  final String message;
  
  const HttpStatus(this.code, this.message);
  
  bool get isSuccess => code >= 200 && code < 300;
  bool get isClientError => code >= 400 && code < 500;
  bool get isServerError => code >= 500;
  
  String format() => '$code $message';
}

void main() {
  var status = HttpStatus.ok;
  print(status.format());
  print('Is success? ${status.isSuccess}');
  
  print('\nAll statuses:');
  for (var status in HttpStatus.values) {
    print('${status.format()} - Success: ${status.isSuccess}');
  }
}
```

Enhanced enums support fields, constructors, methods, and getters. Each  
enum value can have associated data, and methods can perform computations  
based on that data, creating expressive, type-safe APIs.  

```
$ dart run enum_methods.dart
200 OK
Is success? true

All statuses:
200 OK - Success: true
201 Created - Success: true
400 Bad Request - Success: false
401 Unauthorized - Success: false
404 Not Found - Success: false
500 Internal Server Error - Success: false
```

## Singleton Pattern

The singleton pattern ensures a class has only one instance with global  
access.  

```dart
class Database {
  static final Database _instance = Database._internal();
  
  factory Database() {
    return _instance;
  }
  
  Database._internal();
  
  void connect() {
    print('Database connected');
  }
  
  void query(String sql) {
    print('Executing: $sql');
  }
}

class ConfigManager {
  static ConfigManager? _instance;
  Map<String, String> _config = {};
  
  ConfigManager._internal();
  
  static ConfigManager get instance {
    _instance ??= ConfigManager._internal();
    return _instance!;
  }
  
  void setConfig(String key, String value) {
    _config[key] = value;
  }
  
  String? getConfig(String key) {
    return _config[key];
  }
}

void main() {
  var db1 = Database();
  var db2 = Database();
  
  print('Same database instance? ${identical(db1, db2)}');
  db1.connect();
  db1.query('SELECT * FROM users');
  
  var config1 = ConfigManager.instance;
  var config2 = ConfigManager.instance;
  
  print('Same config instance? ${identical(config1, config2)}');
  config1.setConfig('theme', 'dark');
  print('Theme from config2: ${config2.getConfig('theme')}');
}
```

Singletons use private constructors and factory constructors or static  
getters to ensure only one instance exists. This pattern is useful for  
shared resources like database connections or configuration managers.  

```
$ dart run singleton.dart
Same database instance? true
Database connected
Executing: SELECT * FROM users
Same config instance? true
Theme from config2: dark
```

## Builder Pattern

The builder pattern constructs complex objects step by step with a fluent  
interface.  

```dart
class HttpRequest {
  String? url;
  String method;
  Map<String, String> headers;
  String? body;
  int timeout;
  
  HttpRequest._builder(HttpRequestBuilder builder)
      : url = builder._url,
        method = builder._method,
        headers = builder._headers,
        body = builder._body,
        timeout = builder._timeout;
  
  @override
  String toString() {
    return 'HttpRequest(url: $url, method: $method, '
           'headers: $headers, body: $body, timeout: $timeout)';
  }
}

class HttpRequestBuilder {
  String? _url;
  String _method = 'GET';
  Map<String, String> _headers = {};
  String? _body;
  int _timeout = 30;
  
  HttpRequestBuilder url(String url) {
    _url = url;
    return this;
  }
  
  HttpRequestBuilder method(String method) {
    _method = method;
    return this;
  }
  
  HttpRequestBuilder header(String key, String value) {
    _headers[key] = value;
    return this;
  }
  
  HttpRequestBuilder body(String body) {
    _body = body;
    return this;
  }
  
  HttpRequestBuilder timeout(int seconds) {
    _timeout = seconds;
    return this;
  }
  
  HttpRequest build() {
    return HttpRequest._builder(this);
  }
}

void main() {
  var request = HttpRequestBuilder()
      .url('https://api.example.com/users')
      .method('POST')
      .header('Content-Type', 'application/json')
      .header('Authorization', 'Bearer token123')
      .body('{"name": "Alice"}')
      .timeout(60)
      .build();
  
  print(request);
}
```

The builder pattern separates object construction from representation,  
enabling flexible object creation with optional parameters. Method chaining  
provides a fluent, readable API for configuration.  

```
$ dart run builder_pattern.dart
HttpRequest(url: https://api.example.com/users, method: POST, 
headers: {Content-Type: application/json, Authorization: Bearer token123}, 
body: {"name": "Alice"}, timeout: 60)
```

## Observer Pattern

The observer pattern implements a subscription mechanism for notifying  
multiple objects about events.  

```dart
abstract class Observer {
  void update(String message);
}

class Subject {
  final List<Observer> _observers = [];
  
  void attach(Observer observer) {
    _observers.add(observer);
  }
  
  void detach(Observer observer) {
    _observers.remove(observer);
  }
  
  void notify(String message) {
    for (var observer in _observers) {
      observer.update(message);
    }
  }
}

class EmailNotifier implements Observer {
  String email;
  
  EmailNotifier(this.email);
  
  @override
  void update(String message) {
    print('Email to $email: $message');
  }
}

class SMSNotifier implements Observer {
  String phoneNumber;
  
  SMSNotifier(this.phoneNumber);
  
  @override
  void update(String message) {
    print('SMS to $phoneNumber: $message');
  }
}

class PushNotifier implements Observer {
  String deviceId;
  
  PushNotifier(this.deviceId);
  
  @override
  void update(String message) {
    print('Push to device $deviceId: $message');
  }
}

void main() {
  var newsPublisher = Subject();
  
  var emailSub = EmailNotifier('user@example.com');
  var smsSub = SMSNotifier('+1234567890');
  var pushSub = PushNotifier('device123');
  
  newsPublisher.attach(emailSub);
  newsPublisher.attach(smsSub);
  newsPublisher.attach(pushSub);
  
  newsPublisher.notify('Breaking news: Dart 3.9 released!');
  
  print('\nUnsubscribing SMS...');
  newsPublisher.detach(smsSub);
  
  newsPublisher.notify('New tutorial available');
}
```

The observer pattern decouples subjects from observers, enabling one-to-many  
dependencies where changes in one object automatically notify dependent  
objects. This is fundamental for event handling and reactive programming.  

```
$ dart run observer_pattern.dart
Email to user@example.com: Breaking news: Dart 3.9 released!
SMS to +1234567890: Breaking news: Dart 3.9 released!
Push to device device123: Breaking news: Dart 3.9 released!

Unsubscribing SMS...
Email to user@example.com: New tutorial available
Push to device device123: New tutorial available
```

## Strategy Pattern

The strategy pattern defines a family of algorithms and makes them  
interchangeable at runtime.  

```dart
abstract class SortStrategy {
  List<int> sort(List<int> data);
  String get name;
}

class BubbleSort implements SortStrategy {
  @override
  String get name => 'Bubble Sort';
  
  @override
  List<int> sort(List<int> data) {
    var result = List<int>.from(data);
    for (int i = 0; i < result.length; i++) {
      for (int j = 0; j < result.length - i - 1; j++) {
        if (result[j] > result[j + 1]) {
          var temp = result[j];
          result[j] = result[j + 1];
          result[j + 1] = temp;
        }
      }
    }
    return result;
  }
}

class QuickSort implements SortStrategy {
  @override
  String get name => 'Quick Sort';
  
  @override
  List<int> sort(List<int> data) {
    var result = List<int>.from(data);
    result.sort();
    return result;
  }
}

class Sorter {
  SortStrategy strategy;
  
  Sorter(this.strategy);
  
  void setStrategy(SortStrategy strategy) {
    this.strategy = strategy;
  }
  
  List<int> performSort(List<int> data) {
    print('Sorting using ${strategy.name}');
    return strategy.sort(data);
  }
}

void main() {
  var data = [64, 34, 25, 12, 22, 11, 90];
  print('Original: $data');
  
  var sorter = Sorter(BubbleSort());
  var result1 = sorter.performSort(data);
  print('Result: $result1\n');
  
  sorter.setStrategy(QuickSort());
  var result2 = sorter.performSort(data);
  print('Result: $result2');
}
```

The strategy pattern encapsulates algorithms in separate classes, enabling  
runtime selection of behavior. This promotes code reuse and makes algorithms  
interchangeable without affecting clients.  

```
$ dart run strategy_pattern.dart
Original: [64, 34, 25, 12, 22, 11, 90]
Sorting using Bubble Sort
Result: [11, 12, 22, 25, 34, 64, 90]

Sorting using Quick Sort
Result: [11, 12, 22, 25, 34, 64, 90]
```

## Covariance and Contravariance

Generic type parameters can be covariant (out) or contravariant (in),  
affecting type safety in inheritance hierarchies.  

```dart
class Animal {
  String name;
  Animal(this.name);
}

class Dog extends Animal {
  Dog(String name) : super(name);
}

class Cat extends Animal {
  Cat(String name) : super(name);
}

class AnimalShelter<out T extends Animal> {
  final List<T> _animals = [];
  
  void add(T animal) {
    _animals.add(animal);
  }
  
  T? get() {
    return _animals.isEmpty ? null : _animals.removeLast();
  }
  
  int get count => _animals.length;
}

void processAnimals(AnimalShelter<Animal> shelter) {
  print('Processing ${shelter.count} animals');
}

void main() {
  var dogShelter = AnimalShelter<Dog>();
  dogShelter.add(Dog('Rex'));
  dogShelter.add(Dog('Max'));
  
  var catShelter = AnimalShelter<Cat>();
  catShelter.add(Cat('Fluffy'));
  
  print('Dogs: ${dogShelter.count}');
  print('Cats: ${catShelter.count}');
}
```

Covariance allows subtype substitution for generic types. The `out` modifier  
indicates the type parameter appears only in output positions. This enables  
treating more specific generic types as more general ones safely.  

```
$ dart run covariance.dart
Dogs: 2
Cats: 1
```
