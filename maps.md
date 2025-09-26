# Dart Maps

Maps in Dart are unordered collections of key-value pairs, providing efficient  
lookup, insertion, and deletion operations. They maintain unique keys while  
allowing duplicate values, making them ideal for associative data storage.  

Maps support dynamic sizing and offer both generic type safety and flexible  
key-value relationships. They serve as fundamental data structures for  
configuration data, caching mechanisms, and object-oriented programming  
patterns.  

## Creating Maps

Maps can be created using literal syntax with curly braces or constructor  
methods. The literal approach provides the most concise syntax for direct  
initialization.  

```dart
void main() {
  // Map literal with type inference
  var userAges = {'Alice': 25, 'Bob': 30, 'Carol': 28};
  print('User ages: $userAges');
  
  // Explicit type annotation
  Map<String, int> scores = {'Math': 95, 'Science': 88, 'History': 92};
  print('Scores: $scores');
  
  // Empty map with type specification
  Map<String, double> prices = <String, double>{};
  prices['Coffee'] = 4.50;
  prices['Tea'] = 3.25;
  print('Prices: $prices');
}
```

This example demonstrates three approaches to map creation: type inference,  
explicit typing, and empty initialization. The literal syntax using curly  
braces provides the cleanest approach for direct value assignment.  

## Accessing Map Values

Map values are accessed using square bracket notation with keys. Dart provides  
several methods for safe value retrieval and existence checking.  

```dart
void main() {
  var capitals = {
    'France': 'Paris',
    'Germany': 'Berlin',
    'Italy': 'Rome',
    'Spain': 'Madrid'
  };
  
  // Direct access
  print('Capital of France: ${capitals['France']}');
  print('Capital of Japan: ${capitals['Japan']}'); // null
  
  // Safe access with null-aware operator
  print('Capital exists: ${capitals['Germany'] ?? 'Unknown'}');
  
  // Check key existence
  if (capitals.containsKey('Italy')) {
    print('Italy capital: ${capitals['Italy']}');
  }
  
  // Get with default value
  String ukCapital = capitals.putIfAbsent('UK', () => 'London');
  print('UK capital: $ukCapital');
  print('Updated map: $capitals');
}
```

Map access returns null for non-existent keys, enabling safe programming  
patterns with null-aware operators. The `containsKey` method provides explicit  
existence checking, while `putIfAbsent` offers conditional insertion.  

## Map Properties and Methods

Maps provide various properties and methods for inspection and manipulation.  
These enable dynamic analysis of map contents and structure.  

```dart
void main() {
  var inventory = {
    'Laptops': 15,
    'Mice': 45,
    'Keyboards': 23,
    'Monitors': 8
  };
  
  // Basic properties
  print('Map length: ${inventory.length}');
  print('Is empty: ${inventory.isEmpty}');
  print('Is not empty: ${inventory.isNotEmpty}');
  
  // Keys and values
  print('All keys: ${inventory.keys.toList()}');
  print('All values: ${inventory.values.toList()}');
  
  // Entries (key-value pairs)
  print('Entries:');
  for (var entry in inventory.entries) {
    print('  ${entry.key}: ${entry.value} units');
  }
  
  // Total inventory count
  var totalItems = inventory.values.reduce((a, b) => a + b);
  print('Total items: $totalItems');
}
```

Map properties provide essential information about size and content structure.  
The `entries` property returns key-value pairs as MapEntry objects, enabling  
comprehensive iteration over both keys and values simultaneously.  

## Adding and Updating Values

Maps support dynamic modification through assignment, specialized methods, and  
bulk operations. These operations maintain key uniqueness while allowing  
value updates.  

```dart
void main() {
  var productCatalog = <String, double>{};
  
  // Adding new entries
  productCatalog['Notebook'] = 12.99;
  productCatalog['Pen'] = 2.50;
  productCatalog['Marker'] = 4.75;
  print('Initial catalog: $productCatalog');
  
  // Updating existing entries
  productCatalog['Pen'] = 3.00; // Price increase
  print('After price update: $productCatalog');
  
  // Bulk addition using addAll
  var newProducts = {
    'Eraser': 1.25,
    'Ruler': 3.50,
    'Calculator': 18.99
  };
  productCatalog.addAll(newProducts);
  print('After bulk addition: $productCatalog');
  
  // Conditional addition
  productCatalog.putIfAbsent('Stapler', () => 15.75);
  productCatalog.putIfAbsent('Pen', () => 99.99); // Won't update
  print('Final catalog: $productCatalog');
}
```

Map modification supports both individual and bulk operations. Assignment  
operator provides simple updates, while `addAll` enables efficient bulk  
insertions. The `putIfAbsent` method prevents accidental overwrites.  

## Removing Map Entries

Maps provide several methods for removing entries, supporting both targeted  
deletion and conditional removal based on criteria.  

```dart
void main() {
  var bookRatings = {
    'The Great Gatsby': 4.2,
    'To Kill a Mockingbird': 4.8,
    'Pride and Prejudice': 4.5,
    'The Catcher in the Rye': 3.1,
    'Lord of the Flies': 3.9
  };
  
  print('Original ratings: $bookRatings');
  
  // Remove specific key
  var removed = bookRatings.remove('The Catcher in the Rye');
  print('Removed rating: $removed');
  
  // Remove with condition
  bookRatings.removeWhere((title, rating) => rating < 4.0);
  print('After removing low ratings: $bookRatings');
  
  // Clear all entries
  var tempMap = Map.from(bookRatings);
  tempMap.clear();
  print('After clearing: $tempMap');
  print('Original preserved: $bookRatings');
}
```

The `remove` method returns the deleted value or null if the key doesn't  
exist. `removeWhere` enables conditional deletion based on key-value  
predicates, while `clear` removes all entries efficiently.  

## Map Iteration Patterns

Maps support multiple iteration patterns for different access requirements.  
Each pattern optimizes for specific use cases and data access needs.  

```dart
void main() {
  var weatherData = {
    'Monday': 22.5,
    'Tuesday': 24.1,
    'Wednesday': 19.8,
    'Thursday': 26.3,
    'Friday': 23.7
  };
  
  // Iterate over keys
  print('Days of the week:');
  for (var day in weatherData.keys) {
    print('  $day');
  }
  
  // Iterate over values
  print('Temperatures:');
  for (var temp in weatherData.values) {
    print('  ${temp}°C');
  }
  
  // Iterate over entries
  print('Weather forecast:');
  for (var entry in weatherData.entries) {
    print('  ${entry.key}: ${entry.value}°C');
  }
  
  // Using forEach method
  print('Detailed forecast:');
  weatherData.forEach((day, temperature) {
    var description = temperature > 25 ? 'Hot' : 
                     temperature > 20 ? 'Warm' : 'Cool';
    print('  $day: ${temperature}°C ($description)');
  });
}
```

Different iteration patterns serve distinct purposes: keys-only for validation,  
values-only for calculations, and entries for complete data processing. The  
`forEach` method provides functional-style iteration with inline processing.  

## Map Transformation with map()

The `map` method transforms map entries into new collections, enabling  
functional data processing while preserving type safety and immutability  
principles.  

```dart
void main() {
  var employeeSalaries = {
    'Alice': 75000,
    'Bob': 82000,
    'Carol': 68000,
    'David': 91000
  };
  
  // Transform to new map with percentage increase
  var raisedSalaries = employeeSalaries.map(
    (name, salary) => MapEntry(name, salary * 1.05)
  );
  print('After 5% raise: $raisedSalaries');
  
  // Transform to list of formatted strings
  var salaryReports = employeeSalaries.entries.map(
    (entry) => '${entry.key}: \$${entry.value.toStringAsFixed(0)}'
  ).toList();
  print('Salary reports:');
  salaryReports.forEach(print);
  
  // Transform keys to uppercase
  var upperCaseMap = employeeSalaries.map(
    (name, salary) => MapEntry(name.toUpperCase(), salary)
  );
  print('Uppercase names: $upperCaseMap');
  
  // Calculate total and average
  var totalSalary = employeeSalaries.values.reduce((a, b) => a + b);
  var averageSalary = totalSalary / employeeSalaries.length;
  print('Total: \$${totalSalary}, Average: \$${averageSalary.toStringAsFixed(0)}');
}
```

The `map` method creates new Map instances with transformed data, maintaining  
immutability. Converting entries to lists enables additional functional  
operations like filtering and sorting while preserving original data.  

## Filtering Maps

Map filtering creates subsets based on key-value criteria, enabling data  
analysis and conditional processing without modifying original collections.  

```dart
void main() {
  var studentGrades = {
    'Alice': 95,
    'Bob': 67,
    'Carol': 89,
    'David': 72,
    'Emma': 91,
    'Frank': 58,
    'Grace': 84
  };
  
  // Filter high performers (grade >= 85)
  var highPerformers = <String, int>{};
  studentGrades.forEach((name, grade) {
    if (grade >= 85) {
      highPerformers[name] = grade;
    }
  });
  print('High performers: $highPerformers');
  
  // Filter using where on entries
  var passingGrades = Map.fromEntries(
    studentGrades.entries.where((entry) => entry.value >= 70)
  );
  print('Passing grades: $passingGrades');
  
  // Filter by name length
  var shortNames = Map.fromEntries(
    studentGrades.entries.where((entry) => entry.key.length <= 5)
  );
  print('Short names: $shortNames');
  
  // Multiple criteria filtering
  var excellentShortNames = Map.fromEntries(
    studentGrades.entries.where((entry) => 
      entry.key.length <= 5 && entry.value >= 90
    )
  );
  print('Excellent students with short names: $excellentShortNames');
}
```

Filtering combines conditional logic with map reconstruction. The  
`Map.fromEntries` constructor enables clean filtering workflows by processing  
entry sequences and rebuilding filtered maps efficiently.  

## Map with Custom Objects

Maps can store complex objects as values, enabling sophisticated data  
structures for real-world applications and object-oriented design patterns.  

```dart
class Product {
  final String name;
  final double price;
  final int stockQuantity;
  
  Product(this.name, this.price, this.stockQuantity);
  
  @override
  String toString() => 'Product($name, \$${price.toStringAsFixed(2)}, $stockQuantity units)';
}

void main() {
  var inventory = <String, Product>{
    'laptop001': Product('Gaming Laptop', 1299.99, 5),
    'mouse002': Product('Wireless Mouse', 49.99, 23),
    'keyboard003': Product('Mechanical Keyboard', 159.99, 12),
    'monitor004': Product('4K Monitor', 399.99, 8)
  };
  
  // Access product information
  var laptop = inventory['laptop001'];
  print('Laptop details: $laptop');
  
  // Find products by criteria
  var expensiveProducts = <String, Product>{};
  inventory.forEach((sku, product) {
    if (product.price > 200) {
      expensiveProducts[sku] = product;
    }
  });
  print('Expensive products: $expensiveProducts');
  
  // Calculate total inventory value
  var totalValue = inventory.values
      .map((product) => product.price * product.stockQuantity)
      .reduce((a, b) => a + b);
  print('Total inventory value: \$${totalValue.toStringAsFixed(2)}');
  
  // Low stock alert
  print('Low stock items:');
  inventory.forEach((sku, product) {
    if (product.stockQuantity < 10) {
      print('  $sku: ${product.name} (${product.stockQuantity} left)');
    }
  });
}
```

Custom objects in maps enable rich data modeling with business logic  
encapsulation. Object properties support complex filtering and calculations  
while maintaining type safety and clean separation of concerns.  

## Nested Maps and Complex Structures

Maps can contain other maps, creating hierarchical data structures suitable  
for configuration management, data organization, and tree-like representations.  

```dart
void main() {
  var companyData = <String, Map<String, dynamic>>{
    'Engineering': {
      'manager': 'Alice Johnson',
      'employees': 12,
      'budget': 850000,
      'projects': ['Web App', 'Mobile App', 'API Platform']
    },
    'Marketing': {
      'manager': 'Bob Wilson',
      'employees': 8,
      'budget': 420000,
      'projects': ['Social Campaign', 'Product Launch']
    },
    'Sales': {
      'manager': 'Carol Davis',
      'employees': 15,
      'budget': 320000,
      'projects': ['Client Outreach', 'Partnership Development']
    }
  };
  
  // Access nested data
  var engineeringManager = companyData['Engineering']?['manager'];
  print('Engineering Manager: $engineeringManager');
  
  // Process all departments
  companyData.forEach((department, data) {
    print('$department Department:');
    print('  Manager: ${data['manager']}');
    print('  Team size: ${data['employees']} people');
    print('  Budget: \$${data['budget']}');
    print('  Active projects: ${data['projects'].length}');
  });
  
  // Calculate company metrics
  var totalEmployees = companyData.values
      .map((dept) => dept['employees'] as int)
      .reduce((a, b) => a + b);
  var totalBudget = companyData.values
      .map((dept) => dept['budget'] as int)
      .reduce((a, b) => a + b);
  
  print('Company totals: $totalEmployees employees, \$${totalBudget} budget');
}
```

Nested maps provide flexible hierarchical data representation. The `dynamic`  
type enables mixed value types while requiring careful type casting for  
operations. This pattern suits configuration data and structured information.  

## Map Type Conversion and Casting

Maps support various type conversion operations for data interchange,  
serialization, and integration with different systems and APIs.  

```dart
void main() {
  // Original typed map
  Map<String, int> scores = {'Alice': 95, 'Bob': 87, 'Carol': 92};
  
  // Convert to Map<String, dynamic>
  Map<String, dynamic> dynamicScores = scores.cast<String, dynamic>();
  dynamicScores['David'] = 'Incomplete'; // Mixed types now possible
  print('Dynamic scores: $dynamicScores');
  
  // Convert back with validation
  Map<String, int> validScores = <String, int>{};
  dynamicScores.forEach((name, score) {
    if (score is int) {
      validScores[name] = score;
    } else {
      print('Skipping $name: invalid score type');
    }
  });
  print('Valid scores: $validScores');
  
  // Map to List conversion
  List<String> scoresList = scores.entries
      .map((entry) => '${entry.key}: ${entry.value}')
      .toList();
  print('Scores as list: $scoresList');
  
  // List to Map conversion
  List<String> names = ['Emma', 'Frank', 'Grace'];
  List<int> grades = [88, 91, 85];
  Map<String, int> gradesMap = Map.fromIterables(names, grades);
  print('Grades map: $gradesMap');
  
  // JSON-style string representation
  var jsonString = scores.entries
      .map((e) => '"${e.key}": ${e.value}')
      .join(', ');
  print('JSON-like: {$jsonString}');
}
```

Type conversion enables integration between different data representations.  
The `cast` method provides type adaptation, while constructor methods support  
creation from various data sources including lists and iterables.  

## Map Comparison and Equality

Map equality depends on content matching rather than reference identity,  
enabling value-based comparisons for testing and data validation scenarios.  

```dart
void main() {
  var map1 = {'a': 1, 'b': 2, 'c': 3};
  var map2 = {'a': 1, 'b': 2, 'c': 3};
  var map3 = {'c': 3, 'b': 2, 'a': 1}; // Different order
  var map4 = {'a': 1, 'b': 2, 'c': 4}; // Different value
  
  // Content equality (order independent)
  print('map1 == map2: ${map1 == map2}'); // true
  print('map1 == map3: ${map1 == map3}'); // true (order doesn't matter)
  print('map1 == map4: ${map1 == map4}'); // false
  
  // Manual comparison for specific criteria
  bool haveSameKeys(Map<String, int> a, Map<String, int> b) {
    return a.keys.toSet().containsAll(b.keys) && 
           b.keys.toSet().containsAll(a.keys);
  }
  
  print('Same keys (map1, map4): ${haveSameKeys(map1, map4)}');
  
  // Value comparison
  bool allValuesMatch(Map<String, int> a, Map<String, int> b, 
                     bool Function(int, int) condition) {
    if (a.length != b.length) return false;
    
    for (var key in a.keys) {
      if (!b.containsKey(key)) return false;
      if (!condition(a[key]!, b[key]!)) return false;
    }
    return true;
  }
  
  var map5 = {'a': 2, 'b': 3, 'c': 4}; // Each value +1
  print('All values differ by 1: ${allValuesMatch(map1, map5, (a, b) => b == a + 1)}');
}
```

Map equality enables reliable content comparison for testing and validation.  
Custom comparison functions provide flexible criteria for specialized  
equality checks beyond standard content matching.  

## Map with Enums as Keys

Enums as map keys provide type-safe, self-documenting code structure with  
compile-time validation and enhanced readability for categorical data.  

```dart
enum Priority { low, medium, high, critical }
enum TaskStatus { pending, inProgress, completed, cancelled }

class Task {
  final String title;
  final Priority priority;
  final TaskStatus status;
  
  Task(this.title, this.priority, this.status);
  
  @override
  String toString() => 'Task($title, $priority, $status)';
}

void main() {
  // Map with enum keys for priority-based organization
  var tasksByPriority = <Priority, List<Task>>{
    Priority.critical: [
      Task('Fix security vulnerability', Priority.critical, TaskStatus.inProgress),
      Task('Database backup failure', Priority.critical, TaskStatus.pending)
    ],
    Priority.high: [
      Task('Implement new feature', Priority.high, TaskStatus.inProgress),
      Task('Code review', Priority.high, TaskStatus.pending)
    ],
    Priority.medium: [
      Task('Update documentation', Priority.medium, TaskStatus.completed),
      Task('Refactor legacy code', Priority.medium, TaskStatus.inProgress)
    ],
    Priority.low: [
      Task('Organize team lunch', Priority.low, TaskStatus.pending)
    ]
  };
  
  // Process tasks by priority
  for (var priority in Priority.values) {
    var tasks = tasksByPriority[priority] ?? [];
    print('${priority.name.toUpperCase()} Priority Tasks: ${tasks.length}');
    for (var task in tasks) {
      print('  - ${task.title} [${task.status.name}]');
    }
  }
  
  // Status distribution
  var statusCounts = <TaskStatus, int>{};
  for (var tasks in tasksByPriority.values) {
    for (var task in tasks) {
      statusCounts[task.status] = (statusCounts[task.status] ?? 0) + 1;
    }
  }
  
  print('Status distribution:');
  statusCounts.forEach((status, count) {
    print('  ${status.name}: $count tasks');
  });
}
```

Enum keys eliminate string-based errors and provide IDE autocompletion.  
This pattern enhances code maintainability and reduces runtime errors in  
categorical data organization and state management scenarios.  

## Map Performance and Memory Considerations

Understanding map performance characteristics helps optimize applications  
for memory usage and execution speed in performance-critical scenarios.  

```dart
void main() {
  const int testSize = 100000;
  
  // Benchmark map creation
  var stopwatch = Stopwatch()..start();
  var largeMap = <int, String>{};
  for (int i = 0; i < testSize; i++) {
    largeMap[i] = 'Value $i';
  }
  stopwatch.stop();
  print('Map creation: ${stopwatch.elapsedMilliseconds}ms');
  
  // Benchmark lookups
  stopwatch.reset();
  stopwatch.start();
  for (int i = 0; i < 1000; i++) {
    var _ = largeMap[i * 50]; // Random access pattern
  }
  stopwatch.stop();
  print('1000 lookups: ${stopwatch.elapsedMicroseconds}μs');
  
  // Memory-efficient iteration
  stopwatch.reset();
  stopwatch.start();
  var sum = 0;
  for (var key in largeMap.keys) {
    sum += key;
    if (sum > 1000000) break; // Early termination
  }
  stopwatch.stop();
  print('Partial iteration: ${stopwatch.elapsedMicroseconds}μs, sum: $sum');
  
  // Compare with List performance
  var largeList = List.generate(testSize, (i) => 'Value $i');
  stopwatch.reset();
  stopwatch.start();
  for (int i = 0; i < 1000; i++) {
    var _ = largeList[i * 50]; // Direct index access
  }
  stopwatch.stop();
  print('List 1000 accesses: ${stopwatch.elapsedMicroseconds}μs');
  
  print('Map size: ${largeMap.length} entries');
}
```

Map operations are generally O(1) for access and insertion, making them  
efficient for lookup-heavy operations. However, they use more memory than  
lists due to hash table overhead and key storage requirements.  

## Map Functional Operations

Maps integrate with functional programming patterns, supporting method  
chaining and transformation pipelines for clean, declarative data processing.  

```dart
void main() {
  var salesData = {
    'January': 45000,
    'February': 52000,
    'March': 48000,
    'April': 61000,
    'May': 55000,
    'June': 58000
  };
  
  // Functional transformation pipeline
  var quarterlyAnalysis = salesData.entries
      .where((entry) => ['January', 'February', 'March'].contains(entry.key))
      .map((entry) => MapEntry(entry.key, entry.value * 1.1)) // 10% projection
      .toList()
      ..sort((a, b) => b.value.compareTo(a.value)); // Sort by value descending
  
  print('Q1 Analysis (10% projected growth):');
  for (var entry in quarterlyAnalysis) {
    print('  ${entry.key}: \$${entry.value.toStringAsFixed(0)}');
  }
  
  // Functional aggregation
  var totalSales = salesData.values.reduce((a, b) => a + b);
  var averageSales = salesData.values.reduce((a, b) => a + b) / salesData.length;
  var bestMonth = salesData.entries
      .reduce((a, b) => a.value > b.value ? a : b);
  var worstMonth = salesData.entries
      .reduce((a, b) => a.value < b.value ? a : b);
  
  print('Sales Summary:');
  print('  Total: \$${totalSales.toStringAsFixed(0)}');
  print('  Average: \$${averageSales.toStringAsFixed(0)}');
  print('  Best month: ${bestMonth.key} (\$${bestMonth.value})');
  print('  Worst month: ${worstMonth.key} (\$${worstMonth.value})');
  
  // Growth analysis
  var monthlyGrowth = <String, double>{};
  var months = salesData.keys.toList();
  for (int i = 1; i < months.length; i++) {
    var currentMonth = months[i];
    var previousMonth = months[i - 1];
    var growth = ((salesData[currentMonth]! - salesData[previousMonth]!) / 
                  salesData[previousMonth]!) * 100;
    monthlyGrowth[currentMonth] = growth;
  }
  
  print('Monthly growth rates:');
  monthlyGrowth.forEach((month, growth) {
    print('  $month: ${growth.toStringAsFixed(1)}%');
  });
}
```

Functional operations enable declarative data processing with method chaining.  
This approach promotes immutability and creates readable transformation  
pipelines for complex data analysis and reporting scenarios.  

## Map Extension Methods

Extension methods enhance map functionality with domain-specific operations  
while maintaining clean APIs and type safety for specialized use cases.  

```dart
extension MapExtensions<K, V> on Map<K, V> {
  // Safe get with default value
  V getOrDefault(K key, V defaultValue) => this[key] ?? defaultValue;
  
  // Update multiple keys
  void updateAll(Map<K, V> updates) {
    updates.forEach((key, value) => this[key] = value);
  }
  
  // Invert key-value pairs (assuming V is valid key type)
  Map<V, K> get inverted => Map.fromEntries(
    entries.map((entry) => MapEntry(entry.value, entry.key))
  );
}

extension NumericMapExtensions<K> on Map<K, num> {
  // Calculate statistics
  num get sum => values.reduce((a, b) => a + b);
  double get average => sum / length;
  num get max => values.reduce((a, b) => a > b ? a : b);
  num get min => values.reduce((a, b) => a < b ? a : b);
  
  // Scale all values
  Map<K, num> scale(num factor) => map((key, value) => 
    MapEntry(key, value * factor));
}

void main() {
  var config = <String, String>{
    'host': 'localhost',
    'port': '8080',
    'protocol': 'https'
  };
  
  // Use extension methods
  print('Database: ${config.getOrDefault('database', 'default_db')}');
  
  config.updateAll({
    'database': 'production_db',
    'timeout': '30000'
  });
  print('Updated config: $config');
  
  // Invert configuration for reverse lookup
  var invertedConfig = config.inverted;
  print('Inverted config: $invertedConfig');
  
  // Numeric operations
  var metrics = <String, num>{
    'cpu_usage': 75.5,
    'memory_usage': 82.3,
    'disk_usage': 45.1,
    'network_io': 23.8
  };
  
  print('Metrics summary:');
  print('  Average: ${metrics.average.toStringAsFixed(2)}%');
  print('  Maximum: ${metrics.max}%');
  print('  Minimum: ${metrics.min}%');
  print('  Total: ${metrics.sum}');
  
  // Scale metrics to 0-1 range
  var normalizedMetrics = metrics.scale(0.01);
  print('Normalized metrics: $normalizedMetrics');
}
```

Extension methods provide clean, reusable functionality without inheritance.  
They enable domain-specific operations while maintaining type safety and  
method chaining capabilities for enhanced developer experience.  

## Map Serialization and JSON

Maps integrate naturally with JSON serialization for data persistence,  
API communication, and configuration management in modern applications.  

```dart
import 'dart:convert';

void main() {
  // Complex data structure
  var userProfile = <String, dynamic>{
    'id': 12345,
    'username': 'alice_dev',
    'profile': {
      'firstName': 'Alice',
      'lastName': 'Johnson',
      'email': 'alice@example.com',
      'age': 28
    },
    'preferences': {
      'theme': 'dark',
      'language': 'en',
      'notifications': true
    },
    'tags': ['developer', 'dart', 'flutter'],
    'loginCount': 127,
    'isActive': true
  };
  
  // Serialize to JSON string
  String jsonString = jsonEncode(userProfile);
  print('JSON serialization:');
  print(jsonString);
  
  // Deserialize from JSON
  Map<String, dynamic> deserializedProfile = jsonDecode(jsonString);
  print('Deserialized successfully: ${deserializedProfile['username']}');
  
  // Access nested data
  var firstName = deserializedProfile['profile']['firstName'];
  var theme = deserializedProfile['preferences']['theme'];
  List<String> tags = List<String>.from(deserializedProfile['tags']);
  
  print('User details:');
  print('  Name: $firstName');
  print('  Theme: $theme');
  print('  Tags: ${tags.join(', ')}');
  
  // Validate and clean data
  var cleanedProfile = <String, dynamic>{};
  deserializedProfile.forEach((key, value) {
    if (value != null && key != 'loginCount') { // Exclude sensitive data
      cleanedProfile[key] = value;
    }
  });
  
  print('Cleaned profile keys: ${cleanedProfile.keys.toList()}');
  
  // Pretty print JSON
  var encoder = JsonEncoder.withIndent('  ');
  print('Pretty JSON:');
  print(encoder.convert(cleanedProfile));
}
```

JSON integration enables seamless data exchange with web services and  
persistent storage. The `dynamic` type accommodates varied JSON structures  
while requiring careful type checking for safe data access.  

## Map Pattern Matching (Dart 3.x)

Modern Dart supports pattern matching with maps, enabling powerful  
destructuring and conditional logic based on map contents and structure.  

```dart
void main() {
  // API response simulation
  var responses = [
    {'status': 'success', 'data': {'user': 'Alice', 'score': 95}},
    {'status': 'error', 'message': 'Network timeout', 'code': 408},
    {'status': 'success', 'data': {'user': 'Bob', 'score': 87}},
    {'status': 'error', 'message': 'Invalid credentials', 'code': 401},
    {'status': 'pending', 'eta': '2 minutes'}
  ];
  
  // Process responses with pattern matching
  for (var response in responses) {
    var result = switch (response) {
      {'status': 'success', 'data': {'user': String user, 'score': int score}} =>
        'Success: $user scored $score points',
      
      {'status': 'error', 'message': String msg, 'code': int code} when code >= 400 =>
        'Error $code: $msg',
      
      {'status': 'pending', 'eta': String eta} =>
        'Request pending, ETA: $eta',
      
      {'status': String status} =>
        'Unknown response type: $status',
      
      _ => 'Invalid response format'
    };
    
    print(result);
  }
  
  // Configuration pattern matching
  var configs = [
    {'env': 'production', 'debug': false, 'workers': 8},
    {'env': 'development', 'debug': true, 'hot_reload': true},
    {'env': 'test', 'debug': true, 'workers': 2, 'timeout': 5000}
  ];
  
  print('Configuration analysis:');
  for (var config in configs) {
    var analysis = switch (config) {
      {'env': 'production', 'debug': false, 'workers': int w} when w > 4 =>
        'Production ready with $w workers',
      
      {'env': 'development', 'debug': true, 'hot_reload': true} =>
        'Development mode with hot reload enabled',
      
      {'env': 'test', 'timeout': int timeout} =>
        'Test environment with ${timeout}ms timeout',
      
      {'env': String env} =>
        'Basic $env configuration',
      
      _ => 'Incomplete configuration'
    };
    
    print('  $analysis');
  }
}
```

Pattern matching provides concise, type-safe map processing with structural  
validation. Guard conditions (`when`) enable additional filtering while  
destructuring extracts specific values directly into variables.  

## Map Caching and Memoization

Maps serve as efficient caching mechanisms for expensive computations,  
providing memoization patterns that improve application performance  
through result reuse.  

```dart
class FibonacciCalculator {
  final Map<int, int> _cache = <int, int>{};
  
  int calculate(int n) {
    if (n <= 1) return n;
    
    // Check cache first
    if (_cache.containsKey(n)) {
      print('Cache hit for fibonacci($n)');
      return _cache[n]!;
    }
    
    // Calculate and cache result
    print('Computing fibonacci($n)');
    var result = calculate(n - 1) + calculate(n - 2);
    _cache[n] = result;
    return result;
  }
  
  void clearCache() => _cache.clear();
  int get cacheSize => _cache.length;
  Map<int, int> get cacheSnapshot => Map.unmodifiable(_cache);
}

class WebPageCache {
  final Map<String, (DateTime, String)> _cache = {};
  final Duration _ttl;
  
  WebPageCache({required Duration ttl}) : _ttl = ttl;
  
  String? getPage(String url) {
    var cached = _cache[url];
    if (cached != null) {
      var (timestamp, content) = cached;
      if (DateTime.now().difference(timestamp) < _ttl) {
        print('Serving cached content for $url');
        return content;
      } else {
        _cache.remove(url); // Expired
      }
    }
    return null;
  }
  
  void cachePage(String url, String content) {
    _cache[url] = (DateTime.now(), content);
  }
  
  void cleanup() {
    var now = DateTime.now();
    _cache.removeWhere((url, cached) {
      var (timestamp, _) = cached;
      return now.difference(timestamp) >= _ttl;
    });
  }
  
  int get size => _cache.length;
}

void main() {
  // Fibonacci memoization
  var fibCalculator = FibonacciCalculator();
  
  print('Fibonacci calculations:');
  print('fib(10) = ${fibCalculator.calculate(10)}');
  print('fib(12) = ${fibCalculator.calculate(12)}'); // Reuses cached values
  print('Cache contains ${fibCalculator.cacheSize} entries');
  print('Cache contents: ${fibCalculator.cacheSnapshot}');
  
  // Web page caching simulation
  var pageCache = WebPageCache(ttl: Duration(seconds: 5));
  
  // Simulate page loading
  pageCache.cachePage('/home', '<html>Home Page Content</html>');
  pageCache.cachePage('/about', '<html>About Page Content</html>');
  
  print('\\nWeb page cache:');
  print('Cache size: ${pageCache.size}');
  
  // Access cached pages
  var homePage = pageCache.getPage('/home');
  print('Home page: ${homePage?.substring(0, 20)}...');
  
  // Wait and cleanup (simulate expiration)
  Future.delayed(Duration(seconds: 2), () {
    var aboutPage = pageCache.getPage('/about');
    print('About page still cached: ${aboutPage != null}');
    
    pageCache.cleanup();
    print('After cleanup: ${pageCache.size} entries');
  });
}
```

Caching with maps provides O(1) lookup performance for expensive operations.  
Time-based expiration and size limits prevent unbounded memory growth while  
maintaining cache effectiveness for frequently accessed data.  

## Map State Management Patterns

Maps excel in state management scenarios, providing reactive data structures  
for application state, configuration management, and event-driven programming.  

```dart
class StateManager<T> {
  final Map<String, T> _state = <String, T>{};
  final Map<String, List<void Function(T?)>> _listeners = {};
  
  T? get(String key) => _state[key];
  
  void set(String key, T value) {
    var oldValue = _state[key];
    _state[key] = value;
    _notifyListeners(key, value, oldValue);
  }
  
  void subscribe(String key, void Function(T?) listener) {
    _listeners.putIfAbsent(key, () => []).add(listener);
  }
  
  void unsubscribe(String key, void Function(T?) listener) {
    _listeners[key]?.remove(listener);
  }
  
  void _notifyListeners(String key, T newValue, T? oldValue) {
    _listeners[key]?.forEach((listener) => listener(newValue));
  }
  
  Map<String, T> get snapshot => Map.unmodifiable(_state);
  
  void batch(Map<String, T> updates) {
    updates.forEach(set);
  }
}

class ShoppingCart {
  final Map<String, int> _items = <String, int>{};
  final Map<String, double> _prices = <String, double>{};
  
  void addItem(String product, int quantity, double price) {
    _items[product] = (_items[product] ?? 0) + quantity;
    _prices[product] = price;
  }
  
  void removeItem(String product, {int quantity = 1}) {
    if (_items.containsKey(product)) {
      _items[product] = (_items[product]! - quantity).clamp(0, double.infinity).toInt();
      if (_items[product]! <= 0) {
        _items.remove(product);
        _prices.remove(product);
      }
    }
  }
  
  double get total => _items.entries
      .map((entry) => entry.value * _prices[entry.key]!)
      .reduce((a, b) => a + b);
  
  int get itemCount => _items.values.reduce((a, b) => a + b);
  
  Map<String, (int, double)> get items => Map.fromEntries(
    _items.entries.map((e) => MapEntry(e.key, (e.value, _prices[e.key]!)))
  );
  
  void clear() {
    _items.clear();
    _prices.clear();
  }
}

void main() {
  // State management example
  var appState = StateManager<dynamic>();
  
  // Subscribe to state changes
  appState.subscribe('user', (value) {
    print('User changed: $value');
  });
  
  appState.subscribe('theme', (value) {
    print('Theme changed: $value');
  });
  
  // Update state
  appState.set('user', {'name': 'Alice', 'role': 'admin'});
  appState.set('theme', 'dark');
  appState.set('user', {'name': 'Bob', 'role': 'user'});
  
  print('Current state: ${appState.snapshot}');
  
  // Batch updates
  appState.batch({
    'language': 'en',
    'region': 'US',
    'timezone': 'EST'
  });
  
  print('After batch update: ${appState.snapshot}');
  
  // Shopping cart example
  var cart = ShoppingCart();
  
  cart.addItem('Laptop', 1, 999.99);
  cart.addItem('Mouse', 2, 29.99);
  cart.addItem('Keyboard', 1, 79.99);
  
  print('\\nShopping cart:');
  cart.items.forEach((product, info) {
    var (quantity, price) = info;
    print('  $product: $quantity × \$${price.toStringAsFixed(2)} = \$${(quantity * price).toStringAsFixed(2)}');
  });
  
  print('Total items: ${cart.itemCount}');
  print('Total cost: \$${cart.total.toStringAsFixed(2)}');
  
  cart.removeItem('Mouse', quantity: 1);
  print('After removing 1 mouse: \$${cart.total.toStringAsFixed(2)}');
}
```

State management patterns with maps enable reactive programming and  
centralized data control. Observer patterns provide change notifications  
while maintaining encapsulation and type safety for complex applications.  

## Map Utilities and Helper Functions

Utility functions enhance map functionality for common operations, data  
validation, and specialized processing requirements in production applications.  

```dart
class MapUtils {
  // Deep merge two maps
  static Map<String, dynamic> deepMerge(
    Map<String, dynamic> map1, 
    Map<String, dynamic> map2
  ) {
    var result = Map<String, dynamic>.from(map1);
    
    map2.forEach((key, value) {
      if (result.containsKey(key) && 
          result[key] is Map<String, dynamic> && 
          value is Map<String, dynamic>) {
        result[key] = deepMerge(result[key], value);
      } else {
        result[key] = value;
      }
    });
    
    return result;
  }
  
  // Flatten nested map
  static Map<String, dynamic> flatten(
    Map<String, dynamic> map, 
    {String separator = '.'}
  ) {
    var result = <String, dynamic>{};
    
    void _flatten(Map<String, dynamic> current, String prefix) {
      current.forEach((key, value) {
        var newKey = prefix.isEmpty ? key : '$prefix$separator$key';
        
        if (value is Map<String, dynamic>) {
          _flatten(value, newKey);
        } else {
          result[newKey] = value;
        }
      });
    }
    
    _flatten(map, '');
    return result;
  }
  
  // Pick specific keys
  static Map<K, V> pick<K, V>(Map<K, V> map, List<K> keys) {
    return Map.fromEntries(
      keys.where(map.containsKey).map((key) => MapEntry(key, map[key]!))
    );
  }
  
  // Omit specific keys
  static Map<K, V> omit<K, V>(Map<K, V> map, List<K> keys) {
    return Map.fromEntries(
      map.entries.where((entry) => !keys.contains(entry.key))
    );
  }
  
  // Group list by key function
  static Map<K, List<T>> groupBy<T, K>(
    Iterable<T> items, 
    K Function(T) keySelector
  ) {
    var result = <K, List<T>>{};
    
    for (var item in items) {
      var key = keySelector(item);
      result.putIfAbsent(key, () => []).add(item);
    }
    
    return result;
  }
  
  // Count occurrences
  static Map<T, int> countOccurrences<T>(Iterable<T> items) {
    var counts = <T, int>{};
    
    for (var item in items) {
      counts[item] = (counts[item] ?? 0) + 1;
    }
    
    return counts;
  }
}

void main() {
  // Deep merge example
  var config1 = {
    'database': {
      'host': 'localhost',
      'port': 5432
    },
    'cache': {'ttl': 300}
  };
  
  var config2 = {
    'database': {
      'name': 'production_db',
      'ssl': true
    },
    'logging': {'level': 'info'}
  };
  
  var mergedConfig = MapUtils.deepMerge(config1, config2);
  print('Merged config: $mergedConfig');
  
  // Flatten example
  var nestedData = {
    'user': {
      'profile': {
        'name': 'Alice',
        'age': 30
      },
      'settings': {
        'theme': 'dark',
        'notifications': true
      }
    },
    'app': {
      'version': '1.2.3'
    }
  };
  
  var flattenedData = MapUtils.flatten(nestedData);
  print('Flattened data: $flattenedData');
  
  // Pick and omit examples
  var userData = {
    'id': 123,
    'name': 'Bob',
    'email': 'bob@example.com',
    'password': 'secret',
    'createdAt': '2023-01-01'
  };
  
  var publicData = MapUtils.omit(userData, ['password']);
  var essentialData = MapUtils.pick(userData, ['id', 'name', 'email']);
  
  print('Public data: $publicData');
  print('Essential data: $essentialData');
  
  // Group by example
  var students = [
    {'name': 'Alice', 'grade': 'A', 'subject': 'Math'},
    {'name': 'Bob', 'grade': 'B', 'subject': 'Math'},
    {'name': 'Carol', 'grade': 'A', 'subject': 'Science'},
    {'name': 'David', 'grade': 'C', 'subject': 'Math'},
    {'name': 'Emma', 'grade': 'A', 'subject': 'Science'}
  ];
  
  var byGrade = MapUtils.groupBy(students, (student) => student['grade']);
  var bySubject = MapUtils.groupBy(students, (student) => student['subject']);
  
  print('Students by grade: $byGrade');
  print('Students by subject: $bySubject');
  
  // Count occurrences
  var fruits = ['apple', 'banana', 'apple', 'orange', 'banana', 'apple'];
  var fruitCounts = MapUtils.countOccurrences(fruits);
  print('Fruit counts: $fruitCounts');
  
  var grades = students.map((s) => s['grade']).toList();
  var gradeCounts = MapUtils.countOccurrences(grades);
  print('Grade distribution: $gradeCounts');
}
```

Utility functions provide reusable map operations for complex data processing  
scenarios. These patterns enable clean code organization and reduce duplication  
in applications requiring sophisticated data manipulation capabilities.  

## Advanced Map Patterns and Best Practices

Advanced map usage patterns demonstrate sophisticated techniques for  
performance optimization, memory management, and architectural design  
in production applications.  

```dart
// Immutable map wrapper
class ImmutableMap<K, V> {
  final Map<K, V> _map;
  
  ImmutableMap(Map<K, V> map) : _map = Map.unmodifiable(Map.from(map));
  
  V? operator [](K key) => _map[key];
  bool containsKey(K key) => _map.containsKey(key);
  Iterable<K> get keys => _map.keys;
  Iterable<V> get values => _map.values;
  Iterable<MapEntry<K, V>> get entries => _map.entries;
  int get length => _map.length;
  bool get isEmpty => _map.isEmpty;
  
  // Return new instance with changes
  ImmutableMap<K, V> put(K key, V value) {
    var newMap = Map<K, V>.from(_map);
    newMap[key] = value;
    return ImmutableMap(newMap);
  }
  
  ImmutableMap<K, V> remove(K key) {
    var newMap = Map<K, V>.from(_map);
    newMap.remove(key);
    return ImmutableMap(newMap);
  }
  
  ImmutableMap<K, V> putAll(Map<K, V> other) {
    var newMap = Map<K, V>.from(_map);
    newMap.addAll(other);
    return ImmutableMap(newMap);
  }
  
  @override
  String toString() => _map.toString();
}

// LRU Cache implementation
class LRUCache<K, V> {
  final int _maxSize;
  final Map<K, V> _cache = <K, V>{};
  final List<K> _accessOrder = <K>[];
  
  LRUCache(this._maxSize);
  
  V? get(K key) {
    if (_cache.containsKey(key)) {
      _updateAccessOrder(key);
      return _cache[key];
    }
    return null;
  }
  
  void put(K key, V value) {
    if (_cache.containsKey(key)) {
      _cache[key] = value;
      _updateAccessOrder(key);
    } else {
      if (_cache.length >= _maxSize) {
        _evictLeastRecentlyUsed();
      }
      _cache[key] = value;
      _accessOrder.add(key);
    }
  }
  
  void _updateAccessOrder(K key) {
    _accessOrder.remove(key);
    _accessOrder.add(key);
  }
  
  void _evictLeastRecentlyUsed() {
    if (_accessOrder.isNotEmpty) {
      var lruKey = _accessOrder.removeAt(0);
      _cache.remove(lruKey);
    }
  }
  
  int get size => _cache.length;
  bool containsKey(K key) => _cache.containsKey(key);
  void clear() {
    _cache.clear();
    _accessOrder.clear();
  }
  
  Map<K, V> get snapshot => Map.unmodifiable(_cache);
}

// Trie-like structure using maps
class TrieMap {
  final Map<String, TrieMap> _children = <String, TrieMap>{};
  bool _isEndOfWord = false;
  String? _value;
  
  void insert(String word, [String? value]) {
    var current = this;
    
    for (var char in word.split('')) {
      current._children.putIfAbsent(char, () => TrieMap());
      current = current._children[char]!;
    }
    
    current._isEndOfWord = true;
    current._value = value ?? word;
  }
  
  String? search(String word) {
    var current = this;
    
    for (var char in word.split('')) {
      if (!current._children.containsKey(char)) {
        return null;
      }
      current = current._children[char]!;
    }
    
    return current._isEndOfWord ? current._value : null;
  }
  
  List<String> getWordsWithPrefix(String prefix) {
    var current = this;
    
    // Navigate to prefix end
    for (var char in prefix.split('')) {
      if (!current._children.containsKey(char)) {
        return [];
      }
      current = current._children[char]!;
    }
    
    // Collect all words from this point
    var results = <String>[];
    _collectWords(current, prefix, results);
    return results;
  }
  
  void _collectWords(TrieMap node, String prefix, List<String> results) {
    if (node._isEndOfWord && node._value != null) {
      results.add(node._value!);
    }
    
    node._children.forEach((char, child) {
      _collectWords(child, prefix + char, results);
    });
  }
}

void main() {
  // Immutable map example
  var immutableConfig = ImmutableMap({'env': 'dev', 'debug': true});
  print('Original config: $immutableConfig');
  
  var prodConfig = immutableConfig
      .put('env', 'production')
      .put('debug', false)
      .put('workers', 8);
  
  print('Production config: $prodConfig');
  print('Original unchanged: $immutableConfig');
  
  // LRU Cache example
  var cache = LRUCache<String, String>(3);
  
  cache.put('page1', 'Home Page');
  cache.put('page2', 'About Page');
  cache.put('page3', 'Contact Page');
  print('Cache after 3 inserts: ${cache.snapshot}');
  
  cache.get('page1'); // Access page1 (moves to end)
  cache.put('page4', 'Products Page'); // Should evict page2
  print('After accessing page1 and adding page4: ${cache.snapshot}');
  
  // Trie example for autocomplete
  var trie = TrieMap();
  var words = [
    'dart', 'data', 'database', 'dashboard', 
    'development', 'developer', 'design', 'debug'
  ];
  
  for (var word in words) {
    trie.insert(word);
  }
  
  print('Trie search results:');
  print('  "dat" exists: ${trie.search('dat')}');
  print('  "dart" exists: ${trie.search('dart')}');
  print('  "data" suggestions: ${trie.getWordsWithPrefix('dat')}');
  print('  "dev" suggestions: ${trie.getWordsWithPrefix('dev')}');
  
  // Performance comparison
  var largeMap = <String, int>{};
  var stopwatch = Stopwatch()..start();
  
  for (int i = 0; i < 100000; i++) {
    largeMap['key_$i'] = i;
  }
  stopwatch.stop();
  print('Large map creation: ${stopwatch.elapsedMilliseconds}ms');
  
  // Memory-conscious operations
  stopwatch.reset();
  stopwatch.start();
  largeMap.removeWhere((key, value) => value % 10000 == 0);
  stopwatch.stop();
  print('Selective removal: ${stopwatch.elapsedMilliseconds}ms');
  print('Remaining entries: ${largeMap.length}');
}
```

Advanced patterns showcase production-ready techniques including immutability,  
caching strategies, and specialized data structures. These patterns enhance  
performance, maintain data integrity, and provide sophisticated functionality  
for complex application requirements.  