

<h1>Enum Types in Dart</h1>

 
<p>
Enums (enumerations) in Dart are a special kind of class used to represent a
fixed number of constant values. This tutorial covers Dart's enum types from
basic usage to advanced features like enhanced enums and pattern matching.
</p>

<h2>Dart Enum Overview</h2>

<p>
Dart's enum types allow you to define a set of named constant values. Enums are
useful for representing a fixed set of related options, such as days of the week,
traffic light states, or user roles. Dart's enum system is simple yet powerful,
providing built-in properties and methods that make working with enums easy and
efficient.
</p>

<p>
Enums in Dart can be basic, where they simply represent a set of named
constants, or enhanced, where they can include properties, methods, and
constructors. Dart also supports pattern matching with enums, allowing for
concise and expressive switch statements and expressions. 
</p>

<table class="compact">
<thead>
<tr>
    <th>Feature</th>
    <th>Description</th>
    <th>Example</th>
</tr>
</thead>
<tbody>
<tr>
    <td>Basic Enum</td>
    <td>Simple set of named constants</td>
    <td><code>enum Color { red, green, blue }</code></td>
</tr>
<tr>
    <td>Enhanced Enum</td>
    <td>Enums with properties and methods</td>
    <td><code>enum Status { active('A'), inactive('I') }</code></td>
</tr>
<tr>
    <td>Switch Matching</td>
    <td>Pattern matching with enums</td>
    <td><code>switch(color) { case Color.red: ... }</code></td>
</tr>
<tr>
    <td>Enum Properties</td>
    <td>Built-in index and name access</td>
    <td><code>color.index, color.name</code></td>
</tr>
</tbody>
</table>

<h2>Basic Enum Declaration</h2>

<p>
The simplest form of enum in Dart declares a set of named constant values. These
enums are useful for representing a fixed set of related options where each
option is equally important and has no additional data.
</p>

<div class="codehead">basic_enum.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
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
</pre>

<p>
Basic enums automatically get several useful properties. The <code>index</code>
property returns the zero-based position of the enum value in its declaration.
The <code>name</code> property returns the string representation of the enum
value. The <code>values</code> constant provides a list of all enum values in
declaration order.
</p>

<pre class="compact">
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
</pre>

<h2>Enhanced Enums</h2>

<p>
Dart's enhanced enums allow adding properties, methods, and constructors to enum
declarations. This feature makes enums more powerful by enabling them to carry
additional data and behavior.
</p>

<div class="codehead">enhanced_enum.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
enum Planet {
  mercury(mass: 3.303e+23, radius: 2.4397e6),
  venus(mass: 4.869e+24, radius: 6.0518e6),
  earth(mass: 5.976e+24, radius: 6.37814e6),
  mars(mass: 6.421e+23, radius: 3.3972e6);
  
  final double mass;
  final double radius;
  double get gravity =&gt; 6.67430e-11 * mass / (radius * radius);
  
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
</pre>

<p>
Enhanced enums can declare final fields that must be initialized by a constant
constructor. They can also include getters and methods to provide additional
functionality. Each enum value must call the constructor with the required
parameters. The enhanced enum syntax maintains type safety while adding
significant expressive power.
</p>

<pre class="compact">
$ dart run enhanced_enum.dart
earth has mass 6.0e+24 kg and radius 6.4e+6 m
Surface gravity: 9.80 m/s²
We are on Earth!
</pre>

<h2>Pattern Matching with Enums</h2>

<p>
Dart's switch expressions and pattern matching work particularly well with enum
types. This allows for exhaustive checking of all possible enum values and
provides compile-time safety.
</p>

<div class="codehead">pattern_matching.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
enum TrafficLight {
  red,
  yellow,
  green
}

String getTrafficInstruction(TrafficLight light) {
  return switch (light) {
    TrafficLight.red =&gt; 'Stop',
    TrafficLight.yellow =&gt; 'Caution',
    TrafficLight.green =&gt; 'Go',
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
</pre>

<p>
When using switch expressions with enums, Dart can ensure that all cases are
handled. If a new enum value is added later, the compiler will flag any switch
statements that don't account for it. This exhaustive checking helps prevent
runtime errors from unhandled cases. The pattern matching syntax is concise and
clearly expresses the mapping between enum values and their corresponding
behavior.
</p>

<pre class="compact">
$ dart run pattern_matching.dart
Caution
TrafficLight.red: Stop
TrafficLight.yellow: Caution
TrafficLight.green: Go
</pre>

<h2>Enum Properties and Methods</h2>

<p>
All Dart enums automatically come with several useful properties and methods.
These built-in members make it easy to work with enum values without additional
boilerplate code.
</p>

<div class="codehead">enum_properties.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
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
</pre>

<p>
The <code>index</code> property returns the zero-based position of an enum value
in its declaration. The <code>name</code> property provides the string
representation of the enum value. The static <code>values</code> constant
contains all enum values in declaration order. The <code>asNameMap()</code>
method creates a lookup table from names to enum values, which is useful for
parsing strings into enum values.
</p>

<pre class="compact">
$ dart run enum_properties.dart
Index: 2
Name: east
All directions:
NORTH (0)
SOUTH (1)
EAST (2)
WEST (3)
Parsed direction: Direction.south
</pre>

<h2>Mixing Enums with Other Features</h2>

<p>
Dart enums work well with other language features like generics, extensions, and
factory constructors. This allows for powerful combinations that can solve
complex problems elegantly.
</p>

<div class="codehead">mixed_features.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
enum ApiStatus {
  success,
  failure,
  loading;

  factory ApiStatus.fromCode(int code) {
    return switch (code) {
      200 =&gt; ApiStatus.success,
      400 || 500 =&gt; ApiStatus.failure,
      _ =&gt; ApiStatus.loading,
    };
  }
}

extension ApiStatusExtension on ApiStatus {
  String get message =&gt; switch (this) {
    ApiStatus.success =&gt; 'Operation succeeded',
    ApiStatus.failure =&gt; 'Operation failed',
    ApiStatus.loading =&gt; 'Operation in progress',
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
</pre>

<p>
Factory constructors in enums enable alternative creation patterns, such as
parsing from other data types. Extensions can add functionality to enums without
modifying their original declaration. The combination of these features with
pattern matching results in clean, maintainable code that clearly expresses the
program's intent while maintaining type safety.
</p>



