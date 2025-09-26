
# Dart Records

In this article, we explore how to use **records** in the Dart programming  
language. Records provide a concise way to group multiple values into a  
single unit without requiring a custom class. They are immutable, ordered,  
and have a fixed size, making them an efficient alternative to traditional  
data structures.  

Records in Dart function similarly to *tuples* in other programming  
languages. They allow developers to return multiple values from a function,  
store related data conveniently, and maintain clean, readable code. Unlike  
lists, which offer dynamic size and mutability, records are designed to be  
lightweight and predictable.  

A key advantage of using records is improved type safety and structure. Each  
field in a record maintains its distinct type, ensuring that data remains  
properly categorized. This makes records particularly useful when working  
with temporary, grouped data in scenarios such as function outputs and  
intermediate calculations.  

By leveraging records, Dart developers can write more expressive, concise,  
and efficient code without unnecessary boilerplate. In the following  
sections, we will demonstrate how to create and work with records  
effectively.  

## Simple Records

The following example demonstrates how to create and use a simple record in  
Dart with positional fields.  

```dart
void main() {
  // Creating a record
  final person = ('John', 30, true);

  // Accessing record elements
  print('Name: ${person.$1}');
  print('Age: ${person.$2}');
  print('Is Active: ${person.$3}');
}
```

This program creates a record containing a name, age, and active status.  
We then access and print each element using positional field syntax.  

We create a record with three elements: a string, an integer, and a boolean.  
The elements are accessed using the `$1`, `$2`, and `$3` syntax, which  
corresponds to the first, second, and third positional fields respectively.  

## Records with Named Fields

Records can also have named fields, which make the code more readable and  
self-explanatory by providing meaningful identifiers for each value.  

```dart
void main() {
  // Creating a record with named fields
  final person = (name: 'Alice', age: 25, isActive: false);

  // Accessing record elements using named fields
  print('Name: ${person.name}');
  print('Age: ${person.age}');
  print('Is Active: ${person.isActive}');
}
```

In this program, we create a record with named fields and access the elements  
using the field names. This approach improves code readability and makes the  
intent clearer compared to positional access.  

We create a record with named fields: `name`, `age`, and `isActive`. Named  
fields allow us to access record elements using descriptive names rather  
than positional indices, making the code more maintainable and less prone  
to errors when the field order changes.  


## Records in Functions

Records are particularly useful for returning multiple values from a function,  
eliminating the need for complex data structures or output parameters.  

```dart
(String, int) getUserInfo() {
  return ('Bob', 40);
}

void main() {
  final userInfo = getUserInfo();
  print('Name: ${userInfo.$1}');
  print('Age: ${userInfo.$2}');
}
```

This program defines a function that returns a record containing a name and  
age. The function signature explicitly declares the return type as a record  
with two positional fields: a String and an int.  

We define a function with return type `(String, int)` that creates and returns  
a record with two elements. The calling code receives the record and accesses  
its elements using positional syntax. This approach is cleaner than returning  
a Map or creating a custom class for simple data groupings.  


## Records with Pattern Matching

Dart supports pattern matching, which allows you to destructure records into  
individual variables in a single operation.  

```dart
void main() {
  final person = ('Charlie', 35, true);

  // Destructuring the record
  final (name, age, isActive) = person;

  print('Name: $name');
  print('Age: $age');
  print('Is Active: $isActive');
}
```

This program demonstrates record destructuring using pattern matching. Instead  
of accessing individual fields, we extract all values into separate variables  
in one assignment operation.  

The destructuring assignment `final (name, age, isActive) = person` extracts  
the three elements from the record into individual variables. This pattern  
matching syntax is more elegant than accessing fields individually and allows  
for cleaner, more readable code when working with record data.  


## Records as Map Keys

Records can be used as keys in Dart maps because they provide value-based  
equality and consistent hash codes for records with identical contents.  

```dart
void main() {
  final record1 = ('apple', 1);
  final record2 = ('banana', 2);
  final record3 = ('apple', 1);

  final map = {
    record1: 'Red fruit',
    record2: 'Yellow fruit',
  };

  print(map[record1]); // Red fruit
  print(map[record3]); // Red fruit (record1 == record3)
}
```

This example demonstrates how records with identical values are considered  
equal, making them suitable as map keys. Records `record1` and `record3`  
contain the same values, so they are treated as equivalent keys.  

Record equality is based on the values of all fields, not object identity.  
This means two records with the same field values will always be equal and  
produce the same hash code, making them interchangeable as map keys.  


## Records in Collections

Records can be stored in collections like lists and sets. Dart uses value  
equality for records, ensuring that duplicate records are automatically  
handled in sets and other collection operations.  

```dart
void main() {
  final r1 = ('x', 10);
  final r2 = ('y', 20);
  final r3 = ('x', 10);

  final list = [r1, r2, r3];
  print(list);

  final set = {r1, r2, r3};
  print(set);
}
```

The list contains all three record references, while the set contains only  
two unique records since `r1` and `r3` have identical values.  

Sets automatically eliminate duplicates based on record equality. Even though  
`r1` and `r3` are different objects, they contain the same values and are  
considered equal, so the set keeps only one instance.  


## Records with Mixed Fields

A record can combine both positional and named fields in a single structure,  
providing flexibility in how you organize and access data.  

```dart
void main() {
  final data = ('A', 42, flag: true, description: 'sample');

  print('First: \'${data.$1}\'');
  print('Second: ${data.$2}');
  print('Flag: ${data.flag}');
  print('Description: ${data.description}');
}
```

This record contains two positional fields accessed with `$1` and `$2`, and  
two named fields accessed using their descriptive names.  

Mixed field records are useful when you need both ordered data (accessed  
positionally) and descriptive data (accessed by name). Positional fields  
come first in the record definition, followed by named fields.  

## Record Type Annotations

Record types can be explicitly declared using type annotations, providing  
better documentation and type safety in function signatures and variable  
declarations.  

```dart
// Function with explicit record return type
({String name, int age}) createPerson(String name, int age) {
  return (name: name, age: age);
}

// Variable with explicit record type annotation
(double, double) coordinates = (3.14, 2.71);

void main() {
  // Using typed record functions
  var person = createPerson('Diana', 28);
  print('Created: ${person.name}, age ${person.age}');
  
  // Working with typed coordinates
  print('Point at (${coordinates.$1}, ${coordinates.$2})');
  
  // Complex nested record type
  ({String title, ({String author, int year}) book}) bookInfo = (
    title: 'Dart Programming', 
    book: (author: 'John Doe', year: 2023)
  );
  
  print('Book: ${bookInfo.title} by ${bookInfo.book.author}');
}
```

Explicit type annotations improve code clarity and enable better IDE support  
with autocompletion and error detection. Complex record types can represent  
sophisticated data structures while maintaining type safety throughout the  
application.  

## Record Comparisons and Equality

Records support value-based equality comparison, making them ideal for  
comparing structured data without implementing custom equality methods.  

```dart
void main() {
  final person1 = (name: 'Emma', age: 30);
  final person2 = (name: 'Emma', age: 30);
  final person3 = (name: 'Frank', age: 30);
  
  // Value equality comparison
  print('person1 == person2: ${person1 == person2}'); // true
  print('person1 == person3: ${person1 == person3}'); // false
  
  // Records with different field order
  final pointA = (x: 10, y: 20);
  final pointB = (y: 20, x: 10);
  print('pointA == pointB: ${pointA == pointB}'); // false - order matters!
  
  // Hash code consistency
  final set = {person1, person2, person3};
  print('Unique persons in set: ${set.length}'); // 2
  
  // Comparing records with different types
  final mixed1 = ('test', 42, active: true);
  final mixed2 = ('test', 42, active: true);
  print('Mixed records equal: ${mixed1 == mixed2}'); // true
}
```

Record equality is structural - two records are equal if all their fields  
have the same values in the same positions. Field order matters for named  
fields, and records with identical values produce identical hash codes,  
making them suitable for use in hash-based collections.  

## Record Destructuring in Switch Patterns

Switch expressions can destructure records directly, enabling powerful  
pattern matching that combines record extraction with conditional logic.  

```dart
String analyzeCoordinate((int, int) point) {
  return switch (point) {
    (0, 0) => 'Origin point',
    (0, var y) => 'On Y-axis at $y',
    (var x, 0) => 'On X-axis at $x',
    (var x, var y) when x == y => 'Diagonal at ($x, $y)',
    (var x, var y) when x > 0 && y > 0 => 'First quadrant: ($x, $y)',
    (var x, var y) => 'Other quadrant: ($x, $y)',
  };
}

String processUser(({String name, int age, bool admin}) user) {
  return switch (user) {
    (name: var n, age: var a, admin: true) when a < 18 => 
      'Young admin: $n ($a)',
    (name: var n, age: var a, admin: true) => 
      'Admin: $n ($a)',
    (name: var n, age: var a, admin: false) when a >= 65 => 
      'Senior user: $n ($a)',
    (name: var n, age: var a, admin: false) => 
      'Regular user: $n ($a)',
  };
}

void main() {
  print(analyzeCoordinate((0, 0)));
  print(analyzeCoordinate((3, 3)));
  print(analyzeCoordinate((5, -2)));
  
  print(processUser((name: 'Grace', age: 16, admin: true)));
  print(processUser((name: 'Henry', age: 45, admin: false)));
}
```

Switch patterns with records enable sophisticated data analysis by combining  
destructuring, variable binding, and guard conditions. This approach  
eliminates complex if-else chains and provides more readable, maintainable  
code for processing structured data.  

## Nested Records

Records can contain other records as fields, creating hierarchical data  
structures without defining custom classes.  

```dart
void main() {
  // Creating nested records
  final address = (
    street: '123 Main St',
    city: 'Springfield',
    coordinates: (latitude: 40.7128, longitude: -74.0060)
  );
  
  final employee = (
    personal: (name: 'Isabella', age: 32),
    contact: (email: 'isabella@example.com', phone: '555-0123'),
    address: address,
    department: 'Engineering'
  );
  
  // Accessing nested record fields
  print('Employee: ${employee.personal.name}');
  print('Department: ${employee.department}');
  print('Email: ${employee.contact.email}');
  print('City: ${employee.address.city}');
  print('Location: ${employee.address.coordinates.latitude}, '
        '${employee.address.coordinates.longitude}');
  
  // Destructuring nested records
  final (
    personal: (name: empName, age: empAge),
    contact: (email: empEmail, phone: _),
    address: (city: empCity, coordinates: (latitude: lat, longitude: lng)),
    department: dept
  ) = employee;
  
  print('Destructured: $empName ($empAge) from $empCity works in $dept');
  print('Located at: $lat, $lng');
}
```

Nested records provide a lightweight alternative to complex class hierarchies  
for representing structured data. Deep nesting is supported, and both field  
access and destructuring work intuitively with nested structures.  

## Record Iteration with For-In Loops

Collections of records can be processed efficiently using for-in loops with  
pattern matching, allowing direct destructuring during iteration.  

```dart
void main() {
  // List of employee records
  final employees = [
    (name: 'Jack', department: 'Engineering', salary: 75000),
    (name: 'Kate', department: 'Marketing', salary: 68000),
    (name: 'Liam', department: 'Engineering', salary: 82000),
    (name: 'Maya', department: 'Sales', salary: 71000),
  ];
  
  // Iterate with destructuring
  print('Employee Summary:');
  for (final (name: empName, department: dept, salary: sal) in employees) {
    print('  $empName ($dept): \$${sal.toString()}');
  }
  
  // Filtered iteration with pattern matching
  print('\nEngineering Team:');
  for (final employee in employees) {
    switch (employee) {
      case (name: var n, department: 'Engineering', salary: var s):
        print('  Engineer $n earns \$${s.toString()}');
    }
  }
  
  // Complex records with coordinates
  final locations = [
    (name: 'Office A', coords: (x: 100, y: 200), active: true),
    (name: 'Office B', coords: (x: 300, y: 150), active: false),
    (name: 'Office C', coords: (x: 250, y: 400), active: true),
  ];
  
  // Process active locations
  print('\nActive Locations:');
  for (final location in locations) {
    switch (location) {
      case (name: var locName, coords: (x: var posX, y: var posY), active: true):
        print('  $locName at ($posX, $posY)');
    }
  }
}
```

For-in loops with record patterns enable elegant processing of structured  
data collections. Pattern matching within loops allows filtering and  
destructuring in a single operation, reducing boilerplate code significantly.  

## Record Transformation with Map Operations

Records work seamlessly with collection methods like map, where, and fold,  
enabling functional programming patterns for data transformation.  

```dart
void main() {
  final products = [
    (name: 'Laptop', price: 999.99, category: 'Electronics'),
    (name: 'Book', price: 19.99, category: 'Education'),
    (name: 'Phone', price: 699.99, category: 'Electronics'),
    (name: 'Desk', price: 299.99, category: 'Furniture'),
  ];
  
  // Transform prices with tax
  final withTax = products
      .map((product) => (
            name: product.name,
            originalPrice: product.price,
            priceWithTax: product.price * 1.08,
            category: product.category
          ))
      .toList();
  
  print('Products with Tax:');
  for (final item in withTax) {
    print('  ${item.name}: \$${item.priceWithTax.toStringAsFixed(2)}');
  }
  
  // Filter and transform electronics
  final expensiveElectronics = products
      .where((p) => p.category == 'Electronics' && p.price > 500)
      .map((p) => (
            product: p.name,
            discountedPrice: p.price * 0.9,
            savings: p.price * 0.1
          ));
  
  print('\nDiscounted Electronics:');
  for (final deal in expensiveElectronics) {
    print('  ${deal.product}: Save \$${deal.savings.toStringAsFixed(2)}');
  }
  
  // Aggregate operations
  final totalValue = products.fold(0.0, (sum, product) => sum + product.price);
  final avgPrice = totalValue / products.length;
  
  final summary = (
    totalProducts: products.length,
    totalValue: totalValue,
    averagePrice: avgPrice,
    categories: products.map((p) => p.category).toSet()
  );
  
  print('\nSummary: ${summary.totalProducts} products, '
        'avg \$${summary.averagePrice.toStringAsFixed(2)}');
}
```

Record transformations with collection methods provide powerful data  
processing capabilities. The immutable nature of records ensures safe  
transformations, while pattern matching and record literals make complex  
data manipulations readable and maintainable.  

## Record Validation and Error Handling

Records can be used with validation functions to create robust data  
processing pipelines that handle errors gracefully.  

```dart
// Result type using records
typedef ValidationResult<T> = ({bool isValid, T? data, String? error});

ValidationResult<({String name, int age})> validatePerson(
    Map<String, dynamic> input) {
  if (!input.containsKey('name') || input['name'] is! String) {
    return (isValid: false, data: null, error: 'Invalid name');
  }
  
  if (!input.containsKey('age') || input['age'] is! int) {
    return (isValid: false, data: null, error: 'Invalid age');
  }
  
  final name = input['name'] as String;
  final age = input['age'] as int;
  
  if (name.isEmpty) {
    return (isValid: false, data: null, error: 'Name cannot be empty');
  }
  
  if (age < 0 || age > 150) {
    return (isValid: false, data: null, error: 'Age must be 0-150');
  }
  
  return (isValid: true, data: (name: name, age: age), error: null);
}

void processPerson(Map<String, dynamic> personData) {
  final result = validatePerson(personData);
  
  switch (result) {
    case (isValid: true, data: var person, error: null):
      print('Valid person: ${person!.name} (${person.age})');
    case (isValid: false, data: null, error: var err):
      print('Validation failed: $err');
  }
}

void main() {
  final testData = [
    {'name': 'Noah', 'age': 25},
    {'name': '', 'age': 30},
    {'name': 'Olivia', 'age': -5},
    {'age': 40}, // missing name
    {'name': 'Paul', 'age': 35},
  ];
  
  for (final data in testData) {
    processPerson(data);
  }
  
  // Batch validation with records
  final results = testData.map(validatePerson).toList();
  final validPeople = results
      .where((r) => r.isValid)
      .map((r) => r.data!)
      .toList();
  
  print('\nValid people: ${validPeople.length}');
  for (final person in validPeople) {
    print('  ${person.name} (${person.age})');
  }
}
```

Record-based validation patterns provide clear, type-safe error handling.  
The validation result record encapsulates success state, data, and error  
information in a single structure, making error handling explicit and  
reducing the chance of unhandled error conditions.  

## Advanced Pattern Matching with Records

Complex pattern matching scenarios demonstrate the full power of records  
combined with Dart's pattern matching capabilities.  

```dart
// Complex event system using records
typedef Event = ({
  String type,
  DateTime timestamp,
  Map<String, dynamic> data
});

String processEvent(Event event) {
  return switch (event) {
    // User events with specific patterns
    (type: 'user_login', data: var data, timestamp: _) 
      when data.containsKey('userId') && data.containsKey('platform') =>
      'User ${data['userId']} logged in from ${data['platform']}',
    
    (type: 'user_logout', data: var data, timestamp: _) 
      when data.containsKey('userId') =>
      'User ${data['userId']} logged out',
    
    // Order events with amount validation
    (type: 'order_created', data: var data, timestamp: _) 
      when data.containsKey('orderId') && data.containsKey('amount') && (data['amount'] as num) > 100 =>
      'Large order created: ${data['orderId']} (\$${(data['amount'] as num).toStringAsFixed(2)})',
    
    (type: 'order_created', data: var data, timestamp: _) 
      when data.containsKey('orderId') && data.containsKey('amount') =>
      'Order created: ${data['orderId']} (\$${(data['amount'] as num).toStringAsFixed(2)})',
    
    // System events with nested records
    (type: 'system_alert', data: var data, timestamp: _) 
      when data.containsKey('severity') && data['severity'] == 'critical' =>
      'CRITICAL ALERT: ${data.containsKey('details') ? (data['details'] as Map)['message'] ?? 'Unknown error' : 'Unknown error'}',
    
    // System events (non-critical)
    (type: 'system_alert', data: var data, timestamp: _) =>
      'System alert: ${data['severity'] ?? 'unknown severity'}',
    
    // Catch-all for unknown events
    (type: var t, timestamp: var ts, data: _) =>
      'Unknown event type: $t at ${ts.toIso8601String()}',
  };
}

void main() {
  final events = [
    (
      type: 'user_login',
      timestamp: DateTime.now(),
      data: {'userId': 'user123', 'platform': 'mobile'}
    ),
    (
      type: 'order_created',
      timestamp: DateTime.now(),
      data: {'orderId': 'ord456', 'amount': 250.75}
    ),
    (
      type: 'system_alert',
      timestamp: DateTime.now(),
      data: {
        'severity': 'critical',
        'details': {'message': 'Database connection failed', 'code': 500}
      }
    ),
    (
      type: 'user_logout',
      timestamp: DateTime.now(),
      data: {'userId': 'user123'}
    ),
    (
      type: 'unknown_event',
      timestamp: DateTime.now(),
      data: {'info': 'test'}
    ),
  ];
  
  print('Processing Events:');
  for (final event in events) {
    print('  ${processEvent(event)}');
  }
  
  // Pattern matching with list of records
  final coordinates = [(x: 0, y: 0), (x: 1, y: 1), (x: 2, y: 4)];
  
  for (final point in coordinates) {
    final description = switch (point) {
      (x: 0, y: 0) => 'Origin',
      (x: var a, y: var b) when a == b => 'On diagonal',
      (x: var a, y: var b) when b == a * a => 'On parabola',
      _ => 'General point'
    };
    print('Point $point: $description');
  }
}
```

Advanced pattern matching with records enables sophisticated data processing  
scenarios. Complex nested patterns, guard conditions, and type-based matching  
create powerful control structures that can handle intricate business logic  
while maintaining code readability and type safety.  


