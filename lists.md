<h1>Dart List</h1>

<p>
In Dart, List is an ordered collection of elements. It maintains insertion order
and allows duplicate values. Lists are similar to arrays in other languages.
</p>

<p>
Lists in Dart can be either fixed-length or growable. Growable lists can change
size dynamically. Lists are zero-indexed and support various operations.
</p>

<h2>Creating a List</h2>

<p>
There are several ways to create a List in Dart. The simplest is using list
literals with square brackets.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  // Using list literal
  var fruits = ['apple', 'banana', 'orange'];
  print(fruits);

  // Using List constructor
  var numbers = List&lt;int&gt;.filled(3, 0);
  numbers[0] = 1;
  numbers[1] = 2;
  numbers[2] = 3;
  print(numbers);
}
</pre>

<p>
The first example creates a growable list of strings. The second creates a
fixed-length list initialized with zeros. We then assign values to each index.
</p>

<pre class="compact">
$ dart main.dart
[apple, banana, orange]
[1, 2, 3]
</pre>

<h2>Accessing List Elements</h2>

<p>
List elements can be accessed using index notation. Dart provides various methods
for safe element access.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
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
</pre>

<p>
We demonstrate different ways to access list elements. elementAt is safer than
[] as it throws RangeError for invalid indices. first and last are convenient.
</p>

<pre class="compact">
$ dart main.dart
red
yellow
blue
First: red
Last: yellow
Length: 4
</pre>

<h2>Adding and Removing Elements</h2>

<p>
Growable lists support dynamic addition and removal of elements. Here are common
operations.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
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
</pre>

<p>
add appends a single element, while addAll adds multiple. insert places an
element at a specific position. Various remove methods delete elements.
</p>

<pre class="compact">
$ dart main.dart
After adding: [0, 1, 2, 3, 4, 5, 6]
After removing: [1, 2, 3, 4]
After clear: []
</pre>

<h2>List Iteration</h2>

<p>
Dart provides multiple ways to iterate through list elements. Here are the most
common patterns.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  var languages = ['Dart', 'Python', 'Java', 'C#'];

  // for loop
  print('for loop:');
  for (var i = 0; i &lt; languages.length; i++) {
    print(languages[i]);
  }

  // for-in loop
  print('\nfor-in loop:');
  for (var lang in languages) {
    print(lang);
  }

  // forEach
  print('\nforEach:');
  languages.forEach((lang) =&gt; print(lang));

  // map
  print('\nmap:');
  var upper = languages.map((lang) =&gt; lang.toUpperCase());
  print(upper);
}
</pre>

<p>
The for loop provides index access. for-in is simpler for element access.
forEach is functional style. map transforms elements to a new iterable.
</p>

<pre class="compact">
$ dart main.dart
for loop:
Dart
Python
Java
C#

for-in loop:
Dart
Python
Java
C#

forEach:
Dart
Python
Java
C#

map:
(DART, PYTHON, JAVA, C#)
</pre>

<h2>List Manipulation</h2>

<p>
Dart lists support various manipulation operations like sorting, filtering, and
slicing.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  var numbers = [5, 2, 8, 1, 7, 3, 9, 4, 6];

  // Sorting
  numbers.sort();
  print('Sorted: $numbers');

  // Reversing
  var reversed = numbers.reversed.toList();
  print('Reversed: $reversed');

  // Filtering
  var even = numbers.where((n) =&gt; n % 2 == 0).toList();
  print('Even numbers: $even');

  // Sublist
  var middle = numbers.sublist(2, 5);
  print('Middle sublist: $middle');

  // Shuffling
  numbers.shuffle();
  print('Shuffled: $numbers');
}
</pre>

<p>
sort modifies the list in place. reversed returns an iterable that needs toList.
where filters elements. sublist extracts a portion. shuffle randomizes order.
</p>

<pre class="compact">
$ dart main.dart
Sorted: [1, 2, 3, 4, 5, 6, 7, 8, 9]
Reversed: [9, 8, 7, 6, 5, 4, 3, 2, 1]
Even numbers: [2, 4, 6, 8]
Middle sublist: [3, 4, 5]
Shuffled: [5, 9, 1, 6, 3, 8, 2, 4, 7]
</pre>

<h2>Spread Operator and Collection If/For</h2>

<p>
Dart supports spread operator (...) and collection if/for in list literals.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
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
</pre>

<p>
The spread operator expands a list into individual elements. Collection if
conditionally includes elements. Collection for transforms elements during
list creation.
</p>


<h2>Best Practices</h2>

<ul>
<li><strong>Type Safety:</strong> Always specify list types for better code clarity.</li>
<li><strong>Growable Lists:</strong> Prefer growable lists unless fixed size is needed.</li>
<li><strong>Immutable Lists:</strong> Use List.unmodifiable for read-only lists.</li>
<li><strong>Performance:</strong> Consider LinkedList for frequent insertions/deletions.</li>
</ul>



<script src="/cfg/utils.js"></script>
</body>
</html>
