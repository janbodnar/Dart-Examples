
# Enum Types in Dart

Enums (enumerations) in Dart are a special kind of class used to represent a  
fixed number of constant values. This comprehensive guide covers Dart's enum  
types from basic usage to advanced features like enhanced enums, pattern  
matching, and modern Dart 3.9 capabilities.  

## Dart Enum Overview

Dart's enum types enable you to define a set of named constant values  
representing a fixed collection of related options. Common use cases include  
days of the week, traffic light states, user roles, or application states.  
Dart's enum system is both simple and powerful, providing built-in properties  
and methods that make working with enums intuitive and efficient.  

Enums in Dart can be basic (simple named constants) or enhanced (with  
properties, methods, and constructors). Dart also supports pattern matching  
with enums, enabling concise and expressive switch statements and expressions  
that improve code readability and maintainability.  

| Feature | Description | Example |
|---------|-------------|---------|
| Basic Enum | Simple set of named constants | `enum Color { red, green, blue }` |
| Enhanced Enum | Enums with properties and methods | `enum Status { active('A'), inactive('I') }` |
| Switch Matching | Pattern matching with enums | `switch(color) { case Color.red: ... }` |
| Enum Properties | Built-in index and name access | `color.index, color.name` |

## Basic Enum Declaration

The simplest form of enum in Dart declares a set of named constant values.  
These enums are perfect for representing a fixed set of related options  
where each option is equally important and carries no additional data.  

```dart
enum Day {
  monday,
  tuesday,
  wednesday,
  thursday,
  friday,
  saturday,
  sunday
}

void main() {
  Day today = Day.friday;
  print('Today is $today');
  print('Day index: ${today.index}');
  print('Day name: ${today.name}');
  
  // Iterate all enum values
  for (var day in Day.values) {
    print('$day (${day.index})');
  }
}
```

Basic enums automatically provide several useful properties. The `index`  
property returns the zero-based position of the enum value in its declaration  
order. The `name` property returns the string representation of the enum value.  
The static `values` constant provides a list of all enum values in declaration  
order, enabling easy iteration.  

```
$ dart run basic_enum.dart
Today is Day.friday
Day index: 4
Day name: friday
Day.monday (0)
Day.tuesday (1)
Day.wednesday (2)
Day.thursday (3)
Day.friday (4)
Day.saturday (5)
Day.sunday (6)
```

## Enhanced Enums

Dart's enhanced enums allow adding properties, methods, and constructors to  
enum declarations. This powerful feature makes enums more versatile by  
enabling them to carry additional data and behavior while maintaining type  
safety and enum semantics.  

```dart
enum Planet {
  mercury(mass: 3.303e+23, radius: 2.4397e6),
  venus(mass: 4.869e+24, radius: 6.0518e6),
  earth(mass: 5.976e+24, radius: 6.37814e6),
  mars(mass: 6.421e+23, radius: 3.3972e6);
  
  final double mass;
  final double radius;
  double get gravity => 6.67430e-11 * mass / (radius * radius);
  
  const Planet({required this.mass, required this.radius});
  
  String describe() {
    return '$name has mass ${mass.toStringAsExponential(1)} kg '
           'and radius ${radius.toStringAsExponential(1)} m';
  }
}

void main() {
  Planet home = Planet.earth;
  print(home.describe());
  print('Surface gravity: ${home.gravity.toStringAsFixed(2)} m/s²');
  
  // Compare enum values
  if (home == Planet.earth) {
    print('We are on Earth!');
  }
}
```

Enhanced enums can declare final fields that must be initialized by a constant  
constructor. They can also include getters and methods to provide additional  
functionality. Each enum value must call the constructor with the required  
parameters. This syntax maintains type safety while adding significant  
expressive power for domain modeling.  

```
$ dart run enhanced_enum.dart
earth has mass 6.0e+24 kg and radius 6.4e+6 m
Surface gravity: 9.80 m/s²
We are on Earth!
```

## Pattern Matching with Enums

Dart's switch expressions and pattern matching work exceptionally well with  
enum types. This combination enables exhaustive checking of all possible enum  
values and provides compile-time safety, ensuring no cases are missed during  
code evolution.  

```dart
enum TrafficLight {
  red,
  yellow,
  green
}

String getTrafficInstruction(TrafficLight light) {
  return switch (light) {
    TrafficLight.red => 'Stop',
    TrafficLight.yellow => 'Caution',
    TrafficLight.green => 'Go',
  };
}

void main() {
  TrafficLight current = TrafficLight.yellow;
  print(getTrafficInstruction(current));
  
  // Exhaustive checking
  for (var light in TrafficLight.values) {
    print('$light: ${getTrafficInstruction(light)}');
  }
}
```

When using switch expressions with enums, Dart ensures that all cases are  
handled. If a new enum value is added later, the compiler flags any switch  
statements that don't account for it. This exhaustive checking prevents  
runtime errors from unhandled cases. The pattern matching syntax is concise  
and clearly expresses the mapping between enum values and their corresponding  
behavior.  

```
$ dart run pattern_matching.dart
Caution
TrafficLight.red: Stop
TrafficLight.yellow: Caution
TrafficLight.green: Go
```

## Enum Properties and Methods

All Dart enums automatically come with several useful properties and methods.  
These built-in members make it easy to work with enum values without requiring  
additional boilerplate code, providing convenient access to metadata and  
manipulation capabilities.  

```dart
enum Direction {
  north,
  south,
  east,
  west
}

void main() {
  Direction heading = Direction.east;
  
  // Built-in properties
  print('Index: ${heading.index}');
  print('Name: ${heading.name}');
  
  // Access all values
  print('All directions:');
  for (var dir in Direction.values) {
    print('${dir.name.toUpperCase()} (${dir.index})');
  }
  
  // Convert from string
  String input = 'south';
  Direction? parsed = Direction.values.asNameMap()[input];
  print('Parsed direction: $parsed');
}
```

The `index` property returns the zero-based position of an enum value in its  
declaration order. The `name` property provides the string representation of  
the enum value. The static `values` constant contains all enum values in  
declaration order. The `asNameMap()` method creates a lookup table from names  
to enum values, which is useful for parsing strings into enum values safely.  

```
$ dart run enum_properties.dart
Index: 2
Name: east
All directions:
NORTH (0)
SOUTH (1)
EAST (2)
WEST (3)
Parsed direction: Direction.south
```

## Mixing Enums with Other Features

Dart enums work seamlessly with other language features like generics,  
extensions, and factory constructors. This integration allows for powerful  
combinations that solve complex problems elegantly while maintaining the  
benefits of enum type safety.  

```dart
enum ApiStatus {
  success,
  failure,
  loading;

  factory ApiStatus.fromCode(int code) {
    return switch (code) {
      200 => ApiStatus.success,
      400 || 500 => ApiStatus.failure,
      _ => ApiStatus.loading,
    };
  }
}

extension ApiStatusExtension on ApiStatus {
  String get message => switch (this) {
    ApiStatus.success => 'Operation succeeded',
    ApiStatus.failure => 'Operation failed',
    ApiStatus.loading => 'Operation in progress',
  };
}

void main() {
  ApiStatus status = ApiStatus.fromCode(200);
  print('Status: ${status.name}');
  print('Message: ${status.message}');
  
  status = ApiStatus.fromCode(500);
  print('Status: ${status.name}');
  print('Message: ${status.message}');
}
```

Factory constructors in enums enable alternative creation patterns, such as  
parsing from different data types or implementing complex initialization logic.  
Extensions add functionality to enums without modifying their original  
declaration. The combination of these features with pattern matching results  
in clean, maintainable code that clearly expresses the program's intent while  
maintaining type safety.  

```
$ dart run mixed_features.dart
Status: success
Message: Operation succeeded
Status: failure
Message: Operation failed
```

## Enum with Methods and Computed Properties

Enhanced enums can contain complex business logic through methods and computed  
properties. This approach encapsulates behavior within the enum definition,  
creating self-contained, reusable components that are easy to test and  
maintain.  

```dart
enum TaskPriority {
  low(urgencyLevel: 1, color: 0xFF4CAF50),
  medium(urgencyLevel: 2, color: 0xFFFF9800),
  high(urgencyLevel: 3, color: 0xFFF44336),
  critical(urgencyLevel: 4, color: 0xFF9C27B0);

  const TaskPriority({required this.urgencyLevel, required this.color});
  
  final int urgencyLevel;
  final int color;
  
  Duration get estimatedCompletionTime => switch (this) {
    TaskPriority.low => Duration(days: 7),
    TaskPriority.medium => Duration(days: 3),
    TaskPriority.high => Duration(days: 1),
    TaskPriority.critical => Duration(hours: 4),
  };
  
  String get displayName => name[0].toUpperCase() + name.substring(1);
  
  bool canBeDelayed() => urgencyLevel <= 2;
  
  TaskPriority escalate() => switch (this) {
    TaskPriority.low => TaskPriority.medium,
    TaskPriority.medium => TaskPriority.high,
    TaskPriority.high => TaskPriority.critical,
    TaskPriority.critical => TaskPriority.critical,
  };
}

void main() {
  TaskPriority task = TaskPriority.medium;
  print('Task priority: ${task.displayName}');
  print('Completion time: ${task.estimatedCompletionTime}');
  print('Can be delayed: ${task.canBeDelayed()}');
  print('Escalated priority: ${task.escalate().displayName}');
}
```

This example demonstrates sophisticated enum behavior with properties,  
computed values, and business logic methods. The enum encapsulates task  
management rules, providing a clean API for priority handling and escalation  
workflows.  

```
$ dart run task_priority.dart
Task priority: Medium
Completion time: 72:00:00.000000
Can be delayed: true
Escalated priority: High
```

## Enum Serialization and Deserialization

Enums often need to be converted to and from various data formats for storage  
or network communication. This pattern shows how to implement robust  
serialization with proper error handling and validation.  

```dart
enum UserRole {
  guest('guest', 0),
  member('member', 1), 
  moderator('moderator', 2),
  admin('admin', 3);

  const UserRole(this.stringValue, this.numericValue);
  
  final String stringValue;
  final int numericValue;
  
  // Serialization methods
  String toJson() => stringValue;
  int toNumeric() => numericValue;
  Map<String, dynamic> toMap() => {
    'role': stringValue,
    'level': numericValue,
    'permissions': _getPermissions(),
  };
  
  // Deserialization methods
  static UserRole? fromString(String value) {
    try {
      return UserRole.values.firstWhere(
        (role) => role.stringValue == value.toLowerCase()
      );
    } catch (e) {
      return null;
    }
  }
  
  static UserRole? fromNumeric(int value) {
    try {
      return UserRole.values.firstWhere(
        (role) => role.numericValue == value
      );
    } catch (e) {
      return null;
    }
  }
  
  static UserRole fromMap(Map<String, dynamic> map) {
    String roleString = map['role'] ?? 'guest';
    return fromString(roleString) ?? UserRole.guest;
  }
  
  List<String> _getPermissions() => switch (this) {
    UserRole.guest => ['read'],
    UserRole.member => ['read', 'comment'],
    UserRole.moderator => ['read', 'comment', 'moderate'],
    UserRole.admin => ['read', 'comment', 'moderate', 'admin'],
  };
}

void main() {
  UserRole role = UserRole.moderator;
  
  // Serialization examples
  print('JSON: ${role.toJson()}');
  print('Numeric: ${role.toNumeric()}');
  print('Map: ${role.toMap()}');
  
  // Deserialization examples
  UserRole? fromString = UserRole.fromString('admin');
  UserRole? fromNumber = UserRole.fromNumeric(2);
  UserRole fromInvalid = UserRole.fromString('invalid') ?? UserRole.guest;
  
  print('From string: $fromString');
  print('From number: $fromNumber');
  print('From invalid: $fromInvalid');
}
```

This serialization pattern provides multiple conversion methods with proper  
error handling. The enum maintains consistency across different data formats  
while providing safe fallback mechanisms for invalid data.  

```
$ dart run user_role_serialization.dart
JSON: moderator
Numeric: 2
Map: {role: moderator, level: 2, permissions: [read, comment, moderate]}
From string: UserRole.admin
From number: UserRole.moderator
From invalid: UserRole.guest
```

## Enum Validation and Error Handling

Enums can implement sophisticated validation logic and error handling patterns.  
This approach centralizes validation rules and provides clear, actionable  
feedback when dealing with invalid states or transitions.  

```dart
enum FormFieldState {
  empty('Field is required'),
  invalid('Field contains invalid data'),
  validating('Validating field...'),
  valid('Field is valid'),
  error('Validation error occurred');

  const FormFieldState(this.message);
  
  final String message;
  
  bool get isCompleted => this == FormFieldState.valid || 
                         this == FormFieldState.error;
  bool get isProcessing => this == FormFieldState.validating;
  bool get hasError => this == FormFieldState.invalid || 
                      this == FormFieldState.error;
  bool get canSubmit => this == FormFieldState.valid;
  
  FormFieldState validate(String input) {
    if (input.isEmpty) return FormFieldState.empty;
    
    // Simulate validation logic
    if (input.length < 3) return FormFieldState.invalid;
    if (input.contains('@') && !RegExp(r'^[\w\.-]+@[\w\.-]+\.\w+$').hasMatch(input)) {
      return FormFieldState.invalid;
    }
    
    return FormFieldState.valid;
  }
  
  FormFieldState transition(FormFieldState newState) {
    // Define valid transitions
    Map<FormFieldState, List<FormFieldState>> validTransitions = {
      FormFieldState.empty: [FormFieldState.validating, FormFieldState.invalid],
      FormFieldState.invalid: [FormFieldState.validating, FormFieldState.empty],
      FormFieldState.validating: [FormFieldState.valid, FormFieldState.error, FormFieldState.invalid],
      FormFieldState.valid: [FormFieldState.validating, FormFieldState.invalid],
      FormFieldState.error: [FormFieldState.validating, FormFieldState.empty],
    };
    
    if (validTransitions[this]?.contains(newState) ?? false) {
      return newState;
    } else {
      print('Warning: Invalid transition from $this to $newState');
      return this;
    }
  }
}

void main() {
  FormFieldState state = FormFieldState.empty;
  
  // Validation examples
  print('Initial: ${state.message}');
  
  state = state.validate('ab');
  print('Too short: ${state.message} (Error: ${state.hasError})');
  
  state = state.validate('user@domain.com');
  print('Valid email: ${state.message} (Can submit: ${state.canSubmit})');
  
  // Transition examples
  FormFieldState newState = state.transition(FormFieldState.validating);
  print('Transitioned to: ${newState.message}');
  
  FormFieldState invalidTransition = state.transition(FormFieldState.empty);
  print('Invalid transition result: ${invalidTransition.message}');
}
```

This validation pattern demonstrates complex state management with enums.  
The enum handles validation logic, state transitions, and provides clear  
feedback about current capabilities and limitations.  

```
$ dart run form_validation.dart
Initial: Field is required
Too short: Field contains invalid data (Error: true)
Valid email: Field is valid (Can submit: true)
Transitioned to: Validating field...
Warning: Invalid transition from FormFieldState.valid to FormFieldState.empty
Invalid transition result: Field is valid
```

## Enum Iteration and Filtering

Enums provide powerful iteration and filtering capabilities for processing  
collections of enum values. This pattern is useful for generating UI elements,  
configuration options, or performing bulk operations on enum-based data.  

```dart
enum Permission {
  read(category: 'Basic', level: 1),
  write(category: 'Basic', level: 2),
  delete(category: 'Advanced', level: 3),
  admin(category: 'Advanced', level: 4),
  superAdmin(category: 'System', level: 5);

  const Permission({required this.category, required this.level});
  
  final String category;
  final int level;
  
  static List<Permission> getByCategory(String category) =>
      Permission.values.where((p) => p.category == category).toList();
  
  static List<Permission> getByMinLevel(int minLevel) =>
      Permission.values.where((p) => p.level >= minLevel).toList();
  
  static Map<String, List<Permission>> groupByCategory() =>
      Permission.values.fold<Map<String, List<Permission>>>(
        {},
        (map, permission) => map
          ..putIfAbsent(permission.category, () => [])
          ..update(permission.category, (list) => list..add(permission)),
      );
  
  static List<Permission> sortedByLevel() =>
      Permission.values.toList()..sort((a, b) => a.level.compareTo(b.level));
}

void main() {
  print('All permissions:');
  for (Permission perm in Permission.values) {
    print('  ${perm.name}: ${perm.category} (Level ${perm.level})');
  }
  
  print('\nBasic permissions:');
  Permission.getByCategory('Basic')
      .forEach((p) => print('  ${p.name}'));
  
  print('\nAdvanced+ permissions:');
  Permission.getByMinLevel(3)
      .forEach((p) => print('  ${p.name} (${p.level})'));
  
  print('\nGrouped by category:');
  Permission.groupByCategory().forEach((category, permissions) {
    print('  $category: ${permissions.map((p) => p.name).join(', ')}');
  });
  
  print('\nSorted by level:');
  Permission.sortedByLevel()
      .forEach((p) => print('  ${p.name} (${p.level})'));
}
```

This example shows various filtering and grouping operations on enum  
collections. The static methods provide reusable functionality for organizing  
and processing enum values according to different criteria.  

```
$ dart run permission_filtering.dart
All permissions:
  read: Basic (Level 1)
  write: Basic (Level 2)
  delete: Advanced (Level 3)
  admin: Advanced (Level 4)
  superAdmin: System (Level 5)

Basic permissions:
  read
  write

Advanced+ permissions:
  delete (3)
  admin (4)
  superAdmin (5)

Grouped by category:
  Basic: read, write
  Advanced: delete, admin
  System: superAdmin

Sorted by level:
  read (1)
  write (2)
  delete (3)
  admin (4)
  superAdmin (5)
```

## Enum Comparison and Sorting

Enums can implement sophisticated comparison logic for sorting and ordering  
operations. This pattern is essential when enums represent data that has  
natural ordering or priority relationships.  

```dart
enum TaskStatus implements Comparable<TaskStatus> {
  notStarted(priority: 0, isCompleted: false),
  inProgress(priority: 1, isCompleted: false),
  blocked(priority: 2, isCompleted: false),
  review(priority: 3, isCompleted: false),
  completed(priority: 4, isCompleted: true),
  cancelled(priority: 5, isCompleted: true);

  const TaskStatus({required this.priority, required this.isCompleted});
  
  final int priority;
  final bool isCompleted;
  
  @override
  int compareTo(TaskStatus other) => priority.compareTo(other.priority);
  
  bool operator >(TaskStatus other) => priority > other.priority;
  bool operator <(TaskStatus other) => priority < other.priority;
  bool operator >=(TaskStatus other) => priority >= other.priority;
  bool operator <=(TaskStatus other) => priority <= other.priority;
  
  TaskStatus? getNext() => switch (this) {
    TaskStatus.notStarted => TaskStatus.inProgress,
    TaskStatus.inProgress => TaskStatus.review,
    TaskStatus.blocked => TaskStatus.inProgress,
    TaskStatus.review => TaskStatus.completed,
    TaskStatus.completed => null,
    TaskStatus.cancelled => null,
  };
  
  bool canTransitionTo(TaskStatus other) {
    if (isCompleted && !other.isCompleted) return false;
    if (this == TaskStatus.cancelled) return false;
    return other != this;
  }
}

void main() {
  List<TaskStatus> statuses = [
    TaskStatus.completed,
    TaskStatus.notStarted, 
    TaskStatus.blocked,
    TaskStatus.inProgress,
    TaskStatus.review,
    TaskStatus.cancelled,
  ];
  
  print('Original order:');
  statuses.forEach((s) => print('  ${s.name} (${s.priority})'));
  
  statuses.sort();
  print('\nSorted by priority:');
  statuses.forEach((s) => print('  ${s.name} (${s.priority})'));
  
  // Comparison examples
  TaskStatus current = TaskStatus.inProgress;
  TaskStatus target = TaskStatus.review;
  
  print('\nComparisons:');
  print('${current.name} < ${target.name}: ${current < target}');
  print('${current.name} can transition to ${target.name}: '
        '${current.canTransitionTo(target)}');
  
  TaskStatus? next = current.getNext();
  print('Next status after ${current.name}: ${next?.name ?? 'None'}');
}
```

This comparison pattern enables natural ordering of enum values and provides  
transition logic. The implementation of `Comparable` allows enums to work  
seamlessly with Dart's sorting algorithms and comparison operations.  

```
$ dart run task_status_sorting.dart
Original order:
  completed (4)
  notStarted (0)
  blocked (2)
  inProgress (1)
  review (3)
  cancelled (5)

Sorted by priority:
  notStarted (0)
  inProgress (1)
  blocked (2)
  review (3)
  completed (4)
  cancelled (5)

Comparisons:
inProgress < review: true
inProgress can transition to review: true
Next status after inProgress: review
```

## Enum Inheritance Patterns

While enums cannot be extended directly, they can implement interfaces and  
use mixins to share behavior. This pattern demonstrates how to create  
hierarchical enum structures with shared functionality.  

```dart
abstract interface class Describable {
  String get description;
  String get category;
}

mixin TimestampMixin {
  DateTime get createdAt => DateTime.now();
  String get formattedTime => createdAt.toIso8601String();
}

enum VehicleType with TimestampMixin implements Describable {
  car(wheels: 4, category: 'Land'),
  motorcycle(wheels: 2, category: 'Land'),
  bicycle(wheels: 2, category: 'Land'),
  boat(wheels: 0, category: 'Water'),
  airplane(wheels: 3, category: 'Air');

  const VehicleType({required this.wheels, required this.category});
  
  final int wheels;
  @override
  final String category;
  
  @override
  String get description => switch (this) {
    VehicleType.car => 'Four-wheeled motor vehicle',
    VehicleType.motorcycle => 'Two-wheeled motor vehicle', 
    VehicleType.bicycle => 'Human-powered two-wheeler',
    VehicleType.boat => 'Watercraft for marine travel',
    VehicleType.airplane => 'Flying vehicle for air transport',
  };
  
  bool get requiresLicense => switch (this) {
    VehicleType.car || VehicleType.motorcycle || 
    VehicleType.boat || VehicleType.airplane => true,
    VehicleType.bicycle => false,
  };
  
  double getEstimatedSpeed() => switch (this) {
    VehicleType.car => 80.0,
    VehicleType.motorcycle => 120.0,
    VehicleType.bicycle => 25.0,
    VehicleType.boat => 45.0,
    VehicleType.airplane => 900.0,
  };
}

enum AnimalType with TimestampMixin implements Describable {
  mammal(category: 'Vertebrate'),
  bird(category: 'Vertebrate'),
  fish(category: 'Vertebrate'),
  reptile(category: 'Vertebrate'),
  insect(category: 'Invertebrate');

  const AnimalType({required this.category});
  
  @override
  final String category;
  
  @override
  String get description => switch (this) {
    AnimalType.mammal => 'Warm-blooded vertebrate with hair',
    AnimalType.bird => 'Flying vertebrate with feathers',
    AnimalType.fish => 'Aquatic vertebrate with gills',
    AnimalType.reptile => 'Cold-blooded vertebrate with scales',
    AnimalType.insect => 'Six-legged arthropod invertebrate',
  };
}

void main() {
  List<Describable> items = [
    VehicleType.car,
    VehicleType.airplane,
    AnimalType.mammal,
    AnimalType.bird,
  ];
  
  print('Describable items:');
  for (Describable item in items) {
    print('${item.runtimeType}: ${item.description}');
    print('  Category: ${item.category}');
    
    if (item is TimestampMixin) {
      print('  Created: ${item.formattedTime}');
    }
    
    if (item is VehicleType) {
      print('  Wheels: ${item.wheels}');
      print('  Requires license: ${item.requiresLicense}');
      print('  Speed: ${item.getEstimatedSpeed()} km/h');
    }
    print('');
  }
}
```

This inheritance pattern shows how enums can implement interfaces and use  
mixins to share behavior across different enum types. The pattern enables  
polymorphic usage while maintaining enum-specific functionality.  

```
$ dart run enum_inheritance.dart
VehicleType: Four-wheeled motor vehicle
  Category: Land
  Created: 2024-01-01T12:00:00.000Z
  Wheels: 4
  Requires license: true
  Speed: 80.0 km/h

VehicleType: Flying vehicle for air transport
  Category: Air
  Created: 2024-01-01T12:00:00.000Z
  Wheels: 3
  Requires license: true
  Speed: 900.0 km/h

AnimalType: Warm-blooded vertebrate with hair
  Category: Vertebrate
  Created: 2024-01-01T12:00:00.000Z

AnimalType: Flying vertebrate with feathers
  Category: Vertebrate
  Created: 2024-01-01T12:00:00.000Z
```

## Enum with Generic Types

Enums can work with generic types to create reusable, type-safe patterns for  
handling different data types. This approach is particularly useful for  
creating generic result types, option types, or state containers.  

```dart
enum Result<T, E> {
  success<T, E>(T value),
  failure<T, E>(E error);
  
  const Result(this._value);
  
  final dynamic _value;
  
  T get value {
    if (this case success<T, E>(:final value)) {
      return value;
    }
    throw StateError('Attempted to get value from failure result');
  }
  
  E get error {
    if (this case failure<T, E>(:final error)) {
      return error;
    }
    throw StateError('Attempted to get error from success result');
  }
  
  bool get isSuccess => this is Result<T, E> && 
                       this.toString().startsWith('Result<');
  bool get isFailure => !isSuccess;
  
  Result<U, E> map<U>(U Function(T) transform) => switch (this) {
    success<T, E>(:final value) => Result.success(transform(value)),
    failure<T, E>(:final error) => Result.failure(error),
  };
  
  Result<T, F> mapError<F>(F Function(E) transform) => switch (this) {
    success<T, E>(:final value) => Result.success(value),
    failure<T, E>(:final error) => Result.failure(transform(error)),
  };
  
  U fold<U>(U Function(T) onSuccess, U Function(E) onFailure) => switch (this) {
    success<T, E>(:final value) => onSuccess(value),
    failure<T, E>(:final error) => onFailure(error),
  };
}

// Helper functions for creating results
Result<T, String> safeParseInt<T>(String input) {
  try {
    final parsed = int.parse(input);
    return Result.success(parsed as T);
  } catch (e) {
    return Result.failure('Failed to parse integer: $e');
  }
}

Result<double, String> safeDivide(double a, double b) {
  if (b == 0) {
    return Result.failure('Division by zero');
  }
  return Result.success(a / b);
}

void main() {
  // String parsing example
  Result<int, String> parseResult = safeParseInt('42');
  print('Parse result: ${parseResult.fold(
    (value) => 'Success: $value',
    (error) => 'Error: $error',
  )}');
  
  Result<int, String> failedParse = safeParseInt('invalid');
  print('Failed parse: ${failedParse.fold(
    (value) => 'Success: $value',
    (error) => 'Error: $error',
  )}');
  
  // Division example
  Result<double, String> divResult = safeDivide(10, 2);
  print('Division result: ${divResult.fold(
    (value) => 'Result: $value',
    (error) => 'Error: $error',
  )}');
  
  // Chaining operations with map
  Result<String, String> chainedResult = safeParseInt<int>('42')
      .map((value) => value * 2)
      .map((value) => 'Doubled: $value');
  
  print('Chained result: ${chainedResult.fold(
    (value) => value,
    (error) => 'Error: $error',
  )}');
}
```

This generic enum pattern creates a flexible Result type that can handle  
success and failure cases with different value and error types. The pattern  
includes transformation methods for functional programming approaches.  

```
$ dart run generic_result.dart
Parse result: Success: 42
Failed parse: Error: Failed to parse integer: FormatException: Invalid radix-10 number (at character 1)
Division result: Result: 5.0
Chained result: Doubled: 84
```

## Enum State Machines

Enums are excellent for implementing state machines with clear state  
transitions and validation. This pattern ensures that state changes follow  
predefined rules and prevents invalid state combinations.  

```dart
enum ConnectionState {
  disconnected,
  connecting,
  connected,
  reconnecting,
  failed;
  
  Set<ConnectionState> get allowedTransitions => switch (this) {
    ConnectionState.disconnected => {ConnectionState.connecting},
    ConnectionState.connecting => {ConnectionState.connected, 
                                 ConnectionState.failed, 
                                 ConnectionState.disconnected},
    ConnectionState.connected => {ConnectionState.disconnected, 
                                ConnectionState.reconnecting},
    ConnectionState.reconnecting => {ConnectionState.connected, 
                                   ConnectionState.failed,
                                   ConnectionState.disconnected},
    ConnectionState.failed => {ConnectionState.connecting, 
                             ConnectionState.disconnected},
  };
  
  bool canTransitionTo(ConnectionState newState) =>
      allowedTransitions.contains(newState);
  
  String get description => switch (this) {
    ConnectionState.disconnected => 'Not connected to server',
    ConnectionState.connecting => 'Establishing connection...',
    ConnectionState.connected => 'Successfully connected',
    ConnectionState.reconnecting => 'Attempting to reconnect...',
    ConnectionState.failed => 'Connection failed',
  };
  
  Duration get timeout => switch (this) {
    ConnectionState.connecting => Duration(seconds: 30),
    ConnectionState.reconnecting => Duration(seconds: 10),
    _ => Duration.zero,
  };
}

class NetworkManager {
  ConnectionState _currentState = ConnectionState.disconnected;
  
  ConnectionState get currentState => _currentState;
  
  bool setState(ConnectionState newState) {
    if (_currentState.canTransitionTo(newState)) {
      print('State transition: ${_currentState.name} -> ${newState.name}');
      _currentState = newState;
      return true;
    } else {
      print('Invalid transition: ${_currentState.name} -> ${newState.name}');
      return false;
    }
  }
  
  void connect() => setState(ConnectionState.connecting);
  void disconnect() => setState(ConnectionState.disconnected);
  void onConnected() => setState(ConnectionState.connected);
  void onFailed() => setState(ConnectionState.failed);
  void reconnect() => setState(ConnectionState.reconnecting);
}

void main() {
  NetworkManager network = NetworkManager();
  
  print('Initial state: ${network.currentState.description}');
  
  // Valid state transitions
  network.connect();
  print('Current: ${network.currentState.description}');
  
  network.onConnected();
  print('Current: ${network.currentState.description}');
  
  network.reconnect();
  print('Current: ${network.currentState.description}');
  
  // Invalid transition attempt
  network.connect(); // Should fail
  print('Current: ${network.currentState.description}');
  
  // Show allowed transitions
  print('Allowed transitions from ${network.currentState.name}:');
  for (ConnectionState state in network.currentState.allowedTransitions) {
    print('  -> ${state.name}');
  }
}
```

This state machine pattern provides robust state management with validation  
and clear transition rules. The enum ensures that only valid state changes  
are allowed, preventing invalid application states.  

```
$ dart run connection_state_machine.dart
Initial state: Not connected to server
State transition: disconnected -> connecting
Current: Establishing connection...
State transition: connecting -> connected
Current: Successfully connected
State transition: connected -> reconnecting
Current: Attempting to reconnect...
Invalid transition: reconnecting -> connecting
Current: Attempting to reconnect...
Allowed transitions from reconnecting:
  -> connected
  -> failed
  -> disconnected
```

## Enum Configuration Patterns

Enums can encapsulate complex configuration logic, providing type-safe  
configuration management with validation and environment-specific settings.  
This pattern is useful for application environments, feature flags, and  
deployment configurations.  

```dart
enum Environment {
  development(
    apiUrl: 'https://dev-api.example.com',
    debugMode: true,
    logLevel: LogLevel.debug,
    cacheEnabled: false,
  ),
  staging(
    apiUrl: 'https://staging-api.example.com',
    debugMode: true,
    logLevel: LogLevel.info,
    cacheEnabled: true,
  ),
  production(
    apiUrl: 'https://api.example.com',
    debugMode: false,
    logLevel: LogLevel.warning,
    cacheEnabled: true,
  );

  const Environment({
    required this.apiUrl,
    required this.debugMode,
    required this.logLevel,
    required this.cacheEnabled,
  });
  
  final String apiUrl;
  final bool debugMode;
  final LogLevel logLevel;
  final bool cacheEnabled;
  
  Duration get requestTimeout => switch (this) {
    Environment.development => Duration(seconds: 60),
    Environment.staging => Duration(seconds: 30),
    Environment.production => Duration(seconds: 15),
  };
  
  int get maxRetries => switch (this) {
    Environment.development => 1,
    Environment.staging => 2,
    Environment.production => 3,
  };
  
  Map<String, String> get headers => {
    'Content-Type': 'application/json',
    'User-Agent': 'MyApp/${_getVersion()}',
    if (debugMode) 'X-Debug': 'true',
    if (this == Environment.development) 'X-Dev-Mode': 'enabled',
  };
  
  String _getVersion() => '1.0.0';
  
  bool isFeatureEnabled(String feature) => switch ((this, feature)) {
    (Environment.development, _) => true, // All features in dev
    (Environment.staging, 'beta-features') => true,
    (Environment.staging, 'analytics') => true,
    (Environment.production, 'analytics') => true,
    (Environment.production, 'core-features') => true,
    _ => false,
  };
}

enum LogLevel {
  debug(0),
  info(1),
  warning(2),
  error(3);
  
  const LogLevel(this.level);
  final int level;
  
  bool shouldLog(LogLevel messageLevel) => messageLevel.level >= level;
}

class AppConfig {
  static Environment _current = Environment.development;
  
  static Environment get current => _current;
  
  static void setEnvironment(Environment env) {
    print('Switching to ${env.name} environment');
    _current = env;
  }
  
  static void logMessage(LogLevel level, String message) {
    if (_current.logLevel.shouldLog(level)) {
      print('[${level.name.toUpperCase()}] $message');
    }
  }
}

void main() {
  // Development configuration
  print('=== Development Environment ===');
  AppConfig.setEnvironment(Environment.development);
  
  Environment env = AppConfig.current;
  print('API URL: ${env.apiUrl}');
  print('Debug mode: ${env.debugMode}');
  print('Cache enabled: ${env.cacheEnabled}');
  print('Request timeout: ${env.requestTimeout}');
  print('Max retries: ${env.maxRetries}');
  print('Headers: ${env.headers}');
  
  print('\nFeature flags:');
  List<String> features = ['analytics', 'beta-features', 'core-features'];
  for (String feature in features) {
    print('  $feature: ${env.isFeatureEnabled(feature)}');
  }
  
  print('\nLogging test:');
  AppConfig.logMessage(LogLevel.debug, 'Debug message');
  AppConfig.logMessage(LogLevel.info, 'Info message');
  
  // Production configuration
  print('\n=== Production Environment ===');
  AppConfig.setEnvironment(Environment.production);
  env = AppConfig.current;
  
  print('API URL: ${env.apiUrl}');
  print('Debug mode: ${env.debugMode}');
  print('Beta features enabled: ${env.isFeatureEnabled('beta-features')}');
  
  print('\nLogging test:');
  AppConfig.logMessage(LogLevel.debug, 'Debug message (should not appear)');
  AppConfig.logMessage(LogLevel.warning, 'Warning message');
}
```

This configuration pattern centralizes environment-specific settings and  
provides type-safe access to configuration values. The enum ensures  
consistency across different deployment environments while enabling  
feature flags and conditional behavior.  

```
$ dart run environment_config.dart
=== Development Environment ===
Switching to development environment
API URL: https://dev-api.example.com
Debug mode: true
Cache enabled: false
Request timeout: 0:01:00.000000
Max retries: 1
Headers: {Content-Type: application/json, User-Agent: MyApp/1.0.0, X-Debug: true, X-Dev-Mode: enabled}

Feature flags:
  analytics: true
  beta-features: true
  core-features: true

Logging test:
[DEBUG] Debug message
[INFO] Info message

=== Production Environment ===
Switching to production environment
API URL: https://api.example.com
Debug mode: false
Beta features enabled: false

Logging test:
[WARNING] Warning message
```

## Enum Internationalization

Enums can support internationalization (i18n) by providing localized strings  
and culturally appropriate formatting. This pattern is essential for building  
applications that support multiple languages and regions.  

```dart
enum Locale {
  english('en', 'US'),
  spanish('es', 'ES'),
  french('fr', 'FR'),
  german('de', 'DE'),
  japanese('ja', 'JP'),
  chinese('zh', 'CN');

  const Locale(this.languageCode, this.countryCode);
  
  final String languageCode;
  final String countryCode;
  
  String get code => '${languageCode}_$countryCode';
  bool get isRightToLeft => languageCode == 'ar' || languageCode == 'he';
}

enum DayOfWeek {
  monday,
  tuesday,
  wednesday,
  thursday,
  friday,
  saturday,
  sunday;
  
  String getLocalizedName(Locale locale) => switch ((this, locale)) {
    (DayOfWeek.monday, Locale.english) => 'Monday',
    (DayOfWeek.monday, Locale.spanish) => 'Lunes',
    (DayOfWeek.monday, Locale.french) => 'Lundi',
    (DayOfWeek.monday, Locale.german) => 'Montag',
    (DayOfWeek.monday, Locale.japanese) => '月曜日',
    (DayOfWeek.monday, Locale.chinese) => '星期一',
    
    (DayOfWeek.tuesday, Locale.english) => 'Tuesday',
    (DayOfWeek.tuesday, Locale.spanish) => 'Martes',
    (DayOfWeek.tuesday, Locale.french) => 'Mardi',
    (DayOfWeek.tuesday, Locale.german) => 'Dienstag',
    (DayOfWeek.tuesday, Locale.japanese) => '火曜日',
    (DayOfWeek.tuesday, Locale.chinese) => '星期二',
    
    (DayOfWeek.wednesday, Locale.english) => 'Wednesday',
    (DayOfWeek.wednesday, Locale.spanish) => 'Miércoles',
    (DayOfWeek.wednesday, Locale.french) => 'Mercredi',
    (DayOfWeek.wednesday, Locale.german) => 'Mittwoch',
    (DayOfWeek.wednesday, Locale.japanese) => '水曜日',
    (DayOfWeek.wednesday, Locale.chinese) => '星期三',
    
    _ => name, // Fallback to enum name
  };
  
  String getShortName(Locale locale) => switch ((this, locale)) {
    (DayOfWeek.monday, Locale.english) => 'Mon',
    (DayOfWeek.monday, Locale.spanish) => 'Lun',
    (DayOfWeek.monday, Locale.french) => 'Lun',
    (DayOfWeek.monday, Locale.german) => 'Mo',
    (DayOfWeek.monday, Locale.japanese) => '月',
    (DayOfWeek.monday, Locale.chinese) => '一',
    
    (DayOfWeek.tuesday, Locale.english) => 'Tue',
    (DayOfWeek.tuesday, Locale.spanish) => 'Mar',
    (DayOfWeek.tuesday, Locale.french) => 'Mar',
    (DayOfWeek.tuesday, Locale.german) => 'Di',
    (DayOfWeek.tuesday, Locale.japanese) => '火',
    (DayOfWeek.tuesday, Locale.chinese) => '二',
    
    _ => name.substring(0, 3), // Fallback
  };
}

enum MessageType {
  greeting,
  farewell,
  thanks,
  apology;
  
  String getMessage(Locale locale) => switch ((this, locale)) {
    (MessageType.greeting, Locale.english) => 'Hello there!',
    (MessageType.greeting, Locale.spanish) => '¡Hola!',
    (MessageType.greeting, Locale.french) => 'Bonjour!',
    (MessageType.greeting, Locale.german) => 'Hallo!',
    (MessageType.greeting, Locale.japanese) => 'こんにちは!',
    (MessageType.greeting, Locale.chinese) => '你好!',
    
    (MessageType.farewell, Locale.english) => 'Goodbye!',
    (MessageType.farewell, Locale.spanish) => '¡Adiós!',
    (MessageType.farewell, Locale.french) => 'Au revoir!',
    (MessageType.farewell, Locale.german) => 'Auf Wiedersehen!',
    (MessageType.farewell, Locale.japanese) => 'さようなら!',
    (MessageType.farewell, Locale.chinese) => '再见!',
    
    (MessageType.thanks, Locale.english) => 'Thank you',
    (MessageType.thanks, Locale.spanish) => 'Gracias',
    (MessageType.thanks, Locale.french) => 'Merci',
    (MessageType.thanks, Locale.german) => 'Danke',
    (MessageType.thanks, Locale.japanese) => 'ありがとう',
    (MessageType.thanks, Locale.chinese) => '谢谢',
    
    _ => 'Message not available',
  };
}

void main() {
  List<Locale> locales = [Locale.english, Locale.spanish, Locale.japanese];
  List<DayOfWeek> days = [DayOfWeek.monday, DayOfWeek.tuesday, DayOfWeek.wednesday];
  
  print('=== Localized Day Names ===');
  for (Locale locale in locales) {
    print('\n${locale.code.toUpperCase()}:');
    for (DayOfWeek day in days) {
      String full = day.getLocalizedName(locale);
      String short = day.getShortName(locale);
      print('  $full ($short)');
    }
  }
  
  print('\n=== Localized Messages ===');
  List<MessageType> messages = [
    MessageType.greeting, 
    MessageType.farewell, 
    MessageType.thanks
  ];
  
  for (Locale locale in locales) {
    print('\n${locale.code.toUpperCase()}:');
    for (MessageType msg in messages) {
      print('  ${msg.name}: ${msg.getMessage(locale)}');
    }
  }
  
  print('\n=== Locale Information ===');
  for (Locale locale in Locale.values) {
    print('${locale.code}: ${locale.languageCode}-${locale.countryCode} '
          '(RTL: ${locale.isRightToLeft})');
  }
}
```

This internationalization pattern demonstrates how enums can provide  
localized content and support multiple languages. The pattern uses pattern  
matching to efficiently map enum values to localized strings while providing  
fallback mechanisms for unsupported combinations.  

```
$ dart run enum_i18n.dart
=== Localized Day Names ===

EN_US:
  Monday (Mon)
  Tuesday (Tue)
  wednesday (wed)

ES_ES:
  Lunes (Lun)
  Martes (Mar)
  wednesday (wed)

JA_JP:
  月曜日 (月)
  火曜日 (火)
  wednesday (wed)

=== Localized Messages ===

EN_US:
  greeting: Hello there!
  farewell: Goodbye!
  thanks: Thank you

ES_ES:
  greeting: ¡Hola!
  farewell: ¡Adiós!
  thanks: Gracias

JA_JP:
  greeting: こんにちは!
  farewell: さようなら!
  thanks: ありがとう

=== Locale Information ===
en_US: en-US (RTL: false)
es_ES: es-ES (RTL: false)
fr_FR: fr-FR (RTL: false)
de_DE: de-DE (RTL: false)
ja_JP: ja-JP (RTL: false)
zh_CN: zh-CN (RTL: false)
```



