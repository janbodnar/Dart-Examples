


<h1>Dart loops</h1>


<p>
In this article we show how to create loops in Dart language. We create loops
with for and while statements. In addition, we present the break and continue
statements.
</p>

<p>
The <code>for</code> and <code>while</code> statement are used to create loops.
The <code>break</code> and <code>continue</code> statments are used to alter
the loop execution.
</p>

<p>
Loops are used to execute statements multiple times or to traverse containers.
</p>


<h2>Dart while loop</h2>

<p>
The <code>while</code> statement is a control flow statement that allows code to
be executed repeatedly based on a given boolean condition.
</p>

<p>
This is the general form of the <code>while</code> loop:
</p>

<pre class="compact">
while (expression)
{
    statement;
}
</pre>

<p>
The <code>while</code> keyword executes the statements inside the block enclosed
by the curly brackets. The statements are executed each time the expression is
evaluated to true.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  int i = 0;
  int sum = 0;

  while (i &lt; 10) {
    i++;
    sum += i;
  }

  print(sum);
}
</pre>

<p>
In the code example, we calculate the sum of values from a range of numbers.
</p>

<p>
The <code>while</code> loop has three parts. Initialization, testing and
updating. Each execution of the statement is called a cycle.
</p>

<pre class="explanation">
int i = 0;
</pre>

<p>
We initiate the <code>i</code> variable. It is used as a counter.
</p>

<pre class="explanation">
while (i &lt; 10) {
   ...
}
</pre>

<p>
The expression inside the round brackets following the <code>while</code>
keyword is the second phase: the testing. The statements in the body are
executed until the expression is evaluated to false.
</p>

<pre class="explanation">
i++;
</pre>

<p>
This is the last, third phase of the <code>while</code> loop: the updating. We
increment the counter. Note that improper handling of the <code>while</code>
loops may lead to endless cycles.
</p>

<pre class="compact">
$ dart main.dart
55
</pre>



<h2>Dart classic for loop</h2>

<p>
The classic for loop was taken from the C programming language. A for loop has
also three phases: initialization, condition and code block execution, and
incrementation.
</p>


<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  var sum = 0;

  for (int i = 0; i &lt; 10; i++) {
    sum += i;
  }

  print(sum);
}

</pre>

<p>
In this example, we sum values 0..9 and print the result to the console.
</p>

<pre class="explanation">
for (int i = 0; i &lt; 10; i++) {
  sum += i;
}
</pre>

<p>
In the first phase, we initiate the counter <code>i</code> to zero.
This phase is done only once. Next comes the condition <code>i &lt; 10</code>. 
If the condition is met, the statement inside the for block is executed. In the
third phase the counter is increased. Now we repeat the 2, 3 phases until the
condition is not met and the for loop is left. In our case, when the counter i
is equal to 10, the for loop stops executing.
</p>


<h2>Dart for loop List</h2>

<p>
A for loop can be used for traversal of containers such as lists or maps. From
the <code>length</code> property of the list we get its size.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  final planets = [
    "Mercury",
    "Venus",
    "Earth",
    "Mars",
    "Jupiter",
    "Saturn",
    "Uranus",
    "Pluto"
  ];

  for (int i = 0; i &lt; planets.length; i++) {
    print(planets[i]);
  }

  print("In reverse:");

  for (int i = planets.length - 1; i &gt;= 0; i--) {
    print(planets[i]);
  }
}
</pre>

<p>
We have a list holding the names of planets in our Solar System. Using two for
loops, we print the values in ascending and descending orders. 
</p>

<pre class="explanation">
for (int i = 0; i &lt; planets.length; i++) {
  print(planets[i]);
}
</pre>

<p>
The lists are accessed by zero-based indexing. The first item has index 0.
Therefore, the <code>i</code> variable is initialized to zero. The condition
checks if the <code>i</code> variable is less than the length of the list. In
the final phase, the <code>i</code> variable is incremented.
</p>

<pre class="explanation">
for (int i = planets.length - 1; i &gt;= 0; i--) {
  print(planets[i]);
}
</pre>

<p>
This for loop prints the elements of the list in reverse order. The
<code>i</code> counter is initialized to list size. Since the indexing is zero
based, the last element has index list size-1. The condition ensures that the
counter is greater or equal to zero. (List indexes cannot be negative). In the
third step, the <code>i</code> counter is decremented by one.
</p>

<pre class="compact">
$ dart main.dart
Mercury
Venus
Earth
Mars
Jupiter
Saturn
Uranus
Pluto
In reverse:
Pluto
Uranus
Saturn
Jupiter
Mars
Earth
Venus
Mercury
</pre>


<h2>Dart for loop Map</h2>

<p>
A for loop can be used to traverse a Map.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  final fruit = {1: 'Apple', 2: 'Banana', 3: 'Cherry', 4: 'Orange'};

  for (final key in fruit.keys) print(key);
  for (final value in fruit.values) print(value);

  for (final me in fruit.entries) {
    print("${me.key}: ${me.value}");
  }
}
</pre>

<p>
In the example, we use for loops to print al keys, values, and map entries
(key/value pairs).
</p>

<pre class="explanation">
for (var key in fruit.keys) print(key);
</pre>

<p>
In this for loop, we go through all map keys. If a for loop consists only of 
one statement, we can put the whole loop one a single line.
</p>

<pre class="compact">
$ dart main.dart 
1
2
3
4
Apple
Banana
Cherry
Orange
1: Apple
2: Banana
3: Cherry
4: Orange
</pre>


<h2>Dart for/in loop</h2>

<p>
The <code>for/in</code> simplifies traversing over collections of data. It has
no explicit counter. It goes through the array or collection one by one and the
current value is copied to a variable defined in the construct. 
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  final vals = [1, 2, 3, 4, 5];
  for (final e in vals) {
    print(e * e);
  }
}
</pre>

<p>
In the example, we use the for range to go through a list of numbers.
</p>

<pre class="explanation">
for (final e in vals) {
  print(e * e);
}
</pre>

<p>
We iterate through the list of numbers. The <code>e</code> is a temporary
variable that contains the current value of the list. The for statement goes 
through all the numbers and prints their squares to the console.
</p>

<pre class="compact">
$ dart main.dart
1
4
9
16
25
</pre>


<h2>Dart nested for loop</h2>

<p>
For statements can be nested; i.e. a for statement can be placed inside another
for statement. All cycles of a nested for loops are executed for each cycle of
the outer for loop. 
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  final a1 = ["A", "B", "C"];
  final a2 = ["A", "B", "C"];

  for (int i = 0; i &lt; a1.length; i++) {
    for (int j = 0; j &lt; a2.length; j++) {
      print(a1[i] + a2[j]);
    }
  }
}
</pre>

<p>
In this example, we create a cartesian product of two lists. 
</p>

<pre class="explanation">
for (int i = 0; i &lt; a1.length; i++) {
  for (int j = 0; j &lt; a2.length; j++) {
    print(a1[i] + a2[j]);
  }
}
</pre>

<p>
There is a nested for loop inside another parent for loop. The nested for loop
is executed fully for each of the cycles of the parent for loop. 
</p>

<pre class="compact">
$ dart main.dart
AA
AB
AC
BA
BB
BC
CA
CB
CC
</pre>


<h2>Dart break statement</h2>

<p>
The break statement can be used to terminate the execution of a loop created 
by <code>while</code> and <code>for</code> statements.
</p>

<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
import 'dart:math';

void main() {
  const int MAX = 30;

  while (true) {
    var num = new Random().nextInt(MAX);
    print("$num");

    if (num == 22) {
      break;
    }
  }

  print("\n");
}
</pre>

<p>
We define an endless while loop. We use the <code>break</code> statement to get
out of this loop. We choose a random value from 1 to 30. We print the value. If
the value equals to 22, we finish the endless while loop. 
</p>

<pre class="compact">
$ dart main.dart
21
4
20
20
22
</pre>


<h2>Dart continue statement</h2>

<p>
The <code>continue</code> statement is used to skip a part of the loop and
continue with the next iteration of the loop. In the following example, we print
a list of numbers that cannot be divided by 2 without a remainder. 
</p>


<div class="codehead">main.dart
  <i class="fas fa-copy copy-icon" onclick="copyCode(this)"></i>
</div>
<pre class="code">
void main() {
  int num = 0;

  while (num &lt; 1000) {
    num++;

    if ((num % 2) == 0) {
      continue;
    }

    print("$num");
  }

  print("\n");
}
</pre>

<p>
We iterate through numbers 1..999 with the while loop. 
</p>

<pre class="explanation">
if ((num % 2) == 0) {
  continue;
}
</pre>

<p>
If the expression <code>num % 2</code> returns 0, the number in question can be
divided by 2. The continue statement is executed and the rest of the cycle is
skipped. In our case, the last statement of the loop is skipped and the number
is not printed to the console. The next iteration is started. 
</p>

