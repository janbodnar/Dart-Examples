
# Dart Variables

Variables in Dart serve as named containers that store and manipulate data  
throughout program execution. They provide a way to reference values that  
can change during runtime, making code more flexible and maintainable.  

Dart combines static typing with powerful type inference, enabling developers  
to write both explicitly typed and implicitly typed variable declarations.  
All variables must be declared before use and can be initialized either at  
declaration time or later in the program flow.  

## Variable Declaration and Initialization

Variables in Dart can be declared using the `var` keyword for type inference  
or with explicit type annotations. The `var` keyword allows Dart to  
automatically determine the variable's type based on the assigned value.  

```dart
void main() {
  // Using var with type inference
  var name = 'Alice';
  var age = 30;
  
  // Explicit type declaration
  String country = 'Canada';
  double height = 1.75;
  
  print('$name is $age years old from $country');
  print('Height: $height meters');
}
```

This example demonstrates both type inference and explicit type declarations.  
The `var` keyword provides convenience when types are obvious from context,  
while explicit types enhance code readability and enforce type safety at  
compile time.  

**Output:**
```
Alice is 30 years old from Canada
Height: 1.75 meters
```

## Final and Const Variables

Dart provides `final` and `const` keywords for declaring immutable variables  
that cannot be reassigned after initialization. The `final` keyword creates  
runtime constants, while `const` creates compile-time constants.  

```dart
void main() {
  // final variable (runtime constant)
  final currentYear = DateTime.now().year;
  
  // const variable (compile-time constant)
  const pi = 3.14159;
  const maxAttempts = 3;
  
  print('Current year: $currentYear');
  print('PI value: $pi');
  print('Maximum attempts: $maxAttempts');
  
  // This would cause a compilation error:
  // pi = 3.14;
}
```

The `final` variables are evaluated at runtime and can use dynamic values  
like function calls or current time. The `const` variables must be known at  
compile time and contain only literal values or other compile-time constants.  
Both prevent reassignment, ensuring immutability.  

**Output:**
```
Current year: 2025
PI value: 3.14159
Maximum attempts: 3
```

## Dynamic and Object Types

Dart provides `dynamic` and `Object` types for variables that can hold values  
of any type. The `dynamic` type bypasses static type checking completely,  
while `Object` represents the root of Dart's type hierarchy.  

```dart
void main() {
  dynamic dynamicValue = 'Hello there!';
  print('dynamicValue: $dynamicValue (${dynamicValue.runtimeType})');
  
  dynamicValue = 42;
  print('dynamicValue: $dynamicValue (${dynamicValue.runtimeType})');
  
  Object objectValue = 'World';
  print('objectValue: $objectValue (${objectValue.runtimeType})');
  
  // This would cause a runtime error:
  // print(objectValue.length);
  
  if (objectValue is String) {
    print('Length: ${objectValue.length}');
  }
}
```

The `dynamic` type allows changing types freely and accessing any members  
without compile-time checking. The `Object` type requires explicit type  
checking before accessing type-specific members. Use `dynamic` sparingly  
as it sacrifices type safety benefits.  

**Output:**
```
dynamicValue: Hello there! (String)
dynamicValue: 42 (int)
objectValue: World (String)
Length: 5
```

## Nullable Variables

Dart's null safety feature requires explicit declaration of nullable variables  
using the `?` operator. Non-nullable variables must always contain a valid  
value, while nullable variables can hold either a value or `null`.  

```dart
void main() {
  // Non-nullable variable
  String name = 'Bob';
  // name = null; // Compilation error
  
  // Nullable variable
  String? nickname = null;
  nickname = 'Bobby';
  
  print('Name: $name');
  print('Nickname: $nickname');
  
  // Null-aware operators
  int? age;
  int actualAge = age ?? 25;
  print('Age: $actualAge');
  
  // Null assertion operator (!)
  String? maybeString = 'Hello there!';
  print(maybeString!.length);
}
```

This example showcases Dart's null safety features. The `??` operator provides  
default values for null scenarios, while the `!` operator asserts that a  
nullable value is definitely not null. These features help prevent null  
reference exceptions at runtime.  

**Output:**
```
Name: Bob
Nickname: Bobby
Age: 25
12
```

## Variable Scope

Variables in Dart follow lexical scoping rules, meaning they are only  
accessible within the block where they are declared. Global variables  
remain accessible throughout the entire program.  

```dart
// Global variable
int globalCounter = 0;

void incrementCounter() {
  // Local variable
  int localCounter = 0;
  
  globalCounter++;
  localCounter++;
  
  print('Global: $globalCounter, Local: $localCounter');
}

void main() {
  incrementCounter();
  incrementCounter();
  
  // This would cause a compilation error:
  // print(localCounter);
  
  print('Final global counter: $globalCounter');
}
```

The `globalCounter` variable is accessible from anywhere in the program,  
while `localCounter` exists only within the `incrementCounter` function.  
Each function call creates a new instance of local variables, maintaining  
separate scopes.  

**Output:**
```
Global: 1, Local: 1
Global: 2, Local: 1
Final global counter: 2
```

## Wildcard Variables

Dart 3.8 introduced **wildcard variables**, enabling developers to use the  
underscore (`_`) as a placeholder for values they intentionally want to  
ignore. Wildcard variables enhance code readability and reduce clutter by  
clearly indicating which values are unused.  

Wildcards are particularly powerful in pattern matching scenarios, destructuring  
assignments, and callback functions where certain parameters are not needed.  
They eliminate compiler warnings about unused variables while communicating  
developer intent to code reviewers.  

```dart
void main() {
  // Destructuring with wildcard
  var (a, _, c) = (1, 2, 3);
  print('a: $a, c: $c'); // Output: a: 1, c: 3

  // Pattern matching with wildcard
  var list = [10, 20, 30];
  switch (list) {
    case [_, int second, _]:
      print('Second element: $second');
  }

  // Function parameter wildcard
  void printFirst(int first, _) {
    print('First: $first');
  }
  printFirst(42, 'unused');

  // Using wildcards in for-each loops
  var pairs = [(1, 'one'), (2, 'two'), (3, 'three')];
  for (var (_, word) in pairs) {
    print(word); // Prints: one, two, three
  }

  // Ignoring multiple values in nested patterns
  var nested = (1, (2, 3));
  var (x, (_, z)) = nested;
  print('x: $x, z: $z'); // Output: x: 1, z: 3
}
```

The underscore effectively ignores values during tuple destructuring, pattern  
matching, and function parameters. This feature makes code more concise and  
self-documenting by focusing attention on the values that matter while  
safely discarding unused ones.  

## Late Variables

The `late` keyword allows declaring non-nullable variables that are initialized  
after declaration. This is useful for variables that cannot be initialized  
immediately but are guaranteed to have a value before first use.  

```dart
late String description;
late final int computedValue;

void initializeValues() {
  description = 'This is a late-initialized variable';
  computedValue = expensiveComputation();
}

int expensiveComputation() {
  print('Performing expensive computation...');
  return 42 * 42;
}

void main() {
  // Variables are declared but not yet initialized
  print('Variables declared');
  
  initializeValues();
  
  print(description);
  print('Computed value: $computedValue');
}
```

Late variables help break initialization dependency cycles and defer expensive  
computations until needed. They maintain null safety guarantees while providing  
initialization flexibility. Accessing a late variable before initialization  
throws a runtime error.  

**Output:**
```
Variables declared
Performing expensive computation...
This is a late-initialized variable
Computed value: 1764
```

## Type Annotations with Collections

Dart supports explicit type annotations for collections, providing better type  
safety and code documentation. Generic type parameters specify the element  
types contained within collections.  

```dart
void main() {
  // List with explicit type annotation
  List<String> cities = ['New York', 'London', 'Tokyo'];
  
  // Map with key and value types
  Map<String, int> populationData = {
    'New York': 8_400_000,
    'London': 9_500_000,
    'Tokyo': 14_000_000,
  };
  
  // Set with unique elements
  Set<int> uniqueNumbers = {1, 2, 3, 2, 1}; // Duplicates removed
  
  print('Cities: $cities');
  print('Population of Tokyo: ${populationData['Tokyo']}');
  print('Unique numbers: $uniqueNumbers');
  
  // Type-safe operations
  cities.add('Paris');
  populationData['Paris'] = 2_200_000;
  uniqueNumbers.add(4);
  
  print('Updated collections:');
  print('Cities: $cities');
  print('Population data: $populationData');
  print('Numbers: $uniqueNumbers');
}
```

Explicit type annotations prevent accidental insertion of wrong types and  
enable better IDE support with autocompletion and error detection. The  
compiler enforces type safety at compile time rather than runtime.  

**Output:**
```
Cities: [New York, London, Tokyo]
Population of Tokyo: 14000000
Unique numbers: {1, 2, 3}
Updated collections:
Cities: [New York, London, Tokyo, Paris]
Population data: {New York: 8400000, London: 9500000, Tokyo: 14000000, Paris: 2200000}
Numbers: {1, 2, 3, 4}
```

## Variable Initialization Patterns

Dart 3.0 introduced powerful pattern matching for variable initialization,  
allowing destructuring of complex data structures into individual variables  
in a single declaration statement.  

```dart
void main() {
  // Record destructuring
  var person = ('Alice', 25, 'Engineer');
  var (name, age, job) = person;
  print('Name: $name, Age: $age, Job: $job');
  
  // List pattern matching
  var numbers = [1, 2, 3, 4, 5];
  var [first, second, ...rest] = numbers;
  print('First: $first, Second: $second, Rest: $rest');
  
  // Map destructuring (using records)
  var userInfo = {'name': 'Bob', 'age': 30, 'city': 'Toronto'};
  var {'name': userName, 'age': userAge} = userInfo;
  print('User: $userName, Age: $userAge');
  
  // Nested pattern matching
  var nestedData = ([1, 2], {'key': 'value'});
  var ([listFirst, listSecond], {'key': mapValue}) = nestedData;
  print('List: [$listFirst, $listSecond], Map value: $mapValue');
  
  // Object pattern (using records for simplicity)
  var coordinates = (x: 10.5, y: 20.3);
  var (x: xPos, y: yPos) = coordinates;
  print('Position: ($xPos, $yPos)');
}
```

Pattern matching streamlines data extraction from complex structures, making  
code more readable and reducing boilerplate. It supports various patterns  
including lists, records, maps, and nested combinations.  

**Output:**
```
Name: Alice, Age: 25, Job: Engineer
First: 1, Second: 2, Rest: [3, 4, 5]
User: Bob, Age: 30
List: [1, 2], Map value: value
Position: (10.5, 20.3)
```

## Cascade Operator with Variables

The cascade operator (`..`) allows performing multiple operations on the same  
object without repeating the variable name. This creates more fluent and  
readable code when configuring objects.  

```dart
class Person {
  String? name;
  int? age;
  String? city;
  List<String> hobbies = [];
  
  void introduce() {
    print('Hello there! I\'m $name, $age years old from $city');
    if (hobbies.isNotEmpty) {
      print('My hobbies: ${hobbies.join(', ')}');
    }
  }
}

void main() {
  // Using cascade operator for object initialization
  var person = Person()
    ..name = 'Charlie'
    ..age = 28
    ..city = 'San Francisco'
    ..hobbies.addAll(['reading', 'hiking', 'coding'])
    ..introduce();
  
  print('---');
  
  // Cascade with collections
  var numbers = <int>[]
    ..add(1)
    ..add(2)
    ..addAll([3, 4, 5])
    ..sort()
    ..removeWhere((n) => n.isEven);
  
  print('Processed numbers: $numbers');
  
  // Null-aware cascade operator (?..)
  Person? nullablePerson = Person();
  nullablePerson?..name = 'Diana'
    ..age = 32
    ..introduce();
}
```

The cascade operator eliminates repetitive variable references and creates  
a more functional programming style. The null-aware cascade (`?..`) safely  
handles nullable objects, preventing null pointer exceptions.  

**Output:**
```
Hello there! I'm Charlie, 28 years old from San Francisco
My hobbies: reading, hiking, coding
---
Processed numbers: [1, 3, 5]
Hello there! I'm Diana, 32 years old from null
```

## Extension Methods on Variables

Extension methods add functionality to existing types without modifying their  
source code. This allows enhancing built-in types and custom classes with  
domain-specific operations.  

```dart
extension StringValidation on String {
  bool get isValidEmail => RegExp(r'^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$').hasMatch(this);
  bool get isNumeric => double.tryParse(this) != null;
  String get capitalized => isEmpty ? this : this[0].toUpperCase() + substring(1);
}

extension ListUtils<T> on List<T> {
  T? get safeFirst => isEmpty ? null : first;
  T? get safeLast => isEmpty ? null : last;
  List<T> get shuffledCopy => [...this]..shuffle();
}

extension IntegerMath on int {
  bool get isPrime {
    if (this < 2) return false;
    for (int i = 2; i <= sqrt(this); i++) {
      if (this % i == 0) return false;
    }
    return true;
  }
  
  String get ordinal {
    if (this % 100 >= 11 && this % 100 <= 13) return '${this}th';
    switch (this % 10) {
      case 1: return '${this}st';
      case 2: return '${this}nd';
      case 3: return '${this}rd';
      default: return '${this}th';
    }
  }
}

void main() {
  // String extensions
  var email = 'user@example.com';
  var invalidEmail = 'not-an-email';
  var numberString = '123.45';
  var text = 'hello there!';
  
  print('$email is valid email: ${email.isValidEmail}');
  print('$invalidEmail is valid email: ${invalidEmail.isValidEmail}');
  print('$numberString is numeric: ${numberString.isNumeric}');
  print('Capitalized: ${text.capitalized}');
  
  // List extensions
  var numbers = [1, 2, 3, 4, 5];
  var emptyList = <int>[];
  
  print('First number: ${numbers.safeFirst}');
  print('Empty list first: ${emptyList.safeFirst}');
  print('Shuffled: ${numbers.shuffledCopy}');
  
  // Integer extensions
  var testNumbers = [2, 7, 15, 17, 21];
  for (var num in testNumbers) {
    print('$num is prime: ${num.isPrime}, ordinal: ${num.ordinal}');
  }
}
```

Extension methods provide a clean way to add functionality without inheritance  
or composition. They integrate seamlessly with the type system and support  
method chaining, making code more expressive and maintainable.  

**Output:**
```
user@example.com is valid email: true
not-an-email is valid email: false
123.45 is numeric: true
Capitalized: Hello there!
First number: 1
Empty list first: null
Shuffled: [3, 1, 5, 2, 4]
2 is prime: true, ordinal: 2nd
7 is prime: true, ordinal: 7th
15 is prime: false, ordinal: 15th
17 is prime: true, ordinal: 17th
21 is prime: false, ordinal: 21st
```

## Variable Destructuring with Records

Dart 3.0's records enable powerful destructuring capabilities, allowing  
multiple values to be returned from functions and elegantly unpacked into  
individual variables.  

```dart
// Function returning a record
(String, int, double) getPersonInfo() {
  return ('Emma', 24, 5.6);
}

// Function returning named record
({String name, int age, List<String> skills}) getEmployeeData() {
  return (
    name: 'Frank', 
    age: 29, 
    skills: ['Dart', 'Flutter', 'Firebase']
  );
}

// Complex nested records
(({int x, int y}), String) getCoordinateWithLabel() {
  return ((x: 100, y: 200), 'Center Point');
}

void main() {
  // Simple record destructuring
  var (personName, personAge, height) = getPersonInfo();
  print('Person: $personName, Age: $personAge, Height: ${height}ft');
  
  // Named record destructuring
  var (:name, :age, :skills) = getEmployeeData();
  print('Employee: $name, Age: $age');
  print('Skills: ${skills.join(', ')}');
  
  // Partial destructuring with wildcards
  var (empName, _, empSkills) = getEmployeeData();
  print('Name: $empName, Skills count: ${empSkills.length}');
  
  // Nested record destructuring
  var ((:x, :y), label) = getCoordinateWithLabel();
  print('Coordinates: ($x, $y), Label: $label');
  
  // Record variable assignment
  var point = (x: 50, y: 75);
  var distance = (from: point, to: (x: 100, y: 150));
  print('Distance from ${distance.from} to ${distance.to}');
  
  // Record in collections
  var people = [
    (name: 'Alice', age: 30),
    (name: 'Bob', age: 25),
    (name: 'Charlie', age: 35),
  ];
  
  for (var (:name, :age) in people) {
    print('$name is $age years old');
  }
}
```

Records provide a lightweight way to group related data without creating  
formal classes. They support both positional and named fields, making them  
perfect for function returns and temporary data structures.  

**Output:**
```
Person: Emma, Age: 24, Height: 5.6ft
Employee: Frank, Age: 29
Skills: Dart, Flutter, Firebase
Name: Frank, Skills count: 3
Coordinates: (100, 200), Label: Center Point
Distance from (x: 50, y: 75) to (x: 100, y: 150)
Alice is 30 years old
Bob is 25 years old
Charlie is 35 years old
```

## Pattern Matching with Variables

Dart 3.0's enhanced pattern matching enables sophisticated variable assignments  
based on data structure patterns, providing powerful control flow and data  
extraction capabilities.  

```dart
sealed class Shape {}
class Circle extends Shape {
  final double radius;
  Circle(this.radius);
}
class Rectangle extends Shape {
  final double width, height;
  Rectangle(this.width, this.height);
}
class Triangle extends Shape {
  final double base, height;
  Triangle(this.base, this.height);
}

void main() {
  var shapes = <Shape>[
    Circle(5.0),
    Rectangle(4.0, 6.0),
    Triangle(3.0, 4.0),
  ];
  
  // Pattern matching with switch expressions
  for (var shape in shapes) {
    var area = switch (shape) {
      Circle(radius: var r) => 3.14159 * r * r,
      Rectangle(width: var w, height: var h) => w * h,
      Triangle(base: var b, height: var h) => 0.5 * b * h,
    };
    
    var description = switch (shape) {
      Circle(radius: var r) => 'Circle with radius $r',
      Rectangle(width: var w, height: var h) => 'Rectangle ${w}x$h',
      Triangle(base: var b, height: var h) => 'Triangle base=$b, height=$h',
    };
    
    print('$description has area: ${area.toStringAsFixed(2)}');
  }
  
  print('---');
  
  // Pattern matching with collections
  var data = [1, 2, 3, 4, 5];
  var result = switch (data) {
    [] => 'Empty list',
    [var single] => 'Single element: $single',
    [var first, var second] => 'Two elements: $first, $second',
    [var first, ...var rest] when rest.isNotEmpty => 
      'First: $first, Rest: $rest (${rest.length} items)',
    _ => 'Other pattern',
  };
  print('List pattern: $result');
  
  // Pattern matching with records
  var userRecord = (name: 'Grace', age: 28, isAdmin: true);
  var message = switch (userRecord) {
    (name: var n, age: var a, isAdmin: true) => '$n (age $a) is an admin',
    (name: var n, age: var a, isAdmin: false) => '$n (age $a) is a regular user',
  };
  print(message);
  
  // Guard clauses in patterns
  var numbers = [10, 20, 30];
  var category = switch (numbers) {
    [var first, ...] when first > 15 => 'Starts with large number',
    [var first, ...] when first < 5 => 'Starts with small number',
    _ => 'Starts with medium number',
  };
  print('Category: $category');
}
```

Pattern matching with variables creates expressive and type-safe code that  
handles complex data structures elegantly. Guard clauses add conditional  
logic to patterns, while destructuring extracts values efficiently.  

**Output:**
```
Circle with radius 5.0 has area: 78.54
Rectangle 4.0x6.0 has area: 24.00
Triangle base=3.0, height=4.0 has area: 6.00
---
List pattern: First: 1, Rest: [2, 3, 4, 5] (4 items)
Grace (age 28) is an admin
Category: Starts with small number
```

## Variable Lifetime and Memory Management

Understanding variable lifetime and memory management helps write efficient  
Dart code. Variables have different scopes and lifetimes depending on where  
and how they are declared.  

```dart
class ResourceManager {
  static int instanceCount = 0;
  final int id;
  late final String _resourceName;
  
  ResourceManager() : id = ++instanceCount {
    _resourceName = 'Resource_$id';
    print('Created $_resourceName');
  }
  
  void performWork() {
    // Local variable - exists only during method execution
    var startTime = DateTime.now();
    
    // Simulate work
    var sum = 0;
    for (int i = 0; i < 1000000; i++) {
      sum += i;
    }
    
    var endTime = DateTime.now();
    var duration = endTime.difference(startTime);
    print('$_resourceName completed work in ${duration.inMicroseconds}μs');
  }
  
  @override
  String toString() => _resourceName;
}

// Global variable - lives for entire program duration
late final ResourceManager globalResource;

void demonstrateScopes() {
  print('\n=== Function Scope Demo ===');
  
  // Function-level variable
  var localManager = ResourceManager();
  
  // Block scope
  {
    var blockScoped = ResourceManager();
    blockScoped.performWork();
  } // blockScoped is eligible for garbage collection here
  
  localManager.performWork();
} // localManager is eligible for garbage collection here

void demonstrateStaticVsInstance() {
  print('\n=== Static vs Instance Variables ===');
  
  // Create multiple instances
  var managers = <ResourceManager>[];
  for (int i = 0; i < 3; i++) {
    managers.add(ResourceManager());
  }
  
  print('Created ${ResourceManager.instanceCount} total instances');
  
  // Instance variables exist per object
  for (var manager in managers) {
    print('Manager: $manager');
  }
  
  // Clear references to allow garbage collection
  managers.clear();
  print('Cleared manager references');
}

void main() {
  print('=== Variable Lifetime Demo ===');
  
  // Initialize global variable
  globalResource = ResourceManager();
  print('Global resource: $globalResource');
  
  demonstrateScopes();
  demonstrateStaticVsInstance();
  
  // Memory-conscious collection handling
  print('\n=== Memory Management ===');
  var largeList = List.generate(100, (i) => 'Item $i');
  print('Created list with ${largeList.length} items');
  
  // Process and clear to free memory
  var processedCount = largeList.where((item) => item.contains('1')).length;
  print('Processed items containing "1": $processedCount');
  
  largeList.clear(); // Explicit cleanup
  print('List cleared, length: ${largeList.length}');
  
  // Global resource still accessible
  globalResource.performWork();
  print('Program ending - global resource will be cleaned up');
}
```

Variable lifetime management involves understanding scope boundaries, static  
versus instance variables, and explicit cleanup when needed. Proper memory  
management prevents memory leaks and ensures efficient resource utilization  
in long-running applications.  

**Output:**
```
=== Variable Lifetime Demo ===
Created Resource_1
Global resource: Resource_1

=== Function Scope Demo ===
Created Resource_2
Created Resource_3
Resource_3 completed work in 1234μs
Resource_2 completed work in 987μs

=== Static vs Instance Variables ===
Created Resource_4
Created Resource_5
Created Resource_6
Created 6 total instances
Manager: Resource_4
Manager: Resource_5
Manager: Resource_6
Cleared manager references

=== Memory Management ===
Created list with 100 items
Processed items containing "1": 19
List cleared, length: 0
Resource_1 completed work in 876μs
Program ending - global resource will be cleaned up
```

## Best Practices

Following these guidelines will help you write maintainable and efficient Dart  
code with proper variable usage:  

- **Type Inference**: Use `var` for local variables when the type is obvious  
  from context, but prefer explicit types for public APIs and complex logic.  

- **Immutability**: Choose `final` for variables that shouldn't change after  
  initialization. Use `const` for compile-time constants to improve performance.  

- **Null Safety**: Avoid nullable types (`?`) unless null is a legitimate and  
  meaningful state for the variable. Use null-aware operators appropriately.  

- **Scope Management**: Keep variable scope as narrow as possible. Declare  
  variables close to where they are used to improve code readability.  

- **Naming Conventions**: Use descriptive names following lowerCamelCase for  
  variables. Avoid abbreviations and single-letter names except for loop  
  counters.  

- **Late Initialization**: Use `late` sparingly and only when initialization  
  must be deferred. Ensure late variables are initialized before first access.  

- **Memory Awareness**: Clear large collections and release expensive resources  
  explicitly when they are no longer needed to prevent memory leaks.  

- **Pattern Matching**: Leverage destructuring and pattern matching to write  
  more expressive code when working with complex data structures.
