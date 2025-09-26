# Dart List

In Dart, List is an ordered collection of elements. It maintains insertion order  
and allows duplicate values. Lists are similar to arrays in other languages.  

Lists in Dart can be either fixed-length or growable. Growable lists can change  
size dynamically. Lists are zero-indexed and support various operations.  

## Creating a List

There are several ways to create a List in Dart. The simplest is using list  
literals with square brackets.  

```dart
void main() {
  // Using list literal
  var fruits = ['apple', 'banana', 'orange'];
  print(fruits);

  // Using List constructor
  var numbers = List<int>.filled(3, 0);
  numbers[0] = 1;
  numbers[1] = 2;
  numbers[2] = 3;
  print(numbers);
}
```

The first example creates a growable list of strings. The second creates a  
fixed-length list initialized with zeros. We then assign values to each index.  

## Accessing List Elements

List elements can be accessed using index notation. Dart provides various methods  
for safe element access.  

```dart
void main() {
  var colors = ['red', 'green', 'blue', 'yellow'];

  // Access by index
  print(colors[0]); // red
  print(colors[3]); // yellow

  // Safe access with elementAt
  print(colors.elementAt(2)); // blue

  // First and last elements
  print('First: ${colors.first}');
  print('Last: ${colors.last}');

  // Length
  print('Length: ${colors.length}');
}
```

We demonstrate different ways to access list elements. elementAt is safer than  
[] as it throws RangeError for invalid indices. first and last are convenient.  

## Adding and Removing Elements

Growable lists support dynamic addition and removal of elements. Here are common  
operations.  

```dart
void main() {
  var numbers = [1, 2, 3];

  // Adding elements
  numbers.add(4);
  numbers.addAll([5, 6]);
  numbers.insert(0, 0);
  print('After adding: $numbers');

  // Removing elements
  numbers.remove(6);
  numbers.removeAt(0);
  numbers.removeLast();
  print('After removing: $numbers');

  // Clear all
  numbers.clear();
  print('After clear: $numbers');
}
```

add appends a single element, while addAll adds multiple. insert places an  
element at a specific position. Various remove methods delete elements.  

## List Iteration

Dart provides multiple ways to iterate through list elements. Here are the most  
common patterns.  

```dart
void main() {
  var languages = ['Dart', 'Python', 'Java', 'C#'];

  // for loop
  print('for loop:');
  for (var i = 0; i < languages.length; i++) {
    print(languages[i]);
  }

  // for-in loop
  print('\nfor-in loop:');
  for (var lang in languages) {
    print(lang);
  }

  // forEach
  print('\nforEach:');
  languages.forEach((lang) => print(lang));

  // map
  print('\nmap:');
  var upper = languages.map((lang) => lang.toUpperCase());
  print(upper);
}
```

The for loop provides index access. for-in is simpler for element access.  
forEach is functional style. map transforms elements to a new iterable.  

## List Manipulation

Dart lists support various manipulation operations like sorting, filtering, and  
slicing.  

```dart
void main() {
  var numbers = [5, 2, 8, 1, 7, 3, 9, 4, 6];

  // Sorting
  numbers.sort();
  print('Sorted: $numbers');

  // Reversing
  var reversed = numbers.reversed.toList();
  print('Reversed: $reversed');

  // Filtering
  var even = numbers.where((n) => n % 2 == 0).toList();
  print('Even numbers: $even');

  // Sublist
  var middle = numbers.sublist(2, 5);
  print('Middle sublist: $middle');

  // Shuffling
  numbers.shuffle();
  print('Shuffled: $numbers');
}
```

sort modifies the list in place. reversed returns an iterable that needs toList.  
where filters elements. sublist extracts a portion. shuffle randomizes order.  

## Spread Operator and Collection If/For

Dart supports spread operator (...) and collection if/for in list literals.  

```dart
void main() {
  var list1 = [1, 2, 3];
  var list2 = [4, 5, 6];

  // Spread operator
  var combined = [...list1, ...list2];
  print('Combined: $combined');

  // Collection if
  var includeZero = true;
  var numbers = [if (includeZero) 0, ...list1];
  print('With optional zero: $numbers');

  // Collection for
  var squares = [for (var n in list2) n * n];
  print('Squares: $squares');
}
```

The spread operator expands a list into individual elements. Collection if  
conditionally includes elements. Collection for transforms elements during  
list creation.  


## Best Practices

- **Type Safety:** Always specify list types for better code clarity.  
- **Growable Lists:** Prefer growable lists unless fixed size is needed.  
- **Immutable Lists:** Use List.unmodifiable for read-only lists.  
- **Performance:** Consider LinkedList for frequent insertions/deletions.  

## List Constructors and Factory Methods

Dart provides multiple constructors and factory methods for creating lists  
with specific initialization patterns and constraints.  

```dart
void main() {
  // Empty list with specified type
  List<String> emptyList = <String>[];
  print('Empty list: $emptyList');

  // Fixed-length list with default values
  var fixedList = List<int>.filled(5, 42);
  print('Fixed list: $fixedList');

  // Generate list with function
  var generated = List.generate(5, (index) => index * 2);
  print('Generated: $generated');

  // Unmodifiable list
  var readOnly = List.unmodifiable([1, 2, 3, 4]);
  print('Read-only: $readOnly');
}
```

Different constructors serve different purposes. filled creates lists with  
default values, generate uses a function for initialization, and unmodifiable  
creates immutable lists for safe data sharing.  

## List Equality and Comparison

Lists can be compared for equality and sorted using various comparison  
strategies. Understanding list equality helps with data validation.  

```dart
void main() {
  var list1 = [1, 2, 3];
  var list2 = [1, 2, 3];
  var list3 = [3, 2, 1];

  // Reference equality (different objects)
  print('Same reference: ${identical(list1, list2)}');
  
  // Element-wise equality using collections package would be needed
  // for deep equality, but we can implement basic comparison
  bool listsEqual(List a, List b) {
    if (a.length != b.length) return false;
    for (int i = 0; i < a.length; i++) {
      if (a[i] != b[i]) return false;
    }
    return true;
  }
  
  print('Content equal: ${listsEqual(list1, list2)}');
  print('Different order: ${listsEqual(list1, list3)}');
}
```

Lists are compared by reference by default. For content comparison, custom  
equality functions or external packages like collection are typically used  
for deep equality checks.  

## Advanced Filtering and Searching

Beyond basic where filtering, Dart lists support sophisticated search and  
filtering operations for complex data processing scenarios.  

```dart
void main() {
  var numbers = [1, 5, 10, 15, 20, 25, 30];

  // Multiple conditions
  var filtered = numbers.where((n) => n > 10 && n < 25).toList();
  print('Filtered (10 < n < 25): $filtered');

  // First matching element
  var firstLarge = numbers.firstWhere((n) => n > 15, orElse: () => -1);
  print('First > 15: $firstLarge');

  // Any/every checks
  bool hasLarge = numbers.any((n) => n > 20);
  bool allPositive = numbers.every((n) => n > 0);
  print('Has large: $hasLarge, All positive: $allPositive');

  // Index-based searching
  var indexOfTwenty = numbers.indexOf(20);
  print('Index of 20: $indexOfTwenty');
}
```

Advanced filtering combines multiple conditions, searches for specific elements,  
and validates list contents. These operations enable sophisticated data  
processing and validation logic.  

## List Transformation with fold and reduce

Fold and reduce operations aggregate list elements into single values using  
accumulator patterns, essential for mathematical and statistical operations.  

```dart
void main() {
  var numbers = [1, 2, 3, 4, 5];

  // Sum using fold
  var sum = numbers.fold<int>(0, (prev, element) => prev + element);
  print('Sum: $sum');

  // Product using reduce
  var product = numbers.reduce((value, element) => value * element);
  print('Product: $product');

  // String concatenation with fold
  var words = ['Dart', 'is', 'awesome'];
  var sentence = words.fold<String>('', (prev, word) => 
      prev.isEmpty ? word : '$prev $word');
  print('Sentence: $sentence');

  // Maximum using reduce
  var max = numbers.reduce((a, b) => a > b ? a : b);
  print('Maximum: $max');
}
```

fold provides an initial value and works with empty lists, while reduce uses  
the first element as initial value. Both enable powerful aggregation operations  
for statistical and computational tasks.  

## Nested Lists and Multidimensional Arrays

Dart supports nested lists for representing matrices, tables, and hierarchical  
data structures commonly used in algorithms and data processing.  

```dart
void main() {
  // 2D matrix (3x3)
  var matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
  ];

  // Access elements
  print('Element at [1][2]: ${matrix[1][2]}');

  // Iterate through 2D list
  for (int i = 0; i < matrix.length; i++) {
    for (int j = 0; j < matrix[i].length; j++) {
      print('matrix[$i][$j] = ${matrix[i][j]}');
    }
  }

  // Create dynamic 2D list
  var dynamicMatrix = List.generate(3, (i) => 
      List.generate(3, (j) => i * 3 + j + 1));
  print('Dynamic matrix: $dynamicMatrix');
}
```

Nested lists provide flexible multidimensional data structures. They're useful  
for matrices, game boards, and hierarchical data. Dynamic generation enables  
programmatic creation of complex structures.  

## List Expansion and Flattening

The expand method transforms lists of lists into flat structures, essential  
for data normalization and processing nested collections.  

```dart
void main() {
  var nestedLists = [[1, 2], [3, 4, 5], [6]];

  // Flatten nested lists
  var flattened = nestedLists.expand((list) => list).toList();
  print('Flattened: $flattened');

  // Expand with transformation
  var words = ['hello', 'world'];
  var characters = words.expand((word) => word.split('')).toList();
  print('Characters: $characters');

  // Expand numbers to ranges
  var ranges = [2, 3, 1];
  var expanded = ranges.expand((n) => 
      List.generate(n, (i) => i + 1)).toList();
  print('Expanded ranges: $expanded');
}
```

expand is powerful for flattening hierarchical data and transforming elements  
into multiple outputs. It's essential for data processing pipelines and  
functional programming patterns.  

## List Chunking and Partitioning

Dividing lists into smaller chunks is useful for batch processing, pagination,  
and memory management in large data operations.  

```dart
void main() {
  var numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
  
  // Chunk into groups of 3
  List<List<T>> chunk<T>(List<T> list, int size) {
    List<List<T>> chunks = [];
    for (int i = 0; i < list.length; i += size) {
      chunks.add(list.sublist(i, 
          i + size > list.length ? list.length : i + size));
    }
    return chunks;
  }

  var chunked = chunk(numbers, 3);
  print('Chunked: $chunked');

  // Partition by condition
  var evens = <int>[];
  var odds = <int>[];
  for (var num in numbers) {
    if (num % 2 == 0) {
      evens.add(num);
    } else {
      odds.add(num);
    }
  }
  print('Evens: $evens, Odds: $odds');
}
```

Chunking divides lists into equal-sized groups, while partitioning separates  
elements by conditions. Both techniques are essential for batch processing  
and organizing data efficiently.  

## Custom List Sorting

Beyond basic sorting, Dart supports custom comparison functions for complex  
sorting requirements including multiple criteria and object properties.  

```dart
void main() {
  var words = ['apple', 'Banana', 'cherry', 'Date'];

  // Case-insensitive sorting
  var sorted = [...words];
  sorted.sort((a, b) => a.toLowerCase().compareTo(b.toLowerCase()));
  print('Case-insensitive: $sorted');

  // Sort by length, then alphabetically
  var lengthSorted = [...words];
  lengthSorted.sort((a, b) {
    int lengthCompare = a.length.compareTo(b.length);
    return lengthCompare != 0 ? lengthCompare : a.compareTo(b);
  });
  print('By length: $lengthSorted');

  // Reverse sorting
  var reversed = [...words];
  reversed.sort((a, b) => b.compareTo(a));
  print('Reversed: $reversed');
}
```

Custom sorting enables complex ordering logic. Comparison functions return  
negative, zero, or positive values for less-than, equal, or greater-than  
relationships respectively.  

## List with Custom Objects

Lists commonly store custom objects requiring specialized handling for sorting,  
filtering, and display operations in real-world applications.  

```dart
class Person {
  final String name;
  final int age;
  final String city;

  Person(this.name, this.age, this.city);

  @override
  String toString() => '$name ($age) from $city';
}

void main() {
  var people = [
    Person('Alice', 30, 'New York'),
    Person('Bob', 25, 'London'),
    Person('Charlie', 35, 'Tokyo'),
  ];

  // Sort by age
  people.sort((a, b) => a.age.compareTo(b.age));
  print('By age: $people');

  // Filter by age
  var adults = people.where((p) => p.age >= 30).toList();
  print('Adults: $adults');

  // Group by city (simplified)
  var cities = people.map((p) => p.city).toSet().toList();
  print('Cities: $cities');
}
```

Custom objects in lists require careful consideration of comparison, equality,  
and string representation. Sorting and filtering by object properties enables  
sophisticated data management.  

## List Performance Considerations

Understanding performance characteristics helps choose appropriate operations  
for different scenarios and data sizes in performance-critical applications.  

```dart
void main() {
  // Efficient list building
  var efficientList = <int>[];
  for (int i = 0; i < 1000; i++) {
    efficientList.add(i);
  }
  print('Efficient list size: ${efficientList.length}');

  // Pre-sized list for known capacity
  var preSized = List<int>.filled(1000, 0);
  for (int i = 0; i < preSized.length; i++) {
    preSized[i] = i;
  }
  print('Pre-sized list size: ${preSized.length}');

  // Memory-efficient iteration
  int sum = 0;
  for (var value in efficientList) {
    sum += value;
    if (sum > 10000) break; // Early termination
  }
  print('Early termination sum: $sum');
}
```

List performance depends on operation types. Adding elements is O(1) amortized,  
while insertion at specific positions is O(n). Pre-sizing lists and early  
loop termination improve performance.  

## List Validation and Error Handling

Robust list operations require validation and error handling to prevent  
runtime exceptions and ensure data integrity in production applications.  

```dart
void main() {
  var numbers = <int>[1, 2, 3, 4, 5];

  // Safe element access
  int? safeGet(List<int> list, int index) {
    return index >= 0 && index < list.length ? list[index] : null;
  }

  print('Safe access [2]: ${safeGet(numbers, 2)}');
  print('Safe access [10]: ${safeGet(numbers, 10)}');

  // Validation before operations
  void safeAdd(List<int> list, int? value) {
    if (value != null && value >= 0) {
      list.add(value);
      print('Added: $value');
    } else {
      print('Invalid value rejected: $value');
    }
  }

  safeAdd(numbers, 6);
  safeAdd(numbers, null);
  safeAdd(numbers, -1);
}
```

Safe list operations prevent common runtime errors through validation and  
null checks. This defensive programming approach ensures application stability  
and better user experience.  

## List Serialization and JSON

Converting lists to and from JSON is essential for data persistence, API  
communication, and configuration management in modern applications.  

```dart
import 'dart:convert';

void main() {
  var numbers = [1, 2, 3, 4, 5];
  var fruits = ['apple', 'banana', 'cherry'];

  // List to JSON
  String numbersJson = jsonEncode(numbers);
  String fruitsJson = jsonEncode(fruits);
  print('Numbers JSON: $numbersJson');
  print('Fruits JSON: $fruitsJson');

  // JSON to List
  List<dynamic> decodedNumbers = jsonDecode(numbersJson);
  List<String> decodedFruits = List<String>.from(jsonDecode(fruitsJson));
  print('Decoded numbers: $decodedNumbers');
  print('Decoded fruits: $decodedFruits');

  // Complex object serialization
  var data = {
    'items': [1, 2, 3],
    'metadata': {'count': 3, 'type': 'numbers'}
  };
  String complexJson = jsonEncode(data);
  print('Complex JSON: $complexJson');
}
```

JSON serialization enables data exchange and persistence. Type safety requires  
explicit casting when deserializing to maintain compile-time type checking  
and prevent runtime errors.  

## List Caching and Memoization

Caching list operations improves performance for expensive computations and  
repeated operations, essential for optimization in data-intensive applications.  

```dart
void main() {
  var _cache = <String, List<int>>{};

  List<int> expensiveOperation(String key) {
    if (_cache.containsKey(key)) {
      print('Cache hit for: $key');
      return _cache[key]!;
    }

    print('Computing for: $key');
    // Simulate expensive computation
    var result = List.generate(100, (i) => i * i);
    _cache[key] = result;
    return result;
  }

  // First call - computes and caches
  var result1 = expensiveOperation('squares');
  print('First result length: ${result1.length}');

  // Second call - uses cache
  var result2 = expensiveOperation('squares');
  print('Second result length: ${result2.length}');

  // Cache statistics
  print('Cache size: ${_cache.length}');
}
```

Memoization caches computation results to avoid redundant processing. This  
optimization technique significantly improves performance for repeated  
operations on the same data sets.  

## List Builder Pattern

The builder pattern provides a fluent interface for constructing complex lists  
with validation and configuration, useful in API design and DSL creation.  

```dart
class ListBuilder<T> {
  final List<T> _items = [];

  ListBuilder<T> add(T item) {
    _items.add(item);
    return this;
  }

  ListBuilder<T> addIf(bool condition, T item) {
    if (condition) _items.add(item);
    return this;
  }

  ListBuilder<T> addAll(Iterable<T> items) {
    _items.addAll(items);
    return this;
  }

  List<T> build() => List.unmodifiable(_items);
}

void main() {
  var isIncludeOptional = true;
  
  var numbers = ListBuilder<int>()
      .add(1)
      .add(2)
      .addIf(isIncludeOptional, 3)
      .addAll([4, 5])
      .build();

  print('Built list: $numbers');
}
```

The builder pattern enables fluent, readable list construction with conditional  
logic and validation. It's particularly useful for complex initialization  
scenarios and API design.  

## List Synchronization and Thread Safety

Understanding list behavior in concurrent scenarios helps avoid race conditions  
and data corruption in multi-threaded applications.  

```dart
import 'dart:async';

void main() async {
  var sharedList = <int>[];
  
  // Simulate concurrent access
  var futures = <Future>[];
  
  for (int i = 0; i < 5; i++) {
    futures.add(Future(() async {
      await Future.delayed(Duration(milliseconds: 10));
      sharedList.add(i);
      print('Added: $i, Current list: $sharedList');
    }));
  }

  await Future.wait(futures);
  print('Final list: $sharedList');
  print('Final length: ${sharedList.length}');

  // For thread safety, consider using synchronization mechanisms
  // or immutable data structures in production code
}
```

Dart lists are not thread-safe by default. Concurrent modifications can lead  
to inconsistent state. Use synchronization mechanisms or immutable structures  
for thread-safe operations.  

## List Memory Management

Efficient memory usage prevents memory leaks and improves application  
performance, especially important for large datasets and long-running  
applications.  

```dart
void main() {
  // Large list creation
  var largeList = List.generate(100000, (i) => 'Item $i');
  print('Created large list: ${largeList.length} items');

  // Processing with memory awareness
  var processedCount = 0;
  const batchSize = 1000;
  
  for (int i = 0; i < largeList.length; i += batchSize) {
    var batch = largeList.sublist(i, 
        i + batchSize > largeList.length ? largeList.length : i + batchSize);
    
    // Process batch
    processedCount += batch.length;
    
    // Clear reference to help GC
    batch.clear();
  }

  print('Processed items: $processedCount');

  // Explicit cleanup
  largeList.clear();
  print('List cleared: ${largeList.length}');
}
```

Memory-conscious programming involves batch processing, explicit cleanup, and  
avoiding memory leaks. Regular clearing of unused references helps garbage  
collection and maintains application performance.  

## List Pattern Matching

Dart's pattern matching provides powerful ways to destructure and analyze  
lists, enabling more expressive and safer code for data processing.  

```dart
void main() {
  var numbers = [1, 2, 3, 4, 5];

  // Simple pattern matching with switch
  String analyzeList(List<int> list) {
    return switch (list) {
      [] => 'Empty list',
      [var single] => 'Single element: $single',
      [var first, var second] => 'Two elements: $first, $second',
      [var first, ...var rest] => 'First: $first, Rest: $rest',
    };
  }

  print(analyzeList([]));
  print(analyzeList([42]));
  print(analyzeList([1, 2]));
  print(analyzeList([1, 2, 3, 4]));

  // Pattern matching with if-case
  if (numbers case [var first, ...var rest] when first > 0) {
    print('Positive first element: $first, remaining: ${rest.length}');
  }
}
```

Pattern matching simplifies list analysis and destructuring. It provides  
type-safe ways to extract elements and handle different list structures  
with concise, readable code.  

## List Extension Methods

Extension methods add custom functionality to existing list types, providing  
domain-specific operations without modifying the original List class.  

```dart
extension ListExtensions<T> on List<T> {
  T? get safeFirst => isEmpty ? null : first;
  T? get safeLast => isEmpty ? null : last;
  
  List<T> get shuffledCopy {
    var copy = [...this];
    copy.shuffle();
    return copy;
  }
  
  List<List<T>> chunked(int size) {
    List<List<T>> chunks = [];
    for (int i = 0; i < length; i += size) {
      chunks.add(sublist(i, i + size > length ? length : i + size));
    }
    return chunks;
  }
}

void main() {
  var numbers = [1, 2, 3, 4, 5];
  var emptyList = <int>[];

  print('First: ${numbers.safeFirst}');
  print('Empty first: ${emptyList.safeFirst}');
  print('Shuffled: ${numbers.shuffledCopy}');
  print('Chunked: ${numbers.chunked(2)}');
}
```

Extension methods provide clean APIs for common operations while maintaining  
type safety and method chaining. They enable domain-specific list operations  
without inheritance or composition.  

## List Functional Programming

Functional programming patterns with lists enable declarative, composable  
data transformations that are easier to test and reason about.  

```dart
void main() {
  var numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

  // Chain operations functionally
  var result = numbers
      .where((n) => n > 3)
      .map((n) => n * 2)
      .where((n) => n < 15)
      .toList();
  print('Chained result: $result');

  // Compose functions
  List<T> pipe<T>(List<T> list, List<List<T> Function(List<T>)> operations) {
    return operations.fold(list, (current, operation) => operation(current));
  }

  var composed = pipe(numbers, [
    (list) => list.where((n) => n % 2 == 0).toList(),
    (list) => list.map((n) => n * 3).toList(),
    (list) => list.take(3).toList(),
  ]);
  print('Composed result: $composed');
}
```

Functional programming promotes immutability and composability. Method chaining  
and function composition create readable data transformation pipelines that  
are easier to test and maintain.  

## List Benchmarking and Profiling

Performance measurement helps identify bottlenecks and optimize list operations  
for better application performance and user experience.  

```dart
void main() {
  const int size = 100000;
  
  // Benchmark list creation
  var stopwatch = Stopwatch()..start();
  var list1 = List.generate(size, (i) => i);
  stopwatch.stop();
  print('Generate creation: ${stopwatch.elapsedMicroseconds}μs');

  // Benchmark list copying
  stopwatch.reset();
  stopwatch.start();
  var list2 = [...list1];
  stopwatch.stop();
  print('Spread copy: ${stopwatch.elapsedMicroseconds}μs');

  // Benchmark filtering
  stopwatch.reset();
  stopwatch.start();
  var filtered = list1.where((n) => n % 2 == 0).toList();
  stopwatch.stop();
  print('Filtering: ${stopwatch.elapsedMicroseconds}μs');
  print('Filtered size: ${filtered.length}');

  // Memory usage approximation
  print('Approximate memory per int: ${(list1.length * 8)} bytes');
}
```

Benchmarking reveals performance characteristics of different operations.  
Measuring creation, copying, and transformation times helps optimize  
performance-critical code paths.  

## List State Management

Managing list state changes is crucial for reactive programming and UI  
development, enabling predictable updates and data flow control.  

```dart
class ListState<T> {
  List<T> _items = [];
  List<void Function(List<T>)> _listeners = [];

  List<T> get items => List.unmodifiable(_items);

  void addListener(void Function(List<T>) listener) {
    _listeners.add(listener);
  }

  void add(T item) {
    _items.add(item);
    _notifyListeners();
  }

  void remove(T item) {
    _items.remove(item);
    _notifyListeners();
  }

  void _notifyListeners() {
    for (var listener in _listeners) {
      listener(_items);
    }
  }
}

void main() {
  var state = ListState<String>();
  
  state.addListener((items) {
    print('State changed: $items');
  });

  state.add('First item');
  state.add('Second item');
  state.remove('First item');
}
```

State management patterns provide controlled access to list modifications  
with notification systems. This approach enables reactive programming and  
maintains data consistency across application components.  
