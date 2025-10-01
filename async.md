# Dart Asynchronous Programming

Asynchronous programming in Dart enables non-blocking operations that allow  
applications to remain responsive while performing time-consuming tasks.  
Instead of waiting for operations like file I/O, network requests, or timers  
to complete, asynchronous code lets other work proceed concurrently.  

Dart provides powerful abstractions for asynchronous programming through  
Futures and Streams. Futures represent single asynchronous values that will  
be available at some point in the future, while Streams represent sequences  
of asynchronous events over time. The async-await syntax makes asynchronous  
code readable and maintainable by allowing developers to write asynchronous  
operations in a synchronous style.  

Understanding asynchronous programming is essential for modern Dart  
development, particularly for Flutter applications where responsiveness is  
critical. Proper async handling prevents UI freezing, enables efficient  
resource utilization, and provides better user experiences through smooth,  
non-blocking interactions.  

## Creating a Future

Futures are the foundation of asynchronous programming in Dart. A Future  
represents a value that will be available at some point in the future.  

```dart
void main() {
  // Create a simple Future that completes immediately
  Future<String> simpleFuture = Future.value('Hello there!');
  
  // Create a Future with a computation
  Future<int> calculationFuture = Future(() {
    return 42 * 2;
  });
  
  // Create a delayed Future
  Future<String> delayedFuture = Future.delayed(
    Duration(seconds: 2),
    () => 'Completed after 2 seconds'
  );
  
  print('Futures created');
  
  // Access Future value using then
  simpleFuture.then((value) {
    print('Simple future: $value');
  });
  
  calculationFuture.then((value) {
    print('Calculation result: $value');
  });
  
  delayedFuture.then((value) {
    print('Delayed future: $value');
  });
  
  print('Main function continues...');
}
```

Creating Futures is straightforward using Future.value for immediate values,  
Future() constructor for computations, or Future.delayed for time-based  
operations. The then method registers callbacks to handle results when  
Futures complete.  

## Basic Future Then

The then method allows chaining operations on Future results, creating  
sequential asynchronous workflows that transform data step by step.  

```dart
void main() {
  // Chain multiple then operations
  Future.delayed(Duration(seconds: 1), () => 5)
    .then((value) {
      print('Initial value: $value');
      return value * 2;
    })
    .then((value) {
      print('After doubling: $value');
      return value + 10;
    })
    .then((value) {
      print('After adding 10: $value');
      return value.toString();
    })
    .then((value) {
      print('As string: "$value"');
    });
  
  // Then with different return types
  Future<int>.value(100)
    .then((number) => 'Number: $number')
    .then((text) => text.toUpperCase())
    .then((upper) => print('Final: $upper'));
  
  print('Started future chains');
}
```

The then method enables transformation chains where each step receives the  
previous result and can return a new value. This creates readable pipelines  
for data processing that execute sequentially as each Future completes.  

## Async Await Basics

The async-await syntax provides a more intuitive way to work with Futures,  
making asynchronous code look and behave like synchronous code.  

```dart
Future<String> fetchUserName() async {
  await Future.delayed(Duration(seconds: 1));
  return 'Alice';
}

Future<int> fetchUserAge() async {
  await Future.delayed(Duration(milliseconds: 500));
  return 28;
}

Future<void> displayUserInfo() async {
  print('Fetching user information...');
  
  String name = await fetchUserName();
  print('Name: $name');
  
  int age = await fetchUserAge();
  print('Age: $age');
  
  print('User information complete');
}

void main() async {
  await displayUserInfo();
  print('Done');
}
```

The async keyword marks functions as asynchronous, while await pauses  
execution until a Future completes, returning its value directly. This makes  
async code easier to read and maintain compared to then chains.  

## Future Error Handling

Proper error handling ensures asynchronous operations fail gracefully,  
providing clear error messages and recovery options.  

```dart
Future<int> riskyOperation() async {
  await Future.delayed(Duration(seconds: 1));
  throw Exception('Something went wrong!');
}

Future<int> safeOperation() async {
  await Future.delayed(Duration(seconds: 1));
  return 42;
}

void main() async {
  // Using try-catch with async-await
  try {
    int result = await riskyOperation();
    print('Result: $result');
  } catch (e) {
    print('Caught error: $e');
  }
  
  // Using catchError with then
  riskyOperation()
    .then((value) => print('Success: $value'))
    .catchError((error) => print('Error handler: $error'));
  
  // With finally equivalent
  try {
    await riskyOperation();
  } catch (e) {
    print('Error occurred: $e');
  } finally {
    print('Cleanup always runs');
  }
  
  await Future.delayed(Duration(seconds: 2));
}
```

Error handling in async code uses try-catch blocks with async-await or  
catchError method with Futures. The finally block or whenComplete method  
ensures cleanup code runs regardless of success or failure.  

## Multiple Futures with Future.wait

Future.wait executes multiple Futures concurrently and waits for all to  
complete, significantly improving performance for independent operations.  

```dart
Future<String> fetchFromServer1() async {
  await Future.delayed(Duration(seconds: 2));
  return 'Data from Server 1';
}

Future<String> fetchFromServer2() async {
  await Future.delayed(Duration(seconds: 1));
  return 'Data from Server 2';
}

Future<String> fetchFromServer3() async {
  await Future.delayed(Duration(milliseconds: 1500));
  return 'Data from Server 3';
}

void main() async {
  print('Starting parallel requests...');
  
  Stopwatch sw = Stopwatch()..start();
  
  // Wait for all futures to complete
  List<String> results = await Future.wait([
    fetchFromServer1(),
    fetchFromServer2(),
    fetchFromServer3(),
  ]);
  
  sw.stop();
  
  print('All requests completed in ${sw.elapsedMilliseconds}ms');
  for (int i = 0; i < results.length; i++) {
    print('Result ${i + 1}: ${results[i]}');
  }
}
```

Future.wait runs multiple Futures concurrently rather than sequentially,  
reducing total wait time to the longest operation instead of the sum of all  
operations. This dramatically improves performance for independent async tasks.  

## Future.any for Race Conditions

Future.any returns the first Future to complete successfully, useful for  
implementing timeouts or choosing the fastest response from multiple sources.  

```dart
Future<String> slowServer() async {
  await Future.delayed(Duration(seconds: 3));
  return 'Slow server response';
}

Future<String> fastServer() async {
  await Future.delayed(Duration(seconds: 1));
  return 'Fast server response';
}

Future<String> mediumServer() async {
  await Future.delayed(Duration(seconds: 2));
  return 'Medium server response';
}

void main() async {
  print('Racing servers...');
  
  // Get the first successful result
  String firstResult = await Future.any([
    slowServer(),
    fastServer(),
    mediumServer(),
  ]);
  
  print('Winner: $firstResult');
  
  // All other operations continue in background
  await Future.delayed(Duration(seconds: 4));
  print('All operations complete');
}
```

Future.any completes as soon as any Future succeeds, ignoring slower  
operations. This enables responsive applications that don't wait for slow  
sources while still accessing their data if they complete first.  

## Async Function Return Types

Async functions always return Futures, even when using void or explicit  
return types. Understanding return type semantics ensures proper type safety.  

```dart
// Returns Future<String>
Future<String> getString() async {
  await Future.delayed(Duration(milliseconds: 100));
  return 'Hello there!';
}

// Returns Future<int>
Future<int> getNumber() async {
  await Future.delayed(Duration(milliseconds: 100));
  return 42;
}

// Returns Future<void> - no value returned
Future<void> performAction() async {
  await Future.delayed(Duration(milliseconds: 100));
  print('Action completed');
}

// Returns Future<dynamic> when no type specified
Future getAny() async {
  await Future.delayed(Duration(milliseconds: 100));
  return 'Could be anything';
}

void main() async {
  String str = await getString();
  print('String: $str');
  
  int num = await getNumber();
  print('Number: $num');
  
  await performAction();
  
  dynamic any = await getAny();
  print('Dynamic: $any');
}
```

Async functions automatically wrap return values in Futures. Type annotations  
specify the unwrapped return type, while the actual return type is always  
Future<T>. This enables type-safe async operations.  

## Creating Streams

Streams represent sequences of asynchronous events, enabling reactive  
programming patterns for data that arrives over time.  

```dart
import 'dart:async';

// Create a simple stream
Stream<int> countStream(int max) async* {
  for (int i = 1; i <= max; i++) {
    await Future.delayed(Duration(milliseconds: 500));
    yield i;
  }
}

// Create a stream from iterable
Stream<String> fromList() {
  return Stream.fromIterable(['A', 'B', 'C', 'D']);
}

// Create a periodic stream
Stream<DateTime> timeStream() {
  return Stream.periodic(
    Duration(seconds: 1),
    (count) => DateTime.now()
  ).take(5);
}

void main() async {
  print('Count stream:');
  await for (int value in countStream(5)) {
    print('  $value');
  }
  
  print('\nFrom list:');
  await for (String value in fromList()) {
    print('  $value');
  }
  
  print('\nTime stream:');
  await for (DateTime time in timeStream()) {
    print('  ${time.toString().substring(11, 19)}');
  }
}
```

Streams are created using async* generators with yield, Stream.fromIterable  
for existing data, or Stream.periodic for time-based events. The await for  
loop consumes stream values as they arrive.  

## Stream Listening

Stream listeners receive events as they occur, enabling reactive responses  
to asynchronous data flows.  

```dart
import 'dart:async';

Stream<int> numberStream() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(milliseconds: 500));
    yield i;
  }
}

void main() async {
  // Listen with callback
  StreamSubscription<int> subscription = numberStream().listen(
    (value) {
      print('Received: $value');
    },
    onError: (error) {
      print('Error: $error');
    },
    onDone: () {
      print('Stream complete');
    },
  );
  
  await Future.delayed(Duration(seconds: 3));
  
  // Second listener with await for
  print('\nUsing await for:');
  await for (int value in numberStream()) {
    print('Value: $value');
    if (value == 3) {
      print('Breaking early');
      break;
    }
  }
}
```

Stream.listen registers callbacks for data events, errors, and completion.  
StreamSubscription objects allow pausing, resuming, or canceling listeners.  
The await for loop provides a simpler alternative for sequential processing.  

## Stream Transformations

Stream transformers modify, filter, or combine stream events, creating  
powerful data processing pipelines.  

```dart
import 'dart:async';

Stream<int> sourceStream() async* {
  for (int i = 1; i <= 10; i++) {
    await Future.delayed(Duration(milliseconds: 100));
    yield i;
  }
}

void main() async {
  // Map transformation
  print('Mapped stream:');
  await for (int value in sourceStream().map((x) => x * 2)) {
    print('  $value');
  }
  
  // Filter transformation
  print('\nFiltered stream (even numbers):');
  await for (int value in sourceStream().where((x) => x % 2 == 0)) {
    print('  $value');
  }
  
  // Take transformation
  print('\nFirst 3 values:');
  await for (int value in sourceStream().take(3)) {
    print('  $value');
  }
  
  // Skip transformation
  print('\nSkip first 5:');
  await for (int value in sourceStream().skip(5)) {
    print('  $value');
  }
  
  // Chain multiple transformations
  print('\nChained (filter, map, take):');
  await for (int value in sourceStream()
      .where((x) => x % 2 == 0)
      .map((x) => x * 10)
      .take(3)) {
    print('  $value');
  }
}
```

Stream transformations create new streams with modified events. Common  
transformations include map for conversion, where for filtering, take for  
limiting, and skip for ignoring initial events. These operations can be  
chained for complex processing pipelines.  

## StreamController Basics

StreamController provides programmatic control over stream event generation,  
enabling custom async event sources.  

```dart
import 'dart:async';

void main() async {
  // Create a StreamController
  StreamController<String> controller = StreamController<String>();
  
  // Listen to the stream
  controller.stream.listen(
    (data) => print('Received: $data'),
    onError: (error) => print('Error: $error'),
    onDone: () => print('Stream closed'),
  );
  
  // Add events
  controller.add('Hello there!');
  controller.add('Welcome to Dart');
  controller.add('Async programming');
  
  await Future.delayed(Duration(milliseconds: 100));
  
  // Add error
  controller.addError('An error occurred');
  
  await Future.delayed(Duration(milliseconds: 100));
  
  // Close the stream
  controller.close();
  
  await Future.delayed(Duration(milliseconds: 100));
}
```

StreamController acts as a bridge between imperative code and reactive  
streams. Events are added using add(), errors with addError(), and the  
stream is closed with close(). This enables custom event sources.  

## Broadcast Streams

Broadcast streams allow multiple simultaneous listeners, unlike single-  
subscription streams that permit only one listener at a time.  

```dart
import 'dart:async';

void main() async {
  // Create broadcast stream controller
  StreamController<int> controller = StreamController<int>.broadcast();
  
  // Add first listener
  controller.stream.listen(
    (data) => print('Listener 1: $data'),
  );
  
  // Add second listener
  controller.stream.listen(
    (data) => print('Listener 2: $data'),
  );
  
  // Add third listener
  controller.stream.listen(
    (data) => print('Listener 3: $data'),
  );
  
  // Emit events - all listeners receive them
  for (int i = 1; i <= 3; i++) {
    controller.add(i);
    await Future.delayed(Duration(milliseconds: 500));
  }
  
  controller.close();
  await Future.delayed(Duration(milliseconds: 100));
}
```

Broadcast streams distribute events to all active listeners simultaneously.  
This enables pub-sub patterns where multiple components react to the same  
events without interfering with each other.  

## Stream Subscription Control

StreamSubscription objects provide fine-grained control over stream listening,  
including pausing, resuming, and canceling.  

```dart
import 'dart:async';

Stream<int> infiniteStream() async* {
  int count = 0;
  while (true) {
    await Future.delayed(Duration(milliseconds: 500));
    yield count++;
  }
}

void main() async {
  StreamSubscription<int> subscription = infiniteStream().listen(
    (value) => print('Value: $value'),
  );
  
  // Let it run for a bit
  await Future.delayed(Duration(seconds: 2));
  
  // Pause the subscription
  print('Pausing...');
  subscription.pause();
  
  await Future.delayed(Duration(seconds: 2));
  
  // Resume the subscription
  print('Resuming...');
  subscription.resume();
  
  await Future.delayed(Duration(seconds: 2));
  
  // Cancel the subscription
  print('Canceling...');
  await subscription.cancel();
  
  print('Subscription canceled');
}
```

StreamSubscription enables dynamic control over event reception. Pausing  
stops event delivery temporarily, resume restarts it, and cancel permanently  
stops listening and cleans up resources.  

## Async Error Handling with Try-Catch

Comprehensive error handling ensures robust async operations with proper  
recovery and resource cleanup.  

```dart
Future<int> fetchData(bool shouldFail) async {
  await Future.delayed(Duration(seconds: 1));
  if (shouldFail) {
    throw Exception('Fetch failed!');
  }
  return 42;
}

Future<void> processData() async {
  try {
    print('Fetching data...');
    int data = await fetchData(false);
    print('Success: $data');
    
    print('Fetching data (will fail)...');
    int data2 = await fetchData(true);
    print('This won\'t print: $data2');
    
  } on Exception catch (e) {
    print('Caught Exception: $e');
  } catch (e) {
    print('Caught other error: $e');
  } finally {
    print('Cleanup complete');
  }
}

void main() async {
  await processData();
}
```

Try-catch blocks handle async errors naturally with await. Specific error  
types can be caught with on clauses, while catch handles any error. Finally  
blocks ensure cleanup code always executes.  

## Future Timeout

Timeouts prevent operations from hanging indefinitely, improving application  
responsiveness and user experience.  

```dart
Future<String> slowOperation() async {
  await Future.delayed(Duration(seconds: 5));
  return 'Slow result';
}

Future<String> fastOperation() async {
  await Future.delayed(Duration(seconds: 1));
  return 'Fast result';
}

void main() async {
  // Operation that times out
  try {
    String result = await slowOperation().timeout(
      Duration(seconds: 2),
      onTimeout: () => 'Timeout fallback',
    );
    print('Result: $result');
  } catch (e) {
    print('Error: $e');
  }
  
  // Operation that completes in time
  try {
    String result = await fastOperation().timeout(
      Duration(seconds: 2),
    );
    print('Result: $result');
  } catch (e) {
    print('Error: $e');
  }
  
  // Timeout with error
  try {
    String result = await slowOperation().timeout(
      Duration(seconds: 2),
    );
    print('Result: $result');
  } on TimeoutException catch (e) {
    print('Operation timed out: $e');
  }
}
```

The timeout method limits how long a Future can run. If it exceeds the  
duration, either a TimeoutException is thrown or an onTimeout callback  
provides a fallback value. This prevents indefinite waits.  

## Async Generators

Async generators create streams declaratively using async* and yield,  
simplifying complex stream creation logic.  

```dart
Stream<int> countDown(int from) async* {
  for (int i = from; i > 0; i--) {
    await Future.delayed(Duration(seconds: 1));
    yield i;
  }
  yield 0;
}

Stream<String> fibonacci(int count) async* {
  int a = 0, b = 1;
  
  for (int i = 0; i < count; i++) {
    yield a.toString();
    await Future.delayed(Duration(milliseconds: 500));
    
    int next = a + b;
    a = b;
    b = next;
  }
}

void main() async {
  print('Countdown:');
  await for (int value in countDown(5)) {
    print('  $value');
  }
  
  print('\nFibonacci sequence:');
  await for (String value in fibonacci(10)) {
    print('  $value');
  }
}
```

Async generators use async* to mark stream-producing functions. The yield  
keyword emits values to the stream, with await enabling asynchronous delays  
between emissions. This creates readable stream sources.  

## Stream from Future

Converting Futures to Streams enables uniform handling of single and  
multiple async values.  

```dart
Future<String> fetchUserData() async {
  await Future.delayed(Duration(seconds: 1));
  return 'User: Alice';
}

void main() async {
  // Convert Future to Stream
  Stream<String> userStream = Stream.fromFuture(fetchUserData());
  
  await for (String data in userStream) {
    print('Received: $data');
  }
  
  // Multiple Futures to Stream
  Stream<int> numbersStream = Stream.fromFutures([
    Future.delayed(Duration(seconds: 1), () => 1),
    Future.delayed(Duration(milliseconds: 500), () => 2),
    Future.delayed(Duration(seconds: 2), () => 3),
  ]);
  
  print('\nNumbers from futures:');
  await for (int number in numbersStream) {
    print('  $number');
  }
}
```

Stream.fromFuture converts a single Future into a one-event stream, while  
Stream.fromFutures combines multiple Futures into a sequential stream. This  
enables treating async values uniformly.  

## Combining Streams

Stream combination merges multiple sources into unified event flows for  
complex reactive patterns.  

```dart
import 'dart:async';

Stream<String> stream1() async* {
  yield 'A1';
  await Future.delayed(Duration(milliseconds: 500));
  yield 'A2';
  await Future.delayed(Duration(milliseconds: 500));
  yield 'A3';
}

Stream<String> stream2() async* {
  yield 'B1';
  await Future.delayed(Duration(milliseconds: 300));
  yield 'B2';
  await Future.delayed(Duration(milliseconds: 300));
  yield 'B3';
}

void main() async {
  // Manual combination using StreamController
  StreamController<String> controller = StreamController<String>();
  
  stream1().listen((event) => controller.add(event));
  stream2().listen((event) => controller.add(event));
  
  print('Combined stream:');
  await for (String value in controller.stream.take(6)) {
    print('  $value');
  }
  
  controller.close();
}
```

Combining streams merges events from multiple sources. Manual combination  
uses StreamController to collect events, while specialized libraries offer  
advanced combination operators for complex scenarios.  

## Async Iteration

Async iteration processes sequences where each step may be asynchronous,  
enabling clean sequential async workflows.  

```dart
Future<int> processItem(int item) async {
  await Future.delayed(Duration(milliseconds: 500));
  return item * 2;
}

Future<void> processAll(List<int> items) async {
  print('Processing items sequentially:');
  
  for (int item in items) {
    int result = await processItem(item);
    print('  $item -> $result');
  }
}

Future<void> processAllConcurrently(List<int> items) async {
  print('\nProcessing items concurrently:');
  
  List<Future<int>> futures = items.map((item) => processItem(item)).toList();
  List<int> results = await Future.wait(futures);
  
  for (int i = 0; i < items.length; i++) {
    print('  ${items[i]} -> ${results[i]}');
  }
}

void main() async {
  List<int> items = [1, 2, 3, 4, 5];
  
  Stopwatch sw = Stopwatch()..start();
  await processAll(items);
  print('Sequential time: ${sw.elapsedMilliseconds}ms');
  
  sw.reset();
  await processAllConcurrently(items);
  print('Concurrent time: ${sw.elapsedMilliseconds}ms');
}
```

Async iteration can be sequential using await in loops, or concurrent using  
Future.wait with mapped Futures. Sequential ensures order but takes longer,  
while concurrent executes faster but completes in arbitrary order.  

## Future Microtask

Microtasks execute before regular async tasks, enabling precise control over  
event loop execution order.  

```dart
void main() {
  print('1. Synchronous start');
  
  Future(() => print('5. Regular future'));
  
  Future.microtask(() => print('3. First microtask'));
  Future.microtask(() => print('4. Second microtask'));
  
  Future(() => print('6. Another future'));
  
  print('2. Synchronous end');
  
  // Order: sync code, microtasks, futures
}
```

Microtasks run before regular Future callbacks in the event loop. This  
enables high-priority async operations that need to execute before other  
async work, maintaining precise execution ordering.  

## Completer for Custom Futures

Completer enables manual Future completion, useful for integrating callback-  
based APIs with Future-based code.  

```dart
import 'dart:async';

Future<String> fetchData() {
  Completer<String> completer = Completer<String>();
  
  // Simulate async operation with callback
  Future.delayed(Duration(seconds: 1), () {
    // Complete successfully
    completer.complete('Data fetched successfully');
  });
  
  return completer.future;
}

Future<int> riskyFetch() {
  Completer<int> completer = Completer<int>();
  
  Future.delayed(Duration(seconds: 1), () {
    // Complete with error
    completer.completeError(Exception('Fetch failed'));
  });
  
  return completer.future;
}

void main() async {
  // Successful completion
  try {
    String result = await fetchData();
    print('Success: $result');
  } catch (e) {
    print('Error: $e');
  }
  
  // Error completion
  try {
    int result = await riskyFetch();
    print('Result: $result');
  } catch (e) {
    print('Caught: $e');
  }
}
```

Completer manually controls Future completion. The complete method fulfills  
the Future with a value, while completeError fails it with an error. This  
bridges callback-based APIs with Future-based async patterns.  

## Stream Take and Skip

Take and skip methods control which stream events are processed, enabling  
selective event handling and flow control.  

```dart
Stream<int> numbers() async* {
  for (int i = 1; i <= 10; i++) {
    await Future.delayed(Duration(milliseconds: 200));
    yield i;
  }
}

void main() async {
  // Take first 3 events
  print('First 3 numbers:');
  await for (int value in numbers().take(3)) {
    print('  $value');
  }
  
  // Skip first 5 events
  print('\nAfter skipping 5:');
  await for (int value in numbers().skip(5)) {
    print('  $value');
  }
  
  // Combine take and skip
  print('\nSkip 3, take 4:');
  await for (int value in numbers().skip(3).take(4)) {
    print('  $value');
  }
}
```

Take limits stream processing to the first N events, while skip ignores the  
first N events. Combining them enables precise window selection from stream  
sequences, useful for pagination and filtering.  

## Stream TakeWhile and SkipWhile

TakeWhile and skipWhile provide conditional event filtering based on  
predicates rather than fixed counts.  

```dart
Stream<int> numbers() async* {
  for (int i = 1; i <= 10; i++) {
    await Future.delayed(Duration(milliseconds: 200));
    yield i;
  }
}

void main() async {
  // Take while condition is true
  print('Take while < 5:');
  await for (int value in numbers().takeWhile((n) => n < 5)) {
    print('  $value');
  }
  
  // Skip while condition is true
  print('\nSkip while < 6:');
  await for (int value in numbers().skipWhile((n) => n < 6)) {
    print('  $value');
  }
  
  // Combined conditions
  print('\nSkip while < 3, take while < 8:');
  await for (int value in numbers()
      .skipWhile((n) => n < 3)
      .takeWhile((n) => n < 8)) {
    print('  $value');
  }
}
```

TakeWhile emits events until the predicate returns false, while skipWhile  
ignores events until the predicate returns false. These enable dynamic  
filtering based on event content rather than position.  

## Stream Distinct

Distinct filters duplicate consecutive events, ensuring only unique values  
propagate through the stream.  

```dart
Stream<int> numbersWithDuplicates() async* {
  List<int> values = [1, 1, 2, 2, 2, 3, 1, 1, 4, 4, 5];
  
  for (int value in values) {
    await Future.delayed(Duration(milliseconds: 200));
    yield value;
  }
}

void main() async {
  print('Original stream:');
  await for (int value in numbersWithDuplicates()) {
    print('  $value');
  }
  
  print('\nDistinct stream:');
  await for (int value in numbersWithDuplicates().distinct()) {
    print('  $value');
  }
}
```

Distinct removes consecutive duplicate events from streams, useful for  
debouncing sensors or eliminating redundant state updates. Only distinct  
values propagate downstream, reducing unnecessary processing.  

## Stream HandleError

HandleError intercepts stream errors while allowing the stream to continue,  
providing robust error recovery without terminating event flow.  

```dart
Stream<int> faultyStream() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(milliseconds: 300));
    
    if (i == 3) {
      throw Exception('Error at $i');
    }
    
    yield i;
  }
}

void main() async {
  print('Without error handling:');
  try {
    await for (int value in faultyStream()) {
      print('  $value');
    }
  } catch (e) {
    print('  Stream stopped: $e');
  }
  
  print('\nWith error handling:');
  await for (int value in faultyStream().handleError((error) {
    print('  Handled error: $error');
  })) {
    print('  $value');
  }
}
```

HandleError catches stream errors without stopping the stream. Errors are  
processed by the handler, allowing subsequent events to continue. This  
enables resilient streams that recover from individual failures.  

## Stream Expand

Expand transforms each stream event into multiple events, enabling one-to-  
many mappings in stream processing pipelines.  

```dart
Stream<int> numbers() async* {
  for (int i = 1; i <= 3; i++) {
    await Future.delayed(Duration(milliseconds: 500));
    yield i;
  }
}

void main() async {
  // Expand each number to its multiples
  print('Expanded stream (each number -> 3 multiples):');
  await for (int value in numbers().expand((n) => [n, n * 10, n * 100])) {
    print('  $value');
  }
  
  // Expand with generation
  print('\nExpanded with range:');
  await for (int value in numbers().expand((n) {
    return List.generate(n, (i) => i + 1);
  })) {
    print('  $value');
  }
}
```

Expand converts each stream event into an iterable, emitting each item  
individually. This enables hierarchical data flattening and multiplicative  
transformations where one input produces multiple outputs.  

## Stream Reduce

Reduce accumulates stream values into a single result, useful for computing  
aggregates from asynchronous sequences.  

```dart
Stream<int> numbers() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(milliseconds: 300));
    yield i;
  }
}

void main() async {
  // Sum using reduce
  int sum = await numbers().reduce((previous, current) => previous + current);
  print('Sum: $sum');
  
  // Product using reduce
  int product = await numbers().reduce((prev, curr) => prev * curr);
  print('Product: $product');
  
  // Max using reduce
  int max = await numbers().reduce((a, b) => a > b ? a : b);
  print('Max: $max');
}
```

Reduce combines stream values into a single result using an accumulator  
function. Each event updates the accumulated value, with the final result  
available after stream completion. Useful for statistics and aggregation.  

## Stream Fold

Fold accumulates stream values starting from an initial value, providing  
more flexibility than reduce for aggregation operations.  

```dart
Stream<int> numbers() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(milliseconds: 300));
    yield i;
  }
}

void main() async {
  // Sum with initial value
  int sum = await numbers().fold<int>(0, (prev, curr) => prev + curr);
  print('Sum: $sum');
  
  // String concatenation
  String concatenated = await numbers().fold<String>(
    '',
    (prev, curr) => prev.isEmpty ? '$curr' : '$prev, $curr'
  );
  print('Concatenated: $concatenated');
  
  // Count events
  int count = await numbers().fold<int>(0, (count, _) => count + 1);
  print('Count: $count');
  
  // Build list
  List<int> doubled = await numbers().fold<List<int>>(
    [],
    (list, value) {
      list.add(value * 2);
      return list;
    }
  );
  print('Doubled: $doubled');
}
```

Fold starts with an initial value and updates it for each stream event. This  
enables building complex accumulated values like collections or formatted  
strings, with more control than reduce.  

## Stream AsyncExpand

AsyncExpand transforms each stream event into another stream, flattening  
nested async operations into a single stream.  

```dart
Stream<int> mainStream() async* {
  for (int i = 1; i <= 3; i++) {
    await Future.delayed(Duration(milliseconds: 500));
    yield i;
  }
}

Stream<String> expandNumber(int n) async* {
  for (int i = 1; i <= n; i++) {
    await Future.delayed(Duration(milliseconds: 200));
    yield '$n-$i';
  }
}

void main() async {
  print('AsyncExpand - each number generates a stream:');
  await for (String value in mainStream().asyncExpand(expandNumber)) {
    print('  $value');
  }
}
```

AsyncExpand transforms each event into a stream, flattening the results.  
Unlike expand which returns iterables, asyncExpand returns streams, enabling  
nested async operations to be seamlessly combined.  

## Stream AsyncMap

AsyncMap applies async transformations to stream events, enabling  
asynchronous processing within stream pipelines.  

```dart
Stream<int> numbers() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(milliseconds: 300));
    yield i;
  }
}

Future<String> asyncTransform(int n) async {
  await Future.delayed(Duration(milliseconds: 500));
  return 'Processed: $n';
}

void main() async {
  print('AsyncMap transformation:');
  
  Stopwatch sw = Stopwatch()..start();
  
  await for (String value in numbers().asyncMap(asyncTransform)) {
    print('  $value');
  }
  
  sw.stop();
  print('Total time: ${sw.elapsedMilliseconds}ms');
}
```

AsyncMap applies async functions to stream events sequentially. Each  
transformation completes before the next begins, maintaining order while  
enabling complex async operations within stream processing.  

## Stream First and Last

First and last access specific stream events, providing convenient shortcuts  
for common patterns.  

```dart
Stream<int> numbers() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(milliseconds: 300));
    yield i;
  }
}

void main() async {
  // Get first event
  int first = await numbers().first;
  print('First: $first');
  
  // Get last event
  int last = await numbers().last;
  print('Last: $last');
  
  // First matching condition
  int firstEven = await numbers().firstWhere((n) => n % 2 == 0);
  print('First even: $firstEven');
  
  // Last matching condition
  int lastOdd = await numbers().lastWhere((n) => n % 2 != 0);
  print('Last odd: $lastOdd');
  
  // Single event (errors if more than one)
  try {
    int single = await numbers().single;
    print('Single: $single');
  } catch (e) {
    print('Not single: too many events');
  }
}
```

Stream accessors provide convenient ways to extract specific events. First  
and last get boundary events, while firstWhere and lastWhere find events  
matching conditions. Single validates single-event streams.  

## Stream Length and Contains

Stream utility methods provide metadata and search capabilities without  
consuming the entire stream for processing.  

```dart
Stream<int> numbers() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(milliseconds: 300));
    yield i;
  }
}

void main() async {
  // Count stream events
  int length = await numbers().length;
  print('Length: $length');
  
  // Check if value exists
  bool containsThree = await numbers().contains(3);
  print('Contains 3: $containsThree');
  
  bool containsTen = await numbers().contains(10);
  print('Contains 10: $containsTen');
  
  // Check any condition
  bool hasEven = await numbers().any((n) => n % 2 == 0);
  print('Has even: $hasEven');
  
  // Check all condition
  bool allPositive = await numbers().every((n) => n > 0);
  print('All positive: $allPositive');
  
  bool allEven = await numbers().every((n) => n % 2 == 0);
  print('All even: $allEven');
}
```

Stream utility methods provide information about stream contents. Length  
counts events, contains checks presence, any tests if any event matches,  
and every validates all events match a condition.  

## Timer-Based Async Operations

Timers create periodic async events, useful for animations, polling, and  
scheduled tasks.  

```dart
import 'dart:async';

void main() async {
  print('Timer examples:');
  
  // Single timer
  print('Single timer in 2 seconds...');
  Timer(Duration(seconds: 2), () {
    print('  Timer fired!');
  });
  
  await Future.delayed(Duration(milliseconds: 2500));
  
  // Periodic timer
  print('\nPeriodic timer every second:');
  int count = 0;
  Timer.periodic(Duration(seconds: 1), (timer) {
    count++;
    print('  Tick $count');
    
    if (count >= 3) {
      timer.cancel();
      print('  Timer canceled');
    }
  });
  
  await Future.delayed(Duration(seconds: 4));
  
  // Timer with stream
  print('\nTimer stream:');
  Stream<int> timerStream = Stream.periodic(
    Duration(milliseconds: 500),
    (count) => count
  ).take(5);
  
  await for (int tick in timerStream) {
    print('  Tick: $tick');
  }
}
```

Timers schedule async callbacks. Timer executes once after a delay, while  
Timer.periodic repeats at intervals. Stream.periodic creates streams from  
timer events, integrating timing into reactive patterns.  

## Zone for Async Context

Zones provide execution contexts for async operations, enabling custom error  
handling and resource management.  

```dart
import 'dart:async';

void main() {
  print('Zone examples:');
  
  // Create zone with error handler
  runZoned(() async {
    print('In custom zone');
    
    await Future.delayed(Duration(milliseconds: 100));
    
    throw Exception('Error in zone');
  }, onError: (error, stackTrace) {
    print('Zone caught: $error');
  });
  
  // Zone with custom values
  runZoned(() {
    print('\nZone value: ${Zone.current['key']}');
  }, zoneValues: {
    'key': 'Custom value'
  });
  
  // Nested zones
  runZoned(() {
    print('\nOuter zone');
    
    runZoned(() {
      print('Inner zone');
      throw Exception('Inner error');
    }, onError: (e, s) {
      print('Inner zone caught: $e');
    });
  });
}
```

Zones provide isolated execution contexts with custom error handlers and  
shared values. They enable centralized error handling for async operations  
and context-specific behavior without explicit parameter passing.  

## Isolates for Parallel Async

Isolates enable true parallel execution on separate threads, useful for  
CPU-intensive operations that would block the event loop.  

```dart
import 'dart:isolate';

// Compute function running in isolate
int fibonacci(int n) {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
}

Future<int> computeInIsolate(int n) async {
  ReceivePort receivePort = ReceivePort();
  
  await Isolate.spawn(_isolateEntry, {
    'sendPort': receivePort.sendPort,
    'n': n,
  });
  
  return await receivePort.first;
}

void _isolateEntry(Map<String, dynamic> message) {
  SendPort sendPort = message['sendPort'];
  int n = message['n'];
  
  int result = fibonacci(n);
  sendPort.send(result);
}

void main() async {
  print('Computing Fibonacci(35) in isolate...');
  
  Stopwatch sw = Stopwatch()..start();
  int result = await computeInIsolate(35);
  sw.stop();
  
  print('Result: $result');
  print('Time: ${sw.elapsedMilliseconds}ms');
  
  print('\nMain isolate remains responsive during computation');
}
```

Isolates run code in separate memory spaces on different threads, enabling  
true parallelism. They communicate via message passing, making them ideal  
for CPU-intensive operations that shouldn't block the main event loop.  

## Async File Reading

File I/O operations benefit from async handling to prevent blocking while  
waiting for disk operations to complete.  

```dart
import 'dart:io';

Future<void> readFileAsync() async {
  try {
    File file = File('example.txt');
    
    // Create file if it doesn't exist
    if (!await file.exists()) {
      await file.writeAsString('Hello there!\nWelcome to async file operations.\nDart is powerful!');
      print('File created');
    }
    
    // Read entire file
    String contents = await file.readAsString();
    print('File contents:');
    print(contents);
    
    // Read as lines
    List<String> lines = await file.readAsLines();
    print('\nLine count: ${lines.length}');
    
    // Stream reading for large files
    print('\nStreaming lines:');
    Stream<String> lineStream = file.openRead()
      .transform(SystemEncoding().decoder)
      .transform(LineSplitter());
    
    await for (String line in lineStream) {
      print('  $line');
    }
    
  } catch (e) {
    print('Error: $e');
  }
}

void main() async {
  await readFileAsync();
}
```

Async file operations prevent blocking during I/O. Methods like readAsString  
and writeAsString return Futures, while stream-based reading handles large  
files efficiently by processing chunks incrementally.  

## Async HTTP Requests

Network requests are inherently async, with Futures and Streams providing  
natural abstractions for request-response patterns.  

```dart
import 'dart:io';
import 'dart:convert';

Future<Map<String, dynamic>> fetchJson(String url) async {
  try {
    HttpClient client = HttpClient();
    HttpClientRequest request = await client.getUrl(Uri.parse(url));
    HttpClientResponse response = await request.close();
    
    if (response.statusCode == 200) {
      String jsonString = await response.transform(utf8.decoder).join();
      return jsonDecode(jsonString);
    } else {
      throw Exception('HTTP ${response.statusCode}');
    }
  } catch (e) {
    print('Request failed: $e');
    rethrow;
  }
}

void main() async {
  try {
    // Mock API endpoint (would be real in production)
    print('Fetching data...');
    
    // In real usage, replace with actual API
    // Map<String, dynamic> data = await fetchJson('https://api.example.com/data');
    
    // Simulated response
    Map<String, dynamic> data = {'message': 'Hello there!', 'status': 'success'};
    
    print('Response: $data');
  } catch (e) {
    print('Error: $e');
  }
}
```

HTTP requests use async operations for network communication. Futures handle  
response waiting, while Streams process response bodies incrementally,  
enabling efficient handling of large responses.  

## Future.doWhile for Async Loops

Future.doWhile enables async loops where each iteration is asynchronous,  
useful for polling and retry logic.  

```dart
Future<void> asyncPolling() async {
  int attempts = 0;
  int maxAttempts = 5;
  
  await Future.doWhile(() async {
    attempts++;
    print('Attempt $attempts of $maxAttempts');
    
    await Future.delayed(Duration(seconds: 1));
    
    // Simulate condition check
    bool shouldContinue = attempts < maxAttempts;
    
    if (!shouldContinue) {
      print('Polling complete');
    }
    
    return shouldContinue;
  });
}

void main() async {
  await asyncPolling();
}
```

Future.doWhile executes async operations in a loop, continuing while the  
callback returns true. Each iteration completes before the next begins,  
enabling controlled async polling and retry patterns.  

## Stream Debounce

Debouncing limits event frequency by ignoring events that occur too quickly,  
useful for search inputs and sensor data.  

```dart
import 'dart:async';

Stream<int> rapidEvents() async* {
  for (int i = 1; i <= 10; i++) {
    await Future.delayed(Duration(milliseconds: 200));
    yield i;
  }
}

Stream<T> debounce<T>(Stream<T> source, Duration duration) async* {
  Timer? timer;
  T? lastValue;
  bool hasValue = false;
  
  await for (T value in source) {
    lastValue = value;
    hasValue = true;
    
    timer?.cancel();
    timer = Timer(duration, () {
      if (hasValue) {
        // Would emit here in a real implementation
      }
    });
    
    // Simplified: emit if no event for duration
    await Future.delayed(duration);
    
    if (hasValue && lastValue == value) {
      yield value;
      hasValue = false;
    }
  }
}

void main() async {
  print('Original events:');
  await for (int value in rapidEvents()) {
    print('  $value');
  }
}
```

Debouncing delays event emission until a quiet period occurs, reducing  
processing for rapid events. This prevents excessive reactions to high-  
frequency inputs like keypresses or sensor updates.  

## Stream Throttle

Throttling limits event rate by emitting at most one event per time period,  
controlling downstream processing load.  

```dart
import 'dart:async';

Stream<int> frequentEvents() async* {
  for (int i = 1; i <= 20; i++) {
    await Future.delayed(Duration(milliseconds: 100));
    yield i;
  }
}

Stream<T> throttle<T>(Stream<T> source, Duration duration) async* {
  DateTime? lastEmit;
  
  await for (T value in source) {
    DateTime now = DateTime.now();
    
    if (lastEmit == null || now.difference(lastEmit) >= duration) {
      yield value;
      lastEmit = now;
    }
  }
}

void main() async {
  print('Throttled events (max 1 per second):');
  await for (int value in throttle(frequentEvents(), Duration(seconds: 1))) {
    print('  $value at ${DateTime.now().toString().substring(11, 23)}');
  }
}
```

Throttling ensures maximum emission rate, emitting the first event in each  
time window. This prevents overwhelming downstream processors with high-  
frequency events while maintaining responsiveness.  

## Async Queue Processing

Queues enable sequential async processing where tasks are executed in order  
as capacity allows, useful for rate limiting and resource management.  

```dart
import 'dart:async';
import 'dart:collection';

class AsyncQueue<T> {
  final Queue<Future<T> Function()> _queue = Queue();
  bool _isProcessing = false;
  
  Future<T> add(Future<T> Function() task) {
    Completer<T> completer = Completer<T>();
    
    _queue.add(() async {
      try {
        T result = await task();
        completer.complete(result);
        return result;
      } catch (e) {
        completer.completeError(e);
        rethrow;
      }
    });
    
    _process();
    return completer.future;
  }
  
  Future<void> _process() async {
    if (_isProcessing || _queue.isEmpty) return;
    
    _isProcessing = true;
    
    while (_queue.isNotEmpty) {
      var task = _queue.removeFirst();
      await task();
    }
    
    _isProcessing = false;
  }
}

void main() async {
  AsyncQueue<String> queue = AsyncQueue<String>();
  
  print('Adding tasks to queue:');
  
  queue.add(() async {
    await Future.delayed(Duration(seconds: 1));
    print('  Task 1 complete');
    return 'Result 1';
  });
  
  queue.add(() async {
    await Future.delayed(Duration(milliseconds: 500));
    print('  Task 2 complete');
    return 'Result 2';
  });
  
  queue.add(() async {
    await Future.delayed(Duration(milliseconds: 800));
    print('  Task 3 complete');
    return 'Result 3';
  });
  
  await Future.delayed(Duration(seconds: 3));
  print('All tasks processed');
}
```

Async queues serialize operations, ensuring tasks execute sequentially even  
when added concurrently. This enables rate limiting, resource pooling, and  
ordered processing of async operations.  

## Future Delayed Chains

Delayed chains create sequential async operations with controlled timing  
between steps, useful for animations and staged processes.  

```dart
Future<void> step1() async {
  print('Step 1: Initialize');
  await Future.delayed(Duration(seconds: 1));
  print('Step 1: Complete');
}

Future<void> step2() async {
  print('Step 2: Process data');
  await Future.delayed(Duration(seconds: 1));
  print('Step 2: Complete');
}

Future<void> step3() async {
  print('Step 3: Finalize');
  await Future.delayed(Duration(seconds: 1));
  print('Step 3: Complete');
}

void main() async {
  print('Starting sequential process:');
  
  Stopwatch sw = Stopwatch()..start();
  
  await step1();
  await step2();
  await step3();
  
  sw.stop();
  
  print('Total time: ${sw.elapsedMilliseconds}ms');
}
```

Sequential async operations with delays enable staged processing where each  
step waits for the previous to complete. This ensures proper ordering and  
timing for multi-step async workflows.  

## Stream Buffer

Buffering collects stream events into batches, reducing processing overhead  
for high-frequency streams.  

```dart
import 'dart:async';

Stream<int> rapidStream() async* {
  for (int i = 1; i <= 20; i++) {
    await Future.delayed(Duration(milliseconds: 100));
    yield i;
  }
}

Stream<List<T>> buffer<T>(Stream<T> source, int size) async* {
  List<T> buffer = [];
  
  await for (T value in source) {
    buffer.add(value);
    
    if (buffer.length >= size) {
      yield List.from(buffer);
      buffer.clear();
    }
  }
  
  if (buffer.isNotEmpty) {
    yield buffer;
  }
}

void main() async {
  print('Buffered stream (batches of 5):');
  
  await for (List<int> batch in buffer(rapidStream(), 5)) {
    print('  Batch: $batch');
  }
}
```

Buffering collects events into fixed-size batches before emitting, reducing  
downstream processing frequency. This improves performance for streams with  
many small events that can be processed in groups.  

## Async Retry Logic

Retry logic automatically re-attempts failed async operations with  
exponential backoff, improving reliability for transient failures.  

```dart
import 'dart:math';

Future<T> retry<T>(
  Future<T> Function() operation, {
  int maxAttempts = 3,
  Duration initialDelay = const Duration(seconds: 1),
}) async {
  int attempt = 0;
  Duration delay = initialDelay;
  
  while (true) {
    try {
      attempt++;
      print('Attempt $attempt of $maxAttempts');
      
      return await operation();
    } catch (e) {
      if (attempt >= maxAttempts) {
        print('Max attempts reached, failing');
        rethrow;
      }
      
      print('Failed: $e, retrying in ${delay.inMilliseconds}ms');
      await Future.delayed(delay);
      
      // Exponential backoff
      delay *= 2;
    }
  }
}

int attemptCount = 0;

Future<String> unreliableOperation() async {
  attemptCount++;
  await Future.delayed(Duration(milliseconds: 500));
  
  if (attemptCount < 3) {
    throw Exception('Temporary failure');
  }
  
  return 'Success!';
}

void main() async {
  try {
    String result = await retry(() => unreliableOperation());
    print('Result: $result');
  } catch (e) {
    print('Operation failed permanently: $e');
  }
}
```

Retry logic handles transient failures by automatically re-attempting  
operations. Exponential backoff increases delays between attempts, reducing  
load on failing services while improving success probability.  

## Async Caching

Caching stores async operation results, avoiding redundant computations and  
improving performance for repeated requests.  

```dart
class AsyncCache<K, V> {
  final Map<K, Future<V>> _cache = {};
  final Future<V> Function(K) _fetcher;
  
  AsyncCache(this._fetcher);
  
  Future<V> get(K key) {
    if (_cache.containsKey(key)) {
      print('Cache hit for: $key');
      return _cache[key]!;
    }
    
    print('Cache miss for: $key');
    Future<V> future = _fetcher(key);
    _cache[key] = future;
    return future;
  }
  
  void clear() => _cache.clear();
}

Future<String> expensiveFetch(int id) async {
  print('  Fetching data for ID: $id');
  await Future.delayed(Duration(seconds: 2));
  return 'Data for ID: $id';
}

void main() async {
  AsyncCache<int, String> cache = AsyncCache(expensiveFetch);
  
  print('First request:');
  String result1 = await cache.get(1);
  print('Result: $result1\n');
  
  print('Second request (cached):');
  String result2 = await cache.get(1);
  print('Result: $result2\n');
  
  print('Different key:');
  String result3 = await cache.get(2);
  print('Result: $result3');
}
```

Async caching stores Future results keyed by request parameters. Subsequent  
requests for the same key return cached Futures, avoiding redundant  
operations and dramatically improving performance.  

## Stream Merge

Merging combines multiple streams into one, interleaving events as they  
occur from any source.  

```dart
import 'dart:async';

Stream<String> stream1() async* {
  for (int i = 1; i <= 3; i++) {
    await Future.delayed(Duration(milliseconds: 800));
    yield 'Stream1-$i';
  }
}

Stream<String> stream2() async* {
  for (int i = 1; i <= 3; i++) {
    await Future.delayed(Duration(milliseconds: 500));
    yield 'Stream2-$i';
  }
}

Stream<String> stream3() async* {
  for (int i = 1; i <= 3; i++) {
    await Future.delayed(Duration(milliseconds: 1000));
    yield 'Stream3-$i';
  }
}

Stream<T> merge<T>(List<Stream<T>> streams) {
  StreamController<T> controller = StreamController<T>();
  int activeStreams = streams.length;
  
  for (Stream<T> stream in streams) {
    stream.listen(
      (data) => controller.add(data),
      onError: (error) => controller.addError(error),
      onDone: () {
        activeStreams--;
        if (activeStreams == 0) {
          controller.close();
        }
      },
    );
  }
  
  return controller.stream;
}

void main() async {
  print('Merged stream events:');
  
  await for (String value in merge([stream1(), stream2(), stream3()])) {
    print('  $value at ${DateTime.now().toString().substring(17, 23)}');
  }
  
  print('All streams complete');
}
```

Stream merging combines multiple sources into one output stream, preserving  
event order by time of occurrence. Events from any source appear in the  
merged stream as they happen.  

## Async State Machine

State machines manage complex async workflows with transitions between  
states based on events and conditions.  

```dart
enum State { idle, loading, success, error }

class AsyncStateMachine {
  State _state = State.idle;
  String? _data;
  String? _error;
  
  State get state => _state;
  String? get data => _data;
  String? get error => _error;
  
  Future<void> load() async {
    if (_state == State.loading) {
      print('Already loading');
      return;
    }
    
    _state = State.loading;
    _error = null;
    print('State: loading');
    
    try {
      await Future.delayed(Duration(seconds: 2));
      
      // Simulate random failure
      if (DateTime.now().second % 3 == 0) {
        throw Exception('Load failed');
      }
      
      _data = 'Loaded data';
      _state = State.success;
      print('State: success - $_data');
    } catch (e) {
      _error = e.toString();
      _state = State.error;
      print('State: error - $_error');
    }
  }
  
  void reset() {
    _state = State.idle;
    _data = null;
    _error = null;
    print('State: idle');
  }
}

void main() async {
  AsyncStateMachine machine = AsyncStateMachine();
  
  await machine.load();
  
  if (machine.state == State.error) {
    print('Retrying after error...');
    await Future.delayed(Duration(seconds: 1));
    await machine.load();
  }
  
  machine.reset();
}
```

Async state machines manage workflows through explicit states with  
transitions driven by async operations. This ensures consistent behavior  
and prevents invalid state combinations.  

## Stream Zip

Zip combines events from multiple streams into tuples, synchronizing streams  
for paired processing.  

```dart
import 'dart:async';

Stream<int> numbers() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(milliseconds: 500));
    yield i;
  }
}

Stream<String> letters() async* {
  List<String> chars = ['A', 'B', 'C', 'D', 'E'];
  for (String char in chars) {
    await Future.delayed(Duration(milliseconds: 700));
    yield char;
  }
}

Stream<T> zip<T>(
  List<Stream> streams,
  T Function(List) combiner,
) {
  StreamController<T> controller = StreamController<T>();
  List<StreamSubscription> subscriptions = [];
  List<List> buffers = List.generate(streams.length, (_) => []);
  
  void checkEmit() {
    if (buffers.every((buffer) => buffer.isNotEmpty)) {
      List values = buffers.map((buffer) => buffer.removeAt(0)).toList();
      controller.add(combiner(values));
    }
  }
  
  for (int i = 0; i < streams.length; i++) {
    subscriptions.add(streams[i].listen(
      (value) {
        buffers[i].add(value);
        checkEmit();
      },
      onDone: () => controller.close(),
    ));
  }
  
  return controller.stream;
}

void main() async {
  print('Zipped streams:');
  
  await for (String pair in zip([numbers(), letters()], (values) {
    return '${values[0]}-${values[1]}';
  })) {
    print('  $pair');
  }
}
```

Zip combines corresponding events from multiple streams into tuples,  
synchronizing their outputs. Each tuple contains one event from each source  
stream, enabling coordinated multi-stream processing.  

## Async Circuit Breaker

Circuit breakers prevent cascading failures by stopping requests to failing  
services temporarily, improving system resilience.  

```dart
enum CircuitState { closed, open, halfOpen }

class CircuitBreaker {
  CircuitState _state = CircuitState.closed;
  int _failureCount = 0;
  int _successCount = 0;
  DateTime? _lastFailureTime;
  
  final int failureThreshold;
  final Duration resetTimeout;
  final int successThreshold;
  
  CircuitBreaker({
    this.failureThreshold = 3,
    this.resetTimeout = const Duration(seconds: 10),
    this.successThreshold = 2,
  });
  
  Future<T> execute<T>(Future<T> Function() operation) async {
    if (_state == CircuitState.open) {
      if (_shouldAttemptReset()) {
        print('Circuit half-open, testing...');
        _state = CircuitState.halfOpen;
      } else {
        throw Exception('Circuit breaker open');
      }
    }
    
    try {
      T result = await operation();
      _onSuccess();
      return result;
    } catch (e) {
      _onFailure();
      rethrow;
    }
  }
  
  void _onSuccess() {
    _failureCount = 0;
    
    if (_state == CircuitState.halfOpen) {
      _successCount++;
      if (_successCount >= successThreshold) {
        print('Circuit closed after recovery');
        _state = CircuitState.closed;
        _successCount = 0;
      }
    }
  }
  
  void _onFailure() {
    _lastFailureTime = DateTime.now();
    _failureCount++;
    _successCount = 0;
    
    if (_failureCount >= failureThreshold) {
      print('Circuit opened after $failureThreshold failures');
      _state = CircuitState.open;
    }
  }
  
  bool _shouldAttemptReset() {
    if (_lastFailureTime == null) return false;
    return DateTime.now().difference(_lastFailureTime!) > resetTimeout;
  }
}

int callCount = 0;

Future<String> unreliableService() async {
  callCount++;
  await Future.delayed(Duration(milliseconds: 500));
  
  if (callCount <= 3) {
    throw Exception('Service unavailable');
  }
  
  return 'Success';
}

void main() async {
  CircuitBreaker breaker = CircuitBreaker();
  
  for (int i = 1; i <= 6; i++) {
    try {
      print('\nAttempt $i:');
      String result = await breaker.execute(unreliableService);
      print('Result: $result');
    } catch (e) {
      print('Failed: $e');
    }
    
    await Future.delayed(Duration(seconds: 1));
  }
}
```

Circuit breakers monitor operation failures, opening the circuit after a  
threshold to prevent cascading failures. After a timeout, they test recovery  
in half-open state before fully closing again.  

## Async Rate Limiter

Rate limiters control request frequency to prevent overwhelming services  
or exceeding API quotas.  

```dart
import 'dart:collection';

class RateLimiter {
  final int maxRequests;
  final Duration window;
  final Queue<DateTime> _requests = Queue();
  
  RateLimiter({
    required this.maxRequests,
    required this.window,
  });
  
  Future<T> execute<T>(Future<T> Function() operation) async {
    await _waitForSlot();
    return await operation();
  }
  
  Future<void> _waitForSlot() async {
    _cleanOldRequests();
    
    while (_requests.length >= maxRequests) {
      DateTime oldestRequest = _requests.first;
      DateTime nextAvailable = oldestRequest.add(window);
      Duration waitTime = nextAvailable.difference(DateTime.now());
      
      if (waitTime.inMilliseconds > 0) {
        print('Rate limit reached, waiting ${waitTime.inMilliseconds}ms');
        await Future.delayed(waitTime);
      }
      
      _cleanOldRequests();
    }
    
    _requests.add(DateTime.now());
  }
  
  void _cleanOldRequests() {
    DateTime cutoff = DateTime.now().subtract(window);
    
    while (_requests.isNotEmpty && _requests.first.isBefore(cutoff)) {
      _requests.removeFirst();
    }
  }
}

Future<String> apiCall(int id) async {
  await Future.delayed(Duration(milliseconds: 100));
  return 'Result $id';
}

void main() async {
  // Allow 3 requests per 2 seconds
  RateLimiter limiter = RateLimiter(
    maxRequests: 3,
    window: Duration(seconds: 2),
  );
  
  print('Making rate-limited requests:');
  
  for (int i = 1; i <= 8; i++) {
    String result = await limiter.execute(() => apiCall(i));
    print('$i: $result at ${DateTime.now().toString().substring(17, 23)}');
  }
}
```

Rate limiters ensure operations don't exceed specified frequencies, preventing  
quota exhaustion and service overload. Requests wait automatically when  
limits are reached, resuming when capacity becomes available.  

## Async Batching

Batching groups async operations for efficient bulk processing, reducing  
overhead for operations with high setup costs.  

```dart
import 'dart:async';

class AsyncBatcher<T, R> {
  final Future<List<R>> Function(List<T>) _batchProcessor;
  final Duration _delay;
  final int _maxBatchSize;
  
  List<T> _pending = [];
  List<Completer<R>> _completers = [];
  Timer? _timer;
  
  AsyncBatcher({
    required Future<List<R>> Function(List<T>) processor,
    Duration delay = const Duration(milliseconds: 100),
    int maxBatchSize = 10,
  }) : _batchProcessor = processor,
       _delay = delay,
       _maxBatchSize = maxBatchSize;
  
  Future<R> add(T item) {
    Completer<R> completer = Completer<R>();
    
    _pending.add(item);
    _completers.add(completer);
    
    _timer?.cancel();
    
    if (_pending.length >= _maxBatchSize) {
      _processBatch();
    } else {
      _timer = Timer(_delay, _processBatch);
    }
    
    return completer.future;
  }
  
  Future<void> _processBatch() async {
    if (_pending.isEmpty) return;
    
    List<T> batch = List.from(_pending);
    List<Completer<R>> completers = List.from(_completers);
    
    _pending.clear();
    _completers.clear();
    
    print('Processing batch of ${batch.length} items');
    
    try {
      List<R> results = await _batchProcessor(batch);
      
      for (int i = 0; i < completers.length; i++) {
        completers[i].complete(results[i]);
      }
    } catch (e) {
      for (Completer<R> completer in completers) {
        completer.completeError(e);
      }
    }
  }
}

Future<List<String>> processBatch(List<int> ids) async {
  await Future.delayed(Duration(seconds: 1));
  return ids.map((id) => 'Processed $id').toList();
}

void main() async {
  AsyncBatcher<int, String> batcher = AsyncBatcher(
    processor: processBatch,
    maxBatchSize: 3,
  );
  
  print('Adding items individually:');
  
  List<Future<String>> futures = [];
  
  for (int i = 1; i <= 7; i++) {
    futures.add(batcher.add(i));
    await Future.delayed(Duration(milliseconds: 50));
  }
  
  List<String> results = await Future.wait(futures);
  
  print('\nResults:');
  for (String result in results) {
    print('  $result');
  }
}
```

Async batching collects individual operations into groups for bulk processing,  
dramatically improving throughput for operations with high per-call overhead.  
Items are batched by size or time window.  

## Stream Window

Windowing creates sliding or tumbling windows over stream events, enabling  
time-based aggregations and analytics.  

```dart
import 'dart:async';

Stream<int> eventStream() async* {
  for (int i = 1; i <= 15; i++) {
    await Future.delayed(Duration(milliseconds: 300));
    yield i;
  }
}

Stream<List<T>> tumblingWindow<T>(
  Stream<T> source,
  Duration windowSize,
) async* {
  List<T> window = [];
  DateTime windowStart = DateTime.now();
  
  await for (T event in source) {
    Duration elapsed = DateTime.now().difference(windowStart);
    
    if (elapsed >= windowSize && window.isNotEmpty) {
      yield List.from(window);
      window.clear();
      windowStart = DateTime.now();
    }
    
    window.add(event);
  }
  
  if (window.isNotEmpty) {
    yield window;
  }
}

void main() async {
  print('Tumbling windows (1 second):');
  
  await for (List<int> window in tumblingWindow(
    eventStream(),
    Duration(seconds: 1),
  )) {
    print('  Window: $window (${window.length} events)');
  }
}
```

Windowing groups stream events by time intervals. Tumbling windows create  
non-overlapping segments, while sliding windows (not shown) overlap.  
This enables temporal aggregations and time-series analysis.  

## Async Semaphore

Semaphores limit concurrent async operations, controlling resource usage  
and preventing overload.  

```dart
import 'dart:async';

class Semaphore {
  final int maxConcurrent;
  int _currentCount = 0;
  final List<Completer<void>> _waitQueue = [];
  
  Semaphore(this.maxConcurrent);
  
  Future<T> execute<T>(Future<T> Function() operation) async {
    await _acquire();
    try {
      return await operation();
    } finally {
      _release();
    }
  }
  
  Future<void> _acquire() async {
    if (_currentCount < maxConcurrent) {
      _currentCount++;
      return;
    }
    
    Completer<void> completer = Completer<void>();
    _waitQueue.add(completer);
    return completer.future;
  }
  
  void _release() {
    _currentCount--;
    
    if (_waitQueue.isNotEmpty) {
      _currentCount++;
      Completer<void> completer = _waitQueue.removeAt(0);
      completer.complete();
    }
  }
}

Future<String> simulateWork(int id) async {
  print('Starting task $id');
  await Future.delayed(Duration(seconds: 2));
  print('Completed task $id');
  return 'Result $id';
}

void main() async {
  // Allow only 2 concurrent operations
  Semaphore semaphore = Semaphore(2);
  
  print('Starting 5 tasks (max 2 concurrent):');
  
  List<Future<String>> futures = [];
  
  for (int i = 1; i <= 5; i++) {
    futures.add(semaphore.execute(() => simulateWork(i)));
  }
  
  List<String> results = await Future.wait(futures);
  
  print('\nAll tasks complete');
  for (String result in results) {
    print('  $result');
  }
}
```

Semaphores control concurrency by limiting how many operations can run  
simultaneously. Operations queue when the limit is reached, resuming when  
capacity becomes available. This prevents resource exhaustion.  

## Async Event Sourcing

Event sourcing captures all state changes as events, enabling replay and  
time-travel debugging through async event streams.  

```dart
import 'dart:async';

abstract class Event {
  final DateTime timestamp;
  Event() : timestamp = DateTime.now();
}

class UserCreated extends Event {
  final String name;
  UserCreated(this.name);
}

class UserUpdated extends Event {
  final String name;
  UserUpdated(this.name);
}

class EventStore {
  final List<Event> _events = [];
  final StreamController<Event> _controller = StreamController<Event>.broadcast();
  
  Stream<Event> get events => _controller.stream;
  
  void add(Event event) {
    _events.add(event);
    _controller.add(event);
    print('Event stored: ${event.runtimeType} at ${event.timestamp}');
  }
  
  List<Event> getAll() => List.from(_events);
  
  Stream<Event> replay() async* {
    for (Event event in _events) {
      await Future.delayed(Duration(milliseconds: 100));
      yield event;
    }
  }
}

class UserState {
  String? name;
  
  void apply(Event event) {
    if (event is UserCreated) {
      name = event.name;
    } else if (event is UserUpdated) {
      name = event.name;
    }
  }
}

void main() async {
  EventStore store = EventStore();
  
  // Subscribe to events
  store.events.listen((event) {
    print('  Event received: ${event.runtimeType}');
  });
  
  // Generate events
  print('Creating events:');
  store.add(UserCreated('Alice'));
  await Future.delayed(Duration(milliseconds: 500));
  
  store.add(UserUpdated('Alice Smith'));
  await Future.delayed(Duration(milliseconds: 500));
  
  // Replay events to reconstruct state
  print('\nReplaying events:');
  UserState state = UserState();
  
  await for (Event event in store.replay()) {
    state.apply(event);
    print('  State after ${event.runtimeType}: ${state.name}');
  }
  
  print('\nFinal state: ${state.name}');
}
```

Event sourcing stores state changes as events rather than current state.  
Async streams deliver events to subscribers, while replay enables state  
reconstruction at any point. This provides audit trails and debugging.  

## Async Memoization

Memoization caches async function results based on parameters, avoiding  
redundant computations for deterministic operations.  

```dart
class AsyncMemo<K, V> {
  final Map<K, Future<V>> _cache = {};
  final Future<V> Function(K) _function;
  
  AsyncMemo(this._function);
  
  Future<V> call(K key) {
    return _cache.putIfAbsent(key, () async {
      print('Computing result for: $key');
      return await _function(key);
    });
  }
  
  void clear() => _cache.clear();
  int get size => _cache.length;
}

Future<int> fibonacci(int n) async {
  await Future.delayed(Duration(milliseconds: 100));
  
  if (n <= 1) return n;
  return fibonacci(n - 1).then((a) => 
    fibonacci(n - 2).then((b) => a + b));
}

void main() async {
  AsyncMemo<int, int> memoFib = AsyncMemo(fibonacci);
  
  print('Computing fibonacci(10):');
  Stopwatch sw = Stopwatch()..start();
  
  int result1 = await memoFib(10);
  print('First call: $result1 (${sw.elapsedMilliseconds}ms)');
  
  sw.reset();
  int result2 = await memoFib(10);
  print('Second call (cached): $result2 (${sw.elapsedMilliseconds}ms)');
  
  print('\nCache size: ${memoFib.size}');
}
```

Async memoization caches Future results keyed by parameters, returning  
cached values for repeated calls. This dramatically improves performance  
for expensive, deterministic async operations.  

## Stream Sampling

Sampling extracts periodic values from high-frequency streams, reducing  
processing load while maintaining representative data.  

```dart
import 'dart:async';

Stream<int> highFrequencyStream() async* {
  for (int i = 1; i <= 50; i++) {
    await Future.delayed(Duration(milliseconds: 50));
    yield i;
  }
}

Stream<T> sample<T>(Stream<T> source, Duration interval) async* {
  T? lastValue;
  bool hasValue = false;
  
  Timer.periodic(interval, (_) {
    if (hasValue && lastValue != null) {
      // Would emit here in real implementation
    }
  });
  
  await for (T value in source) {
    lastValue = value;
    hasValue = true;
  }
}

Stream<T> sampleEveryN<T>(Stream<T> source, int n) async* {
  int count = 0;
  
  await for (T value in source) {
    count++;
    if (count % n == 0) {
      yield value;
    }
  }
}

void main() async {
  print('Sampled stream (every 5th value):');
  
  await for (int value in sampleEveryN(highFrequencyStream(), 5)) {
    print('  $value');
  }
}
```

Stream sampling reduces event frequency by emitting only periodic values,  
maintaining data trends while reducing processing overhead. Count-based or  
time-based sampling strategies suit different use cases.  

## Async Pipeline

Async pipelines chain transformations into reusable processing workflows,  
enabling modular async data processing.  

```dart
class AsyncPipeline<T> {
  final List<Future<T> Function(T)> _stages = [];
  
  AsyncPipeline<T> add(Future<T> Function(T) stage) {
    _stages.add(stage);
    return this;
  }
  
  Future<T> execute(T input) async {
    T current = input;
    
    for (var stage in _stages) {
      current = await stage(current);
    }
    
    return current;
  }
}

Future<int> addTen(int n) async {
  await Future.delayed(Duration(milliseconds: 100));
  print('  Added 10: ${n + 10}');
  return n + 10;
}

Future<int> multiplyTwo(int n) async {
  await Future.delayed(Duration(milliseconds: 100));
  print('  Multiplied by 2: ${n * 2}');
  return n * 2;
}

Future<int> subtractFive(int n) async {
  await Future.delayed(Duration(milliseconds: 100));
  print('  Subtracted 5: ${n - 5}');
  return n - 5;
}

void main() async {
  AsyncPipeline<int> pipeline = AsyncPipeline<int>()
    .add(addTen)
    .add(multiplyTwo)
    .add(subtractFive);
  
  print('Processing value 5 through pipeline:');
  int result = await pipeline.execute(5);
  
  print('Final result: $result');
}
```

Async pipelines compose sequential transformations into reusable workflows.  
Each stage receives the previous stage's output, creating clear data flow  
through complex async operations.  

## Async Observer Pattern

Observer pattern enables reactive async notifications, allowing multiple  
subscribers to react to state changes.  

```dart
import 'dart:async';

class AsyncObservable<T> {
  final List<void Function(T)> _observers = [];
  T? _value;
  
  T? get value => _value;
  
  void subscribe(void Function(T) observer) {
    _observers.add(observer);
  }
  
  void unsubscribe(void Function(T) observer) {
    _observers.remove(observer);
  }
  
  Future<void> notify(T value) async {
    _value = value;
    
    for (var observer in _observers) {
      await Future.microtask(() => observer(value));
    }
  }
}

void main() async {
  AsyncObservable<String> observable = AsyncObservable<String>();
  
  // Add observers
  observable.subscribe((value) {
    print('Observer 1: $value');
  });
  
  observable.subscribe((value) {
    print('Observer 2: $value');
  });
  
  observable.subscribe((value) {
    print('Observer 3: $value');
  });
  
  // Notify all observers
  print('Notifying observers:');
  await observable.notify('Hello there!');
  
  await Future.delayed(Duration(milliseconds: 100));
  
  await observable.notify('Second notification');
  
  await Future.delayed(Duration(milliseconds: 100));
}
```

Async observers enable publish-subscribe patterns where multiple subscribers  
react to value changes. Notifications propagate asynchronously to all  
registered observers, enabling loose coupling and reactive patterns.  

## Async Resource Pooling

Resource pools manage limited resources like database connections,  
preventing exhaustion and improving efficiency through reuse.  

```dart
import 'dart:async';
import 'dart:collection';

class Resource {
  final int id;
  bool inUse = false;
  
  Resource(this.id);
  
  Future<String> use(String task) async {
    await Future.delayed(Duration(seconds: 1));
    return 'Resource $id completed: $task';
  }
}

class ResourcePool {
  final List<Resource> _available = [];
  final Queue<Completer<Resource>> _waitQueue = Queue();
  
  ResourcePool(int size) {
    for (int i = 1; i <= size; i++) {
      _available.add(Resource(i));
    }
  }
  
  Future<Resource> acquire() {
    if (_available.isNotEmpty) {
      Resource resource = _available.removeAt(0);
      resource.inUse = true;
      return Future.value(resource);
    }
    
    Completer<Resource> completer = Completer<Resource>();
    _waitQueue.add(completer);
    return completer.future;
  }
  
  void release(Resource resource) {
    resource.inUse = false;
    
    if (_waitQueue.isNotEmpty) {
      Completer<Resource> completer = _waitQueue.removeFirst();
      resource.inUse = true;
      completer.complete(resource);
    } else {
      _available.add(resource);
    }
  }
  
  Future<T> execute<T>(Future<T> Function(Resource) operation) async {
    Resource resource = await acquire();
    try {
      return await operation(resource);
    } finally {
      release(resource);
    }
  }
}

void main() async {
  // Pool of 3 resources, 5 tasks
  ResourcePool pool = ResourcePool(3);
  
  print('Starting 5 tasks with pool of 3 resources:');
  
  List<Future<String>> tasks = [];
  
  for (int i = 1; i <= 5; i++) {
    tasks.add(pool.execute((resource) => resource.use('Task $i')));
  }
  
  List<String> results = await Future.wait(tasks);
  
  print('\nResults:');
  for (String result in results) {
    print('  $result');
  }
}
```

Resource pools limit concurrent resource usage by providing resources from  
a pool, queuing requests when exhausted. Released resources become available  
for queued requests, enabling efficient resource management.  

## Async Reactive Streams

Reactive streams provide backpressure handling, preventing fast producers  
from overwhelming slow consumers in async data flows.  

```dart
import 'dart:async';

class ReactiveStream<T> {
  final StreamController<T> _controller = StreamController<T>();
  bool _isPaused = false;
  final List<T> _buffer = [];
  final int _maxBufferSize;
  
  ReactiveStream({int maxBufferSize = 10}) : _maxBufferSize = maxBufferSize;
  
  Stream<T> get stream => _controller.stream;
  
  Future<void> add(T value) async {
    if (_buffer.length >= _maxBufferSize) {
      print('Buffer full, applying backpressure');
      await Future.delayed(Duration(milliseconds: 100));
    }
    
    if (_isPaused) {
      _buffer.add(value);
      print('  Buffered: $value (buffer size: ${_buffer.length})');
    } else {
      _controller.add(value);
    }
  }
  
  void pause() {
    _isPaused = true;
    print('Stream paused');
  }
  
  void resume() {
    _isPaused = false;
    print('Stream resumed, draining buffer');
    
    while (_buffer.isNotEmpty) {
      _controller.add(_buffer.removeAt(0));
    }
  }
  
  void close() {
    _controller.close();
  }
}

void main() async {
  ReactiveStream<int> stream = ReactiveStream<int>(maxBufferSize: 5);
  
  // Slow consumer
  stream.stream.listen((value) async {
    print('Consuming: $value');
    await Future.delayed(Duration(milliseconds: 500));
  });
  
  // Fast producer
  print('Producing values:');
  for (int i = 1; i <= 10; i++) {
    await stream.add(i);
    await Future.delayed(Duration(milliseconds: 100));
    
    if (i == 5) {
      stream.pause();
      await Future.delayed(Duration(seconds: 1));
      stream.resume();
    }
  }
  
  await Future.delayed(Duration(seconds: 3));
  stream.close();
}
```

Reactive streams handle backpressure when producers generate data faster  
than consumers process it. Buffering and pause/resume mechanisms prevent  
memory exhaustion while maintaining data flow.  

## Best Practices and Performance

Understanding async best practices ensures efficient, maintainable, and  
robust asynchronous code in production applications.  

```dart
import 'dart:async';

// GOOD: Use async-await for readability
Future<String> goodExample() async {
  String user = await fetchUser();
  String profile = await fetchProfile(user);
  return profile;
}

// AVOID: Nested then chains
Future<String> avoidExample() {
  return fetchUser().then((user) {
    return fetchProfile(user).then((profile) {
      return profile;
    });
  });
}

// GOOD: Handle errors properly
Future<void> goodErrorHandling() async {
  try {
    await riskyOperation();
  } catch (e) {
    print('Error: $e');
    // Handle or rethrow
  }
}

// GOOD: Use Future.wait for parallel operations
Future<void> goodParallel() async {
  List<String> results = await Future.wait([
    fetchData1(),
    fetchData2(),
    fetchData3(),
  ]);
  print(results);
}

// AVOID: Sequential when parallel is possible
Future<void> avoidSequential() async {
  String data1 = await fetchData1();
  String data2 = await fetchData2();
  String data3 = await fetchData3();
  print([data1, data2, data3]);
}

// GOOD: Cancel stream subscriptions
Future<void> goodStreamHandling() async {
  StreamSubscription<int> subscription = numberStream().listen((data) {
    print(data);
  });
  
  await Future.delayed(Duration(seconds: 2));
  await subscription.cancel(); // Important cleanup
}

// GOOD: Use timeouts for external operations
Future<String> goodTimeout() async {
  try {
    return await externalApi().timeout(
      Duration(seconds: 5),
      onTimeout: () => 'Default value',
    );
  } catch (e) {
    return 'Error fallback';
  }
}

// GOOD: Avoid mixing sync and async
Future<int> goodAsync() async {
  await Future.delayed(Duration(milliseconds: 100));
  return 42;
}

// AVOID: Unnecessary async
Future<int> avoidUnnecessaryAsync() async {
  return 42; // Should just return Future.value(42) or make sync
}

// Supporting functions for examples
Future<String> fetchUser() async {
  await Future.delayed(Duration(milliseconds: 100));
  return 'user123';
}

Future<String> fetchProfile(String user) async {
  await Future.delayed(Duration(milliseconds: 100));
  return 'Profile for $user';
}

Future<void> riskyOperation() async {
  await Future.delayed(Duration(milliseconds: 100));
}

Future<String> fetchData1() async {
  await Future.delayed(Duration(milliseconds: 100));
  return 'Data1';
}

Future<String> fetchData2() async {
  await Future.delayed(Duration(milliseconds: 100));
  return 'Data2';
}

Future<String> fetchData3() async {
  await Future.delayed(Duration(milliseconds: 100));
  return 'Data3';
}

Stream<int> numberStream() async* {
  for (int i = 1; i <= 10; i++) {
    await Future.delayed(Duration(milliseconds: 200));
    yield i;
  }
}

Future<String> externalApi() async {
  await Future.delayed(Duration(seconds: 1));
  return 'API response';
}

void main() async {
  print('Async Best Practices Examples:');
  
  String result1 = await goodExample();
  print('Good example result: $result1');
  
  await goodErrorHandling();
  
  await goodParallel();
  
  print('\nPerformance comparison:');
  
  Stopwatch sw = Stopwatch()..start();
  await goodParallel();
  int parallelTime = sw.elapsedMilliseconds;
  
  sw.reset();
  await avoidSequential();
  int sequentialTime = sw.elapsedMilliseconds;
  
  print('Parallel: ${parallelTime}ms');
  print('Sequential: ${sequentialTime}ms');
  print('Speedup: ${(sequentialTime / parallelTime).toStringAsFixed(1)}x');
}
```

Best practices include using async-await for readability, proper error  
handling, parallel execution when possible, resource cleanup, timeouts for  
external operations, and avoiding unnecessary async. Following these  
guidelines ensures robust and performant async code.  

Key principles:  
- Use async-await over then chains for better readability  
- Handle errors at appropriate levels with try-catch  
- Execute independent operations in parallel with Future.wait  
- Always cancel stream subscriptions to prevent memory leaks  
- Apply timeouts to external operations to prevent hangs  
- Avoid blocking the event loop with heavy synchronous operations  
- Use isolates for CPU-intensive work  
- Implement proper backpressure for high-throughput streams  
- Cache expensive async operations when appropriate  
- Test async code thoroughly including error scenarios  
