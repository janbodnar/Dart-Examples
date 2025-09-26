
<h1>Dart Variables</h1>

<p class="last_mod">
last modified June 4, 2025
</p>

<p>
In Dart, variables are used to store and manipulate data in programs. They act
as named containers for values that can change during program execution.
</p>

<p>
Dart is statically typed but supports type inference, allowing both explicit and
implicit variable declarations. Variables must be declared before use and can be
initialized at declaration or later.
</p>

<h2>Variable Declaration and Initialization</h2>

<p>
Variables in Dart can be declared using the var keyword or explicit types. The
var keyword lets Dart infer the type automatically based on the assigned value.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  // Using var with type inference
  var name = 'Alice';
  var age = 30;
  
  // Explicit type declaration
  String country = 'Canada';
  double height = 1.75;
  
  print('$name is $age years old from $country');
  print('Height: $height meters');
}
</pre>

<p>
This example shows both inferred and explicit variable declarations. The var
keyword is used when we want Dart to infer the type, while explicit types make
the code more readable and enforce type safety.
</p>

<pre class="compact">
$ dart main.dart
Alice is 30 years old from Canada
Height: 1.75 meters
</pre>

<h2>Final and Const Variables</h2>

<p>
Dart provides final and const keywords for declaring variables that shouldn't
change after initialization. final variables can only be set once, while const
variables are compile-time constants.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  // final variable (runtime constant)
  final currentYear = DateTime.now().year;
  
  // const variable (compile-time constant)
  const pi = 3.14159;
  const maxAttempts = 3;
  
  print('Current year: $currentYear');
  print('PI value: $pi');
  print('Maximum attempts: $maxAttempts');
  
  // This would cause an error:
  // pi = 3.14;
}
</pre>

<p>
final variables are initialized when accessed, while const variables must be
initialized with constant values at compile time. Both prevent reassignment.
</p>

<pre class="compact">
$ dart main.dart
Current year: 2025
PI value: 3.14159
Maximum attempts: 3
</pre>

<h2>Dynamic and Object Types</h2>

<p>
Dart provides dynamic and Object types for variables that can hold any type.
dynamic disables static type checking, while Object is the root of Dart's type
system.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  dynamic dynamicValue = 'Hello';
  print('dynamicValue: $dynamicValue (${dynamicValue.runtimeType})');
  
  dynamicValue = 42;
  print('dynamicValue: $dynamicValue (${dynamicValue.runtimeType})');
  
  Object objectValue = 'World';
  print('objectValue: $objectValue (${objectValue.runtimeType})');
  
  // This would cause a runtime error:
  // print(objectValue.length);
  
  if (objectValue is String) {
    print('Length: ${objectValue.length}');
  }
}
</pre>

<p>
dynamic allows changing type and accessing members without type checking. Object
requires type checking before member access. Use dynamic sparingly as it reduces
type safety.
</p>

<pre class="compact">
$ dart main.dart
dynamicValue: Hello (String)
dynamicValue: 42 (int)
objectValue: World (String)
Length: 5
</pre>

<h2>Nullable Variables</h2>

<p>
With null safety, variables must be explicitly declared as nullable using ? to
hold null values. Non-nullable variables must always contain a value.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  // Non-nullable variable
  String name = 'Bob';
  // name = null; // Compile error
  
  // Nullable variable
  String? nickname = null;
  nickname = 'Bobby';
  
  print('Name: $name');
  print('Nickname: $nickname');
  
  // Null-aware operators
  int? age;
  int actualAge = age ?? 25;
  print('Age: $actualAge');
  
  // Null assertion operator (!)
  String? maybeString = 'Hello';
  print(maybeString!.length);
}
</pre>

<p>
This demonstrates null safety features. The ?? operator provides default values
for null cases, while ! asserts a value is non-null. Null safety helps prevent
null reference errors.
</p>

<pre class="compact">
$ dart main.dart
Name: Bob
Nickname: Bobby
Age: 25
5
</pre>

<h2>Variable Scope</h2>

<p>
Variables in Dart have lexical scope, meaning they're only accessible within the
block they're declared in. Global variables are accessible throughout the program.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
// Global variable
int globalCounter = 0;

void incrementCounter() {
  // Local variable
  int localCounter = 0;
  
  globalCounter++;
  localCounter++;
  
  print('Global: $globalCounter, Local: $localCounter');
}

void main() {
  incrementCounter();
  incrementCounter();
  
  // This would cause an error:
  // print(localCounter);
  
  print('Final global counter: $globalCounter');
}
</pre>

<p>
globalCounter is accessible everywhere, while localCounter is only accessible
within incrementCounter(). Each function call gets a new localCounter instance.
</p>

<pre class="compact">
$ dart main.dart
Global: 1, Local: 1
Global: 2, Local: 1
Final global counter: 2
</pre>

<h2>Wildcard Variables</h2>

<p>
Dart 3.8 introduces <strong>wildcard variables</strong>, allowing developers to
use the underscore (<code>_</code>) as a placeholder for values they wish to
ignore. Wildcard variables can be used in a variety of contexts, such as pattern
matching, destructuring assignments, and function parameters, wherever a value
is present but not needed. This feature makes code more concise, expressive, and
self-documenting by clearly indicating which values are intentionally unused.
Wildcards help eliminate unnecessary variable names and reduce clutter, making
code easier to read and maintain.
</p>

<p>
Wildcard variables are especially powerful in pattern matching scenarios, such
as <code>switch</code> statements, destructuring tuples or collections, and when
defining callbacks or lambda functions where some parameters are not required.
By using <code>_</code>, developers can focus on just the values they care
about, while safely ignoring the rest. This not only avoids compiler warnings
about unused variables, but also communicates intent to other developers
reviewing the code.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  // Destructuring with wildcard
  var (a, _, c) = (1, 2, 3);
  print('a: $a, c: $c'); // Output: a: 1, c: 3

  // Pattern matching with wildcard
  var list = [10, 20, 30];
  switch (list) {
    case [_, int second, _]:
      print('Second element: $second');
  }

  // Function parameter wildcard
  void printFirst(int first, _) {
    print('First: $first');
  }
  printFirst(42, 'unused');

  // Using wildcards in for-each loops
  var pairs = [(1, 'one'), (2, 'two'), (3, 'three')];
  for (var (_, word) in pairs) {
    print(word); // Prints: one, two, three
  }

  // Ignoring multiple values in nested patterns
  var nested = (1, (2, 3));
  var (x, (_, z)) = nested;
  print('x: $x, z: $z'); // Output: x: 1, z: 3
}
</pre>

<p>
In the example above, the underscore is used to ignore the second value during
tuple destructuring, to match but ignore elements in a list pattern, and as a
function parameter that is not used. Additional examples show how wildcards can
be used in loops and nested patterns to further simplify code. 


<h2>Best Practices</h2>

<ul>
<li><strong>Type Inference:</strong> Prefer var for local variables when type is obvious.</li>
<li><strong>Immutability:</strong> Use final for variables that shouldn't change.</li>
<li><strong>Null Safety:</strong> Avoid nullable types unless null is a valid state.</li>
<li><strong>Scope:</strong> Keep variable scope as narrow as possible.</li>
<li><strong>Naming:</strong> Use descriptive names following lowerCamelCase.</li>
</ul>




<script src="/cfg/utils.js"></script>
</body>
</html>
