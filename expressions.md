
# Dart Expressions

Expressions form the core of Dart programs, enabling computations that yield  
values. This tutorial explores Dart expressions, detailing operator types,  
evaluation rules, and unique features like cascade notation.  

## Dart Expressions Overview

In Dart, an expression is a code construct that evaluates to a value, such as  
literals, variables, operators, or function calls. Dart offers a diverse set of  
operators and expression types, facilitating concise and expressive code for  
various programming tasks.  

| Expression Type | Description | Dart Code Example |
|------------------|-------------|-------------------|
| Literal | Direct value representation | `5, 'hello', true` |
| Arithmetic | Mathematical operations | `a + b, x * y` |
| Relational | Comparison operations | `a > b, x == y` |
| Logical | Boolean operations | `a && b, !flag` |
| Conditional | Ternary operator | `a ? b : c` |
| Cascade | Sequential operations | `obj..a()..b()` |
| Assignment | Value assignment | `a = 5, b += 2` |
| String Interpolation | Embed expressions in strings | `'Hello $name'` |
| Type Test | Check object type | `obj is String` |
| Type Cast | Convert object type | `obj as int` |
| Collection If | Conditional collection elements | `[if (flag) item]` |
| Collection For | Loop in collections | `[for (i in list) i * 2]` |
| Record | Structured data | `(x: 1, y: 2)` |
| Pattern Matching | Match data patterns | `switch (value) { case 1 => 'one' }` |
| Function | Anonymous function | `(x) => x * 2` |
| Arrow Function | Concise function syntax | `void greet() => print('Hi')` |
| Await | Asynchronous operation | `await Future.value(42)` |
| Yield | Generator function | `yield value` |
| Throw | Exception throwing | `throw Exception('Error')` |
| Extension Method | Extended functionality | `'hello'.capitalized` |
| Generic | Type parameterization | `List<T>` |
| Null Assertion | Force non-null | `value!` |
| Spread Operator | Collection expansion | `[...list1, ...list2]` |
| Bitwise | Bit manipulation | `a & b, x << 2` |

## Literal Expressions

Literal expressions directly represent fixed values in code, supporting various  
data types in Dart, including integers (e.g., 42), doubles (e.g., 3.14),  
strings (using single, double, or triple quotes), booleans (true, false), lists,  
maps, and the null value. These literals provide a straightforward way to embed  
constant values in programs.  

```dart
void main() {

  // Numeric literals
  int integer = 42;
  double floating = 3.14159;

  // String literals
  String singles = 'Single quotes';
  String doubles = "Double quotes";
  String multiline = '''
    Multi-line
    string
  ''';

  // Boolean literals
  bool truthy = true;
  bool falsy = false;

  // List and Map literals
  List<int> numbers = [1, 2, 3];
  Map<String, int> ages = {'Alice': 25, 'Bob': 30};

  print(integer);
  print(floating);
  print(singles);
  print(doubles);
  print(multiline);
  print(truthy);
  print(falsy);
  print(numbers);
  print(ages);
}
```

This example showcases various Dart literals, demonstrating how to define and  
print integers, doubles, strings (including multiline), booleans, lists, and  
maps, highlighting their direct use in code.  

```
$ dart run literals.dart
42
3.14159
Single quotes
Double quotes
    Multi-line
    string
  
[1, 2, 3]
{Alice: 25, Bob: 30}
```

## Arithmetic Expressions

Dart supports standard arithmetic operations through operators like addition  
(+), subtraction (-), multiplication (*), division (/), integer division (~/),  
modulo (%), and unary negation (-). The division operator always returns a  
double, while increment (++) and decrement (--) operators modify values. These  
operators enable mathematical computations, including mixed-type operations.  

```dart
void main() {

  int a = 10;
  int b = 3;
  double c = 5.0;
  
  // Basic operations
  print('Addition: ${a + b}');
  print('Subtraction: ${a - b}');
  print('Multiplication: ${a * b}');
  print('Division: ${a / b}');
  print('Integer division: ${a ~/ b}');
  print('Modulo: ${a % b}');
  print('Unary minus: ${-a}');
  
  // Mixed type arithmetic
  print('Mixed addition: ${a + c}');
  
  // Increment/decrement
  int counter = 0;
  print('Post-increment: ${counter++}');
  print('After increment: $counter');
  print('Pre-decrement: ${--counter}');
}
```

This example demonstrates arithmetic operations, including basic calculations,  
mixed-type addition, and increment/decrement operations, illustrating how Dart  
handles mathematical expressions.  

```
$ dart run arithmetic.dart
Addition: 13
Subtraction: 7
Multiplication: 30
Division: 3.3333333333333335
Integer division: 3
Modulo: 1
Unary minus: -10
Mixed addition: 15.0
Post-increment: 0
After increment: 1
Pre-decrement: 0
```

## Relational and Logical Expressions

Relational operators, such as equal (==), not equal (!=), greater than (>),  
and less than (<), compare values to produce boolean results. Logical  
operators, including AND (&&), OR (||), and NOT (!), combine boolean  
expressions. Dart employs short-circuit evaluation, skipping unnecessary  
operations (e.g., in && if the first operand is false), enhancing efficiency and  
safety in logical expressions.  

```dart
void main() {

  int a = 5;
  int b = 10;
  
  // Relational operators
  print('Equal: ${a == b}');
  print('Not equal: ${a != b}');
  print('Greater than: ${a > b}');
  print('Less than: ${a < b}');
  print('Greater or equal: ${a >= b}');
  print('Less or equal: ${a <= b}');
  
  // Logical operators
  bool x = true;
  bool y = false;
  
  print('AND: ${x && y}');
  print('OR: ${x || y}');
  print('NOT: ${!x}');
  
  // Short-circuit evaluation
  String? name;
  bool isValid = name != null && name.isNotEmpty;
  print('Is valid: $isValid');
}
```

This example illustrates relational comparisons and logical operations,  
including a demonstration of short-circuit evaluation with a nullable string,  
showing how Dart ensures safe and efficient expression evaluation.  

```
$ dart run relational_logical.dart
Equal: false
Not equal: true
Greater than: false
Less than: true
Greater or equal: false
Less or equal: true
AND: false
OR: true
NOT: false
Is valid: false
```

## Conditional Expressions

Dart's conditional expressions provide concise decision-making constructs. The  
ternary operator (?:) acts as a compact if-else statement, while the null-  
coalescing operator (??) supplies default values for null variables. Null-aware  
operators, such as conditional property access (?.) and null-aware assignment  
(??=), ensure safe handling of nullable types, making code robust and succinct.  

```dart
void main() {

  int age = 20;
  
  // Ternary operator
  String status = age >= 18 ? 'Adult' : 'Minor';
  print('Status: $status');
  
  // Null-coalescing operator
  String? name;
  String displayName = name ?? 'Guest';
  print('Welcome, $displayName');
  
  // Conditional property access
  List<int>? numbers;
  int? first = numbers?.first;
  print('First number: $first');
  
  // Null-aware assignment
  name ??= 'Anonymous';
  print('Name: $name');
}
```

This example showcases conditional expressions, including the ternary operator  
for age-based status, null-coalescing for default names, and null-aware  
operators for safe list access and assignment, demonstrating concise control  
flow.  

```
$ dart run conditional.dart
Status: Adult
Welcome, Guest
First number: null
Name: Anonymous
```

## Cascade Notation

Dart's cascade notation (..) enables multiple operations on the same object in a  
fluent manner, returning the original object after each operation. This feature  
is particularly useful for builder-style APIs and simplifies code by reducing  
repetitive object references, enhancing readability and maintainability in  
sequential method calls.  

```dart
class Person {
  String name = '';
  int age = 0;
  
  void greet() => print('Hello, $name!');
  void birthday() => age++;
}

void main() {

  // Without cascade
  Person p1 = Person();
  p1.name = 'Alice';
  p1.age = 30;
  p1.greet();
  
  // With cascade
  Person p2 = Person()
    ..name = 'Bob'
    ..age = 25
    ..greet()
    ..birthday();
    
  print('Bob\'s age: ${p2.age}');
  
  // Nested cascades
  StringBuffer sb = StringBuffer()
    ..write('Hello')
    ..write(' ')
    ..writeAll(['Dart', '!'], ' ');
  
  print(sb.toString());
}
```

This example contrasts traditional and cascade notation for object operations,  
using a Person class and StringBuffer to show how cascades streamline multiple  
method calls and property assignments.  

```
$ dart run cascade.dart
Hello, Alice!
Hello, Bob!
Bob's age: 26
Hello Dart !
```

## Assignment Expressions

Assignment expressions in Dart store values in variables using the basic  
assignment operator (=) or compound operators like +=, *=, and ~/=. These  
expressions evaluate to the assigned value, enabling chained assignments.  
Null-aware assignment (??=) assigns a value only if the variable is null,  
providing a safe way to set defaults.  

```dart
void main() {

  // Simple assignment
  int x = 5;
  print('x = $x');
  
  // Compound assignment
  x += 3;
  print('x += 3 → $x');
  
  x ~/= 2;
  print('x ~/= 2 → $x');
  
  // Assignment as expression
  int y;
  print('y = ${y = x * 2}');
  
  // Null-aware assignment
  int? z;
  z ??= 10;
  print('z = $z');
}
```

This example demonstrates simple and compound operations, assignment as an  
expression, and null-aware assignment, illustrating how Dart manages variable  
updates and default values.  

```
$ dart run assignment.dart
x = 5
x += 3 → 8
x ~/= 2 → 4
y = 8
z = 10
```

## Expression Evaluation Order

Dart evaluates expressions based on operator precedence and associativity.  
Operators like multiplication have higher precedence than addition, while  
parentheses override default precedence. Most operators are left-associative,  
processing from left to right, except for assignment and conditional operators,  
which are right-associative rules, ensuring predictable computation order.  

```dart
void main() {

  // Operator precedence
  int result = 2 + 3 * 4;
  print('2 + 3 * 4 = $result');
  
  // Parentheses change order
  result = (2 + 3) * 4;
  print('(2 + 3) * 4 = $result');
  
  // Left-associative operators
  result = 10 - 4 - 2;
  print('10 - 4 - 2 = $result');
  
  // Right-associative operators
  bool a = false, b = true, c = false;
  bool logical = a && b || c;
  print('a && b || c = $logical');
}
```

This example illustrates operator precedence, the effect of parentheses, and  
associativity in expression evaluation, showing how Dart processes complex  
expressions systematically.  

```
$ dart run evaluation_order.dart
2 + 3 * 4 = 14
(2 + 3) * 4 = 20
10 - 4 - 2 = 4
a && b || c = false
```

## Bitwise Expressions

Dart's bitwise operators manipulate integer bits, enabling low-level operations  
like setting flags or optimizing computations. Operators include AND (&), OR  
(|), XOR (^), NOT (~), left shift (<<), and right shift (>>). These  
are useful in scenarios like graphics processing or protocol implementations,  
where direct bit manipulation is required.  

```dart
void main() {

  int a = 5; // Binary: 0101
  int b = 3; // Binary: 0011
  
  // Bitwise operators
  print('Bitwise AND: ${a & b}'); // 0101 & 0011 = 0001
  print('Bitwise OR: ${a | b}'); // 0101 | 0011 = 0111
  print('Bitwise XOR: ${a ^ b}'); // 0101 ^ 0011 = 0110
  print('Bitwise NOT: ${~a}'); // ~0101 = ...1010 (inverts bits)
  print('Left shift: ${a << 1}'); // 0101 << 1 = 1010
  print('Right shift: ${a >> 1}'); // 0101 >> 1 = 0010
  
  // Using bitwise for flags
  int read = 1; // 0001
  int write = 2; // 0010
  int permissions = read | write; // 0011
  print('Has read: ${(permissions & read) != 0}');
}
```

This example demonstrates bitwise operations on integers, showing AND, OR, XOR,  
NOT, and shift operations, along with a practical use case for managing  
permission flags, highlighting their utility in low-level programming.  

```
$ dart run bitwise.dart
Bitwise AND: 1
Bitwise OR: 7
Bitwise XOR: 6
Bitwise NOT: -6
Left shift: 10
Right shift: 2
Has read: true
```

## Spread Operator Expressions

The spread operator (...) in Dart expands elements of a collection, enabling  
concise merging or copying of lists, sets, or maps. The null-aware spread  
operator (...?) safely handles null collections, preventing runtime errors. This  
feature simplifies collection manipulation in expressions, enhancing code  
readability and flexibility.  

```dart
void main() {

  // Spread operator with lists
  List<int> part1 = [1, 2];
  List<int> part2 = [3, 4];
  List<int> combined = [...part1, ...part2, 5];
  print('Combined list: $combined');
  
  // Null-aware spread
  List<int>? optional;
  List<int> safeList = [...part1, ...?optional, 6];
  print('Safe list: $safeList');
  
  // Spread with maps
  Map<String, int> map1 = {'a': 1, 'b': 2};
  Map<String, int> map2 = {'c': 3};
  Map<String, int> merged = {...map1, ...map2, 'd': 4};
  print('Merged map: $merged');
  
  // Nested spread
  List<List<int>> nested = [
    [1, 2],
    [3, 4]
  ];
  List<int> flattened = [...nested[0], ...nested[1]];
  print('Flattened: $flattened');
}
```

This example showcases the spread operator for combining lists and maps, the  
null-aware spread for safe handling of nullable collections, and flattening  
nested lists, demonstrating its power in collection expressions.  

```
$ dart run spread.dart
Combined list: [1, 2, 3, 4, 5]
Safe list: [1, 2, 6]
Merged map: {a: 1, b: 2, c: 3, d: 4}
Flattened: [1, 2, 3, 4]
```

## Switch Expressions

Dart's `switch` expressions provide a concise way to select a value  
based on multiple conditions. Unlike traditional `switch` statements,  
switch expressions can be used directly in assignments and return values, making  
code more expressive and reducing boilerplate. Dart supports pattern matching  
and null safety in switch expressions, allowing for powerful and readable  
branching logic.  

```dart
void main() {

  var grade = 'B';
  var result = switch (grade) {
    'A' => 'Excellent',
    'B' => 'Good',
    'C' => 'Average',
    'D' => 'Below average',
    'F' => 'Fail',
    _ => 'Unknown',
  };
  print('Grade: $grade, Result: $result');

  // Pattern matching with types
  Object value = 42;
  var type = switch (value) {
    int i => 'Integer',
    double d => 'Double',
    String s => 'String',
    _ => 'Other',
  };
  print('Type: $type');
}
```

This example demonstrates Dart's switch expressions for value selection and type  
pattern matching. The first switch maps a grade to a description, while the  
second uses type patterns to determine the type of a value. Switch expressions  
improve code clarity and reduce the need for verbose if-else chains.  

## String Interpolation Expressions

String interpolation allows embedding expressions directly within string  
literals using the dollar sign ($) syntax. For simple variables, use $variable,  
while complex expressions require ${expression} syntax. This feature provides  
a clean and readable way to construct strings dynamically, avoiding  
concatenation operators and improving code maintainability.  

```dart
void main() {
  String name = 'Alice';
  int age = 25;
  double height = 1.75;
  
  // Simple interpolation
  print('Hello there, $name!');
  
  // Expression interpolation
  print('$name is ${age + 5} years old in 5 years');
  print('Height in cm: ${height * 100}');
  
  // Conditional interpolation
  String status = age >= 18 ? 'adult' : 'minor';
  print('$name is an $status');
  
  // Method call interpolation
  String upper = 'hello';
  print('Uppercase: ${upper.toUpperCase()}');
  
  // Null-aware interpolation
  String? nickname;
  print('Nickname: ${nickname ?? 'None'}');
}
```

This example demonstrates various string interpolation techniques, including  
simple variable interpolation, expression evaluation within strings, and  
null-safe interpolation patterns, showcasing Dart's flexible string  
construction capabilities.  

```
$ dart run string_interpolation.dart
Hello there, Alice!
Alice is 30 years old in 5 years
Height in cm: 175.0
Alice is an adult
Uppercase: HELLO
Nickname: None
```

## Type Test Expressions

Type test expressions use the `is` and `is!` operators to check object types  
at runtime. These expressions return boolean values and are particularly useful  
for type-safe downcasting and conditional logic based on object types. Dart's  
type system promotes variables to more specific types after successful type  
tests, enabling safe access to type-specific members.  

```dart
void main() {
  Object value = 'Hello there!';
  
  // Basic type tests
  print('Is String: ${value is String}');
  print('Is not int: ${value is! int}');
  
  // Type promotion
  if (value is String) {
    print('Length: ${value.length}'); // value promoted to String
    print('Uppercase: ${value.toUpperCase()}');
  }
  
  // Testing nullable types
  String? nullable = 'test';
  print('Is non-null String: ${nullable is String}');
  
  // Generic type tests
  List<int> numbers = [1, 2, 3];
  print('Is List<int>: ${numbers is List<int>}');
  print('Is List: ${numbers is List}');
  
  // Pattern with type test
  dynamic data = 42;
  String result = switch (data) {
    int i when i > 0 => 'Positive integer',
    String s => 'Text: $s',
    _ => 'Other type'
  };
  print(result);
}
```

This example shows type testing with `is` and `is!` operators, demonstrates  
type promotion for safe member access, and illustrates testing nullable and  
generic types, highlighting Dart's runtime type checking capabilities.  

```
$ dart run type_test.dart
Is String: true
Is not int: true
Length: 12
Uppercase: HELLO THERE!
Is non-null String: true
Is List<int>: true
Is List: true
Positive integer
```

## Type Cast Expressions

Type cast expressions use the `as` operator to explicitly convert objects to  
specific types. Unlike type tests, casts assume the object is of the target  
type and throw a TypeError if the cast fails. Casts are useful when you know  
an object's type but need to inform the type system, especially after type  
tests or when working with dynamic types.  

```dart
void main() {
  Object value = 'Hello there!';
  
  // Basic type cast
  String text = value as String;
  print('Casted string: $text');
  
  // Safe cast after type test
  if (value is String) {
    String safeText = value as String;
    print('Safe cast: ${safeText.length}');
  }
  
  // Casting collections
  List<Object> objects = ['a', 'b', 'c'];
  List<String> strings = objects.cast<String>();
  print('Casted list: $strings');
  
  // Nullable cast
  Object? nullable = 'test';
  String? nullableString = nullable as String?;
  print('Nullable cast: $nullableString');
  
  // Cast in expressions
  dynamic data = 42;
  print('Square: ${(data as int) * (data as int)}');
  
  try {
    // This will throw a TypeError
    int number = 'not a number' as int;
  } catch (e) {
    print('Cast error: ${e.runtimeType}');
  }
}
```

This example demonstrates type casting with the `as` operator, shows safe  
casting practices, collection casting, nullable casts, and error handling  
for invalid casts, illustrating when and how to use explicit type conversion.  

```
$ dart run type_cast.dart
Casted string: Hello there!
Safe cast: 12
Casted list: [a, b, c]
Nullable cast: test
Square: 1764
Cast error: TypeError
```

## Collection If Expressions

Collection if expressions enable conditional inclusion of elements in list,  
set, and map literals. Using `if` conditions within collection literals  
provides a concise way to build collections dynamically based on runtime  
conditions, eliminating the need for separate conditional logic and temporary  
variables.  

```dart
void main() {
  bool includeExtra = true;
  bool isDebug = false;
  String? optionalItem = 'bonus';
  
  // List with conditional elements
  List<String> items = [
    'item1',
    'item2',
    if (includeExtra) 'extra',
    if (optionalItem != null) optionalItem,
    if (isDebug) 'debug_item',
  ];
  print('Items: $items');
  
  // Set with conditions
  Set<int> numbers = {
    1,
    2,
    if (includeExtra) 3,
    if (numbers.length < 5) 4, // Note: this creates a new set
  };
  
  // Map with conditional entries
  Map<String, dynamic> config = {
    'version': '1.0',
    'production': !isDebug,
    if (isDebug) 'debug': true,
    if (includeExtra) 'features': ['extra1', 'extra2'],
  };
  print('Config: $config');
  
  // Nested collection if
  List<List<int>> matrix = [
    [1, 2],
    if (includeExtra) [
      3,
      4,
      if (isDebug) 5,
    ],
  ];
  print('Matrix: $matrix');
}
```

This example showcases collection if expressions in lists, sets, and maps,  
demonstrating conditional element inclusion, nested conditions, and practical  
use cases for building dynamic collections based on runtime conditions.  

```
$ dart run collection_if.dart
Items: [item1, item2, extra, bonus]
Config: {version: 1.0, production: true, features: [extra1, extra2]}
Matrix: [[1, 2], [3, 4]]
```

## Collection For Expressions

Collection for expressions allow iteration within collection literals,  
generating elements by transforming or filtering existing collections. This  
feature combines the power of loops with collection creation, enabling  
functional-style programming and eliminating the need for separate  
iteration and collection building steps.  

```dart
void main() {
  List<int> base = [1, 2, 3, 4, 5];
  
  // List comprehension-style generation
  List<int> doubled = [
    for (int x in base) x * 2
  ];
  print('Doubled: $doubled');
  
  // With filtering
  List<int> evenDoubled = [
    for (int x in base)
      if (x % 2 == 0) x * 2
  ];
  print('Even doubled: $evenDoubled');
  
  // Nested iteration
  List<String> pairs = [
    for (int i = 1; i <= 2; i++)
      for (int j = 1; j <= 2; j++)
        '($i,$j)'
  ];
  print('Pairs: $pairs');
  
  // Set generation
  Set<String> words = {'hello', 'world', 'dart'};
  Set<int> lengths = {
    for (String word in words) word.length
  };
  print('Lengths: $lengths');
  
  // Map generation
  Map<int, String> numberWords = {
    for (int i in base)
      if (i <= 3) i: 'Number $i'
  };
  print('Number words: $numberWords');
  
  // Transform existing map
  Map<String, int> original = {'a': 1, 'b': 2, 'c': 3};
  Map<String, int> transformed = {
    for (var entry in original.entries)
      entry.key.toUpperCase(): entry.value * 10
  };
  print('Transformed: $transformed');
}
```

This example demonstrates collection for expressions across different  
collection types, shows filtering and transformation patterns, nested  
iteration, and map generation, highlighting the versatility of inline  
collection building.  

```
$ dart run collection_for.dart
Doubled: [2, 4, 6, 8, 10]
Even doubled: [4, 8]
Pairs: [(1,1), (1,2), (2,1), (2,2)]
Lengths: {5, 4}
Number words: {1: Number 1, 2: Number 2, 3: Number 3}
Transformed: {A: 10, B: 20, C: 30}
```

## Record Expressions

Record expressions create structured data using positional and named fields  
within parentheses. Records provide a lightweight way to group related values  
without defining custom classes, making them ideal for returning multiple  
values from functions or creating temporary data structures.  

```dart
void main() {
  // Basic record creation
  var point = (3.14, 2.71);
  print('Point: $point');
  print('X: ${point.$1}, Y: ${point.$2}');
  
  // Named fields
  var person = (name: 'Alice', age: 30, height: 1.75);
  print('Person: $person');
  print('Name: ${person.name}, Age: ${person.age}');
  
  // Mixed positional and named
  var result = (42, message: 'Success', isValid: true);
  print('Result: ${result.$1}, ${result.message}, ${result.isValid}');
  
  // Nested records
  var nested = (
    coordinates: (x: 10, y: 20),
    metadata: (created: DateTime.now(), version: '1.0')
  );
  print('X: ${nested.coordinates.x}');
  print('Version: ${nested.metadata.version}');
  
  // Record destructuring
  var (x, y) = (100, 200);
  print('Destructured: x=$x, y=$y');
  
  // Function returning record
  (String, int) getUserInfo() => ('Bob', 25);
  var (username, userAge) = getUserInfo();
  print('User: $username, Age: $userAge');
  
  // Record in collections
  List<({String name, int score})> scores = [
    (name: 'Alice', score: 95),
    (name: 'Bob', score: 87),
    (name: 'Charlie', score: 92),
  ];
  print('High scorers: ${scores.where((s) => s.score > 90).toList()}');
}
```

This example showcases record creation with positional and named fields,  
nested records, destructuring patterns, function returns, and records in  
collections, demonstrating their versatility for structured data handling.  

```
$ dart run record.dart
Point: (3.14, 2.71)
X: 3.14, Y: 2.71
Person: (name: Alice, age: 30, height: 1.75)
Name: Alice, Age: 30
Result: 42, Success, true
X: 10
Version: 1.0
Destructured: x=100, y=200
User: Bob, Age: 25
High scorers: [(name: Alice, score: 95), (name: Charlie, score: 92)]
```

## Function Expressions

Function expressions create anonymous functions that can be assigned to  
variables, passed as arguments, or used inline. These expressions support  
both traditional function syntax and arrow syntax for single expressions.  
Function expressions enable functional programming patterns and higher-order  
function usage in Dart.  

```dart
void main() {
  // Anonymous function expression
  var multiply = (int x, int y) {
    return x * y;
  };
  print('Multiply: ${multiply(5, 3)}');
  
  // Arrow function expression
  var square = (int x) => x * x;
  print('Square: ${square(4)}');
  
  // Function with closure
  int counter = 0;
  var increment = () => ++counter;
  print('Count: ${increment()}, ${increment()}, ${increment()}');
  
  // Higher-order functions
  List<int> numbers = [1, 2, 3, 4, 5];
  var doubled = numbers.map((x) => x * 2).toList();
  var evens = numbers.where((x) => x % 2 == 0).toList();
  print('Doubled: $doubled');
  print('Evens: $evens');
  
  // Function as parameter
  void executeFunction(Function(int) func, int value) {
    print('Executing: ${func(value)}');
  }
  executeFunction((x) => x * x * x, 3);
  
  // Nested function expressions
  var calculator = (String operation) {
    return switch (operation) {
      'add' => (int a, int b) => a + b,
      'subtract' => (int a, int b) => a - b,
      'multiply' => (int a, int b) => a * b,
      _ => (int a, int b) => 0,
    };
  };
  var adder = calculator('add');
  print('Addition result: ${adder(10, 5)}');
}
```

This example demonstrates anonymous function expressions, arrow functions,  
closures, higher-order functions, and nested function expressions,  
showcasing Dart's functional programming capabilities and flexible  
function handling.  

```
$ dart run function_expressions.dart
Multiply: 15
Square: 16
Count: 1, 2, 3
Doubled: [2, 4, 6, 8, 10]
Evens: [2, 4]
Executing: 27
Addition result: 15
```

## Await Expressions

Await expressions pause function execution until a Future completes,  
extracting the resolved value for synchronous-style programming with  
asynchronous operations. Await can only be used within async functions  
and provides error handling through try-catch blocks, making asynchronous  
code more readable and maintainable.  

```dart
import 'dart:async';

// Simulate async operations
Future<String> fetchUserName() async {
  await Future.delayed(Duration(milliseconds: 100));
  return 'Alice';
}

Future<int> fetchUserAge() async {
  await Future.delayed(Duration(milliseconds: 150));
  return 25;
}

Future<List<String>> fetchUserHobbies() async {
  await Future.delayed(Duration(milliseconds: 200));
  return ['reading', 'coding', 'hiking'];
}

void main() async {
  print('Starting async operations...');
  
  // Basic await expression
  String name = await fetchUserName();
  print('User name: $name');
  
  // Await in expressions
  print('Greeting: Hello there, ${await fetchUserName()}!');
  
  // Multiple awaits
  int age = await fetchUserAge();
  List<String> hobbies = await fetchUserHobbies();
  print('$name is $age years old and enjoys: ${hobbies.join(', ')}');
  
  // Await with error handling
  try {
    String result = await Future.error('Network error');
    print('Success: $result');
  } catch (e) {
    print('Caught error: $e');
  }
  
  // Await in collection operations
  List<String> users = ['Alice', 'Bob', 'Charlie'];
  List<int> ages = [];
  for (String user in users) {
    ages.add(await fetchUserAge()); // Simulated user age fetch
  }
  print('Ages: $ages');
  
  // Concurrent operations with Future.wait
  List<Future<String>> futures = [
    fetchUserName(),
    fetchUserName(),
    fetchUserName(),
  ];
  List<String> names = await Future.wait(futures);
  print('All names: $names');
}
```

This example demonstrates await expressions for asynchronous operations,  
error handling with try-catch, awaiting in expressions and loops, and  
concurrent operations with Future.wait, illustrating asynchronous  
programming patterns in Dart.  

```
$ dart run await_expressions.dart
Starting async operations...
User name: Alice
Greeting: Hello there, Alice!
Alice is 25 years old and enjoys: reading, coding, hiking
Caught error: Network error
Ages: [25, 25, 25]
All names: [Alice, Alice, Alice]
```

## Throw Expressions

Throw expressions interrupt normal program flow by raising exceptions.  
They can be used anywhere an expression is expected, making them useful  
for validation, error signaling, and defensive programming. Throw  
expressions never return a value and have the special type Never,  
which is compatible with all other types.  

```dart
class ValidationError extends Error {
  final String message;
  ValidationError(this.message);
  
  @override
  String toString() => 'ValidationError: $message';
}

void main() {
  // Basic throw expression
  try {
    bool condition = false;
    String result = condition ? 'success' : throw Exception('Failed condition');
    print('Result: $result');
  } catch (e) {
    print('Caught: $e');
  }
  
  // Throw in ternary operator
  int divide(int a, int b) {
    return b != 0 ? a ~/ b : throw ArgumentError('Division by zero');
  }
  
  try {
    print('Division: ${divide(10, 2)}');
    print('Division: ${divide(10, 0)}');
  } catch (e) {
    print('Division error: $e');
  }
  
  // Throw in null-coalescing
  String getValue(String? input) {
    return input ?? throw ValidationError('Input cannot be null');
  }
  
  try {
    print('Value: ${getValue('hello')}');
    print('Value: ${getValue(null)}');
  } catch (e) {
    print('Validation error: $e');
  }
  
  // Throw in collection operations
  List<int> numbers = [1, 2, 3, 4, 5];
  try {
    List<int> validated = numbers
        .map((n) => n > 0 ? n : throw ValidationError('Negative number: $n'))
        .toList();
    print('Validated: $validated');
  } catch (e) {
    print('Collection error: $e');
  }
  
  // Throw in switch expression
  String getGrade(int score) {
    return switch (score) {
      >= 90 => 'A',
      >= 80 => 'B',
      >= 70 => 'C',
      >= 60 => 'D',
      _ when score >= 0 => 'F',
      _ => throw RangeError('Invalid score: $score'),
    };
  }
  
  try {
    print('Grade: ${getGrade(85)}');
    print('Grade: ${getGrade(-5)}');
  } catch (e) {
    print('Grade error: $e');
  }
}
```

This example showcases throw expressions in various contexts including  
ternary operators, null-coalescing, collection operations, and switch  
expressions, demonstrating their use for validation and error handling  
throughout different expression types.  

```
$ dart run throw_expressions.dart
Caught: Exception: Failed condition
Division: 5
Division error: Invalid argument(s): Division by zero
Value: hello
Validation error: ValidationError: Input cannot be null
Validated: [1, 2, 3, 4, 5]
Grade: B
Grade error: RangeError (index): Invalid score: -5
```

## Arrow Function Expressions

Arrow function expressions provide a concise syntax for single-expression  
functions using the `=>` operator. They're particularly useful for short  
callback functions, getters, and functional programming patterns. Arrow  
functions automatically return the expression result and cannot contain  
statements, making them ideal for pure functions and transformations.  

```dart
class Point {
  final double x, y;
  Point(this.x, this.y);
  
  // Arrow function methods
  double get magnitude => x * x + y * y;
  Point operator +(Point other) => Point(x + other.x, y + other.y);
  
  @override
  String toString() => 'Point($x, $y)';
}

void main() {
  // Arrow function variables
  var add = (int a, int b) => a + b;
  var greet = (String name) => 'Hello there, $name!';
  var isEven = (int n) => n % 2 == 0;
  
  print('Addition: ${add(5, 3)}');
  print('Greeting: ${greet('Alice')}');
  print('Is even: ${isEven(4)}');
  
  // Arrow functions in collections
  List<int> numbers = [1, 2, 3, 4, 5];
  var squared = numbers.map((x) => x * x).toList();
  var filtered = numbers.where((x) => x > 2).toList();
  var summed = numbers.reduce((a, b) => a + b);
  
  print('Squared: $squared');
  print('Filtered: $filtered');
  print('Sum: $summed');
  
  // Conditional arrow functions
  var getStatus = (int age) => age >= 18 ? 'Adult' : 'Minor';
  var getMessage = (bool success) => success ? 'OK' : throw Exception('Failed');
  
  print('Status: ${getStatus(20)}');
  
  // Arrow functions with Point class
  Point p1 = Point(3, 4);
  Point p2 = Point(1, 2);
  print('Point 1: $p1');
  print('Magnitude: ${p1.magnitude}');
  print('Sum: ${p1 + p2}');
  
  // Nested arrow functions
  var createMultiplier = (int factor) => (int value) => value * factor;
  var doubler = createMultiplier(2);
  var tripler = createMultiplier(3);
  
  print('Double 5: ${doubler(5)}');
  print('Triple 4: ${tripler(4)}');
  
  // Arrow function with complex expression
  var processData = (List<int> data) => data
      .where((x) => x > 0)
      .map((x) => x * 2)
      .fold(0, (sum, x) => sum + x);
  
  print('Processed: ${processData([1, -2, 3, 4, -5])}');
}
```

This example demonstrates arrow function expressions in various contexts  
including variable assignments, class methods, collection operations,  
conditional logic, higher-order functions, and complex data processing  
pipelines, showcasing their conciseness and expressiveness.  

```
$ dart run arrow_functions.dart
Addition: 8
Greeting: Hello there, Alice!
Is even: true
Squared: [1, 4, 9, 16, 25]
Filtered: [3, 4, 5]
Sum: 15
Status: Adult
Point 1: Point(3.0, 4.0)
Magnitude: 25.0
Sum: Point(4.0, 6.0)
Double 5: 10
Triple 4: 12
Processed: 14
```

## Yield Expressions

Yield expressions are used in generator functions to produce values  
lazily. They pause function execution and emit values to consumers,  
resuming when the next value is requested. Generators can be synchronous  
(returning Iterable) or asynchronous (returning Stream), providing  
memory-efficient ways to generate sequences.  

```dart
// Synchronous generator
Iterable<int> generateNumbers(int count) sync* {
  for (int i = 1; i <= count; i++) {
    print('Generating: $i');
    yield i;
  }
}

// Fibonacci generator
Iterable<int> fibonacci() sync* {
  int a = 0, b = 1;
  while (true) {
    yield a;
    int temp = a + b;
    a = b;
    b = temp;
  }
}

// Recursive generator
Iterable<int> countDown(int n) sync* {
  if (n > 0) {
    yield n;
    yield* countDown(n - 1); // yield* delegates to another iterable
  }
}

// Asynchronous generator
Stream<String> fetchData() async* {
  for (int i = 1; i <= 3; i++) {
    await Future.delayed(Duration(milliseconds: 100));
    yield 'Data item $i';
  }
}

// Generator with conditions
Iterable<String> generateMessages(List<int> codes) sync* {
  for (int code in codes) {
    if (code > 0) {
      yield 'Success: $code';
    } else if (code == 0) {
      yield 'Warning: Zero code';
    } else {
      yield* generateErrors(code); // Delegate to another generator
    }
  }
}

Iterable<String> generateErrors(int code) sync* {
  yield 'Error: $code';
  yield 'Additional error info for $code';
}

void main() async {
  print('=== Basic Generator ===');
  var numbers = generateNumbers(3);
  for (int num in numbers) {
    print('Received: $num');
  }
  
  print('\n=== Fibonacci (first 5) ===');
  var fib = fibonacci().take(5);
  print('Fibonacci: ${fib.toList()}');
  
  print('\n=== Countdown ===');
  var countdown = countDown(4);
  print('Countdown: ${countdown.toList()}');
  
  print('\n=== Async Generator ===');
  await for (String data in fetchData()) {
    print('Received: $data');
  }
  
  print('\n=== Conditional Generator ===');
  var messages = generateMessages([1, 0, -1, 2]);
  for (String msg in messages) {
    print(msg);
  }
  
  print('\n=== Lazy Evaluation Demo ===');
  var lazy = generateNumbers(1000000).where((x) => x % 1000 == 0);
  print('First few large multiples: ${lazy.take(3).toList()}');
}
```

This example demonstrates synchronous and asynchronous generators,  
recursive generators, yield delegation with yield*, conditional yielding,  
and lazy evaluation benefits, showcasing how generators provide  
memory-efficient sequence generation and processing.  

```
$ dart run yield_expressions.dart
=== Basic Generator ===
Generating: 1
Received: 1
Generating: 2
Received: 2
Generating: 3
Received: 3

=== Fibonacci (first 5) ===
Fibonacci: [0, 1, 1, 2, 3]

=== Countdown ===
Countdown: [4, 3, 2, 1]

=== Async Generator ===
Received: Data item 1
Received: Data item 2
Received: Data item 3

=== Conditional Generator ===
Success: 1
Warning: Zero code
Error: -1
Additional error info for -1
Success: 2

=== Lazy Evaluation Demo ===
First few large multiples: [1000, 2000, 3000]
```

## Pattern Matching Expressions

Pattern matching expressions use destructuring patterns to extract values  
from complex data structures. Combined with switch expressions, patterns  
enable powerful data analysis and transformation. Dart 3.0 introduced  
extensive pattern matching capabilities including record patterns, list  
patterns, map patterns, and guard clauses.  

```dart
// Data classes for pattern matching
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
  // Record pattern matching
  var point = (x: 10, y: 20);
  var description = switch (point) {
    (x: 0, y: 0) => 'Origin',
    (x: var a, y: 0) => 'On X-axis at $a',
    (x: 0, y: var b) => 'On Y-axis at $b',
    (x: var a, y: var b) when a == b => 'Diagonal at ($a, $b)',
    (x: var a, y: var b) => 'Point at ($a, $b)',
  };
  print('Point description: $description');
  
  // List pattern matching
  List<int> numbers = [1, 2, 3, 4, 5];
  var listResult = switch (numbers) {
    [] => 'Empty list',
    [var single] => 'Single element: $single',
    [var first, var second] => 'Two elements: $first, $second',
    [var first, ...var rest] => 'First: $first, Rest: $rest',
  };
  print('List pattern: $listResult');
  
  // Map pattern matching
  Map<String, dynamic> user = {'name': 'Alice', 'age': 30, 'active': true};
  var userInfo = switch (user) {
    {'name': String name, 'age': int age} when age >= 18 => 
        'Adult: $name ($age years old)',
    {'name': String name, 'age': int age} => 
        'Minor: $name ($age years old)',
    _ => 'Invalid user data',
  };
  print('User info: $userInfo');
  
  // Object pattern matching
  List<Shape> shapes = [
    Circle(5.0),
    Rectangle(4.0, 6.0),
    Triangle(3.0, 4.0),
  ];
  
  for (Shape shape in shapes) {
    var area = switch (shape) {
      Circle(radius: var r) => 3.14159 * r * r,
      Rectangle(width: var w, height: var h) => w * h,
      Triangle(base: var b, height: var h) => 0.5 * b * h,
    };
    print('Shape area: ${area.toStringAsFixed(2)}');
  }
  
  // Nested pattern matching
  var data = [
    {'type': 'user', 'data': {'name': 'Alice', 'age': 30}},
    {'type': 'product', 'data': {'title': 'Book', 'price': 29.99}},
    {'type': 'invalid'},
  ];
  
  for (var item in data) {
    var result = switch (item) {
      {
        'type': 'user',
        'data': {'name': String name, 'age': int age}
      } => 'User: $name, Age: $age',
      {
        'type': 'product',
        'data': {'title': String title, 'price': double price}
      } => 'Product: $title, Price: \$${price.toStringAsFixed(2)}',
      _ => 'Unknown item type',
    };
    print('Nested pattern: $result');
  }
  
  // Variable pattern with destructuring
  var (a, b, c) = ('hello', 42, true);
  print('Destructured: a=$a, b=$b, c=$c');
  
  // Pattern in for-in loops
  var pairs = [(1, 'one'), (2, 'two'), (3, 'three')];
  for (var (number, word) in pairs) {
    print('$number -> $word');
  }
}
```

This example demonstrates pattern matching with records, lists, maps,  
objects, nested structures, and destructuring assignments, showcasing  
Dart's powerful pattern matching capabilities for data extraction and  
analysis in modern Dart 3.0+ applications.  

```
$ dart run pattern_matching.dart
Point description: Point at (10, 20)
List pattern: First: 1, Rest: [2, 3, 4, 5]
User info: Adult: Alice (30 years old)
Shape area: 78.54
Shape area: 24.00
Shape area: 6.00
Nested pattern: User: Alice, Age: 30
Nested pattern: Product: Book, Price: $29.99
Nested pattern: Unknown item type
Destructured: a=hello, b=42, c=true
1 -> one
2 -> two
3 -> three
```

## Extension Method Expressions

Extension method expressions add functionality to existing types without  
modifying their original implementation. Extensions can add methods,  
getters, setters, and operators to any type, enabling clean and readable  
code enhancement. They're particularly useful for adding domain-specific  
functionality to built-in types or third-party classes.  

```dart
// String extensions
extension StringExtensions on String {
  String get capitalized => 
      isEmpty ? this : this[0].toUpperCase() + substring(1).toLowerCase();
  
  String get reversed => split('').reversed.join();
  
  bool get isPalindrome {
    String cleaned = toLowerCase().replaceAll(RegExp(r'[^a-z0-9]'), '');
    return cleaned == cleaned.split('').reversed.join();
  }
  
  String repeat(int times) => List.filled(times, this).join();
  
  String truncate(int maxLength, [String suffix = '...']) =>
      length <= maxLength ? this : substring(0, maxLength - suffix.length) + suffix;
}

// List extensions
extension ListExtensions<T> on List<T> {
  T? get secondOrNull => length >= 2 ? this[1] : null;
  
  List<T> get unique => {...this}.toList();
  
  List<List<T>> chunked(int size) {
    List<List<T>> chunks = [];
    for (int i = 0; i < length; i += size) {
      chunks.add(sublist(i, (i + size < length) ? i + size : length));
    }
    return chunks;
  }
  
  T? findWhere(bool Function(T) predicate) {
    for (T item in this) {
      if (predicate(item)) return item;
    }
    return null;
  }
}

// Number extensions
extension IntExtensions on int {
  bool get isEven => this % 2 == 0;
  bool get isOdd => !isEven;
  
  String get ordinal {
    if (this % 100 >= 11 && this % 100 <= 13) return '${this}th';
    return switch (this % 10) {
      1 => '${this}st',
      2 => '${this}nd',
      3 => '${this}rd',
      _ => '${this}th',
    };
  }
  
  Duration get seconds => Duration(seconds: this);
  Duration get minutes => Duration(minutes: this);
  Duration get hours => Duration(hours: this);
}

// DateTime extensions
extension DateTimeExtensions on DateTime {
  bool get isToday {
    DateTime now = DateTime.now();
    return year == now.year && month == now.month && day == now.day;
  }
  
  String get timeAgo {
    Duration diff = DateTime.now().difference(this);
    if (diff.inDays > 0) return '${diff.inDays} days ago';
    if (diff.inHours > 0) return '${diff.inHours} hours ago';
    if (diff.inMinutes > 0) return '${diff.inMinutes} minutes ago';
    return 'Just now';
  }
  
  DateTime get startOfDay => DateTime(year, month, day);
  DateTime get endOfDay => DateTime(year, month, day, 23, 59, 59, 999);
}

void main() {
  // String extensions
  String text = 'hello world';
  print('Capitalized: ${text.capitalized}');
  print('Reversed: ${text.reversed}');
  print('Is palindrome: ${'racecar'.isPalindrome}');
  print('Repeated: ${'Dart'.repeat(3)}');
  print('Truncated: ${'This is a long sentence'.truncate(10)}');
  
  // List extensions
  List<int> numbers = [1, 2, 3, 2, 4, 3, 5];
  print('Second element: ${numbers.secondOrNull}');
  print('Unique: ${numbers.unique}');
  print('Chunked: ${numbers.chunked(3)}');
  print('Find even: ${numbers.findWhere((n) => n.isEven)}');
  
  // Number extensions
  for (int i = 1; i <= 5; i++) {
    print('$i is ${i.isEven ? 'even' : 'odd'}, ordinal: ${i.ordinal}');
  }
  
  print('Duration: ${30.seconds}');
  print('Duration: ${2.hours}');
  
  // DateTime extensions
  DateTime now = DateTime.now();
  DateTime yesterday = now.subtract(1.days);
  
  print('Is today: ${now.isToday}');
  print('Yesterday: ${yesterday.timeAgo}');
  print('Start of day: ${now.startOfDay}');
  
  // Chaining extension methods
  String processed = 'hello world'
      .capitalized
      .reversed
      .truncate(8);
  print('Chained operations: $processed');
  
  // Extension methods in expressions
  List<String> words = ['hello', 'world', 'dart', 'programming'];
  var result = words
      .where((w) => w.length > 4)
      .map((w) => w.capitalized)
      .toList()
      .chunked(2);
  print('Complex processing: $result');
}
```

This example demonstrates extension methods on various types including  
String, List, int, and DateTime, showing how extensions enhance existing  
types with custom functionality, enable method chaining, and integrate  
seamlessly with Dart's expression system for clean, readable code.  

```
$ dart run extension_methods.dart
Capitalized: Hello world
Reversed: dlrow olleh
Is palindrome: true
Repeated: DartDartDart
Truncated: This is...
Second element: 2
Unique: [1, 2, 3, 4, 5]
Chunked: [[1, 2, 3], [2, 4, 3], [5]]
Find even: 2
1 is odd, ordinal: 1st
2 is even, ordinal: 2nd
3 is odd, ordinal: 3rd
4 is even, ordinal: 4th
5 is odd, ordinal: 5th
Duration: 0:00:30.000000
Duration: 2:00:00.000000
Is today: true
Yesterday: 1 days ago
Start of day: 2025-01-27 00:00:00.000
Chained operations: dlroW...
Complex processing: [[Hello, World], [Programming]]
```

## Generic Expressions

Generic expressions work with parameterized types, enabling type-safe  
code that operates on multiple types. Generics provide compile-time type  
checking while maintaining code reusability. They're essential for  
collections, functions, and classes that need to work with various types  
without sacrificing type safety.  

```dart
// Generic function expressions
T identity<T>(T value) => value;

List<T> createList<T>(T item, int count) => List.filled(count, item);

T? findFirst<T>(List<T> list, bool Function(T) predicate) {
  for (T item in list) {
    if (predicate(item)) return item;
  }
  return null;
}

// Generic class with expressions
class Pair<T, U> {
  final T first;
  final U second;
  
  Pair(this.first, this.second);
  
  // Generic method expressions
  R combine<R>(R Function(T, U) combiner) => combiner(first, second);
  
  Pair<U, T> get swapped => Pair(second, first);
  
  @override
  String toString() => 'Pair($first, $second)';
}

// Generic extension
extension ListGenericExtensions<T> on List<T> {
  List<R> mapWithIndex<R>(R Function(T item, int index) mapper) {
    List<R> result = [];
    for (int i = 0; i < length; i++) {
      result.add(mapper(this[i], i));
    }
    return result;
  }
  
  Map<K, List<T>> groupBy<K>(K Function(T) keySelector) {
    Map<K, List<T>> groups = {};
    for (T item in this) {
      K key = keySelector(item);
      groups.putIfAbsent(key, () => []).add(item);
    }
    return groups;
  }
}

// Generic with constraints
class Repository<T extends Comparable<T>> {
  final List<T> _items = [];
  
  void add(T item) => _items.add(item);
  
  List<T> get sorted => [..._items]..sort();
  
  T? findMin() => _items.isEmpty ? null : _items.reduce(
      (a, b) => a.compareTo(b) < 0 ? a : b);
}

void main() {
  // Basic generic expressions
  print('Identity int: ${identity(42)}');
  print('Identity string: ${identity('hello')}');
  
  var numbers = createList(5, 3);
  var words = createList('dart', 2);
  print('Number list: $numbers');
  print('Word list: $words');
  
  // Generic function with predicate
  List<int> data = [1, 2, 3, 4, 5];
  var evenNumber = findFirst(data, (n) => n % 2 == 0);
  print('First even: $evenNumber');
  
  // Generic class usage
  var pair1 = Pair('hello', 42);
  var pair2 = Pair(3.14, true);
  
  print('Pair 1: $pair1');
  print('Swapped: ${pair1.swapped}');
  
  // Generic method with different return type
  var combined = pair1.combine<String>((str, num) => '$str-$num');
  print('Combined: $combined');
  
  // Generic collections with type inference
  Map<String, List<int>> scoresByName = {
    'Alice': [95, 87, 92],
    'Bob': [78, 85, 90],
  };
  
  // Generic extension methods
  List<String> items = ['apple', 'banana', 'cherry'];
  var indexed = items.mapWithIndex((item, index) => '$index: $item');
  print('Indexed: $indexed');
  
  var grouped = items.groupBy((item) => item.length);
  print('Grouped by length: $grouped');
  
  // Generic with constraints
  var intRepo = Repository<int>();
  intRepo.add(3);
  intRepo.add(1);
  intRepo.add(4);
  intRepo.add(2);
  
  print('Sorted ints: ${intRepo.sorted}');
  print('Min int: ${intRepo.findMin()}');
  
  var stringRepo = Repository<String>();
  stringRepo.add('zebra');
  stringRepo.add('apple');
  stringRepo.add('monkey');
  
  print('Sorted strings: ${stringRepo.sorted}');
  print('Min string: ${stringRepo.findMin()}');
  
  // Complex generic expressions
  List<Pair<String, int>> pairs = [
    Pair('Alice', 25),
    Pair('Bob', 30),
    Pair('Charlie', 35),
  ];
  
  var ages = pairs.map((p) => p.second).toList();
  var names = pairs.map((p) => p.first).toList();
  var descriptions = pairs.mapWithIndex(
      (pair, index) => 'Person $index: ${pair.first} (${pair.second})');
  
  print('Ages: $ages');
  print('Names: $names');
  print('Descriptions: $descriptions');
}
```

This example demonstrates generic expressions across functions, classes,  
extensions, and constrained generics, showing type inference, generic  
methods, collection operations, and how generics maintain type safety  
while providing code reusability and flexibility.  

```
$ dart run generic_expressions.dart
Identity int: 42
Identity string: hello
Number list: [5, 5, 5]
Word list: [dart, dart]
First even: 2
Pair 1: Pair(hello, 42)
Swapped: Pair(42, hello)
Combined: hello-42
Indexed: [0: apple, 1: banana, 2: cherry]
Grouped by length: {5: [apple], 6: [banana, cherry]}
Sorted ints: [1, 2, 3, 4]
Min int: 1
Sorted strings: [apple, monkey, zebra]
Min string: apple
Ages: [25, 30, 35]
Names: [Alice, Bob, Charlie]
Descriptions: [Person 0: Alice (25), Person 1: Bob (30), Person 2: Charlie (35)]
```

## Null Assertion Expressions

Null assertion expressions use the `!` operator to convert nullable  
types to non-nullable types, telling the compiler that a value is  
definitely not null. This operation throws a runtime error if the  
value is actually null, so it should be used only when you're certain  
the value is non-null or when null would indicate a programming error.  

```dart
class User {
  String? name;
  int? age;
  List<String>? hobbies;
  
  User({this.name, this.age, this.hobbies});
  
  // Null assertion in getter
  String get displayName => name!; // Assumes name is never null when accessed
  
  // Null assertion in method
  void printInfo() {
    print('Name: ${name!}'); // Assert name is not null
    print('Age: ${age!}');   // Assert age is not null
  }
}

String? findUserName(int id) {
  return id > 0 ? 'User$id' : null;
}

List<String>? getUserHobbies(String name) {
  return name.isNotEmpty ? ['reading', 'coding'] : null;
}

void main() {
  // Basic null assertion
  String? nullable = 'Hello there!';
  String nonNull = nullable!; // Assert it's not null
  print('Non-null string: $nonNull');
  
  // Null assertion in expressions
  String? name = 'Alice';
  int length = name!.length; // Assert and access length
  print('Name length: $length');
  
  // Null assertion with method calls
  List<int>? numbers = [1, 2, 3, 4, 5];
  int first = numbers!.first;
  List<int> doubled = numbers!.map((x) => x * 2).toList();
  print('First: $first');
  print('Doubled: $doubled');
  
  // Null assertion in cascade
  StringBuffer? buffer = StringBuffer();
  buffer!
    ..write('Hello')
    ..write(' ')
    ..write('World');
  print('Buffer: ${buffer.toString()}');
  
  // Null assertion with function results
  String userName = findUserName(1)!; // Assert result is not null
  print('User name: $userName');
  
  // Null assertion in collections
  List<String?> mixedList = ['apple', null, 'banana', 'cherry'];
  List<String> nonNullItems = mixedList
      .where((item) => item != null)
      .map((item) => item!) // Assert each filtered item is not null
      .toList();
  print('Non-null items: $nonNullItems');
  
  // Null assertion with class
  User user = User(name: 'Bob', age: 25);
  print('Display name: ${user.displayName}');
  user.printInfo();
  
  // Null assertion in complex expressions
  Map<String, String?> data = {
    'firstName': 'John',
    'lastName': 'Doe',
    'middleName': null,
  };
  
  String fullName = '${data['firstName']!} ${data['lastName']!}';
  print('Full name: $fullName');
  
  // Null assertion with error handling
  try {
    String? empty;
    String result = empty!; // This will throw
    print('Result: $result');
  } catch (e) {
    print('Caught null assertion error: ${e.runtimeType}');
  }
  
  // Safe patterns vs null assertion
  List<String?> items = ['a', 'b', null, 'c'];
  
  // Using null assertion (risky)
  try {
    for (String? item in items) {
      print('Item: ${item!.toUpperCase()}'); // Will throw on null
    }
  } catch (e) {
    print('Error with null assertion: ${e.runtimeType}');
  }
  
  // Better: using null-aware operators
  for (String? item in items) {
    print('Safe item: ${item?.toUpperCase() ?? 'NULL'}');
  }
  
  // Null assertion in switch expressions
  String? grade = 'A';
  String description = switch (grade!) {
    'A' => 'Excellent',
    'B' => 'Good',
    'C' => 'Average',
    _ => 'Other',
  };
  print('Grade description: $description');
}
```

This example demonstrates null assertion expressions in various contexts  
including variable assignments, method calls, cascades, collections,  
class methods, and complex expressions, while also showing error cases  
and comparing with safer null-aware alternatives.  

```
$ dart run null_assertion.dart
Non-null string: Hello there!
Name length: 5
First: 1
Doubled: [2, 4, 6, 8, 10]
Buffer: Hello World
User name: User1
Non-null items: [apple, banana, cherry]
Display name: Bob
Name: Bob
Age: 25
Full name: John Doe
Caught null assertion error: TypeError
Error with null assertion: TypeError
Safe item: A
Safe item: B
Safe item: NULL
Safe item: C
Grade description: Excellent
```
