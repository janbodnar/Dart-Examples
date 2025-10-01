
# Null Safety in Dart

Null safety is a powerful feature in Dart that helps prevent null reference  
errors at compile time. By making nullability part of the type system, Dart  
ensures that variables cannot contain null values unless explicitly declared  
as nullable. This leads to safer, more reliable code with fewer runtime  
crashes.  

Dart's null safety is sound, meaning the type system guarantees that a  
non-nullable variable will never contain null. The compiler uses flow  
analysis to track variable states and ensure null safety throughout the  
program execution.  

## Introduction to Null Safety

Null safety divides types into nullable and non-nullable categories. A  
non-nullable type guarantees a value will never be null, while nullable  
types explicitly allow null values using the `?` suffix.  

```dart
void main() {
  // Non-nullable types - cannot be null
  String name = 'Alice';
  int age = 30;
  bool isActive = true;
  
  // name = null; // Error: Can't assign null to non-nullable
  
  // Nullable types - can be null
  String? nickname;
  int? score;
  bool? verified;
  
  print('Name: $name');
  print('Nickname: $nickname');
  print('Score: $score');
  print('Verified: $verified');
}
```

This example contrasts non-nullable and nullable types. Non-nullable  
variables must always have values, while nullable variables can be null.  
The type system enforces these rules at compile time, preventing null  
reference errors.  

```
$ dart run intro_null_safety.dart
Name: Alice
Nickname: null
Score: null
Verified: null
```

## Nullable vs Non-nullable Types

Understanding the distinction between nullable and non-nullable types is  
fundamental to working with Dart's null safety. The type system treats  
them as separate types with different capabilities.  

```dart
void main() {
  // Non-nullable String
  String nonNullable = 'Hello there!';
  print('Length: ${nonNullable.length}');
  
  // Nullable String
  String? nullable = 'World';
  // print(nullable.length); // Error: nullable might be null
  
  // Safe access with null check
  if (nullable != null) {
    print('Length: ${nullable.length}');
  }
  
  // Type hierarchy
  String regular = 'text';
  String? optional = regular; // OK: non-nullable to nullable
  // regular = optional; // Error: nullable to non-nullable
  
  // Working with nullable values
  String? maybeNull;
  String definitelyNotNull = maybeNull ?? 'default';
  print('Value: $definitelyNotNull');
}
```

This example shows how non-nullable types can be accessed directly while  
nullable types require null checks or null-aware operators. The type  
system ensures type safety through these restrictions.  

```
$ dart run nullable_vs_non_nullable.dart
Length: 13
Length: 5
Value: default
```

## Null Safety Operators Overview

Dart provides several operators specifically designed for working with  
nullable types. These operators enable safe and concise null handling  
throughout your code.  

```dart
void main() {
  String? nullable = 'test';
  String? nullValue;
  
  // Null assertion operator (!)
  String assured = nullable!;
  print('Assured: $assured');
  
  // Null-aware access operator (?.)
  int? length = nullValue?.length;
  print('Length: $length');
  
  // Null-coalescing operator (??)
  String value = nullValue ?? 'default';
  print('Value: $value');
  
  // Null-aware assignment (??=)
  String? message;
  message ??= 'initialized';
  print('Message: $message');
  
  // Cascade null-aware operator (?..)
  StringBuffer? buffer = StringBuffer();
  buffer?..write('Hello')..write(' ')..write('World');
  print('Buffer: $buffer');
}
```

This example demonstrates the four main null safety operators: null  
assertion (!), null-aware access (?.),  null-coalescing (??), and  
null-aware assignment (??=), each serving distinct purposes in handling  
nullable values.  

```
$ dart run null_operators_overview.dart
Assured: test
Length: null
Value: default
Message: initialized
Buffer: Hello World
```

## Null Assertion Operator

The null assertion operator (!) tells the compiler that a nullable value  
is definitely not null at this point. Use it only when you're certain the  
value cannot be null, as it throws a runtime error if the value is null.  

```dart
void main() {
  // Safe use of null assertion
  String? initialized = 'Hello there!';
  String nonNull = initialized!;
  print('Non-null: $nonNull');
  
  // With method calls
  String? text = 'flutter';
  print('Uppercase: ${text!.toUpperCase()}');
  
  // In collections
  List<String?> items = ['apple', 'banana', null, 'cherry'];
  List<String> nonNullItems = items
      .where((item) => item != null)
      .map((item) => item!)
      .toList();
  print('Items: $nonNullItems');
  
  // Dangerous use - runtime error
  try {
    String? nothing;
    String result = nothing!; // Throws error
    print(result);
  } catch (e) {
    print('Error: ${e.runtimeType}');
  }
}
```

This example shows proper use of null assertion after verification and  
demonstrates the runtime error that occurs when asserting null values.  
Always prefer null-aware operators over null assertion when possible.  


```dart
void main() {
  String word = randomWord()!;
  print('Random word: $word');
}

String? randomWord() {
  List<String?> words = ['apple', null, 'banana', 'cherry', null];
  words.shuffle();
  return words.first;
}
```

This Dart code demonstrates how to work with nullable types and the null assertion operator  
(`!`). It defines a function `randomWord()` that randomly selects the first item from a  
shuffled list containing both strings and `null` values. In `main()`, the result is forcefully  
unwrapped using `!`, assuming it's non-null, and printed. However, because the list includes  
`null`, this can lead to a runtime error if `null` is selected. The example highlights the  
risks of using `!` without proper null checks and underscores the importance of safe handling  
when dealing with potentially nullable data.



## Null-aware Access Operator

The null-aware access operator (?.) safely accesses properties and methods  
on nullable objects. If the object is null, the expression evaluates to  
null instead of throwing an error.  

```dart
void main() {
  String? name = 'Alice';
  String? empty;
  
  // Safe property access
  int? nameLength = name?.length;
  int? emptyLength = empty?.length;
  print('Name length: $nameLength');
  print('Empty length: $emptyLength');
  
  // Safe method calls
  String? upper = name?.toUpperCase();
  String? trimmed = empty?.trim();
  print('Uppercase: $upper');
  print('Trimmed: $trimmed');
  
  // Chaining null-aware access
  String? result = name?.substring(0, 3)?.toUpperCase();
  print('Result: $result');
  
  // With collections
  List<int>? numbers;
  int? first = numbers?.first;
  int? length = numbers?.length;
  print('First: $first, Length: $length');
}
```

This example demonstrates null-aware access for properties, methods, and  
method chaining. Each operation returns null if the receiver is null,  
preventing null reference errors.  

```
$ dart run null_aware_access.dart
Name length: 5
Empty length: null
Uppercase: ALICE
Trimmed: null
Result: ALI
First: null, Length: null
```

## Null-coalescing Operator

The null-coalescing operator (??) provides default values for null  
expressions. It returns the left operand if it's not null, otherwise it  
returns the right operand.  

```dart
void main() {
  String? username;
  String? email = 'user@example.com';
  
  // Providing defaults
  String displayName = username ?? 'Guest';
  String contact = email ?? 'No email';
  print('Display: $displayName');
  print('Contact: $contact');
  
  // Chaining null-coalescing
  String? primary;
  String? secondary;
  String? tertiary = 'third';
  String result = primary ?? secondary ?? tertiary ?? 'default';
  print('Result: $result');
  
  // With expressions
  int? score;
  String message = (score ?? 0) > 50 ? 'Pass' : 'Fail';
  print('Message: $message');
  
  // With collections
  List<String>? items;
  List<String> safeList = items ?? ['default'];
  print('List: $safeList');
}
```

This example shows how null-coalescing provides elegant default values  
for nullable variables, supports chaining for multiple fallbacks, and  
works seamlessly with other expressions.  

```
$ dart run null_coalescing.dart
Display: Guest
Contact: user@example.com
Result: third
Message: Fail
List: [default]
```

## Null-aware Assignment

The null-aware assignment operator (??=) assigns a value to a variable  
only if that variable is currently null. This provides efficient lazy  
initialization and conditional assignment.  

```dart
void main() {
  String? name;
  name ??= 'Default Name';
  print('First assignment: $name');
  
  // Won't reassign if already set
  name ??= 'Another Name';
  print('Second assignment: $name');
  
  // Lazy initialization pattern
  String? cache;
  String getValue() {
    cache ??= 'Expensive computation result';
    return cache!;
  }
  
  print('First call: ${getValue()}');
  print('Second call: ${getValue()}');
  
  // With collections
  List<int>? numbers;
  numbers ??= [1, 2, 3];
  print('Numbers: $numbers');
  
  // In loops
  Map<String, String> config = {};
  config['theme'] ??= 'light';
  config['theme'] ??= 'dark'; // Won't change
  print('Theme: ${config['theme']}');
}
```

This example demonstrates null-aware assignment for default initialization,  
lazy computation patterns, and conditional map value assignment. The  
operator only assigns when the current value is null.  

```
$ dart run null_aware_assignment.dart
First assignment: Default Name
Second assignment: Default Name
First call: Expensive computation result
Second call: Expensive computation result
Numbers: [1, 2, 3]
Theme: light
```

## Null Safety with Collections

Collections in Dart can be nullable, contain nullable elements, or both.  
Understanding these distinctions is crucial for working safely with  
collection types.  

```dart
void main() {
  // Non-nullable list of non-nullable elements
  List<int> numbers = [1, 2, 3];
  // numbers = null; // Error
  // numbers.add(null); // Error
  
  // Nullable list of non-nullable elements
  List<int>? nullableList;
  print('Nullable list: $nullableList');
  nullableList = [4, 5, 6];
  print('After assignment: $nullableList');
  
  // Non-nullable list of nullable elements
  List<int?> mixedList = [1, null, 3, null, 5];
  print('Mixed list: $mixedList');
  
  // Nullable list of nullable elements
  List<String?>? fullyNullable;
  fullyNullable = ['a', null, 'b'];
  print('Fully nullable: $fullyNullable');
  
  // Safe operations
  int? first = nullableList?.first;
  int sum = mixedList.where((e) => e != null).fold(0, (a, b) => a + b!);
  print('First: $first, Sum: $sum');
}
```

This example shows the four possible combinations of nullability with  
collections: nullable/non-nullable containers and nullable/non-nullable  
elements, demonstrating safe operations on each.  


The next Dart example demonstrates safe handling of nullable lists and nullable  
elements within a list.  

```dart
void main() {
  var nullableList = getNullableList(); // Could be null

  // Safe operations
  int? first = nullableList?.first;

  print(first);

  if (nullableList != null) {
    int sum = nullableList.where((e) => e != null).fold(0, (a, b) => a + b!);
    print('First: $first, Sum: $sum');
  }
}

List<int?>? getNullableList() {
  if (DateTime.now().second % 2 == 0) {
    return null; // Return null half the time
  } else if (DateTime.now().second % 3 == 0) {
    return [1, 2, 3]; // Return a list half the time
  } else {
    return [1, null, 3, null, 5]; // Return a list with nulls
  }
}
```

The `getNullableList()` function returns a `List<int?>?`, which can be either  
`null`, a list of non-null integers, or a list containing `null` values—based  
on the current second of the system clock. In `main()`, the code safely  
accesses the first element using the null-aware operator (`?.`) and prints it.  

If the list itself is not `null`, it filters out any `null` entries and  
calculates the sum of the remaining integers using `fold`. The use of `b!`  
inside the fold ensures that each element is non-null at that point, thanks to  
the preceding `where` filter. This example highlights Dart’s null safety  
features and shows how to work with potentially nullable collections in a safe  
and expressive way.  



## Late Final Variables

Combining `late` and `final` creates variables that must be initialized  
exactly once before use, but the initialization can be deferred. This is  
ideal for immutable values that require complex setup.  

```dart
class Configuration {
  late final String apiKey;
  late final int timeout;
  late final bool debugMode;
  
  void loadConfig() {
    apiKey = 'abc123';
    timeout = 30;
    debugMode = true;
  }
}

void main() {
  var config = Configuration();
  config.loadConfig();
  
  print('API Key: ${config.apiKey}');
  print('Timeout: ${config.timeout}');
  print('Debug: ${config.debugMode}');
  
  // config.apiKey = 'new'; // Error: late final can't be reassigned
  
  // Lazy late final
  late final String computed = performComputation();
  print('Using computed value: $computed');
  
  // Only initialized once
  print('Using again: $computed');
}

String performComputation() {
  print('Performing computation...');
  return 'Computed result';
}
```

This example shows late final variables in class definitions and lazy  
evaluation patterns. Late final combines deferred initialization with  
immutability, ensuring values are set exactly once.  

```
$ dart run late_final_variables.dart
API Key: abc123
Timeout: 30
Debug: true
Performing computation...
Using computed value: Computed result
Using again: Computed result
```

## Nullable Function Parameters

Function parameters can be nullable, allowing functions to accept and  
handle null values explicitly. This makes function contracts clear and  
prevents accidental null passing.  

```dart
// Required non-nullable parameter
void greet(String name) {
  print('Hello there, $name!');
}

// Nullable parameter with default
void greetOptional(String? name) {
  print('Hello there, ${name ?? 'Guest'}!');
}

// Nullable with null handling
int? findLength(String? text) {
  return text?.length;
}

// Multiple nullable parameters
String formatName(String? first, String? last) {
  if (first != null && last != null) {
    return '$first $last';
  } else if (first != null) {
    return first;
  } else if (last != null) {
    return last;
  }
  return 'Unknown';
}

void main() {
  greet('Alice');
  greetOptional('Bob');
  greetOptional(null);
  
  print('Length: ${findLength('test')}');
  print('Null length: ${findLength(null)}');
  
  print(formatName('John', 'Doe'));
  print(formatName('Jane', null));
  print(formatName(null, null));
}
```

This example demonstrates various patterns for nullable function  
parameters, including default handling, null propagation, and conditional  
logic based on parameter nullability.  

```
$ dart run nullable_parameters.dart
Hello there, Alice!
Hello there, Bob!
Hello there, Guest!
Length: 4
Null length: null
John Doe
Jane
Unknown
```

## Required Named Parameters

Named parameters in Dart can be marked as required, ensuring callers must  
provide values. This works with null safety to create clear, safe function  
signatures.  

```dart
// Required named parameters
void createUser({
  required String username,
  required String email,
  int? age,
  String? phone,
}) {
  print('User: $username');
  print('Email: $email');
  print('Age: ${age ?? 'Not specified'}');
  print('Phone: ${phone ?? 'Not specified'}');
}

// With nullable required parameters
void processData({
  required String? input,
  required bool validate,
}) {
  if (input == null) {
    print('No input provided');
    return;
  }
  
  if (validate) {
    print('Processing validated: $input');
  } else {
    print('Processing unvalidated: $input');
  }
}

void main() {
  createUser(
    username: 'alice',
    email: 'alice@example.com',
    age: 25,
  );
  
  print('---');
  
  createUser(
    username: 'bob',
    email: 'bob@example.com',
  );
  
  print('---');
  
  processData(input: 'data', validate: true);
  processData(input: null, validate: false);
}
```

This example shows required named parameters with both nullable and  
non-nullable types. Required ensures parameters must be explicitly  
provided, while nullability controls whether null is an acceptable value.  

```
$ dart run required_parameters.dart
User: alice
Email: alice@example.com
Age: 25
Phone: Not specified
---
User: bob
Email: bob@example.com
Age: Not specified
Phone: Not specified
---
Processing validated: data
No input provided
```

## Nullable Return Types

Functions can return nullable types, explicitly indicating they might  
return null. This makes function contracts clear and forces callers to  
handle potential null values.  

```dart
// Returns nullable String
String? findUser(int id) {
  if (id == 1) return 'Alice';
  if (id == 2) return 'Bob';
  return null;
}

// Returns nullable from collection
int? findFirst(List<int> numbers, bool Function(int) predicate) {
  for (var num in numbers) {
    if (predicate(num)) return num;
  }
  return null;
}

// Async nullable return
Future<String?> fetchData(String url) async {
  await Future.delayed(Duration(milliseconds: 100));
  return url.contains('valid') ? 'Data from $url' : null;
}

void main() async {
  // Handling nullable returns
  String? user = findUser(1);
  print('User 1: ${user ?? 'Not found'}');
  
  String? missing = findUser(99);
  print('User 99: ${missing ?? 'Not found'}');
  
  // With predicates
  int? even = findFirst([1, 3, 4, 5], (n) => n % 2 == 0);
  print('First even: ${even ?? 'None'}');
  
  // Async nullable
  String? data = await fetchData('valid-url');
  print('Data: ${data ?? 'Failed'}');
  
  String? failed = await fetchData('invalid');
  print('Failed: ${failed ?? 'No data'}');
}
```

This example demonstrates nullable return types with synchronous and  
asynchronous functions. Callers must check for null or use null-aware  
operators to handle the potential absence of values.  

```
$ dart run nullable_return_types.dart
User 1: Alice
User 99: Not found
First even: 4
Data: Data from valid-url
Failed: No data
```

## Null Safety with Classes

Classes in Dart fully support null safety, requiring careful consideration  
of field initialization and nullability. All non-nullable fields must be  
initialized before the constructor completes.  

```dart
class Person {
  // Non-nullable fields must be initialized
  String name;
  int age;
  
  // Nullable fields can be left uninitialized
  String? nickname;
  String? email;
  
  Person(this.name, this.age);
  
  void printInfo() {
    print('Name: $name, Age: $age');
    print('Nickname: ${nickname ?? 'None'}');
    print('Email: ${email ?? 'None'}');
  }
}

class Product {
  final String id;
  final String name;
  final double price;
  String? description;
  
  Product({
    required this.id,
    required this.name,
    required this.price,
    this.description,
  });
  
  @override
  String toString() {
    return 'Product($name: \$$price)';
  }
}

void main() {
  var person = Person('Alice', 30);
  person.nickname = 'Ali';
  person.printInfo();
  
  print('---');
  
  var product = Product(
    id: 'p1',
    name: 'Widget',
    price: 29.99,
    description: 'A useful widget',
  );
  print(product);
}
```

This example shows null safety in class definitions with required  
initialization for non-nullable fields and optional nullable fields.  
The type system ensures all required fields are properly initialized.  

```
$ dart run null_safety_classes.dart
Name: Alice, Age: 30
Nickname: Ali
Email: None
---
Product(Widget: $29.99)
```

## Nullable Class Properties

Class properties can be nullable, allowing flexible object states. Proper  
handling of nullable properties ensures safe access throughout the class  
lifecycle.  

```dart
class UserProfile {
  String username;
  String? bio;
  String? avatarUrl;
  DateTime? lastLogin;
  
  UserProfile(this.username);
  
  bool get hasCompletedProfile => bio != null && avatarUrl != null;
  
  String get displayBio => bio ?? 'No bio provided';
  
  String? get formattedLastLogin {
    return lastLogin?.toIso8601String();
  }
  
  void updateBio(String? newBio) {
    bio = newBio;
  }
  
  void recordLogin() {
    lastLogin = DateTime.now();
  }
}

void main() {
  var profile = UserProfile('alice123');
  print('Username: ${profile.username}');
  print('Completed: ${profile.hasCompletedProfile}');
  print('Bio: ${profile.displayBio}');
  
  profile.updateBio('Software developer');
  profile.avatarUrl = 'https://example.com/avatar.png';
  profile.recordLogin();
  
  print('---');
  print('Completed: ${profile.hasCompletedProfile}');
  print('Bio: ${profile.displayBio}');
  print('Last login: ${profile.formattedLastLogin}');
}
```

This example demonstrates nullable properties with getters that handle  
null states gracefully. The class provides safe ways to query and update  
nullable properties while maintaining type safety.  

```
$ dart run nullable_properties.dart
Username: alice123
Completed: false
Bio: No bio provided
---
Completed: true
Bio: Software developer
Last login: 2024-01-15T10:30:00.000
```

## Null Safety with Constructors

Constructors must initialize all non-nullable fields. Dart provides  
several patterns for constructor initialization compatible with null  
safety.  

```dart
class Rectangle {
  double width;
  double height;
  String? label;
  
  // Standard constructor
  Rectangle(this.width, this.height, [this.label]);
  
  // Named constructor
  Rectangle.square(double size) : width = size, height = size;
  
  // Named constructor with nullable parameter
  Rectangle.withLabel(this.width, this.height, String? label)
      : label = label ?? 'Unlabeled';
  
  double get area => width * height;
  
  @override
  String toString() {
    return 'Rectangle($width x $height)${label != null ? ' "$label"' : ''}';
  }
}

class Point {
  final double x;
  final double y;
  
  const Point(this.x, this.y);
  
  // Factory constructor that might return null-safe instance
  factory Point.fromJson(Map<String, dynamic> json) {
    return Point(
      (json['x'] as num?)?.toDouble() ?? 0.0,
      (json['y'] as num?)?.toDouble() ?? 0.0,
    );
  }
}

void main() {
  var rect1 = Rectangle(10, 5);
  var rect2 = Rectangle.square(7);
  var rect3 = Rectangle.withLabel(8, 6, 'Main');
  
  print(rect1);
  print(rect2);
  print(rect3);
  
  var point = Point.fromJson({'x': 10, 'y': 20});
  print('Point: (${point.x}, ${point.y})');
}
```

This example shows various constructor patterns with null safety,  
including standard constructors, named constructors, and factory  
constructors that handle nullable inputs safely.  

```
$ dart run null_safety_constructors.dart
Rectangle(10.0 x 5.0)
Rectangle(7.0 x 7.0)
Rectangle(8.0 x 6.0) "Main"
Point: (10.0, 20.0)
```

## Factory Constructors with Null Safety

Factory constructors can implement complex creation logic while maintaining  
null safety. They're useful for singleton patterns, caching, and  
conditional object creation.  

```dart
class Logger {
  final String name;
  static final Map<String, Logger> _cache = {};
  
  // Private constructor
  Logger._(this.name);
  
  // Factory with caching
  factory Logger(String name) {
    return _cache.putIfAbsent(name, () => Logger._(name));
  }
  
  void log(String message) {
    print('[$name] $message');
  }
}

class Result<T> {
  final T? value;
  final String? error;
  
  Result._(this.value, this.error);
  
  factory Result.success(T value) => Result._(value, null);
  factory Result.failure(String error) => Result._(null, error);
  
  bool get isSuccess => value != null;
  bool get isFailure => error != null;
}

void main() {
  // Singleton pattern
  var logger1 = Logger('App');
  var logger2 = Logger('App');
  print('Same instance: ${identical(logger1, logger2)}');
  
  logger1.log('Application started');
  
  // Result pattern
  var success = Result<int>.success(42);
  var failure = Result<int>.failure('Not found');
  
  print('Success: ${success.isSuccess}, Value: ${success.value}');
  print('Failure: ${failure.isFailure}, Error: ${failure.error}');
}
```

This example demonstrates factory constructors for caching and result  
patterns. Factory constructors enable controlled instance creation while  
maintaining null safety guarantees.  

```
$ dart run factory_null_safety.dart
Same instance: true
[App] Application started
Success: true, Value: 42
Failure: true, Error: Not found
```

## Null Safety with Getters and Setters

Getters and setters can work with nullable types, providing controlled  
access to object state while handling null cases appropriately.  

```dart
class Temperature {
  double? _celsius;
  
  // Nullable getter
  double? get celsius => _celsius;
  
  // Non-nullable getter with default
  double get celsiusOrZero => _celsius ?? 0.0;
  
  // Nullable setter
  set celsius(double? value) {
    _celsius = value;
  }
  
  // Computed nullable getter
  double? get fahrenheit {
    return _celsius != null ? _celsius! * 9 / 5 + 32 : null;
  }
  
  // Computed setter with validation
  set fahrenheit(double? value) {
    _celsius = value != null ? (value - 32) * 5 / 9 : null;
  }
}

class Account {
  String _username;
  String? _email;
  
  Account(this._username);
  
  String get username => _username;
  
  // Getter with null-safe return
  String get email => _email ?? 'No email set';
  
  // Setter with validation
  set email(String? value) {
    if (value != null && value.contains('@')) {
      _email = value;
    }
  }
}

void main() {
  var temp = Temperature();
  print('Initial: ${temp.celsius}');
  
  temp.celsius = 25.0;
  print('Celsius: ${temp.celsius}');
  print('Fahrenheit: ${temp.fahrenheit}');
  
  temp.fahrenheit = 86.0;
  print('New Celsius: ${temp.celsius}');
  
  var account = Account('bob');
  print('Email: ${account.email}');
  
  account.email = 'bob@example.com';
  print('Updated: ${account.email}');
}
```

This example shows getters and setters with nullable backing fields,  
computed properties, and validation. The pattern enables encapsulated  
null handling while maintaining clean external APIs.  

```
$ dart run getters_setters_null.dart
Initial: null
Celsius: 25.0
Fahrenheit: 77.0
New Celsius: 30.0
Email: No email set
Updated: bob@example.com
```

## Null Safety in Method Chaining

Method chaining with nullable objects requires careful handling to prevent  
null reference errors. Dart provides null-aware operators specifically for  
safe method chaining.  

```dart
class StringBuilder {
  String _content = '';
  
  StringBuilder append(String text) {
    _content += text;
    return this;
  }
  
  StringBuilder appendLine(String text) {
    _content += '$text\n';
    return this;
  }
  
  StringBuilder clear() {
    _content = '';
    return this;
  }
  
  String build() => _content;
}

class Request {
  String? _url;
  Map<String, String> _headers = {};
  
  Request? url(String? value) {
    if (value == null) return null;
    _url = value;
    return this;
  }
  
  Request? header(String key, String? value) {
    if (value == null) return null;
    _headers[key] = value;
    return this;
  }
  
  String? execute() => _url;
}

void main() {
  // Non-nullable chaining
  var text = StringBuilder()
      .append('Hello')
      .append(' ')
      .append('there!')
      .build();
  print('Text: $text');
  
  // Nullable chaining
  String? result1 = Request()
      .url('https://api.example.com')
      ?.header('Auth', 'token123')
      ?.header('Type', 'JSON')
      ?.execute();
  print('Result 1: $result1');
  
  // Breaks on null
  String? result2 = Request()
      .url(null)
      ?.header('Auth', 'token')
      ?.execute();
  print('Result 2: $result2');
}
```

This example demonstrates method chaining with both non-nullable and  
nullable return types. The null-aware operator (?.) safely handles chains  
that might return null at any step.  

```
$ dart run method_chaining_null.dart
Text: Hello there!
Result 1: https://api.example.com
Result 2: null
```

## Type Promotion with Null Checks

Dart's flow analysis performs type promotion after null checks, allowing  
safe access to nullable variables without explicit casting or null  
assertion.  

```dart
void main() {
  String? nullable = 'Hello there!';
  
  // Type promotion after null check
  if (nullable != null) {
    // nullable is promoted to String here
    print('Length: ${nullable.length}');
    print('Upper: ${nullable.toUpperCase()}');
  }
  
  // Type promotion with early return
  String? value;
  
  if (value == null) {
    print('Value is null, returning');
    return;
  }
  
  // value is promoted to String here
  print('Value: ${value.length}');
}

String processText(String? input) {
  // Type promotion in expressions
  if (input == null || input.isEmpty) {
    return 'Empty';
  }
  
  // input is promoted to String here
  return input.toUpperCase();
}

int? findValue(List<int?> list) {
  for (var item in list) {
    if (item != null && item > 10) {
      // item is promoted to int here
      return item * 2;
    }
  }
  return null;
}

void main2() {
  print(processText('test'));
  print(processText(null));
  
  var result = findValue([5, null, 15, 20]);
  print('Found: ${result ?? 'None'}');
}
```

This example shows type promotion in various contexts: if statements,  
early returns, and loop conditions. The compiler tracks variable states  
to enable safe access without redundant checks.  

```
$ dart run type_promotion.dart
Length: 13
Upper: HELLO THERE!
Value is null, returning
```

## Definite Assignment Analysis

Dart's definite assignment analysis ensures that non-nullable local  
variables are initialized before use. This prevents accessing uninitialized  
variables.  

```dart
void main() {
  // Must initialize before use
  String message;
  
  bool condition = true;
  
  if (condition) {
    message = 'Condition is true';
  } else {
    message = 'Condition is false';
  }
  
  // Safe to use because both branches assign
  print('Message: $message');
  
  // Definite assignment with switch
  int value = 2;
  String description;
  
  switch (value) {
    case 1:
      description = 'One';
      break;
    case 2:
      description = 'Two';
      break;
    default:
      description = 'Other';
  }
  
  print('Description: $description');
}

void processData(bool validate) {
  String result;
  
  if (validate) {
    result = 'Valid data';
  } else {
    result = 'Invalid data';
  }
  
  print(result);
}

void main2() {
  processData(true);
  processData(false);
}
```

This example demonstrates definite assignment analysis ensuring all code  
paths initialize variables before use. The compiler verifies that  
non-nullable variables are assigned in all possible execution paths.  

```
$ dart run definite_assignment.dart
Message: Condition is true
Description: Two
```

## Flow Analysis with Null Safety

Flow analysis tracks the state of variables through control flow  
structures, enabling smart type promotion and null safety guarantees  
without explicit casts.  

```dart
void main() {
  String? name;
  
  // No promotion yet
  // print(name.length); // Error
  
  name = 'Alice';
  
  // Now promoted after assignment
  print('Length: ${name.length}');
  
  // Flow analysis with logical operators
  String? value1;
  String? value2 = 'test';
  
  if (value1 != null && value1.isNotEmpty) {
    print('Value1: $value1');
  }
  
  if (value2 != null && value2.length > 2) {
    // value2 is promoted in this block
    print('Value2: ${value2.toUpperCase()}');
  }
  
  // Flow analysis with return
  String? input = 'data';
  
  if (input == null) return;
  
  // input is promoted after null return
  print('Processing: ${input.length}');
}

String? findItem(List<String> items, String search) {
  for (var item in items) {
    if (item.contains(search)) {
      return item;
    }
  }
  return null;
}

void processItem(List<String> items) {
  var found = findItem(items, 'test');
  
  if (found == null) {
    print('Item not found');
    return;
  }
  
  // found is promoted to String here
  print('Found: ${found.toUpperCase()}');
}

void main2() {
  processItem(['test-item', 'other']);
  processItem(['none', 'other']);
}
```

This example shows flow analysis tracking variable states through  
assignments, conditions, and early returns. The compiler understands  
control flow to determine when variables are definitely non-null.  

```
$ dart run flow_analysis.dart
Length: 5
Value2: TEST
Processing: 4
```

## Null Safety with Generics

Generic types in Dart can be nullable or non-nullable, providing flexible  
type-safe containers. Understanding generic type nullability is essential  
for working with collections and custom generic classes.  

```dart
class Box<T> {
  T value;
  
  Box(this.value);
  
  T getValue() => value;
  
  void setValue(T newValue) {
    value = newValue;
  }
}

class Container<T extends Object> {
  T item;
  
  Container(this.item);
  
  // T is guaranteed non-nullable
  int getHashCode() => item.hashCode;
}

class Optional<T> {
  final T? _value;
  
  Optional.of(T value) : _value = value;
  Optional.empty() : _value = null;
  
  bool get isPresent => _value != null;
  
  T? get value => _value;
  
  T orElse(T defaultValue) => _value ?? defaultValue;
  
  Optional<R> map<R>(R Function(T) transform) {
    return _value != null 
        ? Optional.of(transform(_value as T))
        : Optional.empty();
  }
}

void main() {
  // Non-nullable generic
  var box1 = Box<String>('Hello there!');
  print('Box1: ${box1.getValue()}');
  
  // Nullable generic
  var box2 = Box<String?>(null);
  print('Box2: ${box2.getValue()}');
  
  // Bounded generic
  var container = Container<String>('Item');
  print('Hash: ${container.getHashCode()}');
  
  // Optional pattern
  var present = Optional.of(42);
  var empty = Optional<int>.empty();
  
  print('Present: ${present.value}');
  print('Empty: ${empty.orElse(0)}');
  
  var mapped = present.map((n) => n * 2);
  print('Mapped: ${mapped.value}');
}
```

This example demonstrates generic types with different nullability  
constraints, bounded generics requiring non-nullable types, and the  
Optional pattern for explicit presence/absence handling.  

```
$ dart run generics_null_safety.dart
Box1: Hello there!
Box2: null
Hash: 2619464
Present: 42
Empty: 0
Mapped: 84
```

## Nullable Type Parameters

Type parameters in generic classes and functions can be explicitly nullable,  
allowing more flexible generic code that handles both nullable and  
non-nullable types.  

```dart
T? firstOrNull<T>(List<T> list) {
  return list.isEmpty ? null : list.first;
}

T firstOrDefault<T>(List<T> list, T defaultValue) {
  return list.isEmpty ? defaultValue : list.first;
}

class Result<T, E> {
  final T? success;
  final E? error;
  
  Result.success(T value) : success = value, error = null;
  Result.error(E err) : success = null, error = err;
  
  bool get isSuccess => success != null;
  bool get isError => error != null;
  
  R fold<R>(R Function(T) onSuccess, R Function(E) onError) {
    if (success != null) {
      return onSuccess(success as T);
    } else {
      return onError(error as E);
    }
  }
}

void main() {
  var numbers = [1, 2, 3];
  var empty = <int>[];
  
  print('First: ${firstOrNull(numbers)}');
  print('Empty first: ${firstOrNull(empty)}');
  print('With default: ${firstOrDefault(empty, 0)}');
  
  // Result type
  var success = Result<int, String>.success(42);
  var error = Result<int, String>.error('Failed');
  
  var message1 = success.fold(
    (value) => 'Got: $value',
    (err) => 'Error: $err',
  );
  
  var message2 = error.fold(
    (value) => 'Got: $value',
    (err) => 'Error: $err',
  );
  
  print(message1);
  print(message2);
}
```

This example shows nullable type parameters in generic functions and  
classes. The Result pattern demonstrates handling success and error cases  
with generic nullable types.  

```
$ dart run nullable_type_parameters.dart
First: 1
Empty first: null
With default: 0
Got: 42
Error: Failed
```

## Null Safety with Extensions

Extension methods can work with nullable types, adding functionality to  
both nullable and non-nullable types while respecting null safety rules.  

```dart
extension StringExtensions on String {
  String truncate(int maxLength) {
    return length <= maxLength ? this : '${substring(0, maxLength)}...';
  }
  
  bool get isBlank => trim().isEmpty;
}

extension NullableStringExtensions on String? {
  bool get isNullOrEmpty => this == null || this!.isEmpty;
  
  String orDefault(String defaultValue) => this ?? defaultValue;
  
  int get lengthOrZero => this?.length ?? 0;
  
  String? get trimmedOrNull {
    if (this == null) return null;
    var trimmed = this!.trim();
    return trimmed.isEmpty ? null : trimmed;
  }
}

extension ListExtensions<T> on List<T>? {
  bool get isNullOrEmpty => this == null || this!.isEmpty;
  
  List<T> get orEmpty => this ?? [];
  
  T? get safeFirst => this?.isEmpty ?? true ? null : this!.first;
}

void main() {
  // Non-nullable extension
  String text = 'Hello there!';
  print('Truncated: ${text.truncate(5)}');
  
  // Nullable extension
  String? nullable;
  String? value = 'test';
  
  print('Null or empty: ${nullable.isNullOrEmpty}');
  print('Value empty: ${value.isNullOrEmpty}');
  print('Default: ${nullable.orDefault('default')}');
  print('Length: ${nullable.lengthOrZero}');
  
  // List extension
  List<int>? numbers;
  List<int>? items = [1, 2, 3];
  
  print('Numbers empty: ${numbers.isNullOrEmpty}');
  print('Items empty: ${items.isNullOrEmpty}');
  print('Safe first: ${numbers.safeFirst}');
  print('Items first: ${items.safeFirst}');
}
```

This example demonstrates extensions on both nullable and non-nullable  
types. Extensions on nullable types provide convenient null-safe operations  
without requiring explicit null checks at call sites.  

```
$ dart run extensions_null_safety.dart
Truncated: Hello...
Null or empty: true
Value empty: false
Default: default
Length: 0
Numbers empty: true
Items empty: false
Safe first: null
Items first: 1
```

## Null Safety with Async/Await

Asynchronous programming in Dart fully supports null safety, with Future  
and Stream types respecting nullability annotations for safe async  
operations.  

```dart
Future<String?> fetchUserName(int id) async {
  await Future.delayed(Duration(milliseconds: 100));
  return id == 1 ? 'Alice' : null;
}

Future<String> fetchUserNameOrDefault(int id) async {
  var name = await fetchUserName(id);
  return name ?? 'Unknown';
}

Future<int?> parseAsync(String text) async {
  try {
    await Future.delayed(Duration(milliseconds: 50));
    return int.parse(text);
  } catch (e) {
    return null;
  }
}

Future<void> processNullableData(String? data) async {
  if (data == null) {
    print('No data to process');
    return;
  }
  
  await Future.delayed(Duration(milliseconds: 50));
  print('Processed: $data');
}

void main() async {
  // Nullable Future
  String? name1 = await fetchUserName(1);
  String? name2 = await fetchUserName(2);
  
  print('User 1: ${name1 ?? 'Not found'}');
  print('User 2: ${name2 ?? 'Not found'}');
  
  // Non-nullable Future result
  String name = await fetchUserNameOrDefault(2);
  print('Default name: $name');
  
  // Nullable parsing
  int? valid = await parseAsync('42');
  int? invalid = await parseAsync('invalid');
  
  print('Valid: ${valid ?? 'Failed'}');
  print('Invalid: ${invalid ?? 'Failed'}');
  
  // Process nullable
  await processNullableData('test');
  await processNullableData(null);
}
```

This example shows Future types with nullable and non-nullable results,  
async functions handling nullable parameters, and patterns for safe  
asynchronous operations with null safety.  

```
$ dart run async_null_safety.dart
User 1: Alice
User 2: Not found
Default name: Unknown
Valid: 42
Invalid: Failed
Processed: test
No data to process
```

## Null Safety with Streams

Streams in Dart work seamlessly with null safety, allowing nullable events  
while maintaining type safety throughout stream operations.  

```dart
import 'dart:async';

Stream<String?> generateValues() async* {
  yield 'first';
  yield null;
  yield 'second';
  yield null;
  yield 'third';
}

Stream<String> filterNulls(Stream<String?> source) {
  return source.where((item) => item != null).cast<String>();
}

StreamController<int?> createController() {
  return StreamController<int?>();
}

void main() async {
  // Stream with nullable values
  print('--- Nullable stream ---');
  await for (var value in generateValues()) {
    print('Value: ${value ?? 'NULL'}');
  }
  
  // Filtering nulls
  print('--- Filtered stream ---');
  await for (var value in filterNulls(generateValues())) {
    print('Non-null: $value');
  }
  
  // StreamController with nullable
  print('--- Controller ---');
  var controller = createController();
  
  controller.stream.listen((value) {
    print('Received: ${value ?? 'NULL'}');
  });
  
  controller.add(1);
  controller.add(null);
  controller.add(2);
  
  await Future.delayed(Duration(milliseconds: 100));
  await controller.close();
}
```

This example demonstrates streams with nullable event types, filtering  
null values from streams, and using StreamController with nullable types  
for flexible event-driven programming.  

```
$ dart run streams_null_safety.dart
--- Nullable stream ---
Value: first
Value: NULL
Value: second
Value: NULL
Value: third
--- Filtered stream ---
Non-null: first
Non-null: second
Non-null: third
--- Controller ---
Received: 1
Received: NULL
Received: 2
```

## Null Safety Error Handling

Error handling with null safety combines nullable return types and  
exception handling to create robust, predictable error management  
strategies.  

```dart
class ParseError {
  final String message;
  final String input;
  
  ParseError(this.message, this.input);
  
  @override
  String toString() => 'ParseError: $message (input: "$input")';
}

int? tryParseInt(String input) {
  try {
    return int.parse(input);
  } catch (e) {
    return null;
  }
}

Result<int, ParseError> parseIntSafe(String input) {
  try {
    return Result.success(int.parse(input));
  } catch (e) {
    return Result.error(ParseError('Invalid integer', input));
  }
}

class Result<T, E> {
  final T? _value;
  final E? _error;
  
  Result.success(T value) : _value = value, _error = null;
  Result.error(E error) : _value = null, _error = error;
  
  bool get isSuccess => _value != null;
  T get value => _value!;
  E get error => _error!;
  
  R when<R>({
    required R Function(T) success,
    required R Function(E) error,
  }) {
    return _value != null ? success(_value as T) : error(_error as E);
  }
}

void main() {
  // Nullable return pattern
  var result1 = tryParseInt('42');
  var result2 = tryParseInt('invalid');
  
  print('Result 1: ${result1 ?? 'Failed'}');
  print('Result 2: ${result2 ?? 'Failed'}');
  
  // Result type pattern
  var safe1 = parseIntSafe('123');
  var safe2 = parseIntSafe('bad');
  
  safe1.when(
    success: (value) => print('Parsed: $value'),
    error: (err) => print(err),
  );
  
  safe2.when(
    success: (value) => print('Parsed: $value'),
    error: (err) => print(err),
  );
}
```

This example shows two patterns for error handling with null safety:  
nullable returns for simple cases and Result types for detailed error  
information. Both approaches maintain type safety and explicitness.  

```
$ dart run error_handling_null.dart
Result 1: 42
Result 2: Failed
Parsed: 123
ParseError: Invalid integer (input: "bad")
```

## Migration from Legacy Code

Migrating legacy Dart code to null safety requires understanding migration  
strategies and using tools to ensure smooth transitions while maintaining  
functionality.  

```dart
// Before null safety (legacy code pattern)
class LegacyUser {
  String name;    // Could be null in practice
  int age;        // Could be null in practice
  String email;   // Could be null in practice
  
  // Constructor that might not initialize all fields
  LegacyUser(this.name, this.age, [String email = '']) : this.email = email;
}

// After null safety (migrated code)
class User {
  String name;           // Required, non-nullable
  int age;              // Required, non-nullable
  String? email;        // Explicitly nullable
  
  User({
    required this.name,
    required this.age,
    this.email,
  });
}

// Migration helper functions
String? migrateLegacyString(dynamic value) {
  return value is String ? value : null;
}

int migrateToInt(dynamic value, {int defaultValue = 0}) {
  if (value is int) return value;
  if (value is String) return int.tryParse(value) ?? defaultValue;
  return defaultValue;
}

void main() {
  // Old style - risky
  // var legacy = LegacyUser(null, null); // Would compile in legacy
  
  // New style - safe
  var user = User(
    name: 'Alice',
    age: 30,
    email: 'alice@example.com',
  );
  
  print('User: ${user.name}, ${user.age}');
  print('Email: ${user.email ?? 'Not provided'}');
  
  // Migration pattern
  Map<String, dynamic> legacyData = {
    'name': 'Bob',
    'age': '25',
    'email': null,
  };
  
  var migrated = User(
    name: migrateLegacyString(legacyData['name']) ?? 'Unknown',
    age: migrateToInt(legacyData['age']),
    email: migrateLegacyString(legacyData['email']),
  );
  
  print('Migrated: ${migrated.name}, ${migrated.age}');
}
```

This example contrasts legacy and null-safe code patterns, showing  
migration strategies including explicit nullable types, required  
parameters, and helper functions for safely converting legacy data.  

```
$ dart run migration_null_safety.dart
User: Alice, 30
Email: alice@example.com
Migrated: Bob, 25
```

## Best Practices for Null Safety

Following null safety best practices leads to more maintainable, safer  
code. These patterns help you write idiomatic null-safe Dart.  

```dart
void main() {
  // 1. Prefer non-nullable types
  String name = 'Alice';  // Good
  String? nullable;       // Only when null is meaningful
  
  // 2. Use null-aware operators instead of null checks
  String message = nullable ?? 'default';  // Good
  // if (nullable == null) message = 'default'; // Verbose
  
  // 3. Use late for non-nullable fields with delayed init
  late String computed;
  computed = expensiveComputation();
  print('Computed: $computed');
  
  // 4. Prefer required named parameters
  createUser(name: 'Bob', email: 'bob@example.com');
  
  // 5. Use type promotion instead of null assertion
  String? value = 'test';
  if (value != null) {
    print(value.length);  // Good - promoted
    // print(value!.length); // Avoid - unnecessary
  }
  
  // 6. Return nullable types explicitly
  String? result = findValue([1, 2, 3]);
  print('Found: ${result ?? 'None'}');
  
  // 7. Use nullable collections appropriately
  List<int>? nullableList;           // Nullable list
  List<int?> listWithNulls = [1, null, 3];  // Non-null list with nulls
  
  // 8. Validate nullable inputs early
  processData('valid');
  
  // 9. Use extension methods for nullable patterns
  print('Empty check: ${nullable.isNullOrEmpty}');
  
  // 10. Document null expectations
  var config = loadConfig(path: '/config.json');
  print('Config loaded: ${config != null}');
}

String expensiveComputation() => 'result';

void createUser({required String name, required String email}) {
  print('User: $name, $email');
}

String? findValue(List<int> items) {
  return items.isNotEmpty ? items.first.toString() : null;
}

void processData(String? input) {
  if (input == null || input.isEmpty) {
    print('Invalid input');
    return;
  }
  
  // Process with promoted non-nullable input
  print('Processing: $input');
}

extension NullCheck on String? {
  bool get isNullOrEmpty => this == null || this!.isEmpty;
}

/// Returns config if file exists, null otherwise
Map<String, dynamic>? loadConfig({required String path}) {
  // Simulated config loading
  return {'setting': 'value'};
}
```

This example summarizes key null safety best practices including type  
selection, operator usage, parameter patterns, and validation strategies  
that lead to robust, maintainable Dart code.  

```
$ dart run best_practices_null.dart
Computed: result
User: Bob, bob@example.com
5
Found: 1
Processing: valid
Empty check: true
Config loaded: true
```
