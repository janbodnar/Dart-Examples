
# Dart Loops

Loops enable repetitive execution of code blocks based on conditions or  
iteration over collections. This tutorial covers Dart's loop constructs  
including while, for, for-in loops, and control statements like break and  
continue for precise loop management.  

The `for` and `while` statements create loops that execute code blocks  
repeatedly. The `break` and `continue` statements provide control over loop  
execution, allowing you to exit loops early or skip iterations when specific  
conditions are met.  

Loops are essential for processing collections, performing calculations with  
repetitive operations, and implementing algorithms that require iterative  
processing of data structures.  

## While Loop

The `while` statement executes code repeatedly based on a boolean condition.  
It continues execution as long as the condition evaluates to true, making it  
ideal for situations where the number of iterations is unknown beforehand.  

This is the general form of the `while` loop:  

```dart
while (expression) {
    statement;
}
```

The `while` keyword executes the statements inside the block enclosed by curly  
brackets. The statements are executed each time the expression evaluates to  
true, creating a loop that continues until the condition becomes false.  

```dart
void main() {
  int i = 0;
  int sum = 0;

  while (i < 10) {
    i++;
    sum += i;
  }

  print(sum);
}
```

This example calculates the sum of integers from 1 to 10 using a while loop.  
The loop continues executing until the counter reaches 10, demonstrating the  
three essential phases of loop construction.  

The `while` loop has three distinct phases: initialization, testing, and  
updating. Each complete execution of the loop body is called an iteration  
or cycle, and proper management of these phases prevents infinite loops.  

```dart
int i = 0;
```

We initialize the `i` variable to zero. This variable serves as our loop  
counter, tracking how many iterations have been completed and controlling  
when the loop should terminate.  

```dart
while (i < 10) {
   // loop body
}
```

The expression inside the parentheses following the `while` keyword represents  
the testing phase. The loop body executes repeatedly as long as this condition  
evaluates to true, terminating when it becomes false.  

```dart
i++;
```

This represents the updating phase of the `while` loop. We increment the  
counter to ensure the loop progresses toward termination. Improper handling  
of this phase can lead to infinite loops that never terminate.  




## Classic For Loop

The classic for loop, inherited from C programming, provides a compact way to  
iterate with explicit initialization, condition checking, and increment  
operations. It offers precise control over loop execution with all three  
phases clearly defined in the loop header.  

```dart
void main() {
  var sum = 0;

  for (int i = 0; i < 10; i++) {
    sum += i;
  }

  print(sum);
}
```

This example sums values from 0 to 9 and prints the result. The for loop  
combines all three phases (initialization, condition, increment) in a single  
line, making the loop structure immediately apparent to readers.  

```dart
for (int i = 0; i < 10; i++) {
  sum += i;
}
```

In the initialization phase, we declare and initialize counter `i` to zero.  
This occurs only once before the first iteration. Next, the condition `i < 10`  
is evaluated before each iteration. The loop body executes when true, then the  
increment `i++` occurs, and the cycle repeats until the condition becomes false.  




## For Loop with Lists

A for loop can traverse containers such as lists or maps using indexed access.  
The `length` property provides the collection size, enabling iteration through  
all elements using zero-based indexing for precise element access.  

```dart
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

  for (int i = 0; i < planets.length; i++) {
    print(planets[i]);
  }

  print("In reverse:");

  for (int i = planets.length - 1; i >= 0; i--) {
    print(planets[i]);
  }
}
```

This example demonstrates list traversal in both forward and reverse directions  
using traditional for loops. We iterate through planet names, printing them in  
ascending order first, then in descending order to show different iteration  
patterns with the same data structure.  

```dart
for (int i = 0; i < planets.length; i++) {
  print(planets[i]);
}
```

Lists use zero-based indexing where the first element has index 0. The counter  
`i` initializes to zero, the condition checks if `i` is less than the list  
length, and the increment phase increases `i` by one each iteration, ensuring  
access to every list element.  

```dart
for (int i = planets.length - 1; i >= 0; i--) {
  print(planets[i]);
}
```

This loop prints elements in reverse order by initializing the counter to the  
last valid index (length - 1), checking that it remains non-negative, and  
decrementing after each iteration until reaching the first element.  




## For Loop with Maps

A for loop can traverse Map collections by iterating over keys, values, or  
key-value pairs (entries). This provides flexible ways to access different  
aspects of map data depending on your processing requirements.  

```dart
void main() {
  final fruit = {1: 'Apple', 2: 'Banana', 3: 'Cherry', 4: 'Orange'};

  for (final key in fruit.keys) print(key);
  for (final value in fruit.values) print(value);

  for (final me in fruit.entries) {
    print("${me.key}: ${me.value}");
  }
}
```

This example demonstrates three different approaches to map iteration: accessing  
all keys, all values, and complete key-value pairs. Each approach serves  
different use cases depending on what information you need from the map.  

```dart
for (final key in fruit.keys) print(key);
```

This for loop iterates through all map keys using the `keys` property. When a  
for loop contains only one statement, you can write the entire construct on a  
single line for brevity and improved readability.  




## For-In Loop

The `for-in` loop simplifies iteration over collections by automatically  
handling element access without explicit counters or indexing. It provides  
clean, readable code for processing each element in a collection sequentially.  

```dart
void main() {
  final vals = [1, 2, 3, 4, 5];
  for (final e in vals) {
    print(e * e);
  }
}
```

This example uses the for-in construct to iterate through a list of numbers,  
calculating and printing the square of each value. The loop automatically  
handles the iteration mechanics, letting you focus on element processing.  

```dart
for (final e in vals) {
  print(e * e);
}
```

We iterate through the list of numbers using the for-in syntax. The variable  
`e` temporarily holds the current element value during each iteration. The  
loop automatically progresses through all elements, eliminating index  
management and boundary checking concerns.  




## Nested For Loops

For loops can be nested, placing one loop inside another to create multi-  
dimensional iteration patterns. The inner loop executes completely for each  
iteration of the outer loop, enabling processing of complex data structures  
or generating combinations of elements.  

```dart
void main() {
  final a1 = ["A", "B", "C"];
  final a2 = ["A", "B", "C"];

  for (int i = 0; i < a1.length; i++) {
    for (int j = 0; j < a2.length; j++) {
      print(a1[i] + a2[j]);
    }
  }
}
```

This example creates a Cartesian product of two lists, combining every element  
from the first list with every element from the second list. Nested loops are  
particularly useful for matrix operations, table generation, and combinatorial  
problems.  

```dart
for (int i = 0; i < a1.length; i++) {
  for (int j = 0; j < a2.length; j++) {
    print(a1[i] + a2[j]);
  }
}
```

The nested structure shows the inner loop (with counter `j`) executing  
completely for each iteration of the outer loop (with counter `i`). This  
creates a systematic traversal pattern that processes all possible  
combinations of the two collections.  




## Break Statement

The break statement terminates loop execution immediately, providing an exit  
mechanism for `while`, `for`, and `for-in` loops. This is essential for  
implementing conditional loop termination based on specific criteria or events  
during execution.  

```dart
import 'dart:math';

void main() {
  const int MAX = 30;

  while (true) {
    var num = Random().nextInt(MAX);
    print("$num");

    if (num == 22) {
      break;
    }
  }

  print("Loop terminated!");
}
```

This example demonstrates break usage in an infinite while loop. We generate  
random numbers until we encounter the value 22, at which point the break  
statement terminates the loop immediately, demonstrating controlled exit from  
potentially endless loops.  




## Continue Statement

The `continue` statement skips the remaining code in the current iteration and  
jumps to the next iteration of the loop. This provides selective processing  
within loops, allowing you to skip certain elements or conditions without  
terminating the entire loop.  

```dart
void main() {
  int num = 0;

  while (num < 20) {
    num++;

    if ((num % 2) == 0) {
      continue;
    }

    print("$num");
  }
}
```

This example prints only odd numbers from 1 to 20 by using continue to skip  
even numbers. When a number is divisible by 2, the continue statement bypasses  
the print statement and moves to the next iteration, demonstrating selective  
processing within loops.  

```dart
if ((num % 2) == 0) {
  continue;
}
```

When the expression `num % 2` equals 0, the number is even. The continue  
statement executes, skipping the remaining loop body code (the print statement)  
and beginning the next iteration. This allows filtering elements during  
iteration without complex conditional logic.  



## Do-While Loop

The `do-while` loop executes the loop body at least once before checking the  
condition, ensuring that the code block runs regardless of the initial  
condition state. This guarantees execution when you need the loop body to run  
at least one time.  

```dart
void main() {
  int count = 0;
  
  do {
    print('Count: $count');
    count++;
  } while (count < 3);
  
  print('Final count: $count');
  
  // Example where condition is initially false
  int value = 10;
  do {
    print('This executes once: $value');
    value++;
  } while (value < 5); // false from the start
}
```

This example demonstrates the key difference between `do-while` and regular  
`while` loops. The do-while loop body executes first, then checks the  
condition, guaranteeing at least one execution even when the condition is  
initially false.  



## ForEach Method

The `forEach` method provides a functional approach to iteration, accepting a  
function that executes for each element. This method emphasizes element  
processing over loop mechanics, creating cleaner code for simple operations  
on collections.  

```dart
void main() {
  final colors = ['red', 'green', 'blue', 'yellow'];
  
  // Basic forEach
  colors.forEach((color) => print('Color: $color'));
  
  // forEach with index (using indexed approach)
  colors.asMap().forEach((index, color) {
    print('$index: $color');
  });
  
  // Complex processing with forEach
  final numbers = [1, 2, 3, 4, 5];
  var sum = 0;
  numbers.forEach((num) {
    sum += num * num; // sum of squares
  });
  print('Sum of squares: $sum');
}
```

The forEach method passes each element to the provided function, handling  
iteration internally. The `asMap().forEach` pattern provides both index and  
value when you need positional information during processing.  



## Loop with Where Filter

The `where` method creates filtered iterations, processing only elements that  
meet specified criteria. This functional approach eliminates the need for  
conditional logic within loop bodies, creating more declarative and readable  
code for selective processing.  

```dart
void main() {
  final numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
  
  // Filter and iterate through even numbers
  numbers.where((num) => num % 2 == 0).forEach((even) {
    print('Even number: $even');
  });
  
  // Filter strings by length
  final words = ['cat', 'elephant', 'dog', 'butterfly', 'ant'];
  words.where((word) => word.length > 3).forEach((longWord) {
    print('Long word: $longWord');
  });
  
  // Complex filtering with multiple conditions
  final ages = [15, 22, 17, 30, 16, 25, 19];
  print('Adults:');
  ages.where((age) => age >= 18 && age < 65).forEach((adultAge) {
    print('  Age: $adultAge');
  });
}
```

The where method returns an iterable containing only elements that satisfy the  
predicate function. Combined with forEach, this creates powerful filtering and  
processing pipelines without explicit loop constructs or conditional statements.  



## Loop with Map Transformation

The `map` method transforms each element in a collection, creating a new  
iterable with modified values. This functional approach separates data  
transformation from iteration, enabling clean processing pipelines and  
immutable data handling patterns.  

```dart
void main() {
  final numbers = [1, 2, 3, 4, 5];
  
  // Transform to squares
  final squares = numbers.map((num) => num * num);
  print('Original: $numbers');
  print('Squares: ${squares.toList()}');
  
  // Transform strings to uppercase
  final names = ['alice', 'bob', 'charlie'];
  final upperNames = names.map((name) => name.toUpperCase()).toList();
  print('Uppercase names: $upperNames');
  
  // Complex transformation with objects
  final temperatures = [0, 25, 30, 100];
  final fahrenheit = temperatures.map((celsius) => (celsius * 9 / 5) + 32);
  
  print('Temperature conversions:');
  temperatures.asMap().forEach((index, celsius) {
    final f = fahrenheit.elementAt(index);
    print('  ${celsius}°C = ${f.toStringAsFixed(1)}°F');
  });
}
```

The map method creates a lazy iterable where transformations occur during  
iteration. Call `toList()` to create a concrete list from the transformed  
values when needed for storage or multiple iterations.  



## Loop with Reduce and Fold

The `reduce` and `fold` methods perform iterative accumulation operations,  
combining all elements into a single result. Reduce uses the first element as  
the initial value, while fold allows specifying a custom initial value and  
can handle empty collections safely.  

```dart
void main() {
  final numbers = [1, 2, 3, 4, 5];
  
  // Reduce examples
  final sum = numbers.reduce((a, b) => a + b);
  final product = numbers.reduce((a, b) => a * b);
  final max = numbers.reduce((a, b) => a > b ? a : b);
  
  print('Numbers: $numbers');
  print('Sum: $sum');
  print('Product: $product');
  print('Maximum: $max');
  
  // Fold examples with different initial values
  final concatenated = numbers.fold('Start:', (prev, element) => '$prev-$element');
  final sumWithInitial = numbers.fold(100, (prev, element) => prev + element);
  
  print('Concatenated: $concatenated');
  print('Sum with initial 100: $sumWithInitial');
  
  // Fold with complex objects
  final words = ['Hello', 'there', 'Dart', 'programmer'];
  final totalLength = words.fold(0, (total, word) => total + word.length);
  final sentence = words.fold('', (sentence, word) => 
      sentence.isEmpty ? word : '$sentence $word');
  
  print('Total character count: $totalLength');
  print('Sentence: $sentence');
}
```

Reduce requires a non-empty collection and uses the first element as the  
accumulator's initial value. Fold works with any collection size and accepts  
an explicit initial value, making it more flexible for complex accumulations.  



## Asynchronous Loop with Stream

Stream-based loops handle asynchronous data sequences, processing elements as  
they become available. This approach is essential for handling real-time data,  
file I/O, network responses, or any scenario where data arrives over time  
rather than all at once.  

```dart
import 'dart:async';

void main() async {
  print('Starting stream processing...');
  
  // Create a stream that emits values periodically
  Stream<int> numberStream = Stream.periodic(
    Duration(milliseconds: 500),
    (count) => count + 1
  ).take(5);
  
  // Process stream with await for loop
  await for (final number in numberStream) {
    print('Received: $number');
    
    // Simulate processing time
    await Future.delayed(Duration(milliseconds: 200));
    print('  Processed: ${number * number}');
  }
  
  print('Stream processing complete!');
  
  // Example with stream transformation
  print('\nStream transformation example:');
  final sourceStream = Stream.fromIterable([1, 2, 3, 4, 5]);
  
  await for (final value in sourceStream.map((n) => n * 2).where((n) => n > 4)) {
    print('Transformed value: $value');
  }
}
```

The `await for` loop processes stream elements asynchronously, waiting for each  
element to become available. This pattern is crucial for handling data streams,  
user input events, or any asynchronous sequence of operations.  



## Labeled Break and Continue

Labels provide precise control over nested loop execution, allowing break and  
continue statements to target specific loops rather than just the innermost  
one. This enables complex flow control in multi-level nested structures  
without additional flags or complex logic.  

```dart
void main() {
  print('Labeled break example:');
  
  outer: for (int i = 1; i <= 3; i++) {
    inner: for (int j = 1; j <= 3; j++) {
      if (i == 2 && j == 2) {
        print('Breaking outer loop at ($i, $j)');
        break outer; // Exits both loops
      }
      print('Processing ($i, $j)');
    }
  }
  
  print('\nLabeled continue example:');
  
  outerLoop: for (int i = 1; i <= 3; i++) {
    for (int j = 1; j <= 4; j++) {
      if (j == 3) {
        print('Continuing outer loop at ($i, $j)');
        continue outerLoop; // Skips to next iteration of outer loop
      }
      print('Processing ($i, $j)');
    }
    print('End of inner loop for i = $i');
  }
  
  print('\nComplex nested structure with multiple labels:');
  
  level1: for (int a = 1; a <= 2; a++) {
    level2: for (int b = 1; b <= 3; b++) {
      level3: for (int c = 1; c <= 3; c++) {
        if (a == 1 && b == 2 && c == 2) {
          print('Continue level2 from ($a, $b, $c)');
          continue level2;
        }
        if (a == 2 && b == 1 && c == 3) {
          print('Break level1 from ($a, $b, $c)');
          break level1;
        }
        print('($a, $b, $c)');
      }
    }
  }
}
```

Labels are identifiers followed by a colon, placed before loop statements.  
Break and continue statements can reference these labels to control execution  
flow precisely, enabling sophisticated control structures in complex nested  
scenarios without convoluted conditional logic.  



