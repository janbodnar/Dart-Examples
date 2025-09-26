# Dart Functional Programming

Functional programming in Dart emphasizes immutability, pure functions,  
higher-order functions, and declarative programming patterns. This paradigm  
promotes code that is easier to test, reason about, and maintain.  

Dart supports functional programming through first-class functions, closures,  
method chaining, and powerful collection operations. While Dart is primarily  
object-oriented, it provides excellent functional programming capabilities.  

## Higher-Order Functions

Higher-order functions accept other functions as parameters or return functions  
as results, enabling powerful abstraction and code reuse patterns.  

```dart
void main() {
  // Function that takes another function as parameter
  int apply(int x, int Function(int) operation) {
    return operation(x);
  }
  
  // Define operation functions
  int double(int x) => x * 2;
  int square(int x) => x * x;
  
  print('Double 5: ${apply(5, double)}');
  print('Square 4: ${apply(4, square)}');
  
  // Using anonymous functions
  print('Triple 3: ${apply(3, (x) => x * 3)}');
}
```

Higher-order functions abstract over actions, not just values. They enable  
generic operations that work with different behaviors, promoting code reuse  
and separation of concerns in functional programming.  

## Function Composition

Function composition combines simple functions to build more complex operations,  
creating readable and maintainable transformation pipelines.  

```dart
void main() {
  // Basic function composition
  int Function(int) compose<T>(T Function(T) f, T Function(T) g) {
    return (x) => f(g(x));
  }
  
  int addTwo(int x) => x + 2;
  int multiplyByThree(int x) => x * 3;
  
  var addThenMultiply = compose(multiplyByThree, addTwo);
  print('(5 + 2) * 3 = ${addThenMultiply(5)}');
  
  // Pipe operator simulation
  T pipe<T>(T value, List<T Function(T)> functions) {
    return functions.fold(value, (acc, f) => f(acc));
  }
  
  var result = pipe(10, [
    (x) => x + 5,
    (x) => x * 2,
    (x) => x - 3,
  ]);
  print('Piped result: $result');
}
```

Function composition creates new functions by combining existing ones. The  
pipe operator enables left-to-right function application, making data  
transformation workflows more readable and intuitive.  

## Closures and Lexical Scope

Closures capture variables from their surrounding scope, enabling state  
encapsulation and factory pattern implementations in functional programming.  

```dart
void main() {
  // Closure capturing outer variable
  Function makeCounter(int start) {
    int count = start;
    return () => ++count;
  }
  
  var counter1 = makeCounter(0);
  var counter2 = makeCounter(10);
  
  print('Counter 1: ${counter1()}, ${counter1()}, ${counter1()}');
  print('Counter 2: ${counter2()}, ${counter2()}');
  
  // Closure with parameter
  Function makeMultiplier(int factor) {
    return (int value) => value * factor;
  }
  
  var doubler = makeMultiplier(2);
  var tripler = makeMultiplier(3);
  
  print('Double 7: ${doubler(7)}');
  print('Triple 4: ${tripler(4)}');
}
```

Closures maintain access to variables from their creation scope even after  
the outer function returns. This enables powerful patterns like factories,  
decorators, and stateful function creation in functional programming.  

## Pure Functions and Immutability

Pure functions always return the same output for the same input and have no  
side effects, making them predictable, testable, and composable.  

```dart
void main() {
  // Pure functions - no side effects
  List<int> addToAll(List<int> numbers, int value) {
    return numbers.map((n) => n + value).toList();
  }
  
  List<int> filterEven(List<int> numbers) {
    return numbers.where((n) => n % 2 == 0).toList();
  }
  
  var original = [1, 2, 3, 4, 5];
  var added = addToAll(original, 10);
  var filtered = filterEven(added);
  
  print('Original: $original');
  print('Added 10: $added');
  print('Even numbers: $filtered');
  
  // Immutable data transformation
  var result = [1, 2, 3, 4, 5, 6]
      .map((x) => x * 2)
      .where((x) => x > 5)
      .toList();
  
  print('Transformed: $result');
}
```

Pure functions enable reasoning about code behavior and enable safe  
parallelization. Immutability prevents unexpected state changes and makes  
programs more predictable and easier to debug.  

## Currying and Partial Application

Currying transforms multi-parameter functions into sequences of single-parameter  
functions, enabling partial application and function specialization.  

```dart
void main() {
  // Curried function - returns function that returns function
  int Function(int) Function(int) curriedAdd = (int a) => (int b) => a + b;
  
  var add5 = curriedAdd(5);
  print('Add 5 to 3: ${add5(3)}');
  print('Add 5 to 7: ${add5(7)}');
  
  // Partial application helper
  Function partial<A, B, C>(C Function(A, B) func, A arg) {
    return (B b) => func(arg, b);
  }
  
  int multiply(int a, int b) => a * b;
  var double = partial(multiply, 2);
  var triple = partial(multiply, 3);
  
  print('Double 6: ${double(6)}');
  print('Triple 4: ${triple(4)}');
  
  // Three-parameter curried function
  String Function(String) Function(String) Function(String) greetCurried =
      (String greeting) => (String name) => (String punctuation) =>
          '$greeting, $name$punctuation';
  
  var hello = greetCurried('Hello there');
  var helloJohn = hello('John');
  print(helloJohn('!'));
}
```

Currying enables creating specialized functions from general ones. Partial  
application allows fixing some arguments while leaving others open, creating  
reusable function variants for specific use cases.  

## Map, Filter, and Reduce

Fundamental functional operations for transforming, filtering, and aggregating  
collections in a declarative and composable manner.  

```dart
void main() {
  var numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
  
  // Map - transform each element
  var squares = numbers.map((n) => n * n).toList();
  print('Squares: $squares');
  
  // Filter - select elements matching condition
  var evens = numbers.where((n) => n % 2 == 0).toList();
  print('Evens: $evens');
  
  // Reduce - aggregate to single value
  var sum = numbers.reduce((a, b) => a + b);
  print('Sum: $sum');
  
  // Fold - reduce with initial value
  var product = numbers.fold<int>(1, (acc, n) => acc * n);
  print('Product: $product');
  
  // Chaining operations
  var result = numbers
      .where((n) => n > 3)
      .map((n) => n * 2)
      .where((n) => n < 15)
      .fold<int>(0, (sum, n) => sum + n);
  
  print('Chained result: $result');
}
```

These operations form the foundation of functional collection processing.  
Map transforms, where filters, reduce aggregates, and fold provides more  
control with initial values for aggregation operations.  

## Function Factories and Builders

Function factories create customized functions based on parameters, enabling  
flexible and reusable function generation patterns.  

```dart
void main() {
  // Validator factory
  bool Function(String) createValidator({
    int? minLength,
    int? maxLength,
    bool requireNumbers = false,
  }) {
    return (String input) {
      if (minLength != null && input.length < minLength) return false;
      if (maxLength != null && input.length > maxLength) return false;
      if (requireNumbers && !input.contains(RegExp(r'\d'))) return false;
      return true;
    };
  }
  
  var passwordValidator = createValidator(
    minLength: 8,
    requireNumbers: true,
  );
  
  var usernameValidator = createValidator(
    minLength: 3,
    maxLength: 20,
  );
  
  print('Password "abc": ${passwordValidator("abc")}');
  print('Password "password123": ${passwordValidator("password123")}');
  print('Username "jo": ${usernameValidator("jo")}');
  print('Username "john": ${usernameValidator("john")}');
  
  // Comparison function factory
  int Function(T, T) createComparator<T>(T Function(T) keyExtractor) {
    return (T a, T b) {
      var keyA = keyExtractor(a);
      var keyB = keyExtractor(b);
      return Comparable.compare(keyA as Comparable, keyB as Comparable);
    };
  }
  
  var people = ['Alice Johnson', 'Bob Smith', 'Carol Brown'];
  var byLastName = createComparator<String>((name) => name.split(' ')[1]);
  people.sort(byLastName);
  
  print('Sorted by last name: $people');
}
```

Function factories encapsulate function creation logic, allowing runtime  
customization of behavior. This pattern is particularly useful for creating  
validators, comparators, and other configurable function types.  

## Recursion and Tail Recursion

Recursion solves problems by breaking them into smaller subproblems.  
Tail recursion optimizes recursive calls when the recursive call is the  
last operation.  

```dart
void main() {
  // Classic recursion - factorial
  int factorial(int n) {
    if (n <= 1) return 1;
    return n * factorial(n - 1);
  }
  
  print('Factorial 5: ${factorial(5)}');
  
  // Tail recursive factorial
  int factorialTail(int n, [int acc = 1]) {
    if (n <= 1) return acc;
    return factorialTail(n - 1, acc * n);
  }
  
  print('Tail factorial 5: ${factorialTail(5)}');
  
  // Fibonacci with memoization
  Map<int, int> _fibCache = {};
  
  int fibonacci(int n) {
    if (n <= 1) return n;
    if (_fibCache.containsKey(n)) return _fibCache[n]!;
    
    var result = fibonacci(n - 1) + fibonacci(n - 2);
    _fibCache[n] = result;
    return result;
  }
  
  print('Fibonacci 10: ${fibonacci(10)}');
  
  // Tree traversal recursion
  class TreeNode {
    final int value;
    final List<TreeNode> children;
    TreeNode(this.value, [this.children = const []]);
  }
  
  List<int> traverseDepthFirst(TreeNode node) {
    var result = [node.value];
    for (var child in node.children) {
      result.addAll(traverseDepthFirst(child));
    }
    return result;
  }
  
  var tree = TreeNode(1, [
    TreeNode(2, [TreeNode(4), TreeNode(5)]),
    TreeNode(3, [TreeNode(6)]),
  ]);
  
  print('Tree traversal: ${traverseDepthFirst(tree)}');
}
```

Recursion provides elegant solutions for tree structures and mathematical  
sequences. Memoization optimizes recursive functions by caching results,  
while tail recursion enables stack optimization for iterative-style recursion.  

## Lazy Evaluation

Lazy evaluation defers computation until results are needed, improving  
performance and enabling infinite data structures.  

```dart
void main() {
  // Lazy iterable generation
  Iterable<int> naturalNumbers() sync* {
    int i = 1;
    while (true) {
      yield i++;
    }
  }
  
  // Take first 10 natural numbers
  var first10 = naturalNumbers().take(10).toList();
  print('First 10 naturals: $first10');
  
  // Lazy fibonacci sequence
  Iterable<int> fibonacciSequence() sync* {
    int a = 0, b = 1;
    while (true) {
      yield a;
      var temp = a + b;
      a = b;
      b = temp;
    }
  }
  
  var first15Fibs = fibonacciSequence().take(15).toList();
  print('First 15 Fibonacci: $first15Fibs');
  
  // Lazy computation with caching
  class LazyValue<T> {
    T? _value;
    bool _computed = false;
    final T Function() _computation;
    
    LazyValue(this._computation);
    
    T get value {
      if (!_computed) {
        _value = _computation();
        _computed = true;
      }
      return _value!;
    }
  }
  
  var expensiveComputation = LazyValue<int>(() {
    print('Computing expensive value...');
    return List.generate(1000000, (i) => i).fold(0, (a, b) => a + b);
  });
  
  print('Lazy value created');
  print('Result: ${expensiveComputation.value}');
  print('Cached result: ${expensiveComputation.value}');
}
```

Lazy evaluation enables working with potentially infinite sequences and  
expensive computations. It improves performance by computing values only  
when needed and caching results for subsequent access.  

## Monads and Option Types

Monads provide a way to wrap values and chain operations while handling  
special cases like null values, errors, or asynchronous operations.  

```dart
void main() {
  // Option/Maybe monad for null safety
  abstract class Option<T> {
    bool get isEmpty;
    bool get isNotEmpty => !isEmpty;
    
    Option<U> map<U>(U Function(T) f);
    Option<U> flatMap<U>(Option<U> Function(T) f);
    T getOrElse(T defaultValue);
  }
  
  class Some<T> extends Option<T> {
    final T value;
    Some(this.value);
    
    @override
    bool get isEmpty => false;
    
    @override
    Option<U> map<U>(U Function(T) f) => Some(f(value));
    
    @override
    Option<U> flatMap<U>(Option<U> Function(T) f) => f(value);
    
    @override
    T getOrElse(T defaultValue) => value;
    
    @override
    String toString() => 'Some($value)';
  }
  
  class None<T> extends Option<T> {
    @override
    bool get isEmpty => true;
    
    @override
    Option<U> map<U>(U Function(T) f) => None<U>();
    
    @override
    Option<U> flatMap<U>(Option<U> Function(T) f) => None<U>();
    
    @override
    T getOrElse(T defaultValue) => defaultValue;
    
    @override
    String toString() => 'None';
  }
  
  Option<T> some<T>(T value) => Some(value);
  Option<T> none<T>() => None<T>();
  
  // Using Option monad
  Option<int> divide(int a, int b) {
    return b == 0 ? none<int>() : some(a ~/ b);
  }
  
  var result1 = some(10)
      .flatMap((x) => divide(x, 2))
      .map((x) => x * 3);
  
  var result2 = some(10)
      .flatMap((x) => divide(x, 0))
      .map((x) => x * 3);
  
  print('Valid chain: $result1');
  print('Invalid chain: $result2');
  print('With default: ${result2.getOrElse(-1)}');
}
```

Monads provide a structured way to handle computations that might fail,  
be absent, or require special handling. The Option monad eliminates null  
pointer exceptions through safe chaining operations.  

## Function Memoization

Memoization caches function results to avoid redundant computations,  
significantly improving performance for expensive or recursive functions.  

```dart
void main() {
  // Generic memoization wrapper
  Function memoize<T, R>(R Function(T) func) {
    final Map<T, R> cache = {};
    
    return (T arg) {
      if (cache.containsKey(arg)) {
        return cache[arg];
      }
      
      final result = func(arg);
      cache[arg] = result;
      return result;
    };
  }
  
  // Expensive function to memoize
  int expensiveCalculation(int n) {
    print('Computing for $n...');
    return List.generate(n, (i) => i + 1).fold(0, (a, b) => a + b);
  }
  
  var memoizedCalc = memoize(expensiveCalculation);
  
  print('First call: ${memoizedCalc(1000)}');
  print('Second call: ${memoizedCalc(1000)}');
  print('Third call: ${memoizedCalc(500)}');
  
  // Memoized fibonacci
  late int Function(int) memoizedFib;
  memoizedFib = memoize<int, int>((int n) {
    if (n <= 1) return n;
    return memoizedFib(n - 1) + memoizedFib(n - 2);
  });
  
  print('Memoized Fib 30: ${memoizedFib(30)}');
  print('Memoized Fib 35: ${memoizedFib(35)}');
  
  // Time-based cache expiration
  class MemoizeWithTTL<T, R> {
    final R Function(T) _func;
    final Duration _ttl;
    final Map<T, ({R value, DateTime timestamp})> _cache = {};
    
    MemoizeWithTTL(this._func, this._ttl);
    
    R call(T arg) {
      final now = DateTime.now();
      final cached = _cache[arg];
      
      if (cached != null && now.difference(cached.timestamp) < _ttl) {
        return cached.value;
      }
      
      final result = _func(arg);
      _cache[arg] = (value: result, timestamp: now);
      return result;
    }
  }
  
  var ttlMemo = MemoizeWithTTL<int, String>(
    (n) => 'Computed at ${DateTime.now()}: $n',
    Duration(seconds: 2),
  );
  
  print('TTL call 1: ${ttlMemo(42)}');
  print('TTL call 2: ${ttlMemo(42)}');
}
```

Memoization trades memory for speed by caching function results. It's  
particularly effective for recursive functions and expensive computations  
with repeated calls using the same arguments.  

## Immutable Data Structures

Immutable data structures cannot be modified after creation, promoting  
functional programming principles and preventing unexpected side effects.  

```dart
void main() {
  // Immutable list wrapper
  class ImmutableList<T> {
    final List<T> _items;
    
    ImmutableList(List<T> items) : _items = List.unmodifiable(items);
    ImmutableList.empty() : _items = [];
    
    T operator [](int index) => _items[index];
    int get length => _items.length;
    bool get isEmpty => _items.isEmpty;
    
    ImmutableList<T> add(T item) {
      return ImmutableList([..._items, item]);
    }
    
    ImmutableList<T> remove(T item) {
      return ImmutableList(_items.where((x) => x != item).toList());
    }
    
    ImmutableList<U> map<U>(U Function(T) f) {
      return ImmutableList(_items.map(f).toList());
    }
    
    ImmutableList<T> where(bool Function(T) predicate) {
      return ImmutableList(_items.where(predicate).toList());
    }
    
    @override
    String toString() => _items.toString();
  }
  
  // Usage example
  var list1 = ImmutableList([1, 2, 3]);
  var list2 = list1.add(4);
  var list3 = list2.map((x) => x * 2);
  var list4 = list3.where((x) => x > 4);
  
  print('Original: $list1');
  print('Added 4: $list2');
  print('Doubled: $list3');
  print('Filtered: $list4');
  
  // Immutable record-like structure
  class Person {
    final String name;
    final int age;
    final String email;
    
    const Person({
      required this.name,
      required this.age,
      required this.email,
    });
    
    Person copyWith({String? name, int? age, String? email}) {
      return Person(
        name: name ?? this.name,
        age: age ?? this.age,
        email: email ?? this.email,
      );
    }
    
    @override
    String toString() => 'Person(name: $name, age: $age, email: $email)';
  }
  
  var person1 = Person(name: 'Alice', age: 30, email: 'alice@example.com');
  var person2 = person1.copyWith(age: 31);
  var person3 = person2.copyWith(email: 'alice.smith@example.com');
  
  print('Original person: $person1');
  print('Updated age: $person2');
  print('Updated email: $person3');
}
```

Immutable data structures ensure data integrity and enable safe sharing  
between functions. They support functional programming by preventing  
side effects and making state changes explicit through copying.  

## Function Combinators

Function combinators are higher-order functions that combine or modify other  
functions to create new behaviors and abstractions.  

```dart
void main() {
  // Basic combinators
  T identity<T>(T x) => x;
  
  T Function() constant<T>(T value) => () => value;
  
  C Function(A) compose<A, B, C>(C Function(B) f, B Function(A) g) =>
      (A a) => f(g(a));
  
  // Flip combinator - reverses parameter order
  C Function(B, A) flip<A, B, C>(C Function(A, B) f) =>
      (B b, A a) => f(a, b);
  
  // Example functions
  int add(int a, int b) => a + b;
  int subtract(int a, int b) => a - b;
  String repeat(String s, int times) => s * times;
  
  var flippedSubtract = flip(subtract);
  var flippedRepeat = flip(repeat);
  
  print('Normal subtract 10 - 3: ${subtract(10, 3)}');
  print('Flipped subtract 3 from 10: ${flippedSubtract(3, 10)}');
  print('Normal repeat "Hi" 3 times: ${repeat("Hi", 3)}');
  print('Flipped repeat 3 times "Hi": ${flippedRepeat(3, "Hi")}');
  
  // Conditional combinators
  T Function(T) when<T>(bool condition, T Function(T) f) =>
      condition ? f : identity;
  
  T Function(T) unless<T>(bool condition, T Function(T) f) =>
      condition ? identity : f;
  
  // Apply combinators conditionally
  var numbers = [1, 2, 3, 4, 5];
  bool shouldDouble = true;
  bool shouldFilter = false;
  
  var result = numbers
      .map(when<int>(shouldDouble, (x) => x * 2))
      .where(unless<int>(shouldFilter, (x) => x % 2 == 0))
      .toList();
  
  print('Conditional result: $result');
  
  // Y Combinator for recursion
  T Function(T) fix<T>(T Function(T Function(T)) f) {
    return (T x) => f(fix(f))(x);
  }
  
  var factorial = fix<int>((fact) => (n) => n <= 1 ? 1 : n * fact(n - 1));
  print('Y combinator factorial 5: ${factorial(5)}');
}
```

Function combinators provide building blocks for creating complex behaviors  
from simple functions. They enable elegant abstractions and mathematical  
approaches to function manipulation and composition.  

## Async Functional Programming

Functional programming patterns applied to asynchronous operations enable  
clean handling of concurrent computations and data streams.  

```dart
import 'dart:async';

void main() async {
  // Async map operation
  Future<List<U>> asyncMap<T, U>(
    List<T> items,
    Future<U> Function(T) mapper,
  ) async {
    return Future.wait(items.map(mapper));
  }
  
  // Async filter operation
  Future<List<T>> asyncFilter<T>(
    List<T> items,
    Future<bool> Function(T) predicate,
  ) async {
    var results = await Future.wait(
      items.map((item) async => (item: item, keep: await predicate(item))),
    );
    return results.where((r) => r.keep).map((r) => r.item).toList();
  }
  
  // Simulate async operations
  Future<int> asyncDouble(int x) async {
    await Future.delayed(Duration(milliseconds: 100));
    return x * 2;
  }
  
  Future<bool> asyncIsEven(int x) async {
    await Future.delayed(Duration(milliseconds: 50));
    return x % 2 == 0;
  }
  
  var numbers = [1, 2, 3, 4, 5];
  
  print('Original: $numbers');
  
  var doubled = await asyncMap(numbers, asyncDouble);
  print('Async doubled: $doubled');
  
  var evenNumbers = await asyncFilter(numbers, asyncIsEven);
  print('Async evens: $evenNumbers');
  
  // Async fold/reduce
  Future<T> asyncFold<T, U>(
    List<U> items,
    T initialValue,
    Future<T> Function(T acc, U item) folder,
  ) async {
    var result = initialValue;
    for (var item in items) {
      result = await folder(result, item);
    }
    return result;
  }
  
  Future<int> asyncSum(int acc, int value) async {
    await Future.delayed(Duration(milliseconds: 10));
    return acc + value;
  }
  
  var sum = await asyncFold(numbers, 0, asyncSum);
  print('Async sum: $sum');
  
  // Stream functional operations
  Stream<int> numberStream = Stream.fromIterable([1, 2, 3, 4, 5]);
  
  var streamResult = await numberStream
      .map((x) => x * 3)
      .where((x) => x > 6)
      .fold<List<int>>(<int>[], (list, item) => list..add(item));
  
  print('Stream result: $streamResult');
}
```

Async functional programming extends functional patterns to asynchronous  
operations. It maintains composability while handling concurrency,  
enabling clean async data transformation pipelines.  

## Pattern Matching and Destructuring

Pattern matching enables concise data extraction and conditional logic  
based on data structure and values, supporting functional programming styles.  

```dart
void main() {
  // Algebraic data types with sealed classes
  sealed class Result<T, E> {}
  
  class Success<T, E> extends Result<T, E> {
    final T value;
    Success(this.value);
  }
  
  class Failure<T, E> extends Result<T, E> {
    final E error;
    Failure(this.error);
  }
  
  // Pattern matching with switch expressions
  String handleResult<T>(Result<T, String> result) {
    return switch (result) {
      Success(:var value) => 'Success: $value',
      Failure(:var error) => 'Error: $error',
    };
  }
  
  print(handleResult(Success(42)));
  print(handleResult(Failure('Not found')));
  
  // List pattern matching
  String analyzeList(List<int> numbers) {
    return switch (numbers) {
      [] => 'Empty list',
      [var single] => 'Single element: $single',
      [var first, var second] => 'Two elements: $first, $second',
      [var first, ...var rest] => 'First: $first, rest: $rest',
    };
  }
  
  print(analyzeList([]));
  print(analyzeList([1]));
  print(analyzeList([1, 2]));
  print(analyzeList([1, 2, 3, 4]));
  
  // Record pattern matching
  ({String name, int age}) person = (name: 'Alice', age: 30);
  
  String describePerson(({String name, int age}) p) {
    return switch (p) {
      (name: var n, age: var a) when a < 18 => '$n is a minor',
      (name: var n, age: var a) when a >= 65 => '$n is a senior',
      (name: var n, age: var a) => '$n is $a years old',
    };
  }
  
  print(describePerson((name: 'Bob', age: 16)));
  print(describePerson((name: 'Carol', age: 70)));
  print(describePerson(person));
  
  // Custom pattern matching with objects
  class Shape {}
  class Circle extends Shape {
    final double radius;
    Circle(this.radius);
  }
  class Rectangle extends Shape {
    final double width, height;
    Rectangle(this.width, this.height);
  }
  
  double calculateArea(Shape shape) {
    return switch (shape) {
      Circle(:var radius) => 3.14159 * radius * radius,
      Rectangle(:var width, :var height) => width * height,
    };
  }
  
  print('Circle area: ${calculateArea(Circle(5))}');
  print('Rectangle area: ${calculateArea(Rectangle(4, 6))}');
}
```

Pattern matching provides concise syntax for destructuring data and  
conditional logic. It enables functional-style data processing with  
exhaustive checking and elegant error handling patterns.  

## Functional Error Handling

Functional error handling avoids exceptions in favor of explicit error types  
and composable error handling patterns.  

```dart
void main() {
  // Either type for error handling
  sealed class Either<L, R> {
    T fold<T>(T Function(L) onLeft, T Function(R) onRight);
    Either<L, U> map<U>(U Function(R) f);
    Either<L, U> flatMap<U>(Either<L, U> Function(R) f);
  }
  
  class Left<L, R> extends Either<L, R> {
    final L value;
    Left(this.value);
    
    @override
    T fold<T>(T Function(L) onLeft, T Function(R) onRight) => onLeft(value);
    
    @override
    Either<L, U> map<U>(U Function(R) f) => Left(value);
    
    @override
    Either<L, U> flatMap<U>(Either<L, U> Function(R) f) => Left(value);
    
    @override
    String toString() => 'Left($value)';
  }
  
  class Right<L, R> extends Either<L, R> {
    final R value;
    Right(this.value);
    
    @override
    T fold<T>(T Function(L) onLeft, T Function(R) onRight) => onRight(value);
    
    @override
    Either<L, U> map<U>(U Function(R) f) => Right(f(value));
    
    @override
    Either<L, U> flatMap<U>(Either<L, U> Function(R) f) => f(value);
    
    @override
    String toString() => 'Right($value)';
  }
  
  Either<L, R> left<L, R>(L value) => Left(value);
  Either<L, R> right<L, R>(R value) => Right(value);
  
  // Error-prone operations
  Either<String, int> divide(int a, int b) {
    return b == 0 ? left('Division by zero') : right(a ~/ b);
  }
  
  Either<String, int> sqrt(int n) {
    return n < 0 ? left('Negative number') : right(n ~/ 1);
  }
  
  // Composing operations
  Either<String, int> calculate(int a, int b) {
    return divide(a, b)
        .flatMap((result) => sqrt(result))
        .map((result) => result * 2);
  }
  
  var result1 = calculate(100, 4);  // Success case
  var result2 = calculate(100, 0);  // Division error
  var result3 = calculate(-100, 4); // Sqrt error
  
  print('Success: $result1');
  print('Division error: $result2');
  print('Sqrt error: $result3');
  
  // Validation accumulation
  class ValidationResult<T> {
    final List<String> errors;
    final T? value;
    
    ValidationResult.success(this.value) : errors = [];
    ValidationResult.failure(this.errors) : value = null;
    
    bool get isValid => errors.isEmpty;
    
    ValidationResult<U> map<U>(U Function(T) f) {
      return isValid 
          ? ValidationResult.success(f(value as T))
          : ValidationResult.failure(errors);
    }
    
    static ValidationResult<List<T>> sequence<T>(
        List<ValidationResult<T>> results) {
      var allErrors = <String>[];
      var values = <T>[];
      
      for (var result in results) {
        if (result.isValid) {
          values.add(result.value as T);
        } else {
          allErrors.addAll(result.errors);
        }
      }
      
      return allErrors.isEmpty 
          ? ValidationResult.success(values)
          : ValidationResult.failure(allErrors);
    }
  }
  
  ValidationResult<String> validateEmail(String email) {
    return email.contains('@') 
        ? ValidationResult.success(email)
        : ValidationResult.failure(['Invalid email format']);
  }
  
  ValidationResult<int> validateAge(int age) {
    return age >= 0 && age <= 150
        ? ValidationResult.success(age)
        : ValidationResult.failure(['Age must be between 0 and 150']);
  }
  
  var validations = [
    validateEmail('user@example.com'),
    validateAge(25),
  ];
  
  var combined = ValidationResult.sequence(validations);
  print('Validation result: $combined');
}
```

Functional error handling makes errors explicit and composable. Either types  
provide railway-oriented programming, while validation results enable  
error accumulation for comprehensive input validation.  

## Functional Data Transformation Pipelines

Complex data transformations using functional composition patterns for  
clean, maintainable, and testable data processing workflows.  

```dart
void main() {
  // Sample data
  var salesData = [
    {'product': 'Laptop', 'category': 'Electronics', 'price': 1200, 'quantity': 50},
    {'product': 'Phone', 'category': 'Electronics', 'price': 800, 'quantity': 120},
    {'product': 'Desk', 'category': 'Furniture', 'price': 300, 'quantity': 30},
    {'product': 'Chair', 'category': 'Furniture', 'price': 150, 'quantity': 80},
    {'product': 'Tablet', 'category': 'Electronics', 'price': 500, 'quantity': 60},
  ];
  
  // Functional pipeline builder
  class Pipeline<T> {
    final Iterable<T> _data;
    
    Pipeline(this._data);
    
    Pipeline<T> where(bool Function(T) predicate) {
      return Pipeline(_data.where(predicate));
    }
    
    Pipeline<U> map<U>(U Function(T) mapper) {
      return Pipeline(_data.map(mapper));
    }
    
    Pipeline<T> orderBy<K extends Comparable>(K Function(T) keySelector) {
      var sorted = _data.toList()..sort((a, b) => 
          keySelector(a).compareTo(keySelector(b)));
      return Pipeline(sorted);
    }
    
    Pipeline<T> take(int count) {
      return Pipeline(_data.take(count));
    }
    
    Map<K, List<T>> groupBy<K>(K Function(T) keySelector) {
      var groups = <K, List<T>>{};
      for (var item in _data) {
        var key = keySelector(item);
        groups.putIfAbsent(key, () => []).add(item);
      }
      return groups;
    }
    
    U fold<U>(U initialValue, U Function(U acc, T item) folder) {
      return _data.fold(initialValue, folder);
    }
    
    List<T> toList() => _data.toList();
  }
  
  // Transform sales data
  var highValueElectronics = Pipeline(salesData)
      .where((item) => item['category'] == 'Electronics')
      .where((item) => (item['price'] as int) > 600)
      .map((item) => {
            ...item,
            'revenue': (item['price'] as int) * (item['quantity'] as int),
          })
      .orderBy((item) => item['revenue'] as int)
      .toList();
  
  print('High-value electronics:');
  for (var item in highValueElectronics) {
    print('  ${item['product']}: \$${item['revenue']}');
  }
  
  // Aggregation pipeline
  var categoryStats = Pipeline(salesData)
      .map((item) => {
            'category': item['category'],
            'revenue': (item['price'] as int) * (item['quantity'] as int),
          })
      .groupBy((item) => item['category']);
  
  print('\\nCategory statistics:');
  categoryStats.forEach((category, items) {
    var totalRevenue = items
        .map((item) => item['revenue'] as int)
        .reduce((a, b) => a + b);
    var avgRevenue = totalRevenue / items.length;
    
    print('  $category: Total \$${totalRevenue}, Avg \$${avgRevenue.round()}');
  });
  
  // Complex transformation with multiple stages
  var topProducts = Pipeline(salesData)
      .map((item) => {
            'product': item['product'],
            'score': ((item['price'] as int) * (item['quantity'] as int)) / 1000,
          })
      .where((item) => (item['score'] as double) > 20)
      .orderBy((item) => -(item['score'] as double))
      .take(3)
      .toList();
  
  print('\\nTop products by score:');
  for (var item in topProducts) {
    print('  ${item['product']}: ${(item['score'] as double).toStringAsFixed(1)}');
  }
  
  // Functional composition of transformers
  typedef Transformer<T> = Iterable<T> Function(Iterable<T>);
  
  Transformer<T> compose<T>(List<Transformer<T>> transformers) {
    return (Iterable<T> data) =>
        transformers.fold(data, (acc, transformer) => transformer(acc));
  }
  
  var electronicsFilter = (Iterable<Map<String, dynamic>> data) =>
      data.where((item) => item['category'] == 'Electronics');
  
  var addRevenue = (Iterable<Map<String, dynamic>> data) =>
      data.map((item) => {
            ...item,
            'revenue': (item['price'] as int) * (item['quantity'] as int),
          });
  
  var sortByRevenue = (Iterable<Map<String, dynamic>> data) {
    var sorted = data.toList()..sort((a, b) => 
        (b['revenue'] as int).compareTo(a['revenue'] as int));
    return sorted;
  };
  
  var composedTransformation = compose([
    electronicsFilter,
    addRevenue,
    sortByRevenue,
  ]);
  
  var transformed = composedTransformation(salesData);
  
  print('\\nComposed transformation:');
  for (var item in transformed.take(2)) {
    print('  ${item['product']}: \$${item['revenue']}');
  }
}
```

Functional pipelines enable complex data transformations through composition  
of simple, reusable operations. This approach promotes code reuse,  
testability, and maintainability in data processing applications.  

## Functional Testing Patterns

Functional programming principles applied to testing create more reliable,  
composable, and maintainable test suites through pure functions and  
property-based testing.  

```dart
void main() {
  // Property-based testing concepts
  class Property<T> {
    final bool Function(T) predicate;
    final String description;
    
    Property(this.predicate, this.description);
    
    bool test(T input) => predicate(input);
  }
  
  // Generator functions for test data
  typedef Generator<T> = Iterable<T> Function();
  
  Generator<int> intRange(int min, int max) =>
      () => List.generate(max - min + 1, (i) => min + i);
  
  Generator<String> stringGenerator(int maxLength) => () => 
      List.generate(10, (i) => 'test$i'.substring(0, 
          (i % maxLength + 1).clamp(1, maxLength)));
  
  // Property testing framework
  void checkProperty<T>(
    Property<T> property,
    Generator<T> generator, {
    int testCount = 100,
  }) {
    var inputs = generator().take(testCount);
    var failures = <T>[];
    
    for (var input in inputs) {
      if (!property.test(input)) {
        failures.add(input);
      }
    }
    
    if (failures.isEmpty) {
      print('✓ ${property.description} passed all tests');
    } else {
      print('✗ ${property.description} failed for: $failures');
    }
  }
  
  // Test pure functions with properties
  int add(int a, int b) => a + b;
  int multiply(int a, int b) => a * b;
  
  // Properties for mathematical functions
  var commutativityAdd = Property<(int, int)>(
    ((int, int) pair) => add(pair.$1, pair.$2) == add(pair.$2, pair.$1),
    'Addition is commutative',
  );
  
  var associativityAdd = Property<(int, int, int)>(
    ((int, int, int) triple) {
      var (a, b, c) = triple;
      return add(add(a, b), c) == add(a, add(b, c));
    },
    'Addition is associative',
  );
  
  var identityAdd = Property<int>(
    (int n) => add(n, 0) == n,
    'Zero is additive identity',
  );
  
  // Run property tests
  checkProperty(
    commutativityAdd,
    () => intRange(-10, 10)
        .expand((a) => intRange(-10, 10).map((b) => (a, b))),
  );
  
  checkProperty(
    associativityAdd,
    () => intRange(-5, 5)
        .expand((a) => intRange(-5, 5)
            .expand((b) => intRange(-5, 5).map((c) => (a, b, c)))),
  );
  
  checkProperty(identityAdd, intRange);
  
  // Test list operations
  List<T> reverse<T>(List<T> list) => list.reversed.toList();
  
  var doubleReverse = Property<List<int>>(
    (List<int> list) => reverse(reverse(list)).toString() == list.toString(),
    'Double reverse equals original',
  );
  
  Generator<List<int>> listGenerator = () => [
    [],
    [1],
    [1, 2],
    [1, 2, 3],
    [1, 2, 3, 4, 5],
    [-1, 0, 1],
    [10, 20, 30, 40],
  ];
  
  checkProperty(doubleReverse, listGenerator);
  
  // Functional test combinators
  typedef TestResult = ({bool passed, String message});
  
  TestResult Function(T) expect<T>(bool Function(T) predicate, String message) =>
      (T value) => (
        passed: predicate(value),
        message: predicate(value) ? 'PASS: $message' : 'FAIL: $message',
      );
  
  TestResult all(List<TestResult> results) {
    var passed = results.every((r) => r.passed);
    var messages = results.map((r) => r.message).join('\\n  ');
    return (passed: passed, message: messages);
  }
  
  // Test suite example
  var testData = [1, 2, 3, 4, 5];
  var doubled = testData.map((x) => x * 2).toList();
  
  var testSuite = all([
    expect<List<int>>((list) => list.length == 5, 'Length is 5')(doubled),
    expect<List<int>>((list) => list.every((x) => x % 2 == 0), 'All even')(doubled),
    expect<List<int>>((list) => list.first == 2, 'First is 2')(doubled),
    expect<List<int>>((list) => list.last == 10, 'Last is 10')(doubled),
  ]);
  
  print('\\nTest suite results:');
  print(testSuite.passed ? 'ALL TESTS PASSED' : 'SOME TESTS FAILED');
  print('  ${testSuite.message}');
}
```

Functional testing emphasizes pure functions, immutable test data, and  
property-based verification. This approach creates more robust tests that  
verify behavior across a wide range of inputs rather than specific cases.  

## Advanced Function Patterns

Sophisticated functional programming patterns including trampolines,  
continuation-passing style, and advanced higher-order function techniques.  

```dart
void main() {
  // Trampoline pattern for stack-safe recursion
  abstract class Trampoline<T> {
    T run() {
      Trampoline<T> current = this;
      while (current is! Done<T>) {
      current = (current as More<T>).next();
      }
      return current.value;
    }
  }
  
  class Done<T> extends Trampoline<T> {
    final T value;
    Done(this.value);
  }
  
  class More<T> extends Trampoline<T> {
    final Trampoline<T> Function() next;
    More(this.next);
  }
  
  // Stack-safe factorial using trampoline
  Trampoline<int> factorialTrampoline(int n, [int acc = 1]) {
    if (n <= 1) return Done(acc);
    return More(() => factorialTrampoline(n - 1, acc * n));
  }
  
  print('Trampoline factorial 10: ${factorialTrampoline(10).run()}');
  print('Trampoline factorial 100: ${factorialTrampoline(100).run()}');
  
  // Continuation Passing Style (CPS)
  typedef Continuation<T, R> = R Function(T);
  
  void addCPS<R>(int a, int b, Continuation<int, R> k) => k(a + b);
  void multiplyCPS<R>(int a, int b, Continuation<int, R> k) => k(a * b);
  void squareCPS<R>(int n, Continuation<int, R> k) => k(n * n);
  
  // CPS computation chain
  print('\\nCPS computation:');
  addCPS(3, 4, (sum) {
    print('3 + 4 = $sum');
    multiplyCPS(sum, 2, (product) {
      print('$sum * 2 = $product');
      squareCPS(product, (square) {
        print('$product² = $square');
      });
    });
  });
  
  // Church numerals (functional number encoding)
  typedef ChurchNum<T> = T Function(T Function(T), T);
  
  ChurchNum<T> zero<T>() => <T>(T Function(T) f, T x) => x;
  ChurchNum<T> one<T>() => <T>(T Function(T) f, T x) => f(x);
  ChurchNum<T> two<T>() => <T>(T Function(T) f, T x) => f(f(x));
  ChurchNum<T> three<T>() => <T>(T Function(T) f, T x) => f(f(f(x)));
  
  ChurchNum<T> successor<T>(ChurchNum<T> n) =>
      <T>(T Function(T) f, T x) => f(n(f, x));
  
  ChurchNum<T> add<T>(ChurchNum<T> m, ChurchNum<T> n) =>
      <T>(T Function(T) f, T x) => m(f, n(f, x));
  
  ChurchNum<T> multiply<T>(ChurchNum<T> m, ChurchNum<T> n) =>
      <T>(T Function(T) f, T x) => m(n(f), x);
  
  // Convert Church numeral to integer
  int churchToInt(ChurchNum<int> n) => n((x) => x + 1, 0);
  
  var four = successor(three<int>());
  var five = add(two<int>(), three<int>());
  var six = multiply(two<int>(), three<int>());
  
  print('\\nChurch numerals:');
  print('Successor of 3: ${churchToInt(four)}');
  print('2 + 3: ${churchToInt(five)}');
  print('2 * 3: ${churchToInt(six)}');
  
  // Fixed-point combinator for recursive functions
  T Function(T) fix<T>(T Function(T Function(T)) f) {
    // Y combinator implementation
    return (T x) {
      late T Function(T) rec;
      rec = (T y) => f(rec)(y);
      return f(rec)(x);
    };
  }
  
  // Define recursive functions using fix
  var fibonacciFix = fix<int>((fib) => (n) =>
      n <= 1 ? n : fib(n - 1) + fib(n - 2));
  
  var factorialFix = fix<int>((fact) => (n) =>
      n <= 1 ? 1 : n * fact(n - 1));
  
  print('\\nFixed-point combinators:');
  print('Fibonacci 8: ${fibonacciFix(8)}');
  print('Factorial 6: ${factorialFix(6)}');
  
  // Lens pattern for functional updates
  class Lens<S, A> {
    final A Function(S) getter;
    final S Function(S, A) setter;
    
    Lens(this.getter, this.setter);
    
    A view(S source) => getter(source);
    S set(S source, A value) => setter(source, value);
    S over(S source, A Function(A) f) => set(source, f(getter(source)));
    
    Lens<S, B> compose<B>(Lens<A, B> other) {
      return Lens<S, B>(
        (S s) => other.getter(getter(s)),
        (S s, B b) => setter(s, other.setter(getter(s), b)),
      );
    }
  }
  
  class Person {
    final String name;
    final Address address;
    
    Person(this.name, this.address);
    
    Person copyWith({String? name, Address? address}) =>
        Person(name ?? this.name, address ?? this.address);
    
    @override
    String toString() => 'Person($name, $address)';
  }
  
  class Address {
    final String street;
    final String city;
    
    Address(this.street, this.city);
    
    Address copyWith({String? street, String? city}) =>
        Address(street ?? this.street, city ?? this.city);
    
    @override
    String toString() => 'Address($street, $city)';
  }
  
  // Define lenses
  var personNameLens = Lens<Person, String>(
    (p) => p.name,
    (p, name) => p.copyWith(name: name),
  );
  
  var personAddressLens = Lens<Person, Address>(
    (p) => p.address,
    (p, address) => p.copyWith(address: address),
  );
  
  var addressCityLens = Lens<Address, String>(
    (a) => a.city,
    (a, city) => a.copyWith(city: city),
  );
  
  var personCityLens = personAddressLens.compose(addressCityLens);
  
  var person = Person('Alice', Address('123 Main St', 'Springfield'));
  var updatedPerson = personCityLens.set(person, 'Shelbyville');
  
  print('\\nLens example:');
  print('Original: $person');
  print('Updated city: $updatedPerson');
  print('City view: ${personCityLens.view(person)}');
}
```

Advanced functional patterns provide sophisticated tools for complex  
programming challenges. Trampolines ensure stack safety, CPS enables  
control flow abstraction, and lenses provide composable data access  
patterns for immutable structures.  

## Functional Reactive Programming

Functional reactive programming (FRP) combines functional programming with  
reactive streams, enabling declarative handling of time-varying values  
and asynchronous events.  

```dart
import 'dart:async';

void main() async {
  // Stream transformations with functional operators
  var numberStream = Stream.periodic(Duration(milliseconds: 100), (i) => i)
      .take(10);
  
  // Functional stream processing
  var processedStream = numberStream
      .where((n) => n % 2 == 0)
      .map((n) => n * n)
      .scan(0, (acc, value) => acc + value);
  
  print('Processed stream:');
  await for (var value in processedStream) {
    print('Running sum of even squares: $value');
  }
  
  // Custom stream operators
  static Stream<T> scan<T, S>(Stream<S> stream, T initial, T Function(T, S) accumulator) {
    late StreamController<T> controller;
    T current = initial;
    
    controller = StreamController<T>(
      onListen: () {
        controller.add(current);
        stream.listen(
          (value) {
            current = accumulator(current, value);
            controller.add(current);
          },
          onError: controller.addError,
          onDone: controller.close,
        );
      },
    );
    
    return controller.stream;
  }
  
  // Combine multiple streams functionally
  Stream<C> combineStreams<A, B, C>(
    Stream<A> streamA,
    Stream<B> streamB,
    C Function(A, B) combiner,
  ) async* {
    A? lastA;
    B? lastB;
    
    await for (var event in StreamGroup.merge([
      streamA.map((a) => ('A', a)),
      streamB.map((b) => ('B', b)),
    ])) {
      if (event.$1 == 'A') {
        lastA = event.$2 as A;
      } else {
        lastB = event.$2 as B;
      }
      
      if (lastA != null && lastB != null) {
        yield combiner(lastA!, lastB!);
      }
    }
  }
  
  // Reactive state management
  class ReactiveState<T> {
    final StreamController<T> _controller = StreamController.broadcast();
    T _value;
    
    ReactiveState(this._value);
    
    T get value => _value;
    Stream<T> get stream => _controller.stream;
    
    void update(T newValue) {
      _value = newValue;
      _controller.add(newValue);
    }
    
    void updateWith(T Function(T) updater) {
      update(updater(_value));
    }
    
    Stream<U> map<U>(U Function(T) mapper) =>
        stream.map(mapper);
    
    Stream<T> where(bool Function(T) predicate) =>
        stream.where(predicate);
    
    void dispose() => _controller.close();
  }
  
  var counter = ReactiveState(0);
  var evenCounter = counter.where((n) => n % 2 == 0);
  
  evenCounter.listen((value) => print('Even counter: $value'));
  
  counter.update(1);
  counter.update(2);
  counter.update(3);
  counter.update(4);
  
  await Future.delayed(Duration(milliseconds: 100));
  counter.dispose();
}

// Helper class for stream merging
class StreamGroup {
  static Stream<T> merge<T>(Iterable<Stream<T>> streams) {
    late StreamController<T> controller;
    var subscriptions = <StreamSubscription>[];
    
    controller = StreamController<T>(
      onListen: () {
        for (var stream in streams) {
          subscriptions.add(stream.listen(
            controller.add,
            onError: controller.addError,
          ));
        }
      },
      onCancel: () {
        for (var sub in subscriptions) {
          sub.cancel();
        }
      },
    );
    
    return controller.stream;
  }
}
```

Functional reactive programming provides declarative handling of asynchronous  
data streams. It enables clean composition of time-based operations and  
reactive state management through functional stream transformations.  

## Type-Level Programming

Type-level programming uses Dart's type system to encode logic and constraints  
at compile-time, ensuring correctness through type safety and phantom types.  

```dart
void main() {
  // Phantom types for compile-time safety
  class Validated<T> {
    final T value;
    const Validated._(this.value);
    
    @override
    String toString() => 'Validated($value)';
  }
  
  class Unvalidated<T> {
    final T value;
    const Unvalidated(this.value);
    
    @override
    String toString() => 'Unvalidated($value)';
  }
  
  // Validation functions that change types
  Validated<String>? validateEmail(Unvalidated<String> email) {
    return email.value.contains('@') 
        ? Validated._(email.value)
        : null;
  }
  
  Validated<int>? validateAge(Unvalidated<int> age) {
    return age.value >= 0 && age.value <= 150
        ? Validated._(age.value)
        : null;
  }
  
  // Functions that only accept validated data
  void sendEmail(Validated<String> email) {
    print('Sending email to ${email.value}');
  }
  
  void calculateInsurance(Validated<int> age) {
    print('Calculating insurance for age ${age.value}');
  }
  
  // Usage with type safety
  var rawEmail = Unvalidated('user@example.com');
  var rawAge = Unvalidated(25);
  
  var validatedEmail = validateEmail(rawEmail);
  var validatedAge = validateAge(rawAge);
  
  if (validatedEmail != null && validatedAge != null) {
    sendEmail(validatedEmail);
    calculateInsurance(validatedAge);
  }
  
  // State machine types
  abstract class ConnectionState {}
  class Disconnected extends ConnectionState {}
  class Connecting extends ConnectionState {}
  class Connected extends ConnectionState {}
  class Failed extends ConnectionState {}
  
  class Connection<S extends ConnectionState> {
    final String host;
    final S state;
    
    Connection._(this.host, this.state);
    
    // Factory constructors for type-safe state creation
    factory Connection.disconnected(String host) => 
        Connection._(host, Disconnected() as S);
    
    // Type-safe state transitions
    Connection<Connecting> connect() {
      if (S == Disconnected) {
        print('Connecting to $host...');
        return Connection._(host, Connecting());
      }
      throw StateError('Cannot connect from ${S.toString()}');
    }
    
    Connection<Connected> establish() {
      if (S == Connecting) {
        print('Connected to $host');
        return Connection._(host, Connected());
      }
      throw StateError('Cannot establish from ${S.toString()}');
    }
    
    Connection<Disconnected> disconnect() {
      print('Disconnected from $host');
      return Connection._(host, Disconnected());
    }
    
    // Actions only available in specific states
    void sendData(String data) {
      if (S == Connected) {
        print('Sending: $data');
      } else {
        throw StateError('Cannot send data when not connected');
      }
    }
  }
  
  // Type-safe usage
  var connection = Connection<Disconnected>.disconnected('example.com')
      .connect()
      .establish();
  
  connection.sendData('Hello there!');
  
  // Units of measure with phantom types
  class Meter {
    final double value;
    const Meter(this.value);
    @override String toString() => '${value}m';
  }
  
  class Second {
    final double value;
    const Second(this.value);
    @override String toString() => '${value}s';
  }
  
  class MeterPerSecond {
    final double value;
    const MeterPerSecond(this.value);
    @override String toString() => '${value}m/s';
  }
  
  // Type-safe unit operations
  MeterPerSecond velocity(Meter distance, Second time) {
    return MeterPerSecond(distance.value / time.value);
  }
  
  Meter distance(MeterPerSecond velocity, Second time) {
    return Meter(velocity.value * time.value);
  }
  
  var dist = Meter(100);
  var time = Second(10);
  var vel = velocity(dist, time);
  
  print('Distance: $dist, Time: $time, Velocity: $vel');
  print('Distance traveled at $vel for 5s: ${distance(vel, Second(5))}');
}
```

Type-level programming leverages Dart's type system for compile-time  
verification and safety. Phantom types prevent runtime errors by encoding  
invariants in types, while state machines ensure valid state transitions.  

## Category Theory Concepts

Category theory provides mathematical foundations for functional programming  
through concepts like functors, applicatives, and monads implemented in Dart.  

```dart
void main() {
  // Functor abstraction
  abstract class Functor<F> {
    Functor<F> map<A, B>(B Function(A) f);
  }
  
  // Maybe/Option as Functor
  abstract class Maybe<T> implements Functor<Maybe> {
    bool get isEmpty;
    bool get isNotEmpty => !isEmpty;
    
    @override
    Maybe<U> map<A, B>(U Function(T) f);
    
    T getOrElse(T defaultValue);
  }
  
  class Just<T> extends Maybe<T> {
    final T value;
    Just(this.value);
    
    @override
    bool get isEmpty => false;
    
    @override
    Maybe<U> map<A, B>(U Function(T) f) => Just(f(value));
    
    @override
    T getOrElse(T defaultValue) => value;
    
    @override
    String toString() => 'Just($value)';
  }
  
  class Nothing<T> extends Maybe<T> {
    @override
    bool get isEmpty => true;
    
    @override
    Maybe<U> map<A, B>(U Function(T) f) => Nothing<U>();
    
    @override
    T getOrElse(T defaultValue) => defaultValue;
    
    @override
    String toString() => 'Nothing';
  }
  
  Maybe<T> just<T>(T value) => Just(value);
  Maybe<T> nothing<T>() => Nothing<T>();
  
  // Applicative functor
  abstract class Applicative<F> extends Functor<F> {
    Applicative<F> pure<A>(A value);
    Applicative<F> apply<A, B>(Applicative<F> Function(A) f);
  }
  
  // List as Applicative
  class FunctionalList<T> implements Applicative<FunctionalList> {
    final List<T> items;
    
    FunctionalList(this.items);
    
    @override
    FunctionalList<U> map<A, B>(U Function(T) f) =>
        FunctionalList(items.map(f).toList());
    
    @override
    FunctionalList<U> pure<U>(U value) => FunctionalList([value]);
    
    @override
    FunctionalList<U> apply<A, B>(FunctionalList<U Function(T)> functions) {
      var result = <U>[];
      for (var f in functions.items) {
        result.addAll(items.map(f));
      }
      return FunctionalList(result);
    }
    
    // Applicative operations
    FunctionalList<C> liftA2<B, C>(
      C Function(T, B) f,
      FunctionalList<B> other,
    ) {
      return apply(other.map((b) => (T a) => f(a, b)));
    }
    
    @override
    String toString() => 'FList($items)';
  }
  
  // Monad abstraction
  abstract class Monad<M> extends Applicative<M> {
    Monad<M> flatMap<A, B>(Monad<M> Function(A) f);
  }
  
  // IO Monad for side effects
  class IO<T> implements Monad<IO> {
    final T Function() _computation;
    
    IO(this._computation);
    
    T unsafePerformIO() => _computation();
    
    @override
    IO<U> map<A, B>(U Function(T) f) =>
        IO(() => f(_computation()));
    
    @override
    IO<U> pure<U>(U value) => IO(() => value);
    
    @override
    IO<U> apply<A, B>(IO<U Function(T)> functions) =>
        IO(() => functions._computation()(_computation()));
    
    @override
    IO<U> flatMap<A, B>(IO<U> Function(T) f) =>
        IO(() => f(_computation()).unsafePerformIO());
    
    // Sequential composition
    IO<U> then<U>(IO<U> other) => flatMap((_) => other);
    
    @override
    String toString() => 'IO(...)';
  }
  
  // Example usage of category theory concepts
  print('Functor example:');
  var maybeValue = just(5).map((x) => x * 2).map((x) => x + 1);
  print('Maybe result: $maybeValue');
  
  var nothingValue = nothing<int>().map((x) => x * 2).map((x) => x + 1);
  print('Nothing result: $nothingValue');
  
  print('\\nApplicative example:');
  var list1 = FunctionalList([1, 2]);
  var list2 = FunctionalList([10, 20]);
  var combined = list1.liftA2<int, int>((a, b) => a + b, list2);
  print('Applicative result: $combined');
  
  print('\\nMonad example (IO):');
  var greeting = IO(() => 'Hello there');
  var name = IO(() => 'Dart');
  
  var message = greeting.flatMap((g) =>
      name.map((n) => '$g, $n!'));
  
  print('IO result: ${message.unsafePerformIO()}');
  
  // Free monad for interpreters
  abstract class Free<F, A> {}
  
  class Pure<F, A> extends Free<F, A> {
    final A value;
    Pure(this.value);
  }
  
  class Suspend<F, A> extends Free<F, A> {
    final F operation;
    final Free<F, A> Function(dynamic) continuation;
    
    Suspend(this.operation, this.continuation);
  }
  
  // Console operations algebra
  abstract class ConsoleOp<A> {}
  
  class WriteLine<A> extends ConsoleOp<A> {
    final String message;
    WriteLine(this.message);
  }
  
  class ReadLine extends ConsoleOp<String> {}
  
  // Free monad operations
  Free<ConsoleOp, Unit> writeLine(String message) =>
      Suspend(WriteLine(message), (unit) => Pure(Unit()));
  
  Free<ConsoleOp, String> readLine() =>
      Suspend(ReadLine(), (input) => Pure(input as String));
  
  class Unit {}
  
  print('\\nFree monad program construction complete');
}
```

Category theory provides mathematical foundations for functional programming.  
Functors enable mapping, applicatives allow function application in context,  
and monads provide sequential composition with context preservation.  

## Parallel and Concurrent Functional Programming

Functional programming patterns for parallel and concurrent execution,  
leveraging immutability and pure functions for safe concurrent operations.  

```dart
import 'dart:async';
import 'dart:isolate';
import 'dart:math';

void main() async {
  // Parallel map using isolates
  Future<List<U>> parallelMap<T, U>(
    List<T> items,
    U Function(T) mapper, {
    int? concurrency,
  }) async {
    concurrency ??= Platform.numberOfProcessors;
    
    if (items.isEmpty) return [];
    
    var chunks = <List<T>>[];
    var chunkSize = (items.length / concurrency).ceil();
    
    for (int i = 0; i < items.length; i += chunkSize) {
      chunks.add(items.sublist(
        i, 
        math.min(i + chunkSize, items.length)
      ));
    }
    
    var futures = chunks.map((chunk) => 
        Isolate.run(() => chunk.map(mapper).toList()));
    
    var results = await Future.wait(futures);
    return results.expand((list) => list).toList();
  }
  
  // Heavy computation for testing
  int fibonacci(int n) {
    if (n <= 1) return n;
    return fibonacci(n - 1) + fibonacci(n - 2);
  }
  
  var numbers = [30, 31, 32, 33, 34];
  
  print('Computing Fibonacci in parallel...');
  var stopwatch = Stopwatch()..start();
  var results = await parallelMap(numbers, fibonacci);
  stopwatch.stop();
  
  print('Results: $results');
  print('Parallel time: ${stopwatch.elapsedMilliseconds}ms');
  
  // Compare with sequential execution
  stopwatch.reset();
  stopwatch.start();
  var sequentialResults = numbers.map(fibonacci).toList();
  stopwatch.stop();
  
  print('Sequential time: ${stopwatch.elapsedMilliseconds}ms');
  
  // Concurrent async operations
  Future<List<U>> concurrentMap<T, U>(
    List<T> items,
    Future<U> Function(T) mapper, {
    int maxConcurrency = 10,
  }) async {
    if (items.isEmpty) return [];
    
    var semaphore = Semaphore(maxConcurrency);
    var futures = items.map((item) => 
        semaphore.acquire().then((_) => 
            mapper(item).whenComplete(semaphore.release)));
    
    return Future.wait(futures);
  }
  
  // Simulate async operation
  Future<String> processAsync(int value) async {
    await Future.delayed(Duration(milliseconds: 100));
    return 'Processed: $value';
  }
  
  print('\\nConcurrent async processing:');
  var asyncResults = await concurrentMap(
    [1, 2, 3, 4, 5],
    processAsync,
    maxConcurrency: 3,
  );
  
  for (var result in asyncResults) {
    print(result);
  }
  
  // Functional concurrent pipeline
  class ConcurrentPipeline<T> {
    final Stream<T> _source;
    
    ConcurrentPipeline(this._source);
    
    ConcurrentPipeline<U> mapConcurrent<U>(
      Future<U> Function(T) mapper, {
      int concurrency = 10,
    }) {
      var controller = StreamController<U>();
      var semaphore = Semaphore(concurrency);
      
      _source.listen(
        (item) {
          semaphore.acquire().then((_) {
            mapper(item)
                .then(controller.add)
                .catchError(controller.addError)
                .whenComplete(semaphore.release);
          });
        },
        onError: controller.addError,
        onDone: controller.close,
      );
      
      return ConcurrentPipeline(controller.stream);
    }
    
    Future<List<T>> toList() => _source.toList();
    Stream<T> get stream => _source;
  }
  
  // Actor model implementation
  class Actor<M> {
    final ReceivePort _receivePort = ReceivePort();
    final StreamController<M> _messageController = StreamController();
    late final SendPort sendPort;
    
    Actor(Future<void> Function(M) handler) {
      sendPort = _receivePort.sendPort;
      
      _receivePort.listen((message) {
        _messageController.add(message as M);
      });
      
      _messageController.stream.listen(handler);
    }
    
    void send(M message) {
      sendPort.send(message);
    }
    
    void dispose() {
      _receivePort.close();
      _messageController.close();
    }
  }
  
  // Counter actor
  class CounterMessage {}
  class Increment extends CounterMessage {}
  class GetCount extends CounterMessage {
    final SendPort replyTo;
    GetCount(this.replyTo);
  }
  
  var counterActor = Actor<CounterMessage>((message) async {
    static int count = 0;
    
    switch (message) {
      case Increment():
        count++;
        print('Count incremented to: $count');
      case GetCount(:var replyTo):
        replyTo.send(count);
    }
  });
  
  // Use actor
  counterActor.send(Increment());
  counterActor.send(Increment());
  
  var replyPort = ReceivePort();
  counterActor.send(GetCount(replyPort.sendPort));
  
  var finalCount = await replyPort.first;
  print('Final count: $finalCount');
  
  replyPort.close();
  counterActor.dispose();
}

// Semaphore for controlling concurrency
class Semaphore {
  final int maxCount;
  int _currentCount;
  final Queue<Completer<void>> _waitQueue = Queue();
  
  Semaphore(this.maxCount) : _currentCount = maxCount;
  
  Future<void> acquire() {
    if (_currentCount > 0) {
      _currentCount--;
      return Future.value();
    } else {
      var completer = Completer<void>();
      _waitQueue.add(completer);
      return completer.future;
    }
  }
  
  void release() {
    if (_waitQueue.isNotEmpty) {
      var completer = _waitQueue.removeFirst();
      completer.complete();
    } else {
      _currentCount++;
    }
  }
}
```

Functional concurrent programming leverages immutability for safe parallel  
execution. Pure functions enable easy parallelization while functional  
patterns like actors and pipelines provide structured concurrent abstractions.  

## Functional Architecture Patterns

Architectural patterns that apply functional programming principles to  
structure large applications with clean separation of concerns and  
predictable data flow.  

```dart
void main() {
  // Command Query Responsibility Segregation (CQRS) with functional approach
  
  // Events (immutable data)
  abstract class Event {
    final DateTime timestamp;
    Event() : timestamp = DateTime.now();
  }
  
  class UserCreated extends Event {
    final String userId;
    final String email;
    final String name;
    
    UserCreated(this.userId, this.email, this.name);
    
    @override
    String toString() => 'UserCreated($userId, $email, $name)';
  }
  
  class UserEmailUpdated extends Event {
    final String userId;
    final String newEmail;
    
    UserEmailUpdated(this.userId, this.newEmail);
    
    @override
    String toString() => 'UserEmailUpdated($userId, $newEmail)';
  }
  
  // Commands (pure data structures)
  abstract class Command {}
  
  class CreateUser extends Command {
    final String email;
    final String name;
    
    CreateUser(this.email, this.name);
  }
  
  class UpdateUserEmail extends Command {
    final String userId;
    final String newEmail;
    
    UpdateUserEmail(this.userId, this.newEmail);
  }
  
  // State (immutable)
  class UserState {
    final Map<String, User> users;
    
    const UserState(this.users);
    
    UserState.empty() : users = const {};
    
    UserState addUser(String id, User user) {
      return UserState({...users, id: user});
    }
    
    UserState updateUser(String id, User Function(User) updater) {
      var user = users[id];
      if (user == null) return this;
      
      return UserState({...users, id: updater(user)});
    }
  }
  
  class User {
    final String id;
    final String email;
    final String name;
    
    const User(this.id, this.email, this.name);
    
    User copyWith({String? email, String? name}) {
      return User(id, email ?? this.email, name ?? this.name);
    }
    
    @override
    String toString() => 'User($id, $email, $name)';
  }
  
  // Pure command handlers
  class CommandHandlers {
    static List<Event> handleCreateUser(CreateUser cmd, UserState state) {
      var userId = _generateId();
      return [UserCreated(userId, cmd.email, cmd.name)];
    }
    
    static List<Event> handleUpdateUserEmail(UpdateUserEmail cmd, UserState state) {
      if (!state.users.containsKey(cmd.userId)) {
        return []; // User not found, no events
      }
      
      return [UserEmailUpdated(cmd.userId, cmd.newEmail)];
    }
    
    static String _generateId() => 'user_${DateTime.now().millisecondsSinceEpoch}';
  }
  
  // Pure event reducers
  class EventReducers {
    static UserState reduce(UserState state, Event event) {
      return switch (event) {
        UserCreated(:var userId, :var email, :var name) =>
          state.addUser(userId, User(userId, email, name)),
        UserEmailUpdated(:var userId, :var newEmail) =>
          state.updateUser(userId, (user) => user.copyWith(email: newEmail)),
        _ => state,
      };
    }
  }
  
  // Functional event store
  class EventStore {
    final List<Event> _events = [];
    
    void append(List<Event> events) {
      _events.addAll(events);
    }
    
    List<Event> getAllEvents() => List.unmodifiable(_events);
    
    UserState projectTo<T>(
      T initialState,
      T Function(T state, Event event) reducer,
    ) {
      return _events.fold(initialState as UserState, reducer);
    }
  }
  
  // Application service (coordinates everything)
  class UserService {
    final EventStore _eventStore;
    
    UserService(this._eventStore);
    
    void execute(Command command) {
      var currentState = _getCurrentState();
      var events = _handleCommand(command, currentState);
      
      if (events.isNotEmpty) {
        _eventStore.append(events);
        print('Events applied: ${events.map((e) => e.toString()).join(', ')}');
      }
    }
    
    UserState query() => _getCurrentState();
    
    UserState _getCurrentState() {
      return _eventStore.projectTo(UserState.empty(), EventReducers.reduce);
    }
    
    List<Event> _handleCommand(Command command, UserState state) {
      return switch (command) {
        CreateUser() => CommandHandlers.handleCreateUser(command, state),
        UpdateUserEmail() => CommandHandlers.handleUpdateUserEmail(command, state),
        _ => [],
      };
    }
  }
  
  // Functional pipeline architecture
  typedef Pipeline<I, O> = O Function(I);
  
  class PipelineBuilder<T> {
    final List<Function> _steps = [];
    
    PipelineBuilder<U> pipe<U>(U Function(T) step) {
      _steps.add(step);
      return PipelineBuilder<U>();
    }
    
    Pipeline<I, T> build<I>() {
      return (I input) {
        dynamic current = input;
        for (var step in _steps) {
          current = step(current);
        }
        return current as T;
      };
    }
  }
  
  // Usage example
  var eventStore = EventStore();
  var userService = UserService(eventStore);
  
  print('CQRS Functional Architecture Demo:');
  
  // Execute commands
  userService.execute(CreateUser('alice@example.com', 'Alice'));
  userService.execute(CreateUser('bob@example.com', 'Bob'));
  
  var state = userService.query();
  print('Current state: ${state.users}');
  
  // Update user
  var userId = state.users.keys.first;
  userService.execute(UpdateUserEmail(userId, 'alice.new@example.com'));
  
  var updatedState = userService.query();
  print('Updated state: ${updatedState.users}');
  
  print('\\nAll events in store:');
  for (var event in eventStore.getAllEvents()) {
    print('  $event');
  }
  
  // Functional dependency injection
  class ServiceRegistry {
    final Map<Type, Function> _factories = {};
    final Map<Type, dynamic> _singletons = {};
    
    void register<T>(T Function() factory) {
      _factories[T] = factory;
    }
    
    void registerSingleton<T>(T instance) {
      _singletons[T] = instance;
    }
    
    T resolve<T>() {
      if (_singletons.containsKey(T)) {
        return _singletons[T] as T;
      }
      
      var factory = _factories[T];
      if (factory != null) {
        return factory() as T;
      }
      
      throw ArgumentError('No registration found for type $T');
    }
  }
  
  // Example functional module system
  abstract class Module {
    void configure(ServiceRegistry registry);
  }
  
  class UserModule implements Module {
    @override
    void configure(ServiceRegistry registry) {
      registry.registerSingleton<EventStore>(EventStore());
      registry.register<UserService>(() => 
          UserService(registry.resolve<EventStore>()));
    }
  }
  
  var registry = ServiceRegistry();
  var userModule = UserModule();
  userModule.configure(registry);
  
  var serviceFromRegistry = registry.resolve<UserService>();
  print('\\nService from registry works: ${serviceFromRegistry != null}');
}
```

Functional architecture patterns promote separation of concerns through  
immutability and pure functions. CQRS with event sourcing provides clear  
data flow while functional dependency injection enables clean composition.  

## Domain-Specific Languages (DSL)

Creating domain-specific languages using functional programming techniques  
to build expressive, type-safe APIs for specific problem domains.  

```dart
void main() {
  // Query DSL for data filtering and transformation
  
  class Query<T> {
    final List<T> data;
    final List<bool Function(T)> filters;
    final List<T Function(T)> transformations;
    final List<Comparable Function(T)> sortKeys;
    
    Query._(this.data, this.filters, this.transformations, this.sortKeys);
    
    Query.from(List<T> data) : this._(data, [], [], []);
    
    Query<T> where(bool Function(T) predicate) {
      return Query._(data, [...filters, predicate], transformations, sortKeys);
    }
    
    Query<U> select<U>(U Function(T) selector) {
      return Query<U>._(
        [],
        [],
        [],
        [],
      )._withBase(data, filters, [...transformations, selector], []);
    }
    
    Query<T> orderBy(Comparable Function(T) keySelector) {
      return Query._(data, filters, transformations, [...sortKeys, keySelector]);
    }
    
    Query<T> take(int count) {
      return Query._(data.take(count).toList(), filters, transformations, sortKeys);
    }
    
    Query<U> _withBase<U>(
      List data,
      List<bool Function(dynamic)> filters,
      List<Function> transformations,
      List<Comparable Function(dynamic)> sortKeys,
    ) {
      return Query<U>._(
        data as List<U>,
        filters as List<bool Function(U)>,
        transformations as List<U Function(U)>,
        sortKeys as List<Comparable Function(U)>,
      );
    }
    
    List<T> execute() {
      var result = List<T>.from(data);
      
      // Apply filters
      for (var filter in filters) {
        result = result.where(filter).toList();
      }
      
      // Apply transformations
      dynamic current = result;
      for (var transform in transformations) {
        current = (current as List).map(transform).toList();
      }
      result = current as List<T>;
      
      // Apply sorting
      if (sortKeys.isNotEmpty) {
        result.sort((a, b) {
          for (var keySelector in sortKeys) {
            var comparison = Comparable.compare(
              keySelector(a),
              keySelector(b),
            );
            if (comparison != 0) return comparison;
          }
          return 0;
        });
      }
      
      return result;
    }
  }
  
  // Sample data
  var people = [
    {'name': 'Alice', 'age': 30, 'city': 'New York'},
    {'name': 'Bob', 'age': 25, 'city': 'Boston'},
    {'name': 'Carol', 'age': 35, 'city': 'New York'},
    {'name': 'David', 'age': 28, 'city': 'Boston'},
  ];
  
  // Query DSL usage
  var result = Query.from(people)
      .where((p) => (p['age'] as int) > 27)
      .where((p) => p['city'] == 'New York')
      .select((p) => '${p['name']} (${p['age']})')
      .execute();
  
  print('Query DSL result: $result');
  
  // Validation DSL
  class ValidationRule<T> {
    final bool Function(T) predicate;
    final String message;
    
    ValidationRule(this.predicate, this.message);
  }
  
  class Validator<T> {
    final List<ValidationRule<T>> rules = [];
    
    Validator<T> must(bool Function(T) predicate, String message) {
      rules.add(ValidationRule(predicate, message));
      return this;
    }
    
    Validator<T> mustNot(bool Function(T) predicate, String message) {
      rules.add(ValidationRule((value) => !predicate(value), message));
      return this;
    }
    
    Validator<String> get isEmail => must(
      (email) => email.contains('@') && email.contains('.'),
      'Must be a valid email address',
    ) as Validator<String>;
    
    Validator<String> get isNotEmpty => must(
      (value) => value.isNotEmpty,
      'Must not be empty',
    ) as Validator<String>;
    
    Validator<int> get isPositive => must(
      (value) => value > 0,
      'Must be positive',
    ) as Validator<int>;
    
    Validator<int> isInRange(int min, int max) => must(
      (value) => value >= min && value <= max,
      'Must be between $min and $max',
    ) as Validator<int>;
    
    List<String> validate(T value) {
      var errors = <String>[];
      
      for (var rule in rules) {
        if (!rule.predicate(value)) {
          errors.add(rule.message);
        }
      }
      
      return errors;
    }
  }
  
  // Validation DSL usage
  var emailValidator = Validator<String>()
      .isNotEmpty
      .isEmail
      .must((email) => email.length >= 5, 'Must be at least 5 characters');
  
  var ageValidator = Validator<int>()
      .isPositive
      .isInRange(18, 100);
  
  print('\\nValidation DSL:');
  print('Email "test@": ${emailValidator.validate("test@")}');
  print('Email "user@example.com": ${emailValidator.validate("user@example.com")}');
  print('Age 150: ${ageValidator.validate(150)}');
  print('Age 25: ${ageValidator.validate(25)}');
  
  // Configuration DSL
  class ConfigBuilder {
    final Map<String, dynamic> _config = {};
    
    ConfigBuilder database(void Function(DatabaseConfig) configure) {
      var dbConfig = DatabaseConfig();
      configure(dbConfig);
      _config['database'] = dbConfig._build();
      return this;
    }
    
    ConfigBuilder server(void Function(ServerConfig) configure) {
      var serverConfig = ServerConfig();
      configure(serverConfig);
      _config['server'] = serverConfig._build();
      return this;
    }
    
    ConfigBuilder logging(void Function(LoggingConfig) configure) {
      var loggingConfig = LoggingConfig();
      configure(loggingConfig);
      _config['logging'] = loggingConfig._build();
      return this;
    }
    
    Map<String, dynamic> build() => Map.unmodifiable(_config);
  }
  
  class DatabaseConfig {
    String? host;
    int? port;
    String? name;
    bool ssl = false;
    
    void setHost(String host) => this.host = host;
    void setPort(int port) => this.port = port;
    void setName(String name) => this.name = name;
    void enableSsl() => ssl = true;
    
    Map<String, dynamic> _build() => {
      'host': host,
      'port': port,
      'name': name,
      'ssl': ssl,
    };
  }
  
  class ServerConfig {
    String? host;
    int? port;
    int maxConnections = 100;
    
    void setHost(String host) => this.host = host;
    void setPort(int port) => this.port = port;
    void setMaxConnections(int max) => maxConnections = max;
    
    Map<String, dynamic> _build() => {
      'host': host,
      'port': port,
      'maxConnections': maxConnections,
    };
  }
  
  class LoggingConfig {
    String level = 'info';
    List<String> outputs = [];
    
    void setLevel(String level) => this.level = level;
    void addOutput(String output) => outputs.add(output);
    
    Map<String, dynamic> _build() => {
      'level': level,
      'outputs': outputs,
    };
  }
  
  // Configuration DSL usage
  var config = ConfigBuilder()
      .database((db) {
        db.setHost('localhost');
        db.setPort(5432);
        db.setName('myapp');
        db.enableSsl();
      })
      .server((server) {
        server.setHost('0.0.0.0');
        server.setPort(8080);
        server.setMaxConnections(200);
      })
      .logging((log) {
        log.setLevel('debug');
        log.addOutput('console');
        log.addOutput('file');
      })
      .build();
  
  print('\\nConfiguration DSL result:');
  config.forEach((key, value) {
    print('$key: $value');
  });
  
  // Mathematical expression DSL
  abstract class Expr {
    double evaluate();
    String toString();
  }
  
  class Num extends Expr {
    final double value;
    Num(this.value);
    
    @override
    double evaluate() => value;
    
    @override
    String toString() => value.toString();
  }
  
  class Add extends Expr {
    final Expr left, right;
    Add(this.left, this.right);
    
    @override
    double evaluate() => left.evaluate() + right.evaluate();
    
    @override
    String toString() => '($left + $right)';
  }
  
  class Multiply extends Expr {
    final Expr left, right;
    Multiply(this.left, this.right);
    
    @override
    double evaluate() => left.evaluate() * right.evaluate();
    
    @override
    String toString() => '($left * $right)';
  }
  
  // DSL builder functions
  Expr num(double value) => Num(value);
  Expr add(Expr left, Expr right) => Add(left, right);
  Expr multiply(Expr left, Expr right) => Multiply(left, right);
  
  // Mathematical expression DSL usage
  var expression = add(
    multiply(num(2), num(3)),
    add(num(4), num(5)),
  );
  
  print('\\nMath DSL:');
  print('Expression: $expression');
  print('Result: ${expression.evaluate()}');
}
```

Domain-specific languages provide expressive, type-safe APIs for specific  
domains. They combine functional composition patterns with fluent interfaces  
to create intuitive and powerful abstractions for complex operations.  