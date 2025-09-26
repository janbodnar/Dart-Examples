
<h1>Dart Records</h1>


<p>
In this article, we explore how to use <strong>records</strong> in the Dart
programming language. Records provide a concise way to group multiple values
into a single unit without requiring a custom class. They are immutable,
ordered, and have a fixed size, making them an efficient alternative to
traditional data structures.
</p>

<p>
Records in Dart function similarly to <em>tuples</em> in other programming
languages. They allow developers to return multiple values from a function,
store related data conveniently, and maintain clean, readable code. Unlike
lists, which offer dynamic size and mutability, records are designed to be
lightweight and predictable. 
</p>

<p>
A key advantage of using records is improved type safety and structure. Each
field in a record maintains its distinct type, ensuring that data remains
properly categorized. This makes records particularly useful when working with
temporary, grouped data in scenarios such as function outputs and intermediate
calculations. 
</p>

<p>
By leveraging records, Dart developers can write more expressive, concise, and
efficient code without unnecessary boilerplate. In the following sections, we
will demonstrate how to create and work with records effectively.
</p>


<h2>Dart Records Simple Example</h2>

<p>
The following example demonstrates how to create and use a simple record in Dart.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  // Creating a record
  final person = ('John', 30, true);

  // Accessing record elements
  print('Name: ${person.$1}');
  print('Age: ${person.$2}');
  print('Is Active: ${person.$3}');
}
</pre>

<p>
In this program, we create a record containing a name, age, and active status.
We then access and print each element of the record.
</p>

<pre class="explanation">
final person = ('John', 30, true);
</pre>

<p>
We create a record with three elements: a string, an integer, and a boolean.
</p>

<pre class="explanation">
print('Name: ${person.$1}');
print('Age: ${person.$2}');
print('Is Active: ${person.$3}');
</pre>

<p>
We access the elements of the record using the <code>$1</code>, <code>$2</code>, and <code>$3</code> syntax.
</p>

<pre class="compact">
$ dart main.dart
Name: John
Age: 30
Is Active: true
</pre>


<h2>Dart Records with Named Fields</h2>

<p>
Records can also have named fields, which make the code more readable and self-explanatory.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  // Creating a record with named fields
  final person = (name: 'Alice', age: 25, isActive: false);

  // Accessing record elements using named fields
  print('Name: ${person.name}');
  print('Age: ${person.age}');
  print('Is Active: ${person.isActive}');
}
</pre>

<p>
In this program, we create a record with named fields and access the elements
using the field names.
</p>

<pre class="explanation">
final person = (name: 'Alice', age: 25, isActive: false);
</pre>

<p>
We create a record with named fields: <code>name</code>, <code>age</code>, and
<code>isActive</code>.
</p>

<pre class="explanation">
print('Name: ${person.name}');
print('Age: ${person.age}');
print('Is Active: ${person.isActive}');
</pre>

<p>
We access the elements of the record using the named fields.
</p>

<pre class="compact">
$ dart main.dart
Name: Alice
Age: 25
Is Active: false
</pre>


<h2>Dart Records in Functions</h2>

<p>
Records are particularly useful for returning multiple values from a function.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
(String, int) getUserInfo() {
  return ('Bob', 40);
}

void main() {
  final userInfo = getUserInfo();
  print('Name: ${userInfo.$1}');
  print('Age: ${userInfo.$2}');
}
</pre>

<p>
In this program, we define a function that returns a record containing a name
and age. We then call the function and access the returned values.
</p>

<pre class="explanation">
(String, int) getUserInfo() {
  return ('Bob', 40);
}
</pre>

<p>
We define a function that returns a record with two elements: a string and an integer.
</p>

<pre class="explanation">
final userInfo = getUserInfo();
print('Name: ${userInfo.$1}');
print('Age: ${userInfo.$2}');
</pre>

<p>
We call the function and access the returned record's elements.
</p>

<pre class="compact">
$ dart main.dart
Name: Bob
Age: 40
</pre>


<h2>Dart Records with Pattern Matching</h2>

<p>
Dart supports pattern matching, which allows you to destructure records into
individual variables.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  final person = ('Charlie', 35, true);

  // Destructuring the record
  final (name, age, isActive) = person;

  print('Name: $name');
  print('Age: $age');
  print('Is Active: $isActive');
}
</pre>

<p>
In this program, we use pattern matching to destructure a record into individual
variables.
</p>

<pre class="explanation">
final (name, age, isActive) = person;
</pre>

<p>
We destructure the record into three variables: <code>name</code>,
<code>age</code>, and <code>isActive</code>.
</p>

<pre class="explanation">
print('Name: $name');
print('Age: $age');
print('Is Active: $isActive');
</pre>

<p>
We print the values of the destructured variables.
</p>

<pre class="compact">
$ dart main.dart
Name: Charlie
Age: 35
Is Active: true
</pre>


<h2>Dart Records as Map Keys</h2>

<p>
Records can be used as keys in Dart maps. Records with the same values are
considered equal and have the same hash code.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
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
</pre>

<p>
In this example, <code>record1</code> and <code>record3</code> are equal, so
they refer to the same map entry.
</p>


<h2>Dart Records in Collections</h2>

<p>
You can store records in lists and sets. Dart uses value equality for records,
so duplicates are not added to sets.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  final r1 = ('x', 10);
  final r2 = ('y', 20);
  final r3 = ('x', 10);

  final list = [r1, r2, r3];
  print(list);

  final set = {r1, r2, r3};
  print(set);
}
</pre>

<p>
The set contains only two unique records, even though three were added.
</p>


<h2>Dart Records with Positional and Named Fields</h2>

<p>
A record can have both positional and named fields. You can access them using
their respective syntax.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  final data = ('A', 42, flag: true, description: 'sample');

  print('First: \'${data.$1}\'');
  print('Second: ${data.$2}');
  print('Flag: ${data.flag}');
  print('Description: ${data.description}');
}
</pre>

<p>
This record has two positional fields and two named fields. Access positional
fields with <code>$1</code>, <code>$2</code> and named fields with their names.
</p>




<script src="/cfg/utils.js"></script>
</body>
</html>
