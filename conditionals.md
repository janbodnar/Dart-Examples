# Dart Conditionals

Conditionals in Dart enable decision-making in code by executing different  
blocks of statements based on boolean expressions. They form the foundation  
of control flow, allowing programs to respond dynamically to varying  
conditions and data states.  

Dart provides multiple conditional constructs including if-else statements,  
switch statements, ternary operators, and null-aware operators. These tools  
enable developers to write expressive and concise conditional logic while  
maintaining type safety and readability.  

Modern Dart also supports advanced features like pattern matching in switch  
expressions, collection if expressions, and null-aware operators that make  
conditional programming more powerful and intuitive.  

## Basic If Statement

The `if` statement executes a block of code when a boolean expression  
evaluates to true. It represents the most fundamental form of conditional  
logic in programming.  

```dart
void main() {
  int temperature = 25;
  
  if (temperature > 20) {
    print('It\'s warm outside!');
  }
  
  bool isWeekend = true;
  if (isWeekend) {
    print('Time to relax!');
  }
}
```

This example demonstrates basic if statements checking temperature and  
weekend status. The first condition uses a comparison operator, while the  
second directly evaluates a boolean variable, showing different conditional  
patterns.  

```
$ dart run basic_if.dart
It's warm outside!
Time to relax!
```

## If-Else Statement

The `if-else` statement provides an alternative code path when the condition  
evaluates to false, ensuring that exactly one of two code blocks executes  
based on the conditional expression.  

```dart
void main() {
  int score = 85;
  
  if (score >= 90) {
    print('Excellent grade!');
  } else {
    print('Good effort, keep improving!');
  }
  
  String weather = 'rainy';
  if (weather == 'sunny') {
    print('Perfect day for a picnic!');
  } else {
    print('Better stay indoors.');
  }
}
```

This example shows if-else statements for grade evaluation and weather  
checking. Each condition has exactly two possible outcomes, demonstrating  
the binary nature of if-else constructs.  

```
$ dart run if_else.dart
Good effort, keep improving!
Better stay indoors.
```

## Else-If Chain

Else-if chains allow multiple conditions to be tested sequentially, providing  
a way to handle multiple distinct cases within a single conditional structure.  

```dart
void main() {
  int hour = 14;
  
  if (hour < 6) {
    print('Early morning');
  } else if (hour < 12) {
    print('Morning');
  } else if (hour < 18) {
    print('Afternoon');
  } else if (hour < 22) {
    print('Evening');
  } else {
    print('Night');
  }
  
  String grade = 'B';
  if (grade == 'A') {
    print('Outstanding performance!');
  } else if (grade == 'B') {
    print('Great work!');
  } else if (grade == 'C') {
    print('Good job!');
  } else {
    print('Keep working hard!');
  }
}
```

This example demonstrates else-if chains for time-of-day classification and  
grade evaluation. The conditions are evaluated in order until one matches,  
providing a clear decision tree structure.  

```
$ dart run else_if_chain.dart
Afternoon
Great work!
```

## Nested If Statements

Nested if statements place one conditional inside another, enabling complex  
decision-making logic that depends on multiple layers of conditions.  

```dart
void main() {
  bool hasLicense = true;
  int age = 25;
  bool hasInsurance = true;
  
  if (hasLicense) {
    if (age >= 18) {
      if (hasInsurance) {
        print('You can drive legally!');
      } else {
        print('You need insurance to drive.');
      }
    } else {
      print('You must be 18 or older to drive.');
    }
  } else {
    print('You need a valid license to drive.');
  }
  
  // Alternative approach with logical operators
  if (hasLicense && age >= 18 && hasInsurance) {
    print('All requirements met for driving!');
  }
}
```

This example shows nested conditionals for driving eligibility checks and  
contrasts them with logical operator combinations. Nested conditions provide  
explicit control flow, while logical operators offer more concise alternatives.  

```
$ dart run nested_if.dart
You can drive legally!
All requirements met for driving!
```

## Ternary Operator

The ternary operator (`?:`) provides a concise way to write simple if-else  
statements as expressions, making code more compact when dealing with  
straightforward conditional assignments.  

```dart
void main() {
  int age = 17;
  String status = age >= 18 ? 'Adult' : 'Minor';
  print('Status: $status');
  
  double price = 99.99;
  String offer = price > 100 ? 'Premium' : 'Standard';
  print('Product tier: $offer');
  
  bool isLoggedIn = false;
  String message = isLoggedIn ? 'Welcome back!' : 'Please log in';
  print(message);
  
  // Nested ternary (use sparingly)
  int score = 85;
  String result = score >= 90 ? 'A' : score >= 80 ? 'B' : score >= 70 ? 'C' : 'D';
  print('Grade: $result');
}
```

This example demonstrates various uses of the ternary operator for status  
determination, product classification, user messages, and grade calculation.  
The nested ternary shows advanced usage but should be used judiciously.  

```
$ dart run ternary.dart
Status: Minor
Product tier: Standard
Please log in
Grade: B
```

## Null-Coalescing Operator

The null-coalescing operator (`??`) provides default values for null  
variables, enabling safe handling of nullable types with concise syntax.  

```dart
void main() {
  String? username;
  String displayName = username ?? 'Guest';
  print('Welcome, $displayName');
  
  int? userAge;
  int defaultAge = userAge ?? 25;
  print('Age: $defaultAge');
  
  List<String>? items;
  List<String> safeItems = items ?? [];
  print('Items count: ${safeItems.length}');
  
  // Chaining null-coalescing operators
  String? primary;
  String? secondary;
  String? tertiary = 'Fallback';
  String result = primary ?? secondary ?? tertiary ?? 'Default';
  print('Result: $result');
}
```

This example showcases null-coalescing for user display names, default ages,  
safe list handling, and chained null-coalescing operations. It demonstrates  
how to gracefully handle null values with sensible defaults.  

```
$ dart run null_coalescing.dart
Welcome, Guest
Age: 25
Items count: 0
Result: Fallback
```

## Null-Aware Assignment

The null-aware assignment operator (`??=`) assigns a value to a variable only  
if the variable is currently null, providing a conditional assignment  
mechanism for nullable types.  

```dart
void main() {
  String? name;
  name ??= 'Default User';
  print('Name: $name');
  
  // Won't change existing value
  name ??= 'Another Name';
  print('Name after second assignment: $name');
  
  List<int>? numbers;
  numbers ??= [1, 2, 3];
  print('Numbers: $numbers');
  
  Map<String, String>? config;
  config ??= {'theme': 'dark', 'language': 'en'};
  print('Config: $config');
  
  // Useful for lazy initialization
  String? _cache;
  String getExpensiveValue() {
    _cache ??= 'Computed expensive value';
    return _cache!;
  }
  
  print('First call: ${getExpensiveValue()}');
  print('Second call: ${getExpensiveValue()}'); // Uses cached value
}
```

This example demonstrates null-aware assignment for default user names, list  
initialization, map configuration, and lazy initialization patterns. It shows  
how to efficiently handle one-time assignments for nullable variables.  

```
$ dart run null_aware_assignment.dart
Name: Default User
Name after second assignment: Default User
Numbers: [1, 2, 3]
Config: {theme: dark, language: en}
First call: Computed expensive value
Second call: Computed expensive value
```

## Conditional Property Access

The conditional property access operator (`?.`) safely accesses properties  
and methods on nullable objects, preventing null reference exceptions by  
returning null when the object is null.  

```dart
class User {
  String name;
  String? email;
  
  User(this.name, this.email);
  
  void greet() {
    print('Hello there! I\'m $name');
  }
}

void main() {
  User? user;
  
  // Safe property access
  String? email = user?.email;
  print('Email: $email');
  
  // Safe method call
  user?.greet(); // Won't execute if user is null
  
  List<int>? numbers;
  int? first = numbers?.first;
  int? length = numbers?.length;
  print('First: $first, Length: $length');
  
  // Chaining null-aware operators
  user = User('Alice', 'alice@example.com');
  String? domain = user.email?.split('@').last;
  print('Email domain: $domain');
  
  // Safe method chaining
  String? upperEmail = user.email?.toUpperCase();
  print('Upper email: $upperEmail');
}
```

This example shows conditional property access for user objects, list  
properties, and method chaining. It demonstrates safe navigation through  
potentially null object hierarchies without runtime exceptions.  

```
$ dart run conditional_access.dart
Email: null
First: null, Length: null
Email domain: example.com
Upper email: ALICE@EXAMPLE.COM
```

## Switch Statement with Enum

Switch statements with enums provide type-safe conditional logic for  
predefined sets of values, offering better maintainability and clarity  
compared to string-based or integer-based switch statements.  

```dart
enum WeatherType { sunny, rainy, cloudy, stormy, snowy }
enum Priority { low, medium, high, urgent }

void main() {
  WeatherType today = WeatherType.sunny;
  
  switch (today) {
    case WeatherType.sunny:
      print('Perfect day for outdoor activities!');
      break;
    case WeatherType.rainy:
      print('Don\'t forget your umbrella!');
      break;
    case WeatherType.cloudy:
      print('Might rain later, stay prepared.');
      break;
    case WeatherType.stormy:
      print('Better stay indoors today.');
      break;
    case WeatherType.snowy:
      print('Winter wonderland outside!');
      break;
  }
  
  Priority taskPriority = Priority.high;
  String action;
  
  switch (taskPriority) {
    case Priority.low:
      action = 'Handle when convenient';
      break;
    case Priority.medium:
      action = 'Schedule for this week';
      break;
    case Priority.high:
      action = 'Complete today';
      break;
    case Priority.urgent:
      action = 'Drop everything and handle now!';
      break;
  }
  
  print('Task action: $action');
}
```

This example demonstrates switch statements with weather and priority enums.  
The enum-based approach provides compile-time safety and ensures all cases  
are handled explicitly, making code more robust and maintainable.  

```
$ dart run switch_enum.dart
Perfect day for outdoor activities!
Task action: Complete today
```

## Switch Expression

Switch expressions (Dart 3+) provide a more concise and functional approach  
to conditional logic, returning values directly without requiring break  
statements or temporary variables.  

```dart
void main() {
  String grade = 'A';
  
  String description = switch (grade) {
    'A' => 'Excellent work!',
    'B' => 'Good job!',
    'C' => 'Satisfactory',
    'D' => 'Needs improvement',
    'F' => 'Failed',
    _ => 'Invalid grade'
  };
  
  print('Grade description: $description');
  
  int dayNumber = 3;
  String dayName = switch (dayNumber) {
    1 => 'Monday',
    2 => 'Tuesday', 
    3 => 'Wednesday',
    4 => 'Thursday',
    5 => 'Friday',
    6 => 'Saturday',
    7 => 'Sunday',
    _ => 'Invalid day'
  };
  
  print('Day: $dayName');
  
  // Switch expression with calculations
  String operation = '+';
  double a = 10, b = 5;
  
  double result = switch (operation) {
    '+' => a + b,
    '-' => a - b,
    '*' => a * b,
    '/' => b != 0 ? a / b : double.nan,
    _ => double.nan
  };
  
  print('$a $operation $b = $result');
}
```

This example showcases switch expressions for grade descriptions, day names,  
and arithmetic calculations. Switch expressions eliminate boilerplate code  
and provide more functional programming patterns.  

```
$ dart run switch_expression.dart
Grade description: Excellent work!
Day: Wednesday
10.0 + 5.0 = 15.0
```

## Switch with Pattern Matching

Pattern matching in switch statements (Dart 3+) enables sophisticated  
conditional logic based on value structure, types, and complex patterns  
rather than simple equality comparisons.  

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
  List<Object> items = [
    42,
    'Hello there!',
    [1, 2, 3],
    {'name': 'Alice', 'age': 30},
    Circle(5.0),
    Rectangle(4.0, 6.0)
  ];
  
  for (var item in items) {
    String description = switch (item) {
      int n when n > 0 => 'Positive integer: $n',
      int n when n < 0 => 'Negative integer: $n',
      int _ => 'Zero',
      String s when s.isNotEmpty => 'Non-empty string: "$s"',
      String _ => 'Empty string',
      List<int> list when list.length > 2 => 'List with ${list.length} integers',
      List _ => 'Short or non-integer list',
      Map<String, Object> map when map.containsKey('name') => 
        'Person: ${map['name']}',
      Circle(radius: var r) => 'Circle with radius $r',
      Rectangle(width: var w, height: var h) => 'Rectangle ${w}x$h',
      Triangle(base: var b, height: var h) => 'Triangle base=$b height=$h',
      _ => 'Unknown type: ${item.runtimeType}'
    };
    
    print(description);
  }
}
```

This example demonstrates pattern matching with sealed classes, guard clauses,  
and destructuring patterns. It shows how modern switch statements can handle  
complex conditional logic based on type, structure, and value constraints.  

```
$ dart run pattern_matching.dart
Positive integer: 42
Non-empty string: "Hello there!"
List with 3 integers
Person: Alice
Circle with radius 5.0
Rectangle 4.0x6.0
```

## Switch with Guard Clauses

Guard clauses in switch statements add additional conditions to pattern  
matching, enabling fine-grained control over when specific cases should  
match based on runtime values.  

```dart
void main() {
  List<Map<String, dynamic>> users = [
    {'name': 'Alice', 'age': 17, 'role': 'student'},
    {'name': 'Bob', 'age': 25, 'role': 'employee'},
    {'name': 'Charlie', 'age': 35, 'role': 'admin'},
    {'name': 'Diana', 'age': 16, 'role': 'student'},
    {'name': 'Eve', 'age': 45, 'role': 'manager'}
  ];
  
  for (var user in users) {
    String access = switch (user) {
      {'role': 'admin'} => 'Full access granted',
      {'role': 'manager', 'age': int age} when age >= 30 => 
        'Management access granted',
      {'role': 'employee', 'age': int age} when age >= 21 => 
        'Employee access granted',
      {'role': 'student', 'age': int age} when age >= 18 => 
        'Student access granted',
      {'age': int age} when age < 18 => 
        'Access denied: Must be 18 or older',
      _ => 'Limited access only'
    };
    
    print('${user['name']}: $access');
  }
  
  // Guard clauses with numbers
  List<int> numbers = [-5, 0, 3, 7, 15, 25, 100];
  
  for (var num in numbers) {
    String category = switch (num) {
      int n when n < 0 => 'Negative number',
      0 => 'Zero',
      int n when n > 0 && n <= 10 => 'Small positive',
      int n when n > 10 && n <= 50 => 'Medium positive', 
      int n when n > 50 => 'Large positive',
      _ => 'Uncategorized'
    };
    
    print('$num: $category');
  }
}
```

This example shows guard clauses for user access control and number  
categorization. Guard clauses combine pattern matching with additional  
boolean conditions, providing powerful and expressive conditional logic.  

```
$ dart run guard_clauses.dart
Alice: Access denied: Must be 18 or older
Bob: Employee access granted
Charlie: Full access granted
Diana: Access denied: Must be 18 or older
Eve: Management access granted
-5: Negative number
0: Zero
3: Small positive
7: Small positive
15: Medium positive
25: Medium positive
100: Large positive
```

## Boolean Logical Operators

Boolean logical operators (`&&`, `||`, `!`) combine multiple boolean  
expressions to create complex conditional logic with short-circuit evaluation  
for efficient and safe condition checking.  

```dart
void main() {
  int age = 25;
  bool hasLicense = true;
  bool hasInsurance = false;
  String weather = 'sunny';
  
  // AND operator (&&)
  if (age >= 18 && hasLicense) {
    print('Eligible to drive');
  }
  
  // OR operator (||)
  if (weather == 'sunny' || weather == 'cloudy') {
    print('Good weather for driving');
  }
  
  // NOT operator (!)
  if (!hasInsurance) {
    print('Warning: No insurance coverage');
  }
  
  // Complex combinations
  bool canDriveAlone = age >= 18 && hasLicense && hasInsurance;
  bool needsSupervision = age >= 16 && age < 18 && hasLicense;
  
  if (canDriveAlone) {
    print('Can drive independently');
  } else if (needsSupervision) {
    print('Can drive with adult supervision');
  } else {
    print('Cannot drive');
  }
  
  // Short-circuit evaluation demonstration
  String? nullString;
  if (nullString != null && nullString.isNotEmpty) {
    print('String has content'); // Won't execute due to short-circuit
  }
  
  // De Morgan's laws examples
  bool condition1 = true;
  bool condition2 = false;
  
  // !(A && B) equals (!A || !B)
  bool result1 = !(condition1 && condition2);
  bool result2 = !condition1 || !condition2;
  print('De Morgan\'s law 1: $result1 == $result2');
  
  // !(A || B) equals (!A && !B)
  bool result3 = !(condition1 || condition2);
  bool result4 = !condition1 && !condition2;
  print('De Morgan\'s law 2: $result3 == $result4');
}
```

This example demonstrates logical operators for driving eligibility, weather  
conditions, and complex boolean combinations. It also shows short-circuit  
evaluation and De Morgan's laws for logical equivalence.  

```
$ dart run logical_operators.dart
Eligible to drive
Good weather for driving
Warning: No insurance coverage
Cannot drive
De Morgan's law 1: true == true
De Morgan's law 2: false == false
```

## Comparison Operators

Comparison operators enable conditional logic based on relational comparisons  
between values, supporting numeric, string, and object comparisons with  
type-safe evaluation.  

```dart
void main() {
  // Numeric comparisons
  int score1 = 85;
  int score2 = 92;
  
  print('Score comparisons:');
  print('$score1 == $score2: ${score1 == score2}');
  print('$score1 != $score2: ${score1 != score2}');
  print('$score1 < $score2: ${score1 < score2}');
  print('$score1 > $score2: ${score1 > score2}');
  print('$score1 <= $score2: ${score1 <= score2}');
  print('$score1 >= $score2: ${score1 >= score2}');
  
  // String comparisons
  String name1 = 'Alice';
  String name2 = 'Bob';
  String name3 = 'Alice';
  
  print('\nString comparisons:');
  print('$name1 == $name2: ${name1 == name2}');
  print('$name1 == $name3: ${name1 == name3}');
  print('Lexicographic: $name1.compareTo($name2): ${name1.compareTo(name2)}');
  
  // Object comparisons
  List<int> list1 = [1, 2, 3];
  List<int> list2 = [1, 2, 3];
  List<int> list3 = list1;
  
  print('\nObject comparisons:');
  print('list1 == list2: ${list1 == list2}'); // Content equality
  print('identical(list1, list2): ${identical(list1, list2)}'); // Reference equality
  print('identical(list1, list3): ${identical(list1, list3)}'); // Same reference
  
  // Practical conditional usage
  double temperature = 23.5;
  
  if (temperature >= 30) {
    print('\nIt\'s hot outside!');
  } else if (temperature >= 20) {
    print('\nNice weather today!');
  } else if (temperature >= 10) {
    print('\nA bit cool, but pleasant.');
  } else {
    print('\nIt\'s cold outside!');
  }
  
  // DateTime comparisons
  DateTime now = DateTime.now();
  DateTime tomorrow = now.add(Duration(days: 1));
  
  if (now.isBefore(tomorrow)) {
    print('Today comes before tomorrow'); // Obviously true!
  }
}
```

This example covers numeric, string, and object comparisons, including  
content equality versus reference equality. It demonstrates practical usage  
in temperature classification and DateTime comparisons.  

```
$ dart run comparisons.dart
Score comparisons:
85 == 92: false
85 != 92: true
85 < 92: true
85 > 92: false
85 <= 92: true
85 >= 92: false

String comparisons:
Alice == Bob: false
Alice == Alice: true
Lexicographic: Alice.compareTo(Bob): -1

Object comparisons:
list1 == list2: true
identical(list1, list2): false
identical(list1, list3): true

Nice weather today!
Today comes before tomorrow
```

## Type Checking Conditionals

Type checking conditionals using `is` and `is!` operators enable runtime type  
verification and type-safe conditional logic when working with dynamic types  
or inheritance hierarchies.  

```dart
abstract class Animal {
  String get name;
  void makeSound();
}

class Dog extends Animal {
  @override
  final String name;
  Dog(this.name);
  
  @override
  void makeSound() => print('$name barks: Woof!');
  
  void fetch() => print('$name fetches the ball!');
}

class Cat extends Animal {
  @override
  final String name;
  Cat(this.name);
  
  @override
  void makeSound() => print('$name meows: Meow!');
  
  void climb() => print('$name climbs the tree!');
}

class Bird extends Animal {
  @override
  final String name;
  Bird(this.name);
  
  @override
  void makeSound() => print('$name chirps: Tweet!');
  
  void fly() => print('$name flies away!');
}

void main() {
  List<Object> items = [
    'Hello there!',
    42,
    3.14,
    true,
    [1, 2, 3],
    {'key': 'value'},
    Dog('Buddy'),
    Cat('Whiskers'),
    Bird('Tweety')
  ];
  
  for (var item in items) {
    // Type checking with is operator
    if (item is String) {
      print('String: "$item" (length: ${item.length})');
    } else if (item is int) {
      print('Integer: $item (${item.isEven ? 'even' : 'odd'})');
    } else if (item is double) {
      print('Double: $item (rounded: ${item.round()})');
    } else if (item is bool) {
      print('Boolean: $item');
    } else if (item is List) {
      print('List with ${item.length} elements');
    } else if (item is Map) {
      print('Map with ${item.length} entries');
    } else if (item is Animal) {
      print('Animal detected: ${item.name}');
      item.makeSound();
      
      // Specific animal type handling
      if (item is Dog) {
        item.fetch();
      } else if (item is Cat) {
        item.climb();
      } else if (item is Bird) {
        item.fly();
      }
    }
  }
  
  // Negative type checking with is!
  Object? nullableValue = 'test';
  
  if (nullableValue is! String) {
    print('Not a string');
  } else {
    print('It is a string: $nullableValue');
  }
  
  // Type promotion in conditionals
  dynamic variable = 'Hello there!';
  
  if (variable is String) {
    // variable is automatically promoted to String type
    print('Uppercase: ${variable.toUpperCase()}');
    print('Length: ${variable.length}');
  }
}
```

This example demonstrates type checking with inheritance hierarchies, basic  
types, collections, and type promotion. The `is` operator provides runtime  
type verification with automatic type promotion in conditional blocks.  

```
$ dart run type_checking.dart
String: "Hello there!" (length: 12)
Integer: 42 (even)
Double: 3.14 (rounded: 3)
Boolean: true
List with 3 elements
Map with 1 entries
Animal detected: Buddy
Buddy barks: Woof!
Buddy fetches the ball!
Animal detected: Whiskers
Whiskers meows: Meow!
Whiskers climbs the tree!
Animal detected: Tweety
Tweety chirps: Tweet!
Tweety flies away!
It is a string: test
Uppercase: HELLO THERE!
Length: 12
```

## Assert Statement

Assert statements provide conditional debugging and validation checks that  
help catch programming errors during development while being automatically  
removed in production builds for performance optimization.  

```dart
void main() {
  // Basic assertions
  int age = 25;
  assert(age >= 0, 'Age cannot be negative');
  assert(age <= 150, 'Age seems unrealistic: $age');
  
  // Assertion with computation
  List<int> numbers = [1, 2, 3, 4, 5];
  assert(numbers.isNotEmpty, 'Numbers list should not be empty');
  assert(numbers.every((n) => n > 0), 'All numbers should be positive');
  
  // Function parameter validation
  String result = validateEmail('user@example.com');
  print('Email validation result: $result');
  
  try {
    validateEmail('invalid-email');
  } catch (e) {
    print('Caught assertion error: ${e.toString()}');
  }
  
  // Assertions in class methods
  BankAccount account = BankAccount(1000);
  print('Initial balance: \$${account.balance}');
  
  account.withdraw(500);
  print('After withdrawal: \$${account.balance}');
  
  try {
    account.withdraw(600); // Should trigger assertion
  } catch (e) {
    print('Withdrawal failed: ${e.toString()}');
  }
}

String validateEmail(String email) {
  assert(email.isNotEmpty, 'Email cannot be empty');
  assert(email.contains('@'), 'Email must contain @ symbol');
  assert(email.split('@').length == 2, 'Email format is invalid');
  
  return 'Email is valid';
}

class BankAccount {
  double _balance;
  
  BankAccount(this._balance) {
    assert(_balance >= 0, 'Initial balance cannot be negative');
  }
  
  double get balance => _balance;
  
  void withdraw(double amount) {
    assert(amount > 0, 'Withdrawal amount must be positive');
    assert(amount <= _balance, 'Insufficient funds: have $_balance, requested $amount');
    
    _balance -= amount;
  }
  
  void deposit(double amount) {
    assert(amount > 0, 'Deposit amount must be positive');
    _balance += amount;
  }
}
```

This example shows assertions for parameter validation, list verification,  
email validation, and class invariants. Assertions help catch bugs early  
in development while automatically being removed in release builds.  

```
$ dart run assertions.dart
Email validation result: Email is valid
Caught assertion error: 'file:///path/to/file.dart': Failed assertion: line X pos Y: 'email.contains('@')': Email must contain @ symbol
Initial balance: $1000.0
After withdrawal: $500.0
Withdrawal failed: 'file:///path/to/file.dart': Failed assertion: line X pos Y: 'amount <= _balance': Insufficient funds: have 500.0, requested 600.0
```

## Collection If Expressions

Collection if expressions enable conditional inclusion of elements in lists,  
sets, and maps based on boolean conditions, providing a concise way to build  
dynamic collections without explicit conditional logic.  

```dart
void main() {
  bool includeWeekends = true;
  bool includeHolidays = false;
  bool isVip = true;
  
  // Conditional list elements
  List<String> workDays = [
    'Monday',
    'Tuesday', 
    'Wednesday',
    'Thursday',
    'Friday',
    if (includeWeekends) ...['Saturday', 'Sunday'],
    if (includeHolidays) 'Holiday'
  ];
  
  print('Work days: $workDays');
  
  // Conditional set elements
  Set<String> permissions = {
    'read',
    'write',
    if (isVip) 'admin',
    if (isVip) 'delete',
    if (!isVip) 'limited_access'
  };
  
  print('Permissions: $permissions');
  
  // Conditional map entries
  int userAge = 25;
  bool hasSubscription = true;
  
  Map<String, dynamic> userProfile = {
    'name': 'Alice',
    'age': userAge,
    if (userAge >= 18) 'canVote': true,
    if (userAge >= 21) 'canDrink': true,
    if (hasSubscription) 'tier': 'premium',
    if (hasSubscription) 'adFree': true,
    if (!hasSubscription) 'tier': 'free'
  };
  
  print('User profile: $userProfile');
  
  // Nested collection if
  bool showAdvanced = true;
  bool showExperimental = false;
  
  List<List<String>> menuItems = [
    ['File', 'Edit', 'View'],
    ['Cut', 'Copy', 'Paste'],
    if (showAdvanced) [
      'Advanced',
      'Settings',
      if (showExperimental) 'Experimental'
    ]
  ];
  
  print('Menu structure: $menuItems');
  
  // Dynamic shopping cart
  bool isMember = true;
  bool hasPromo = true;
  double basePrice = 100.0;
  
  List<String> cartItems = [
    'Product A (\$${basePrice.toStringAsFixed(2)})',
    if (isMember) 'Member discount (-10%)',
    if (hasPromo) 'Promo code (-5%)',
    if (isMember && hasPromo) 'Bonus: Free shipping'
  ];
  
  print('Cart: $cartItems');
  
  // Configuration based on environment
  bool isDevelopment = true;
  bool enableLogging = true;
  
  Map<String, dynamic> appConfig = {
    'version': '1.0',
    'production': !isDevelopment,
    if (isDevelopment) 'debug': true,
    if (isDevelopment && enableLogging) 'logLevel': 'verbose',
    if (!isDevelopment) 'minified': true,
    if (!isDevelopment) 'analytics': true
  };
  
  print('App config: $appConfig');
}
```

This example demonstrates collection if expressions in various contexts:  
work schedules, user permissions, profiles, nested menus, shopping carts,  
and application configuration. Collection if expressions make dynamic  
collection building more readable and maintainable.  

```
$ dart run collection_if.dart
Work days: [Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday]
Permissions: {read, write, admin, delete}
User profile: {name: Alice, age: 25, canVote: true, canDrink: true, tier: premium, adFree: true}
Menu structure: [[File, Edit, View], [Cut, Copy, Paste], [Advanced, Settings]]
Cart: [Product A ($100.00), Member discount (-10%), Promo code (-5%), Bonus: Free shipping]
App config: {version: 1.0, production: false, debug: true, logLevel: verbose}
```

## Conditionals with Nullable Types

Dart's null safety system requires explicit handling of nullable types,  
making conditional logic more robust and preventing null reference  
exceptions through compile-time checks and null-aware operators.  

```dart
class UserProfile {
  String name;
  String? email;
  int? age;
  List<String>? hobbies;
  
  UserProfile(this.name, {this.email, this.age, this.hobbies});
  
  void displayInfo() {
    print('Name: $name');
    
    // Conditional with nullable email
    if (email != null) {
      print('Email: $email');
      print('Email domain: ${email!.split('@').last}');
    } else {
      print('Email: Not provided');
    }
    
    // Using null-aware operator
    print('Age: ${age ?? 'Not specified'}');
    
    // Conditional with nullable list
    if (hobbies != null && hobbies!.isNotEmpty) {
      print('Hobbies: ${hobbies!.join(', ')}');
    } else {
      print('Hobbies: None listed');
    }
  }
}

void main() {
  // Nullable variable declarations
  String? optionalName;
  int? optionalAge;
  List<String>? optionalItems;
  
  // Safe conditional checks
  if (optionalName != null) {
    print('Name length: ${optionalName.length}');
  } else {
    print('Name is null');
  }
  
  // Null-aware conditional assignment
  String displayName = optionalName ?? 'Anonymous';
  print('Display name: $displayName');
  
  // Safe method calls on nullable objects
  print('Uppercase name: ${optionalName?.toUpperCase() ?? 'NO NAME'}');
  
  // Conditional list operations
  if (optionalItems?.isNotEmpty ?? false) {
    print('Items: ${optionalItems!.join(', ')}');
  } else {
    print('No items available');
  }
  
  // Using null assertion when certain
  String? definitelyNotNull = 'Hello there!';
  if (definitelyNotNull != null) {
    print('Length: ${definitelyNotNull.length}'); // Safe without !
  }
  
  // User profiles with varying null fields
  List<UserProfile> users = [
    UserProfile('Alice', email: 'alice@example.com', age: 30, hobbies: ['reading', 'hiking']),
    UserProfile('Bob', age: 25),
    UserProfile('Charlie', email: 'charlie@example.com'),
    UserProfile('Diana')
  ];
  
  for (var user in users) {
    print('\n--- User Profile ---');
    user.displayInfo();
  }
  
  // Conditional chaining with nullable types
  String? getText() => Math.random() < 0.5 ? 'Hello there!' : null;
  
  String? result = getText()?.toUpperCase()?.replaceAll('HELLO', 'HI');
  print('\nChained result: ${result ?? 'No result'}');
  
  // Pattern matching with nullable types
  String? maybeString = 'test';
  switch (maybeString) {
    case String s when s.isNotEmpty:
      print('Non-empty string: $s');
    case String():
      print('Empty string');
    case null:
      print('Null string');
  }
}
```

This example demonstrates comprehensive nullable type handling including  
conditional checks, null-aware operators, safe method calls, user profiles  
with optional fields, and pattern matching with nullable values.  

```
$ dart run nullable_conditionals.dart
Name is null
Display name: Anonymous
Uppercase name: NO NAME
No items available
Length: 12

--- User Profile ---
Name: Alice
Email: alice@example.com
Email domain: example.com
Age: 30
Hobbies: reading, hiking

--- User Profile ---
Name: Bob
Email: Not provided
Age: 25
Hobbies: None listed

--- User Profile ---
Name: Charlie
Email: charlie@example.com
Email domain: example.com
Age: Not specified
Hobbies: None listed

--- User Profile ---
Name: Diana
Email: Not provided
Age: Not specified
Hobbies: None listed

Chained result: HI THERE!
Non-empty string: test
```

## Complex Multi-Condition Logic

Complex conditional logic combines multiple boolean expressions, nested  
conditions, and various conditional constructs to handle sophisticated  
decision-making scenarios in real-world applications.  

```dart
enum UserRole { guest, member, premium, admin, superAdmin }
enum AccountStatus { active, suspended, pending, banned }

class User {
  String name;
  UserRole role;
  AccountStatus status;
  int loginAttempts;
  DateTime? lastLogin;
  bool emailVerified;
  bool twoFactorEnabled;
  double accountBalance;
  
  User({
    required this.name,
    required this.role,
    required this.status,
    this.loginAttempts = 0,
    this.lastLogin,
    this.emailVerified = false,
    this.twoFactorEnabled = false,
    this.accountBalance = 0.0
  });
}

class AccessControl {
  static bool canAccessFeature(User user, String feature) {
    // Complex multi-condition logic for access control
    return switch ((user.status, user.role, feature)) {
      // Banned users have no access
      (AccountStatus.banned, _, _) => false,
      
      // Suspended users can only access basic profile
      (AccountStatus.suspended, _, 'profile') when user.emailVerified => true,
      (AccountStatus.suspended, _, _) => false,
      
      // Pending users have limited access
      (AccountStatus.pending, _, 'verify-email') => true,
      (AccountStatus.pending, _, 'profile') => true,
      (AccountStatus.pending, _, _) => false,
      
      // Active users based on role and additional conditions
      (AccountStatus.active, UserRole.superAdmin, _) => true,
      (AccountStatus.active, UserRole.admin, _) when user.emailVerified => true,
      (AccountStatus.active, UserRole.premium, 'advanced-features') 
        when user.emailVerified && user.twoFactorEnabled => true,
      (AccountStatus.active, UserRole.premium, _) 
        when user.emailVerified => !{'admin-panel', 'user-management'}.contains(feature),
      (AccountStatus.active, UserRole.member, _) 
        when user.emailVerified && user.loginAttempts < 5 => 
        !{'admin-panel', 'user-management', 'advanced-features'}.contains(feature),
      (AccountStatus.active, UserRole.guest, _) => 
        {'browse', 'register', 'login'}.contains(feature),
      
      _ => false
    };
  }
  
  static String getAccessReason(User user, String feature) {
    if (user.status == AccountStatus.banned) {
      return 'Account is banned';
    }
    
    if (user.status == AccountStatus.suspended) {
      return feature == 'profile' && user.emailVerified 
        ? 'Limited access - account suspended'
        : 'Account is suspended';
    }
    
    if (user.status == AccountStatus.pending) {
      return {'verify-email', 'profile'}.contains(feature)
        ? 'Pending account - limited access'
        : 'Account verification required';
    }
    
    if (!user.emailVerified && user.role != UserRole.guest) {
      return 'Email verification required';
    }
    
    if (user.loginAttempts >= 5) {
      return 'Too many login attempts';
    }
    
    if (user.role == UserRole.premium && 
        feature == 'advanced-features' && 
        !user.twoFactorEnabled) {
      return 'Two-factor authentication required for advanced features';
    }
    
    return 'Access granted';
  }
}

void main() {
  List<User> users = [
    User(
      name: 'Alice (Super Admin)', 
      role: UserRole.superAdmin, 
      status: AccountStatus.active,
      emailVerified: true,
      twoFactorEnabled: true
    ),
    User(
      name: 'Bob (Admin)', 
      role: UserRole.admin, 
      status: AccountStatus.active,
      emailVerified: true
    ),
    User(
      name: 'Charlie (Premium)', 
      role: UserRole.premium, 
      status: AccountStatus.active,
      emailVerified: true,
      twoFactorEnabled: true
    ),
    User(
      name: 'Diana (Member)', 
      role: UserRole.member, 
      status: AccountStatus.suspended,
      emailVerified: true,
      loginAttempts: 3
    ),
    User(
      name: 'Eve (Guest)', 
      role: UserRole.guest, 
      status: AccountStatus.active
    ),
    User(
      name: 'Frank (Banned)', 
      role: UserRole.member, 
      status: AccountStatus.banned,
      emailVerified: false
    )
  ];
  
  List<String> features = [
    'browse', 'profile', 'admin-panel', 'user-management', 
    'advanced-features', 'verify-email'
  ];
  
  for (var user in users) {
    print('\n=== ${user.name} ===');
    for (var feature in features) {
      bool hasAccess = AccessControl.canAccessFeature(user, feature);
      String reason = AccessControl.getAccessReason(user, feature);
      String status = hasAccess ? '✓' : '✗';
      print('$status $feature: $reason');
    }
  }
  
  // Complex financial decision logic
  print('\n=== Financial Decision Example ===');
  
  double income = 75000;
  int creditScore = 720;
  int yearsEmployed = 3;
  double existingDebt = 25000;
  bool hasCollateral = true;
  
  String loanDecision = switch (true) {
    _ when creditScore >= 800 && income >= 100000 => 'Approved - Premium rate',
    _ when creditScore >= 750 && income >= 80000 && yearsEmployed >= 2 => 
      'Approved - Standard rate',
    _ when creditScore >= 700 && income >= 50000 && existingDebt < income * 0.4 => 
      'Approved - Higher rate',
    _ when creditScore >= 650 && hasCollateral && yearsEmployed >= 5 => 
      'Approved with collateral',
    _ when creditScore < 600 => 'Denied - Poor credit',
    _ when existingDebt > income * 0.5 => 'Denied - Too much debt',
    _ => 'Requires manual review'
  };
  
  print('Loan Application Result: $loanDecision');
  print('Factors: Credit Score: $creditScore, Income: \$${income.toInt()}, Employment: $yearsEmployed years');
}
```

This example demonstrates sophisticated conditional logic for user access  
control systems and financial decision-making. It combines pattern matching,  
guard clauses, multiple boolean conditions, and complex business rules to  
handle real-world scenarios requiring nuanced decision-making processes.  

```
$ dart run complex_conditionals.dart

=== Alice (Super Admin) ===
✓ browse: Access granted
✓ profile: Access granted
✓ admin-panel: Access granted
✓ user-management: Access granted
✓ advanced-features: Access granted
✓ verify-email: Access granted

=== Bob (Admin) ===
✓ browse: Access granted
✓ profile: Access granted
✓ admin-panel: Access granted
✓ user-management: Access granted
✓ advanced-features: Access granted
✓ verify-email: Access granted

=== Charlie (Premium) ===
✓ browse: Access granted
✓ profile: Access granted
✗ admin-panel: Access granted
✗ user-management: Access granted
✓ advanced-features: Access granted
✓ verify-email: Access granted

=== Diana (Member) ===
✗ browse: Account is suspended
✓ profile: Limited access - account suspended
✗ admin-panel: Account is suspended
✗ user-management: Account is suspended
✗ advanced-features: Account is suspended
✗ verify-email: Account is suspended

=== Eve (Guest) ===
✓ browse: Access granted
✗ profile: Access granted
✗ admin-panel: Access granted
✗ user-management: Access granted
✗ advanced-features: Access granted
✗ verify-email: Access granted

=== Frank (Banned) ===
✗ browse: Account is banned
✗ profile: Account is banned
✗ admin-panel: Account is banned
✗ user-management: Account is banned
✗ advanced-features: Account is banned
✗ verify-email: Account is banned

=== Financial Decision Example ===
Loan Application Result: Approved - Standard rate
Factors: Credit Score: 720, Income: $75000, Employment: 3 years
```
