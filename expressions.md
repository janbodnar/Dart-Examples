
<h1>Dart Expressions</h1>
 
<p>
Expressions form the core of Dart programs, enabling computations that yield
values. This tutorial explores Dart expressions, detailing operator types,
evaluation rules, and unique features like cascade notation.
</p>

<h2>Dart Expressions Overview</h2>

<p>
In Dart, an expression is a code construct that evaluates to a value, such as
literals, variables, operators, or function calls. Dart offers a diverse set of
operators and expression types, facilitating concise and expressive code for
various programming tasks.
</p>

<table class="compact">
  <thead>
    <tr>
      <th>Expression Type</th>
      <th>Description</th>
      <th>Etitle="Dart Code Example</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Literal</td>
      <td>Direct value representation</td>
      <td><code>5, 'hello', true</code></td>
    </tr>
    <tr>
      <td>Arithmetic</td>
      <td>Mathematical operations</td>
      <td><code>a + b, x * y</code></td>
    </tr>
    <tr>
      <td>Relational</td>
      <td>Comparison operations</td>
      <td><code>a &gt; b, x == y</code></td>
    </tr>
    <tr>
      <td>Logical</td>
      <td>Boolean operations</td>
      <td><code>a &amp;&amp; b, !flag</code></td>
    </tr>
    <tr>
      <td>Conditional</td>
      <td>Ternary operator</td>
      <td><code>a ? b : c</code></td>
    </tr>
    <tr>
      <td>Cascade</td>
      <td>Sequential operations</td>
      <td><code>obj..a()..b()</code></td>
    </tr>
    <tr>
      <td>Assignment</td>
      <td>Value assignment</td>
      <td><code>a = 5, b += 2</code></td>
    </tr>
  </tbody>
</table>

<h2>Literal Expressions</h2>

<p>
Literal expressions directly represent fixed values in code, supporting various
data types in Dart, including integers (e.g., 42), doubles (e.g., 3.14),
strings (using single, double, or triple quotes), booleans (true, false), lists,
maps, and the null value. These literals provide a straightforward way to embed
constant values in programs.
</p>

<div class="codehead">literals.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {

  // Numeric literals
  int integer = 42;
  double floating = 3.14159;

  // String literals
  String singles = 'Single quotes';
  String doubles = "Double quotes";
  String multiline = '''
    Multi-line
    string
  ''';

  // Boolean literals
  bool truthy = true;
  bool falsy = false;

  // List and Map literals
  List&lt;int&gt; numbers = [1, 2, 3];
  Map&lt;String, int&gt; ages = {'Alice': 25, 'Bob': 30};

  print(integer);
  print(floating);
  print(singles);
  print(doubles);
  print(multiline);
  print(truthy);
  print(falsy);
  print(numbers);
  print(ages);
}
</pre>

<p>
This example showcases various Dart literals, demonstrating how to define and
print integers, doubles, strings (including multiline), booleans, lists, and
maps, highlighting their direct use in code.
</p>

<pre class="compact">
$ dart run literals.dart
42
3.14159
Single quotes
Double quotes
    Multi-line
    string
  
[1, 2, 3]
{Alice: 25, Bob: 30}
</pre>

<h2>Arithmetic Expressions</h2>

<p>
Dart supports standard arithmetic operations through operators like addition
(+), subtraction (-), multiplication (*), division (/), integer division (~/),
modulo (%), and unary negation (-). The division operator always returns a
double, while increment (++) and decrement (--) operators modify values. These
operators enable mathematical computations, including mixed-type operations.
</p>

<div class="codehead">arithmetic.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {

  int a = 10;
  int b = 3;
  double c = 5.0;
  
  // Basic operations
  print('Addition: ${a + b}');
  print('Subtraction: ${a - b}');
  print('Multiplication: ${a * b}');
  print('Division: ${a / b}');
  print('Integer division: ${a ~/ b}');
  print('Modulo: ${a % b}');
  print('Unary minus: ${-a}');
  
  // Mixed type arithmetic
  print('Mixed addition: ${a + c}');
  
  // Increment/decrement
  int counter = 0;
  print('Post-increment: ${counter++}');
  print('After increment: $counter');
  print('Pre-decrement: ${--counter}');
}
</pre>

<p>
This example demonstrates arithmetic operations, including basic calculations,
mixed-type addition, and increment/decrement operations, illustrating how Dart
handles mathematical expressions.
</p>

<pre class="compact">
$ dart run arithmetic.dart
Addition: 13
Subtraction: 7
Multiplication: 30
Division: 3.3333333333333335
Integer division: 3
Modulo: 1
Unary minus: -10
Mixed addition: 15.0
Post-increment: 0
After increment: 1
Pre-decrement: 0
</pre>

<h2>Relational and Logical Expressions</h2>

<p>
Relational operators, such as equal (==), not equal (!=), greater than (&gt;),
and less than (&lt;), compare values to produce boolean results. Logical
operators, including AND (&amp;&amp;), OR (||), and NOT (!), combine boolean
expressions. Dart employs short-circuit evaluation, skipping unnecessary
operations (e.g., in && if the first operand is false), enhancing efficiency and
safety in logical expressions.
</p>

<div class="codehead">relational_logical.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {

  int a = 5;
  int b = 10;
  
  // Relational operators
  print('Equal: ${a == b}');
  print('Not equal: ${a != b}');
  print('Greater than: ${a &gt; b}');
  print('Less than: ${a &lt; b}');
  print('Greater or equal: ${a &gt;= b}');
  print('Less or equal: ${a &lt;= b}');
  
  // Logical operators
  bool x = true;
  bool y = false;
  
  print('AND: ${x &amp;&amp; y}');
  print('OR: ${x || y}');
  print('NOT: ${!x}');
  
  // Short-circuit evaluation
  String? name;
  bool isValid = name != null &amp;&amp; name.isNotEmpty;
  print('Is valid: $isValid');
}
</pre>

<p>
This example illustrates relational comparisons and logical operations,
including a demonstration of short-circuit evaluation with a nullable string,
showing how Dart ensures safe and efficient expression evaluation.
</p>

<pre class="compact">
$ dart run relational_logical.dart
Equal: false
Not equal: true
Greater than: false
Less than: true
Greater or equal: false
Less or equal: true
AND: false
OR: true
NOT: false
Is valid: false
</pre>

<h2>Conditional Expressions</h2>

<p>
Dart's conditional expressions provide concise decision-making constructs. The
ternary operator (?:) acts as a compact if-else statement, while the null-
coalescing operator (??) supplies default values for null variables. Null-aware
operators, such as conditional property access (?.) and null-aware assignment
(??=), ensure safe handling of nullable types, making code robust and succinct.
</p>

<div class="codehead">conditional.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {

  int age = 20;
  
  // Ternary operator
  String status = age &gt;= 18 ? 'Adult' : 'Minor';
  print('Status: $status');
  
  // Null-coalescing operator
  String? name;
  String displayName = name ?? 'Guest';
  print('Welcome, $displayName');
  
  // Conditional property access
  List&lt;int&gt;? numbers;
  int? first = numbers?.first;
  print('First number: $first');
  
  // Null-aware assignment
  name ??= 'Anonymous';
  print('Name: $name');
}
</pre>

<p>
This example showcases conditional expressions, including the ternary operator
for age-based status, null-coalescing for default names, and null-aware
operators for safe list access and assignment, demonstrating concise control
flow.
</p>

<pre class="compact">
$ dart run conditional.dart
Status: Adult
Welcome, Guest
First number: null
Name: Anonymous
</pre>

<h2>Cascade Notation</h2>

<p>
Dart's cascade notation (..) enables multiple operations on the same object in a
fluent manner, returning the original object after each operation. This feature
is particularly useful for builder-style APIs and simplifies code by reducing
repetitive object references, enhancing readability and maintainability in
sequential method calls.
</p>

<div class="codehead">cascade.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
class Person {
  String name = '';
  int age = 0;
  
  void greet() =&gt; print('Hello, $name!');
  void birthday() =&gt; age++;
}

void main() {

  // Without cascade
  Person p1 = Person();
  p1.name = 'Alice';
  p1.age = 30;
  p1.greet();
  
  // With cascade
  Person p2 = Person()
    ..name = 'Bob'
    ..age = 25
    ..greet()
    ..birthday();
    
  print('Bob\'s age: ${p2.age}');
  
  // Nested cascades
  StringBuffer sb = StringBuffer()
    ..write('Hello')
    ..write(' ')
    ..writeAll(['Dart', '!'], ' ');
  
  print(sb.toString());
}
</pre>

<p>
This example contrasts traditional and cascade notation for object operations,
using a Person class and StringBuffer to show how cascades streamline multiple
method calls and property assignments.
</p>

<pre class="compact">
$ dart run cascade.dart
Hello, Alice!
Hello, Bob!
Bob's age: 26
Hello Dart !
</pre>

<h2>Assignment Expressions</h2>

<p>
Assignment expressions in Dart store values in variables using the basic
assignment operator (=) or compound operators like +=, *=, and ~/=. These
expressions evaluate to the assigned value, enabling chained assignments.
Null-aware assignment (??=) assigns a value only if the variable is null,
providing a safe way to set defaults.
</p>

<div class="codehead">assignment.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {

  // Simple assignment
  int x = 5;
  print('x = $x');
  
  // Compound assignment
  x += 3;
  print('x += 3 → $x');
  
  x ~/= 2;
  print('x ~/= 2 → $x');
  
  // Assignment as expression
  int y;
  print('y = ${y = x * 2}');
  
  // Null-aware assignment
  int? z;
  z ??= 10;
  print('z = $z');
}
</pre>

<p>
This example demonstrates simple and compound operations, assignment as an
expression, and null-aware assignment, illustrating how Dart manages variable
updates and default values.
</p>

<pre class="compact">
$ dart run assignment.dart
x = 5
x += 3 → 8
x ~/= 2 → 4
y = 8
z = 10
</pre>

<h2>Expression Evaluation Order</h2>

<p>
Dart evaluates expressions based on operator precedence and associativity.
Operators like multiplication have higher precedence than addition, while
parentheses override default precedence. Most operators are left-associative,
processing from left to right, except for assignment and conditional operators,
which are right-associative rules, ensuring predictable computation order.
</p>

<div class="codehead">evaluation_order.dart
  <i class="title="evaluation_order.dart">Copy</a></i>
</div>
<pre class="code">
void main() {

  // Operator precedence
  int result = 2 + 3 * 4;
  print('2 + 3 * 4 = $result');
  
  // Parentheses change order
  result = (2 + 3) * 4;
  print('(2 + 3) * 4 = $result');
  
  // Left-associative operators
  result = 10 - 4 - 2;
  print('10 - 4 - 2 = $result');
  
  // Right-associative operators
  bool a = false, b = true, c = false;
  bool logical = a &amp;&amp; b || c;
  print('a &amp;&amp; b || c = $logical');
}
</pre>

<p>
This example illustrates operator precedence, the effect of parentheses, and
associativity in expression evaluation, showing how Dart processes complex
expressions systematically.
</p>

<pre class="compact">
$ dart run evaluation_order.dart
2 + 3 * 4 = 14
(2 + 3) * 4 = 20
10 - 4 - 2 = 4
a && b || c = false
</pre>

<h2>Bitwise Expressions</h2>

<p>
Dart's bitwise operators manipulate integer bits, enabling low-level operations
like setting flags or optimizing computations. Operators include AND (&amp;), OR
(|), XOR (^), NOT (~), left shift (&lt;&lt;), and right shift (&gt;&gt;). These
are useful in scenarios like graphics processing or protocol implementations,
where direct bit manipulation is required.
</p>

<div class="codehead">bitwise.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {

  int a = 5; // Binary: 0101
  int b = 3; // Binary: 0011
  
  // Bitwise operators
  print('Bitwise AND: ${a &amp; b}'); // 0101 &amp; 0011 = 0001
  print('Bitwise OR: ${a | b}'); // 0101 | 0011 = 0111
  print('Bitwise XOR: ${a ^ b}'); // 0101 ^ 0011 = 0110
  print('Bitwise NOT: ${~a}'); // ~0101 = ...1010 (inverts bits)
  print('Left shift: ${a &lt;&lt; 1}'); // 0101 &lt;&lt; 1 = 1010
  print('Right shift: ${a &gt;&gt; 1}'); // 0101 &gt;&gt; 1 = 0010
  
  // Using bitwise for flags
  int read = 1; // 0001
  int write = 2; // 0010
  int permissions = read | write; // 0011
  print('Has read: ${(permissions &amp; read) != 0}');
}
</pre>

<p>
This example demonstrates bitwise operations on integers, showing AND, OR, XOR,
NOT, and shift operations, along with a practical use case for managing
permission flags, highlighting their utility in low-level programming.
</p>

<pre class="compact">
$ dart run bitwise.dart
Bitwise AND: 1
Bitwise OR: 7
Bitwise XOR: 6
Bitwise NOT: -6
Left shift: 10
Right shift: 2
Has read: true
</pre>

<h2>Spread Operator Expressions</h2>

<p>
The spread operator (...) in Dart expands elements of a collection, enabling
concise merging or copying of lists, sets, or maps. The null-aware spread
operator (...?) safely handles null collections, preventing runtime errors. This
feature simplifies collection manipulation in expressions, enhancing code
readability and flexibility.
</p>

<div class="codehead">spread.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {

  // Spread operator with lists
  List&lt;int&gt; part1 = [1, 2];
  List&lt;int&gt; part2 = [3, 4];
  List&lt;int&gt; combined = [...part1, ...part2, 5];
  print('Combined list: $combined');
  
  // Null-aware spread
  List&lt;int&gt;? optional;
  List&lt;int&gt; safeList = [...part1, ...?optional, 6];
  print('Safe list: $safeList');
  
  // Spread with maps
  Map&lt;String, int&gt; map1 = {'a': 1, 'b': 2};
  Map&lt;String, int&gt; map2 = {'c': 3};
  Map&lt;String, int&gt; merged = {...map1, ...map2, 'd': 4};
  print('Merged map: $merged');
  
  // Nested spread
  List&lt;List&lt;int&gt;&gt; nested = [
    [1, 2],
    [3, 4]
  ];
  List&lt;int&gt; flattened = [...nested[0], ...nested[1]];
  print('Flattened: $flattened');
}
</pre>

<p>
This example showcases the spread operator for combining lists and maps, the
null-aware spread for safe handling of nullable collections, and flattening
nested lists, demonstrating its power in collection expressions.
</p>

<pre class="compact">
$ dart run spread.dart
Combined list: [1, 2, 3, 4, 5]
Safe list: [1, 2, 6]
Merged map: {a: 1, b: 2, c: 3, d: 4}
Flattened: [1, 2, 3, 4]
</pre>

<h2>Switch Expressions</h2>

<p>
Dart's <code>switch</code> expressions provide a concise way to select a value
based on multiple conditions. Unlike traditional <code>switch</code> statements,
switch expressions can be used directly in assignments and return values, making
code more expressive and reducing boilerplate. Dart supports pattern matching
and null safety in switch expressions, allowing for powerful and readable
branching logic.
</p>

<div class="codehead">switch_expression.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {

  var grade = 'B';
  var result = switch (grade) {
    'A' =&gt; 'Excellent',
    'B' =&gt; 'Good',
    'C' =&gt; 'Average',
    'D' =&gt; 'Below average',
    'F' =&gt; 'Fail',
    _ =&gt; 'Unknown',
  };
  print('Grade: $grade, Result: $result');

  // Pattern matching with types
  Object value = 42;
  var type = switch (value) {
    int i =&gt; 'Integer',
    double d =&gt; 'Double',
    String s =&gt; 'String',
    _ =&gt; 'Other',
  };
  print('Type: $type');
}
</pre>

<p>
This example demonstrates Dart's switch expressions for value selection and type
pattern matching. The first switch maps a grade to a description, while the
second uses type patterns to determine the type of a value. Switch expressions
improve code clarity and reduce the need for verbose if-else chains.
</p>




<script src="/cfg/utils.js"></script>
</body>
</html>
