# Dart Functions

Functions are fundamental building blocks in Dart programming. They  
encapsulate reusable code, accept inputs through parameters, and can return  
values. Functions are first-class citizens in Dart, meaning they can be  
assigned to variables, passed as arguments, and returned from other functions.  

Dart supports various function types including named functions, anonymous  
functions, arrow functions, higher-order functions, and asynchronous  
functions. Functions enable code organization, reusability, and abstraction,  
making programs more maintainable and modular.  

## Basic Function Definition

A basic function defines a named block of code that can be executed  
repeatedly. Functions can specify return types and parameters for type  
safety.  

```dart
void main() {
  greet();
  var result = add(5, 3);
  print('Result: $result');
}

void greet() {
  print('Hello there!');
}

int add(int a, int b) {
  return a + b;
}
```

The greet function returns nothing (void), while add returns an integer. The  
return keyword specifies the value to return, and function calls execute the  
function body with provided arguments.  

## Function with Return Type

Functions specify return types to ensure type safety and clear contracts about  
what values they produce.  

```dart
void main() {
  String message = getMessage();
  print(message);
  
  double area = calculateCircleArea(5.0);
  print('Area: $area');
  
  bool result = isPositive(-3);
  print('Is positive: $result');
}

String getMessage() {
  return 'Welcome to Dart!';
}

double calculateCircleArea(double radius) {
  return 3.14159 * radius * radius;
}

bool isPositive(int number) {
  return number > 0;
}
```

Each function declares its return type before the function name. The function  
body must return a value matching the declared type, enabling compile-time  
type checking and preventing runtime errors.  

## Required Parameters

Required parameters must be provided when calling a function. They are defined  
in the function signature and accessed by position.  

```dart
void main() {
  printInfo('Alice', 25);
  
  var fullName = createFullName('John', 'Doe');
  print('Full name: $fullName');
  
  var power = calculatePower(2, 3);
  print('2^3 = $power');
}

void printInfo(String name, int age) {
  print('Name: $name, Age: $age');
}

String createFullName(String firstName, String lastName) {
  return '$firstName $lastName';
}

int calculatePower(int base, int exponent) {
  int result = 1;
  for (int i = 0; i < exponent; i++) {
    result *= base;
  }
  return result;
}
```

Required parameters enforce that callers provide all necessary inputs.  
Parameters are accessed by position in the order they appear in the  
function signature, ensuring clear function contracts.  

## Optional Positional Parameters

Optional positional parameters allow functions to have flexible signatures  
where some parameters can be omitted when calling the function.  

```dart
void main() {
  printGreeting('Alice');
  printGreeting('Bob', 'Good morning');
  
  print(formatNumber(42));
  print(formatNumber(42, 2));
}

void printGreeting(String name, [String? greeting]) {
  if (greeting != null) {
    print('$greeting, $name!');
  } else {
    print('Hello there, $name!');
  }
}

String formatNumber(int number, [int? decimalPlaces]) {
  if (decimalPlaces != null) {
    return number.toStringAsFixed(decimalPlaces);
  }
  return number.toString();
}
```

Optional positional parameters are enclosed in square brackets and can be  
nullable or have default values. When not provided, they receive null or  
their default value, allowing flexible function calls.  

## Optional Named Parameters

Named parameters provide clarity and flexibility by allowing arguments to be  
passed by name rather than position.  

```dart
void main() {
  createUser(name: 'Alice', age: 25);
  createUser(name: 'Bob', age: 30, email: 'bob@example.com');
  
  displayBox(width: 10, height: 5);
  displayBox(width: 8, height: 6, color: 'blue');
}

void createUser({required String name, required int age, String? email}) {
  print('User: $name, Age: $age');
  if (email != null) {
    print('Email: $email');
  }
}

void displayBox({required int width, required int height, 
    String color = 'red'}) {
  print('Box: ${width}x$height, Color: $color');
}
```

Named parameters are enclosed in curly braces. The required keyword makes them  
mandatory, while optional ones can have default values or be nullable. Named  
parameters improve readability for functions with many parameters.  

## Default Parameter Values

Default values provide fallback values for optional parameters when not  
explicitly provided by the caller.  

```dart
void main() {
  greetUser('Alice');
  greetUser('Bob', prefix: 'Welcome');
  
  print(multiply(5));
  print(multiply(5, factor: 3));
  
  configureServer();
  configureServer(port: 8080, host: 'localhost');
}

void greetUser(String name, {String prefix = 'Hello there'}) {
  print('$prefix, $name!');
}

int multiply(int value, {int factor = 2}) {
  return value * factor;
}

void configureServer({String host = '0.0.0.0', int port = 3000}) {
  print('Server running on $host:$port');
}
```

Default values are specified using the equals sign. When a parameter with a  
default value is omitted in a function call, it uses its default. This pattern  
simplifies function calls for common scenarios.  

## Arrow Function Syntax

Arrow functions provide concise syntax for functions with a single expression,  
improving readability for simple operations.  

```dart
void main() {
  var sum = add(10, 5);
  print('Sum: $sum');
  
  var squared = square(7);
  print('Square: $squared');
  
  var even = isEven(8);
  print('Is even: $even');
  
  var greeting = greet('Alice');
  print(greeting);
}

int add(int a, int b) => a + b;

int square(int x) => x * x;

bool isEven(int n) => n % 2 == 0;

String greet(String name) => 'Hello there, $name!';
```

Arrow functions use the => syntax for single-expression functions. The  
expression result is automatically returned. Arrow syntax is concise and  
commonly used for simple transformations and predicates.  

## Anonymous Functions

Anonymous functions are unnamed functions that can be defined inline, assigned  
to variables, or passed as arguments.  

```dart
void main() {
  // Anonymous function assigned to variable
  var multiply = (int a, int b) {
    return a * b;
  };
  print('Multiply: ${multiply(4, 5)}');
  
  // Anonymous arrow function
  var square = (int x) => x * x;
  print('Square: ${square(6)}');
  
  // Anonymous function as argument
  var numbers = [1, 2, 3, 4, 5];
  numbers.forEach((num) {
    print('Number: $num');
  });
  
  var doubled = numbers.map((n) => n * 2).toList();
  print('Doubled: $doubled');
}
```

Anonymous functions don't have a name and are useful for short-lived  
operations. They can capture variables from their surrounding scope and are  
commonly used with collection methods like forEach, map, and where.  

## Function as Parameter

Functions can be passed as parameters to other functions, enabling  
higher-order programming patterns and callback mechanisms.  

```dart
void main() {
  executeOperation(5, 3, add);
  executeOperation(5, 3, multiply);
  executeOperation(5, 3, (a, b) => a - b);
  
  processNumber(10, double);
  processNumber(7, square);
}

void executeOperation(int a, int b, int Function(int, int) operation) {
  var result = operation(a, b);
  print('Result: $result');
}

void processNumber(int n, int Function(int) transformer) {
  var result = transformer(n);
  print('Transformed: $result');
}

int add(int a, int b) => a + b;
int multiply(int a, int b) => a * b;
int double(int x) => x * 2;
int square(int x) => x * x;
```

Function types are declared using the Function keyword with parameter and  
return types. This pattern enables strategy pattern implementations and  
customizable behavior in reusable functions.  

## Function Returning Function

Functions can return other functions, enabling factory patterns and function  
composition.  

```dart
void main() {
  var doubler = createMultiplier(2);
  var tripler = createMultiplier(3);
  
  print('Double 5: ${doubler(5)}');
  print('Triple 4: ${tripler(4)}');
  
  var add5 = createAdder(5);
  var add10 = createAdder(10);
  
  print('Add 5 to 3: ${add5(3)}');
  print('Add 10 to 7: ${add10(7)}');
}

int Function(int) createMultiplier(int factor) {
  return (int value) => value * factor;
}

int Function(int) createAdder(int addend) {
  return (int value) => value + addend;
}
```

Functions that return functions create specialized versions of operations.  
This pattern is useful for configuration, partial application, and building  
function pipelines with captured state.  

## Closures

Closures are functions that capture and remember variables from their  
surrounding scope, even after the outer function has returned.  

```dart
void main() {
  var counter1 = makeCounter();
  print('Counter 1: ${counter1()}, ${counter1()}, ${counter1()}');
  
  var counter2 = makeCounter();
  print('Counter 2: ${counter2()}, ${counter2()}');
  
  var bankAccount = createAccount(100);
  bankAccount(50);   // deposit
  bankAccount(-30);  // withdraw
  bankAccount(-80);  // withdraw (insufficient)
}

Function makeCounter() {
  int count = 0;
  return () {
    count++;
    return count;
  };
}

Function createAccount(int initialBalance) {
  int balance = initialBalance;
  return (int amount) {
    balance += amount;
    if (balance >= 0) {
      print('Balance: \$${balance}');
    } else {
      balance -= amount;
      print('Insufficient funds');
    }
  };
}
```

Closures maintain private state that persists between function calls. The  
returned function remembers variables from its creation context, enabling  
data encapsulation and stateful operations.  

## Recursive Functions

Recursive functions call themselves to solve problems by breaking them into  
smaller subproblems.  

```dart
void main() {
  print('Factorial of 5: ${factorial(5)}');
  print('Fibonacci of 8: ${fibonacci(8)}');
  
  print('Sum 1 to 10: ${sum(10)}');
  
  print('Power 2^5: ${power(2, 5)}');
}

int factorial(int n) {
  if (n <= 1) return 1;
  return n * factorial(n - 1);
}

int fibonacci(int n) {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
}

int sum(int n) {
  if (n <= 0) return 0;
  return n + sum(n - 1);
}

int power(int base, int exponent) {
  if (exponent == 0) return 1;
  return base * power(base, exponent - 1);
}
```

Recursive functions need a base case to terminate recursion and avoid infinite  
loops. They provide elegant solutions for problems with recursive structure  
like tree traversal and mathematical sequences.  

## Function Type Aliases

Type aliases create readable names for complex function types, improving code  
clarity and maintainability.  

```dart
void main() {
  Operation add = (a, b) => a + b;
  Operation multiply = (a, b) => a * b;
  
  print('Add: ${executeOp(5, 3, add)}');
  print('Multiply: ${executeOp(5, 3, multiply)}');
  
  Validator isPositive = (n) => n > 0;
  Validator isEven = (n) => n % 2 == 0;
  
  print('5 is positive: ${isPositive(5)}');
  print('4 is even: ${isEven(4)}');
  
  Transformer doubler = (x) => x * 2;
  print('Transform 7: ${transform(7, doubler)}');
}

typedef Operation = int Function(int, int);
typedef Validator = bool Function(int);
typedef Transformer = int Function(int);

int executeOp(int a, int b, Operation op) => op(a, b);

int transform(int value, Transformer t) => t(value);
```

Type aliases simplify complex function signatures and make code more self-  
documenting. They're especially useful when the same function type appears  
multiple times in your code.  

## Generic Functions

Generic functions work with multiple types, providing type-safe reusable code  
that adapts to different data types.  

```dart
void main() {
  print('First int: ${first([1, 2, 3])}');
  print('First string: ${first(['a', 'b', 'c'])}');
  
  var intList = createPair(1, 2);
  var stringList = createPair('hello', 'world');
  print('Int pair: $intList');
  print('String pair: $stringList');
  
  print('Swap ints: ${swap(5, 10)}');
  print('Swap strings: ${swap('A', 'B')}');
}

T first<T>(List<T> items) {
  return items.first;
}

List<T> createPair<T>(T first, T second) {
  return [first, second];
}

List<T> swap<T>(T a, T b) {
  return [b, a];
}
```

Generic functions use type parameters (written in angle brackets) to work with  
any type while maintaining type safety. The compiler infers types from  
arguments, ensuring type correctness at compile time.  

## Multiple Generic Parameters

Functions can have multiple generic type parameters for flexible type  
relationships between parameters and return values.  

```dart
void main() {
  var result1 = transform(5, (n) => n.toString());
  print('Int to String: $result1');
  
  var result2 = transform('42', (s) => int.parse(s));
  print('String to Int: $result2');
  
  var pair = createPair<int, String>(42, 'answer');
  print('Pair: ${pair.$1}, ${pair.$2}');
  
  var combined = combine(5, 'items', (n, s) => '$n $s');
  print('Combined: $combined');
}

R transform<T, R>(T value, R Function(T) transformer) {
  return transformer(value);
}

(T, U) createPair<T, U>(T first, U second) {
  return (first, second);
}

R combine<T, U, R>(T first, U second, R Function(T, U) combiner) {
  return combiner(first, second);
}
```

Multiple type parameters enable functions that transform between different  
types or work with multiple heterogeneous values. Each type parameter can  
represent a different type in the function signature.  

## Higher-Order Functions

Higher-order functions operate on other functions, either accepting them as  
parameters or returning them as results.  

```dart
void main() {
  var numbers = [1, 2, 3, 4, 5];
  
  var doubled = applyToAll(numbers, (n) => n * 2);
  print('Doubled: $doubled');
  
  var filtered = filter(numbers, (n) => n > 2);
  print('Filtered: $filtered');
  
  var sum = reduce(numbers, 0, (acc, n) => acc + n);
  print('Sum: $sum');
  
  var composed = compose((x) => x * 2, (x) => x + 1);
  print('Compose (5+1)*2: ${composed(5)}');
}

List<R> applyToAll<T, R>(List<T> items, R Function(T) fn) {
  return items.map(fn).toList();
}

List<T> filter<T>(List<T> items, bool Function(T) predicate) {
  return items.where(predicate).toList();
}

R reduce<T, R>(List<T> items, R initial, R Function(R, T) fn) {
  return items.fold(initial, fn);
}

int Function(int) compose(int Function(int) f, int Function(int) g) {
  return (x) => f(g(x));
}
```

Higher-order functions abstract control flow and operations, enabling  
functional programming patterns. They promote code reuse by separating  
what to do from how to do it.  

## Function Composition

Function composition combines multiple functions to create new functions,  
building complex operations from simple building blocks.  

```dart
void main() {
  // Simple composition
  var addTwo = (int x) => x + 2;
  var multiplyThree = (int x) => x * 3;
  
  var composed = compose(multiplyThree, addTwo);
  print('Composed (5+2)*3: ${composed(5)}');
  
  // Chain multiple functions
  var pipeline = composeAll([
    (x) => x + 1,
    (x) => x * 2,
    (x) => x - 3,
  ]);
  print('Pipeline 5->6->12->9: ${pipeline(5)}');
  
  // Pipe operator pattern
  var result = pipe(10, [
    (x) => x * 2,
    (x) => x + 5,
    (x) => x ~/ 3,
  ]);
  print('Pipe 10->20->25->8: $result');
}

int Function(int) compose(int Function(int) f, int Function(int) g) {
  return (x) => f(g(x));
}

int Function(int) composeAll(List<int Function(int)> functions) {
  return (x) => functions.fold(x, (value, fn) => fn(value));
}

int pipe(int value, List<int Function(int)> functions) {
  return functions.fold(value, (acc, fn) => fn(acc));
}
```

Composition creates new functions by chaining existing ones. The pipe pattern  
applies functions left-to-right, while compose applies them right-to-left,  
enabling declarative data transformation pipelines.  

## Partial Application

Partial application creates new functions by fixing some arguments of an  
existing function, reducing the number of parameters.  

```dart
void main() {
  // Create specialized functions
  var add5 = partial(add, 5);
  print('Add 5 to 3: ${add5(3)}');
  
  var multiply10 = partial(multiply, 10);
  print('Multiply 4 by 10: ${multiply10(4)}');
  
  // Partial with named parameters
  var greetFormal = partialGreet(formal: true);
  greetFormal('Alice');
  greetFormal('Bob');
  
  var greetCasual = partialGreet(formal: false);
  greetCasual('Charlie');
}

int add(int a, int b) => a + b;
int multiply(int a, int b) => a * b;

int Function(int) partial(int Function(int, int) fn, int fixed) {
  return (int x) => fn(fixed, x);
}

void Function(String) partialGreet({required bool formal}) {
  return (String name) {
    if (formal) {
      print('Good day, $name.');
    } else {
      print('Hey there, $name!');
    }
  };
}
```

Partial application fixes some arguments of a function, creating a new  
function with fewer parameters. This technique is useful for creating  
specialized versions of general functions.  

## Currying

Currying transforms a function with multiple parameters into a sequence of  
functions, each taking a single parameter.  

```dart
void main() {
  // Curried add function
  var add = (int a) => (int b) => a + b;
  var add5 = add(5);
  print('Add 5 to 3: ${add5(3)}');
  print('Add 5 to 10: ${add5(10)}');
  
  // Curried multiply
  var multiply = (int a) => (int b) => a * b;
  var double = multiply(2);
  var triple = multiply(3);
  print('Double 7: ${double(7)}');
  print('Triple 4: ${triple(4)}');
  
  // Curried with three parameters
  var calculateVolume = (int length) => (int width) => (int height) => 
    length * width * height;
  
  var boxBase = calculateVolume(5)(4);
  print('Volume (5x4x3): ${boxBase(3)}');
  print('Volume (5x4x6): ${boxBase(6)}');
}
```

Currying enables partial application and function specialization. Each  
function in the chain captures a parameter and returns another function,  
building up the final computation step by step.  

## Function with Callbacks

Callbacks are functions passed as arguments that are called when an operation  
completes, enabling asynchronous patterns and event handling.  

```dart
void main() {
  processData(42, 
    onSuccess: (result) => print('Success: $result'),
    onError: (error) => print('Error: $error')
  );
  
  fetchData(
    url: 'https://api.example.com/data',
    onComplete: (data) => print('Data received: $data'),
    onFailure: () => print('Failed to fetch data')
  );
  
  performOperation(
    value: 10,
    operation: (x) => x * 2,
    callback: (result) => print('Operation result: $result')
  );
}

void processData(int value, 
    {required Function(int) onSuccess, required Function(String) onError}) {
  if (value > 0) {
    onSuccess(value * 2);
  } else {
    onError('Invalid value');
  }
}

void fetchData({
  required String url,
  required Function(String) onComplete,
  required Function() onFailure
}) {
  // Simulate data fetching
  if (url.isNotEmpty) {
    onComplete('{"status": "ok"}');
  } else {
    onFailure();
  }
}

void performOperation({
  required int value,
  required int Function(int) operation,
  required Function(int) callback
}) {
  var result = operation(value);
  callback(result);
}
```

Callbacks allow functions to notify callers when operations complete. They're  
fundamental to asynchronous programming and enable flexible response handling  
for different outcomes.  

## Async Functions

Async functions enable asynchronous programming, returning Future objects that  
represent values that will be available in the future.  

```dart
void main() async {
  print('Start');
  
  var data = await fetchData();
  print('Data: $data');
  
  var result = await processData(42);
  print('Result: $result');
  
  var user = await getUserInfo('alice');
  print('User: $user');
  
  print('End');
}

Future<String> fetchData() async {
  await Future.delayed(Duration(milliseconds: 100));
  return 'Sample data';
}

Future<int> processData(int value) async {
  await Future.delayed(Duration(milliseconds: 50));
  return value * 2;
}

Future<Map<String, dynamic>> getUserInfo(String username) async {
  await Future.delayed(Duration(milliseconds: 150));
  return {'name': username, 'age': 25};
}
```

Async functions use the async keyword and return Future objects. The await  
keyword pauses execution until the Future completes, making asynchronous  
code look synchronous and easier to understand.  

## Generator Functions (sync*)

Generator functions produce sequences of values lazily using the yield  
keyword, creating efficient iterables without computing all values upfront.  

```dart
void main() {
  // Lazy sequence generation
  var numbers = generateNumbers(5);
  print('Numbers: ${numbers.toList()}');
  
  var evens = generateEvens(10);
  print('Evens: ${evens.toList()}');
  
  var squares = generateSquares(6);
  for (var square in squares) {
    print('Square: $square');
  }
  
  var fibonacci = generateFibonacci(8);
  print('Fibonacci: ${fibonacci.toList()}');
}

Iterable<int> generateNumbers(int count) sync* {
  for (int i = 1; i <= count; i++) {
    yield i;
  }
}

Iterable<int> generateEvens(int max) sync* {
  for (int i = 2; i <= max; i += 2) {
    yield i;
  }
}

Iterable<int> generateSquares(int count) sync* {
  for (int i = 1; i <= count; i++) {
    yield i * i;
  }
}

Iterable<int> generateFibonacci(int count) sync* {
  int a = 0, b = 1;
  for (int i = 0; i < count; i++) {
    yield a;
    int temp = a;
    a = b;
    b = temp + b;
  }
}
```

Sync generator functions use sync* and yield to produce values on demand.  
They're memory efficient for large sequences and enable lazy evaluation  
patterns where values are computed only when needed.  

## Async Generator Functions (async*)

Async generator functions produce asynchronous sequences of values over time,  
useful for streams and event handling.  

```dart
void main() async {
  print('Countdown:');
  await for (var num in countdown(5)) {
    print(num);
  }
  
  print('\nAsync numbers:');
  await for (var num in generateAsyncNumbers(3)) {
    print('Number: $num');
  }
  
  print('\nTimed messages:');
  await for (var msg in timedMessages(3)) {
    print(msg);
  }
}

Stream<int> countdown(int from) async* {
  for (int i = from; i > 0; i--) {
    await Future.delayed(Duration(milliseconds: 100));
    yield i;
  }
  yield 0;
}

Stream<int> generateAsyncNumbers(int count) async* {
  for (int i = 1; i <= count; i++) {
    await Future.delayed(Duration(milliseconds: 50));
    yield i * 10;
  }
}

Stream<String> timedMessages(int count) async* {
  for (int i = 1; i <= count; i++) {
    await Future.delayed(Duration(milliseconds: 100));
    yield 'Message $i at ${DateTime.now().second}s';
  }
}
```

Async generator functions use async* and yield to produce asynchronous  
streams. They're ideal for handling time-based data, user events, or any  
scenario requiring values that arrive over time.  

## Extension Methods

Extension methods add functionality to existing types without modifying them,  
creating convenient custom operations.  

```dart
void main() {
  var text = 'hello there';
  print('Capitalized: ${text.capitalize()}');
  print('Is palindrome: ${text.isPalindrome()}');
  
  var number = 42;
  print('Is even: ${number.isEven}');
  print('Squared: ${number.squared()}');
  print('Factorial: ${5.factorial()}');
  
  var list = [1, 2, 3, 4, 5];
  print('Sum: ${list.sum()}');
  print('Average: ${list.average()}');
}

extension StringExtensions on String {
  String capitalize() {
    if (isEmpty) return this;
    return '${this[0].toUpperCase()}${substring(1)}';
  }
  
  bool isPalindrome() {
    var reversed = split('').reversed.join('');
    return this == reversed;
  }
}

extension IntExtensions on int {
  int squared() => this * this;
  
  int factorial() {
    if (this <= 1) return 1;
    return this * (this - 1).factorial();
  }
}

extension ListExtensions on List<int> {
  int sum() => fold(0, (acc, n) => acc + n);
  
  double average() => isEmpty ? 0 : sum() / length;
}
```

Extension methods enhance existing types with domain-specific functionality.  
They provide a clean syntax for common operations while keeping code  
organized and types focused.  

## Method Cascade

Method cascades enable multiple operations on the same object without  
repeating the object reference, creating fluent interfaces.  

```dart
void main() {
  // Cascade with list
  var numbers = <int>[]
    ..add(1)
    ..add(2)
    ..add(3)
    ..addAll([4, 5])
    ..sort();
  print('Numbers: $numbers');
  
  // Cascade with custom object
  var person = Person()
    ..name = 'Alice'
    ..age = 25
    ..email = 'alice@example.com'
    ..display();
  
  // Cascade with StringBuilder
  var buffer = StringBuffer()
    ..write('Hello')
    ..write(' ')
    ..write('there')
    ..write('!');
  print('Buffer: $buffer');
}

class Person {
  String name = '';
  int age = 0;
  String email = '';
  
  void display() {
    print('Person: $name, Age: $age, Email: $email');
  }
}
```

The cascade operator (..) allows chaining multiple method calls and property  
assignments on the same object. This creates more readable code for object  
configuration and builder patterns.  

## Function Call Operator

Classes can implement the call() method to make instances callable like  
functions, creating function-like objects with state.  

```dart
void main() {
  var adder = Adder(10);
  print('Add 5: ${adder(5)}');
  print('Add 15: ${adder(15)}');
  
  var multiplier = Multiplier(3);
  print('Multiply 4: ${multiplier(4)}');
  print('Multiply 7: ${multiplier(7)}');
  
  var counter = Counter();
  print('Count: ${counter()}, ${counter()}, ${counter()}');
  
  var formatter = Formatter(prefix: '>>> ');
  print(formatter('Hello there'));
  print(formatter('Goodbye'));
}

class Adder {
  final int value;
  Adder(this.value);
  
  int call(int x) => value + x;
}

class Multiplier {
  final int factor;
  Multiplier(this.factor);
  
  int call(int x) => factor * x;
}

class Counter {
  int _count = 0;
  
  int call() => ++_count;
}

class Formatter {
  final String prefix;
  Formatter({this.prefix = ''});
  
  String call(String text) => '$prefix$text';
}
```

The call() method makes class instances callable as functions. This pattern  
is useful for creating stateful functions, implementing strategy patterns,  
or building function-like objects with configuration.  

## Memoization

Memoization caches function results to avoid redundant calculations,  
improving performance for expensive pure functions.  

```dart
void main() {
  // Without memoization
  print('Without memoization:');
  var start1 = DateTime.now();
  print('Fibonacci(35): ${fibonacci(35)}');
  var end1 = DateTime.now();
  print('Time: ${end1.difference(start1).inMilliseconds}ms\n');
  
  // With memoization
  print('With memoization:');
  var memoFib = memoize(fibonacci);
  var start2 = DateTime.now();
  print('Fibonacci(35): ${memoFib(35)}');
  var end2 = DateTime.now();
  print('Time: ${end2.difference(start2).inMilliseconds}ms');
  
  // Second call is instant
  var start3 = DateTime.now();
  print('Fibonacci(35): ${memoFib(35)}');
  var end3 = DateTime.now();
  print('Time: ${end3.difference(start3).inMilliseconds}ms');
}

int fibonacci(int n) {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
}

T Function(A) memoize<A, T>(T Function(A) fn) {
  var cache = <A, T>{};
  return (A arg) {
    if (!cache.containsKey(arg)) {
      cache[arg] = fn(arg);
    }
    return cache[arg]!;
  };
}
```

Memoization wraps a function to cache its results based on input parameters.  
When called with the same arguments, it returns the cached result instead  
of recomputing, dramatically improving performance for recursive functions.  

## Function Error Handling

Functions handle errors through exceptions, return values, or Result types,  
ensuring robust error management.  

```dart
void main() {
  // Try-catch in function
  try {
    var result = divide(10, 2);
    print('Result: $result');
    
    divide(10, 0); // throws exception
  } catch (e) {
    print('Error: $e');
  }
  
  // Validation with exceptions
  try {
    createUser(name: '', age: 25);
  } catch (e) {
    print('Validation error: $e');
  }
  
  // Result-based error handling
  var result1 = safeDivide(10, 2);
  print('Safe divide success: $result1');
  
  var result2 = safeDivide(10, 0);
  print('Safe divide with zero: $result2');
}

double divide(int a, int b) {
  if (b == 0) {
    throw ArgumentError('Cannot divide by zero');
  }
  return a / b;
}

void createUser({required String name, required int age}) {
  if (name.isEmpty) {
    throw ArgumentError('Name cannot be empty');
  }
  if (age < 0) {
    throw ArgumentError('Age must be positive');
  }
  print('User created: $name, $age');
}

String safeDivide(int a, int b) {
  try {
    return (a / b).toString();
  } catch (e) {
    return 'Error: Division by zero';
  }
}
```

Functions use exceptions for error conditions, throwing ArgumentError or other  
exception types. Callers handle errors with try-catch blocks or functions can  
return Result types for explicit error handling.  

## Pure Functions

Pure functions always produce the same output for the same input and have no  
side effects, making code predictable and testable.  

```dart
void main() {
  // Pure functions - same input, same output
  print('Add 5+3: ${add(5, 3)}');
  print('Add 5+3: ${add(5, 3)}'); // always 8
  
  print('Square 4: ${square(4)}');
  print('Square 4: ${square(4)}'); // always 16
  
  var list = [1, 2, 3, 4, 5];
  print('Sum: ${sum(list)}');
  print('Sum: ${sum(list)}'); // always 15
  
  print('Max: ${max(10, 20)}');
  print('Max: ${max(10, 20)}'); // always 20
  
  // Impure function with side effect
  impureIncrement();
  impureIncrement(); // different result each time
}

// Pure functions
int add(int a, int b) => a + b;

int square(int x) => x * x;

int sum(List<int> numbers) => numbers.fold(0, (a, b) => a + b);

int max(int a, int b) => a > b ? a : b;

// Impure function (for contrast)
int _counter = 0;
void impureIncrement() {
  _counter++;
  print('Counter: $_counter');
}
```

Pure functions depend only on their parameters and don't modify external  
state. They're easier to test, reason about, and parallelize. Pure functions  
are a cornerstone of functional programming.  

## Function Documentation

Documenting functions with comments improves code maintainability and helps  
developers understand function purpose and usage.  

```dart
void main() {
  var result = calculateBMI(weight: 70, height: 1.75);
  print('BMI: ${result.toStringAsFixed(2)}');
  
  var formatted = formatCurrency(1234.56, symbol: '\$');
  print('Price: $formatted');
  
  var validated = validateEmail('user@example.com');
  print('Valid email: $validated');
}

/// Calculates Body Mass Index (BMI) from weight and height.
///
/// BMI is calculated as weight (kg) divided by height (m) squared.
/// 
/// Parameters:
///   [weight] - Body weight in kilograms
///   [height] - Body height in meters
/// 
/// Returns the BMI value as a double.
/// 
/// Example:
/// ```dart
/// var bmi = calculateBMI(weight: 70, height: 1.75);
/// print('BMI: $bmi'); // BMI: 22.86
/// ```
double calculateBMI({required double weight, required double height}) {
  return weight / (height * height);
}

/// Formats a numeric value as currency with the specified symbol.
///
/// Parameters:
///   [amount] - The numeric amount to format
///   [symbol] - Currency symbol (default: '\$')
/// 
/// Returns a formatted string with 2 decimal places.
String formatCurrency(double amount, {String symbol = '\$'}) {
  return '$symbol${amount.toStringAsFixed(2)}';
}

/// Validates an email address format.
///
/// Checks if the email contains '@' and a domain.
/// This is a simple validation, not RFC-compliant.
/// 
/// Returns true if email appears valid, false otherwise.
bool validateEmail(String email) {
  return email.contains('@') && email.contains('.');
}
```

Function documentation uses triple-slash (///) comments with markdown syntax.  
Good documentation describes purpose, parameters, return values, and provides  
examples, making code self-explanatory and easier to use.  
