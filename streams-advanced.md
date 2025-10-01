# Dart Advanced Streams

Streams in Dart provide a powerful way to handle asynchronous sequences of  
data. They represent a sequence of events that arrive over time, making them  
ideal for handling user interactions, network responses, file I/O, and other  
asynchronous operations. Streams are the foundation of reactive programming  
in Dart, enabling elegant solutions for complex async scenarios.  

In Dart, streams can be either single-subscription or broadcast. Single-  
subscription streams allow only one listener and are ideal for sequential  
data processing. Broadcast streams support multiple simultaneous listeners,  
perfect for pub-sub patterns. Understanding these distinctions and how to  
transform, control, and manipulate streams is essential for building robust,  
responsive applications.  

This guide covers advanced stream concepts including transformations,  
broadcast streams, stream controllers, error handling, and real-world  
patterns. Each example builds progressively, demonstrating practical  
techniques used in production applications for user input handling, network  
requests, and reactive UI updates.  

## Basic Stream Creation

Streams can be created from various sources including generators, futures,  
and iterables. Understanding creation patterns is foundational.  

```dart
import 'dart:async';

void main() async {
  // Stream from iterable
  Stream<int> fromIterable = Stream.fromIterable([1, 2, 3, 4, 5]);
  
  print('Stream from iterable:');
  await for (int value in fromIterable) {
    print('  $value');
  }
  
  // Stream from future
  Future<String> futureValue = Future.delayed(
    Duration(milliseconds: 500),
    () => 'Completed!',
  );
  Stream<String> fromFuture = Stream.fromFuture(futureValue);
  
  print('\nStream from future:');
  await for (String value in fromFuture) {
    print('  $value');
  }
}
```

Stream.fromIterable converts a collection into a stream that emits each  
element sequentially. Stream.fromFuture creates a single-value stream from  
a Future, useful for integrating future-based APIs into stream pipelines.  

## Async Generator Streams

Async generators provide a clean syntax for creating custom streams using  
async* and yield keywords for controlled event emission.  

```dart
import 'dart:async';

Stream<int> countDown(int from) async* {
  for (int i = from; i >= 1; i--) {
    await Future.delayed(Duration(milliseconds: 300));
    yield i;
  }
  yield 0;
}

void main() async {
  print('Countdown:');
  await for (int value in countDown(5)) {
    print('  $value');
  }
  print('Launch!');
}
```

The async* keyword marks a function as an async generator that returns a  
Stream. The yield keyword emits values to the stream. This pattern provides  
fine-grained control over timing and event generation.  

## Stream Map Transformation

The map transformation converts each stream event to a different value,  
enabling type conversions and data reshaping within pipelines.  

```dart
import 'dart:async';

Stream<int> numbers() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(milliseconds: 200));
    yield i;
  }
}

void main() async {
  // Map to different type
  print('Squared values:');
  await for (int square in numbers().map((n) => n * n)) {
    print('  $square');
  }
  
  // Map to string
  print('\nFormatted values:');
  await for (String formatted in numbers().map((n) => 'Number: $n')) {
    print('  $formatted');
  }
}
```

The map method applies a transformation function to each event. It's non-  
blocking and preserves stream timing. Common uses include formatting data,  
type conversion, and extracting properties from objects.  

## Stream Where Filtering

The where transformation filters stream events based on a predicate,  
allowing only matching events to pass through the pipeline.  

```dart
import 'dart:async';

Stream<int> range(int start, int end) async* {
  for (int i = start; i <= end; i++) {
    await Future.delayed(Duration(milliseconds: 100));
    yield i;
  }
}

void main() async {
  // Filter even numbers
  print('Even numbers:');
  await for (int even in range(1, 10).where((n) => n % 2 == 0)) {
    print('  $even');
  }
  
  // Filter with multiple conditions
  print('\nNumbers divisible by 3:');
  await for (int div3 in range(1, 20).where((n) => n % 3 == 0)) {
    print('  $div3');
  }
}
```

The where method filters events using a boolean predicate function. Events  
that don't match are silently dropped. This is essential for selective  
event processing and validation in stream pipelines.  

## Chaining Multiple Transformations

Stream transformations can be chained to create complex data processing  
pipelines that combine filtering, mapping, and other operations.  

```dart
import 'dart:async';

Stream<int> dataSource() async* {
  for (int i = 1; i <= 20; i++) {
    await Future.delayed(Duration(milliseconds: 50));
    yield i;
  }
}

void main() async {
  print('Pipeline: filter even, map to square, take first 5:');
  
  await for (int value in dataSource()
      .where((n) => n % 2 == 0)
      .map((n) => n * n)
      .take(5)) {
    print('  $value');
  }
}
```

Chained transformations execute in sequence, with each stage processing  
events from the previous stage. This creates clean, readable pipelines for  
complex data processing without nested callbacks or intermediate variables.  

## Stream Expand Transformation

The expand transformation converts each event into multiple events,  
enabling one-to-many mappings in stream processing.  

```dart
import 'dart:async';

Stream<int> source() async* {
  for (int i = 1; i <= 4; i++) {
    await Future.delayed(Duration(milliseconds: 300));
    yield i;
  }
}

void main() async {
  // Expand each number to its factors
  print('Expanded with multiples:');
  await for (int value in source().expand((n) => [n, n * 10, n * 100])) {
    print('  $value');
  }
  
  // Expand with dynamic generation
  print('\nExpanded with range:');
  await for (int value in source().expand((n) => 
      List.generate(n, (i) => i + 1))) {
    print('  $value');
  }
}
```

The expand method transforms each event into an iterable of events, which  
are then emitted individually. This is useful for flattening hierarchical  
data, generating sequences, or duplicating events with variations.  

## Stream AsyncMap Transformation

AsyncMap applies asynchronous transformations to stream events, enabling  
async operations like API calls within stream pipelines.  

```dart
import 'dart:async';

Stream<int> ids() async* {
  for (int i = 1; i <= 4; i++) {
    await Future.delayed(Duration(milliseconds: 200));
    yield i;
  }
}

Future<String> fetchData(int id) async {
  await Future.delayed(Duration(milliseconds: 400));
  return 'Data for ID: $id';
}

void main() async {
  print('AsyncMap processing:');
  
  Stopwatch sw = Stopwatch()..start();
  
  await for (String data in ids().asyncMap(fetchData)) {
    print('  $data');
  }
  
  sw.stop();
  print('Total time: ${sw.elapsedMilliseconds}ms');
}
```

AsyncMap processes events sequentially, waiting for each async operation  
to complete before processing the next. This maintains order and prevents  
overwhelming external services with concurrent requests.  

## Stream AsyncExpand Transformation

AsyncExpand combines expand and asyncMap, allowing async generation of  
multiple events from each source event.  

```dart
import 'dart:async';

Stream<int> categories() async* {
  for (int cat = 1; cat <= 3; cat++) {
    await Future.delayed(Duration(milliseconds: 300));
    yield cat;
  }
}

Future<Iterable<String>> fetchItems(int categoryId) async {
  await Future.delayed(Duration(milliseconds: 200));
  return List.generate(categoryId, (i) => 'Cat$categoryId-Item${i + 1}');
}

void main() async {
  print('AsyncExpand results:');
  
  await for (String item in categories().asyncExpand(fetchItems)) {
    print('  $item');
  }
}
```

AsyncExpand enables fetching related data for each event, where each fetch  
returns multiple items. It's commonly used for loading hierarchical data  
from APIs, expanding categories into products, or users into permissions.  

## Custom Stream Transformer

Stream transformers provide reusable transformation logic that can be  
applied to multiple streams with consistent behavior.  

```dart
import 'dart:async';

class DoubleTransformer<T extends num> extends StreamTransformerBase<T, T> {
  @override
  Stream<T> bind(Stream<T> stream) {
    return stream.map((value) => (value * 2) as T);
  }
}

class FilterNegativeTransformer<T extends num> 
    extends StreamTransformerBase<T, T> {
  @override
  Stream<T> bind(Stream<T> stream) {
    return stream.where((value) => value >= 0);
  }
}

Stream<int> mixedNumbers() async* {
  for (int i in [-2, 5, -1, 8, -3, 12]) {
    await Future.delayed(Duration(milliseconds: 200));
    yield i;
  }
}

void main() async {
  print('Transformed stream:');
  
  await for (int value in mixedNumbers()
      .transform(FilterNegativeTransformer())
      .transform(DoubleTransformer())) {
    print('  $value');
  }
}
```

Custom transformers encapsulate complex transformation logic in reusable  
classes. They can maintain state, handle errors specially, and provide  
configurable behavior, making them ideal for standardized processing.  

## StreamController Basics

StreamController provides programmatic control over stream creation and  
event emission, bridging imperative code with reactive streams.  

```dart
import 'dart:async';

void main() async {
  StreamController<String> controller = StreamController<String>();
  
  // Set up listener
  controller.stream.listen(
    (data) => print('Received: $data'),
    onError: (error) => print('Error: $error'),
    onDone: () => print('Stream closed'),
  );
  
  // Add events
  controller.add('First event');
  controller.add('Second event');
  controller.add('Third event');
  
  await Future.delayed(Duration(milliseconds: 100));
  
  // Close the stream
  controller.close();
  
  await Future.delayed(Duration(milliseconds: 100));
}
```

StreamController exposes a sink for adding events and a stream for listening.  
The add method emits events, addError emits errors, and close terminates  
the stream. This pattern enables custom event sources.  

## StreamController with Error Handling

Stream controllers can emit both data events and errors, providing  
comprehensive control over stream lifecycle and error propagation.  

```dart
import 'dart:async';

void main() async {
  StreamController<int> controller = StreamController<int>();
  
  controller.stream.listen(
    (data) => print('Data: $data'),
    onError: (error) => print('Error: $error'),
    onDone: () => print('Done'),
  );
  
  // Add normal events
  controller.add(1);
  controller.add(2);
  
  await Future.delayed(Duration(milliseconds: 100));
  
  // Add an error
  controller.addError('Something went wrong');
  
  await Future.delayed(Duration(milliseconds: 100));
  
  // Continue with more events
  controller.add(3);
  controller.add(4);
  
  await Future.delayed(Duration(milliseconds: 100));
  
  controller.close();
  await Future.delayed(Duration(milliseconds: 100));
}
```

Errors added via addError are delivered to the stream's error handler. The  
stream continues after errors unless the error handler re-throws. This  
enables resilient streams that recover from individual failures.  

## StreamController with Callbacks

StreamControllers support callbacks for monitoring stream lifecycle events  
like listener additions, cancellations, pauses, and resumes.  

```dart
import 'dart:async';

void main() async {
  StreamController<int> controller = StreamController<int>(
    onListen: () => print('Listener attached'),
    onPause: () => print('Stream paused'),
    onResume: () => print('Stream resumed'),
    onCancel: () => print('Listener cancelled'),
  );
  
  StreamSubscription<int> subscription = controller.stream.listen(
    (data) => print('Data: $data'),
  );
  
  controller.add(1);
  controller.add(2);
  
  await Future.delayed(Duration(milliseconds: 100));
  
  subscription.pause();
  controller.add(3); // Buffered
  
  await Future.delayed(Duration(milliseconds: 100));
  
  subscription.resume();
  
  await Future.delayed(Duration(milliseconds: 100));
  
  subscription.cancel();
  await controller.close();
}
```

Lifecycle callbacks provide hooks for resource management, debugging, and  
implementing backpressure. They're essential for tracking stream behavior  
in complex applications and managing external resources.  

## Broadcast StreamController

Broadcast stream controllers allow multiple simultaneous listeners, unlike  
single-subscription controllers that permit only one listener.  

```dart
import 'dart:async';

void main() async {
  StreamController<int> controller = StreamController<int>.broadcast();
  
  // First listener
  controller.stream.listen(
    (data) => print('Listener 1: $data'),
  );
  
  // Second listener
  controller.stream.listen(
    (data) => print('Listener 2: $data'),
  );
  
  // Third listener
  controller.stream.listen(
    (data) => print('Listener 3: $data'),
  );
  
  // Emit events - all listeners receive them
  for (int i = 1; i <= 4; i++) {
    controller.add(i);
    await Future.delayed(Duration(milliseconds: 300));
  }
  
  controller.close();
  await Future.delayed(Duration(milliseconds: 100));
}
```

Broadcast streams distribute events to all active listeners simultaneously,  
enabling pub-sub patterns. Multiple components can react to the same events  
independently, ideal for UI updates and event broadcasting.  

## Broadcast Stream Late Listeners

Broadcast streams don't buffer events for late listeners. Listeners only  
receive events emitted after they subscribe.  

```dart
import 'dart:async';

void main() async {
  StreamController<int> controller = StreamController<int>.broadcast();
  
  // Early listener
  controller.stream.listen(
    (data) => print('Early listener: $data'),
  );
  
  // Emit some events
  controller.add(1);
  controller.add(2);
  
  await Future.delayed(Duration(milliseconds: 200));
  
  // Late listener - misses events 1 and 2
  controller.stream.listen(
    (data) => print('Late listener: $data'),
  );
  
  // More events - both listeners receive these
  controller.add(3);
  controller.add(4);
  
  await Future.delayed(Duration(milliseconds: 200));
  
  controller.close();
}
```

This behavior is crucial for understanding broadcast streams. Late listeners  
miss earlier events, which can cause synchronization issues. Consider  
buffering or using BehaviorSubject from rxdart for replay capabilities.  

## Converting to Broadcast Stream

Single-subscription streams can be converted to broadcast streams, enabling  
multiple listeners on streams that don't natively support it.  

```dart
import 'dart:async';

Stream<int> singleSubscriptionStream() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(milliseconds: 300));
    yield i;
  }
}

void main() async {
  // Convert to broadcast
  Stream<int> broadcast = singleSubscriptionStream().asBroadcastStream();
  
  // Multiple listeners
  broadcast.listen(
    (data) => print('Listener A: $data'),
  );
  
  broadcast.listen(
    (data) => print('Listener B: $data'),
  );
  
  await Future.delayed(Duration(seconds: 2));
}
```

The asBroadcastStream method creates a broadcast stream from any stream.  
This is useful when multiple components need to observe the same data source  
without creating multiple subscriptions to expensive operations.  

## Stream Subscription Control

Stream subscriptions can be paused, resumed, and cancelled, providing  
fine-grained control over event consumption and resource management.  

```dart
import 'dart:async';

Stream<int> fastStream() async* {
  int count = 0;
  while (true) {
    await Future.delayed(Duration(milliseconds: 100));
    yield count++;
  }
}

void main() async {
  StreamSubscription<int> subscription = fastStream().listen(
    (data) => print('Received: $data'),
  );
  
  // Let it run briefly
  await Future.delayed(Duration(milliseconds: 500));
  
  print('Pausing...');
  subscription.pause();
  
  await Future.delayed(Duration(seconds: 1));
  
  print('Resuming...');
  subscription.resume();
  
  await Future.delayed(Duration(milliseconds: 500));
  
  print('Cancelling...');
  subscription.cancel();
  
  await Future.delayed(Duration(milliseconds: 200));
  print('Done');
}
```

Pause stops event delivery temporarily while buffering events. Resume  
restarts delivery. Cancel permanently stops the subscription and releases  
resources. These controls are essential for managing backpressure.  

## Stream Take and Skip

Take limits stream output to the first N events, while skip ignores the  
first N events, enabling precise window selection.  

```dart
import 'dart:async';

Stream<int> numbers() async* {
  for (int i = 1; i <= 10; i++) {
    await Future.delayed(Duration(milliseconds: 150));
    yield i;
  }
}

void main() async {
  // Take first 3
  print('First 3 numbers:');
  await for (int n in numbers().take(3)) {
    print('  $n');
  }
  
  // Skip first 7
  print('\nAfter skipping 7:');
  await for (int n in numbers().skip(7)) {
    print('  $n');
  }
  
  // Combine: skip 2, take 4
  print('\nSkip 2, take 4:');
  await for (int n in numbers().skip(2).take(4)) {
    print('  $n');
  }
}
```

Take and skip operations enable precise slicing of stream data. They're  
commonly used for pagination, sampling, and windowing operations. The  
stream automatically closes after take completes.  

## Stream TakeWhile and SkipWhile

TakeWhile emits events while a condition holds, SkipWhile ignores events  
until a condition becomes false, providing predicate-based control.  

```dart
import 'dart:async';

Stream<int> sequence() async* {
  for (int i = 1; i <= 10; i++) {
    await Future.delayed(Duration(milliseconds: 150));
    yield i;
  }
}

void main() async {
  // Take while less than 6
  print('TakeWhile < 6:');
  await for (int n in sequence().takeWhile((n) => n < 6)) {
    print('  $n');
  }
  
  // Skip while less than 5
  print('\nSkipWhile < 5:');
  await for (int n in sequence().skipWhile((n) => n < 5)) {
    print('  $n');
  }
  
  // Combine both
  print('\nSkipWhile < 3, TakeWhile < 8:');
  await for (int n in sequence().skipWhile((n) => n < 3)
      .takeWhile((n) => n < 8)) {
    print('  $n');
  }
}
```

These predicate-based operations provide flexible stream slicing based on  
data content rather than position. They're useful for conditional processing  
and dynamic stream termination based on event values.  

## Stream Distinct

The distinct transformation removes consecutive duplicate events, ensuring  
each emitted value differs from its predecessor.  

```dart
import 'dart:async';

Stream<int> withDuplicates() async* {
  var values = [1, 1, 2, 2, 2, 3, 1, 1, 4, 4, 3];
  for (int value in values) {
    await Future.delayed(Duration(milliseconds: 150));
    yield value;
  }
}

void main() async {
  print('Original stream:');
  await for (int n in withDuplicates()) {
    print('  $n');
  }
  
  print('\nWith distinct:');
  await for (int n in withDuplicates().distinct()) {
    print('  $n');
  }
}
```

Distinct only filters consecutive duplicates, not all duplicates. The value  
1 appears twice in the distinct output because non-consecutive occurrences  
pass through. This is useful for debouncing and change detection.  

## Stream HandleError

HandleError intercepts stream errors without terminating the stream,  
enabling graceful error recovery and continued processing.  

```dart
import 'dart:async';

Stream<int> faultyStream() async* {
  for (int i = 1; i <= 8; i++) {
    await Future.delayed(Duration(milliseconds: 200));
    
    if (i == 3 || i == 6) {
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
    print('  Stream terminated: $e');
  }
  
  print('\nWith handleError:');
  await for (int value in faultyStream().handleError((error) {
    print('  Recovered from: $error');
  })) {
    print('  $value');
  }
}
```

HandleError catches errors and allows the stream to continue. This enables  
fault-tolerant streams that recover from individual failures without  
affecting subsequent events, critical for resilient applications.  

## Stream Transform with Error Handling

Custom transformers can implement sophisticated error handling strategies,  
including retry logic and error transformation.  

```dart
import 'dart:async';

class RetryTransformer<T> extends StreamTransformerBase<T, T> {
  final int maxRetries;
  
  RetryTransformer(this.maxRetries);
  
  @override
  Stream<T> bind(Stream<T> stream) async* {
    int errorCount = 0;
    
    await for (var value in stream) {
      try {
        yield value;
        errorCount = 0; // Reset on success
      } catch (e) {
        errorCount++;
        if (errorCount <= maxRetries) {
          print('  Retry $errorCount for error: $e');
        } else {
          print('  Max retries exceeded, propagating error');
          rethrow;
        }
      }
    }
  }
}

Stream<String> unreliableStream() async* {
  var data = ['A', 'B', 'C', 'D'];
  for (String item in data) {
    await Future.delayed(Duration(milliseconds: 200));
    yield item;
  }
}

void main() async {
  print('Stream with retry:');
  await for (String value in unreliableStream()
      .transform(RetryTransformer<String>(3))) {
    print('  $value');
  }
}
```

Custom error handling in transformers enables sophisticated recovery  
strategies. Retry logic, exponential backoff, and error transformation can  
be encapsulated in reusable transformers for consistent behavior.  

## Stream Reduce

Reduce combines all stream events into a single value using an accumulator  
function, similar to List.reduce but for asynchronous sequences.  

```dart
import 'dart:async';

Stream<int> numbers() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(milliseconds: 200));
    yield i;
  }
}

void main() async {
  // Sum all values
  int sum = await numbers().reduce((a, b) => a + b);
  print('Sum: $sum');
  
  // Find maximum
  int max = await numbers().reduce((a, b) => a > b ? a : b);
  print('Max: $max');
  
  // Multiply all
  int product = await numbers().reduce((a, b) => a * b);
  print('Product: $product');
}
```

Reduce processes the entire stream to produce a single result. It requires  
at least one event and uses the first event as the initial accumulator  
value. Common uses include summing, finding extremes, and string joining.  

## Stream Fold

Fold is similar to reduce but accepts an initial value and can change the  
result type, providing more flexibility for accumulation operations.  

```dart
import 'dart:async';

Stream<String> words() async* {
  for (String word in ['Dart', 'is', 'awesome']) {
    await Future.delayed(Duration(milliseconds: 200));
    yield word;
  }
}

Stream<int> values() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(milliseconds: 150));
    yield i;
  }
}

void main() async {
  // Join strings
  String sentence = await words().fold('', (prev, word) => 
      prev.isEmpty ? word : '$prev $word');
  print('Sentence: $sentence');
  
  // Sum with initial value
  int sum = await values().fold(100, (sum, n) => sum + n);
  print('Sum starting at 100: $sum');
  
  // Build list
  List<int> doubled = await values().fold<List<int>>(
    [],
    (list, n) => list..add(n * 2),
  );
  print('Doubled list: $doubled');
}
```

Fold's initial value handles empty streams gracefully and enables type  
changes. It's more versatile than reduce for building collections,  
formatting output, and complex accumulations.  

## Stream First, Last, and Single

Streams provide convenience methods for accessing specific events without  
manual iteration, with built-in validation and error handling.  

```dart
import 'dart:async';

Stream<int> sequence() async* {
  for (int i = 1; i <= 7; i++) {
    await Future.delayed(Duration(milliseconds: 150));
    yield i;
  }
}

Stream<int> singleValue() async* {
  await Future.delayed(Duration(milliseconds: 200));
  yield 42;
}

void main() async {
  // Get first event
  int first = await sequence().first;
  print('First: $first');
  
  // Get last event
  int last = await sequence().last;
  print('Last: $last');
  
  // Get single event (throws if not exactly one)
  int single = await singleValue().single;
  print('Single: $single');
  
  // FirstWhere with predicate
  int firstEven = await sequence().firstWhere((n) => n % 2 == 0);
  print('First even: $firstEven');
}
```

These methods simplify common access patterns. First and last consume  
minimal or full streams respectively. Single validates exactly one event  
exists. FirstWhere finds the first matching event.  

## Stream Length and Contains

Stream utility methods provide metadata and search capabilities for  
analyzing stream content without manual counting or iteration.  

```dart
import 'dart:async';

Stream<int> data() async* {
  for (int i = 1; i <= 8; i++) {
    await Future.delayed(Duration(milliseconds: 150));
    yield i;
  }
}

void main() async {
  // Count events
  int length = await data().length;
  print('Length: $length');
  
  // Check if value exists
  bool hasThree = await data().contains(3);
  print('Contains 3: $hasThree');
  
  bool hasTen = await data().contains(10);
  print('Contains 10: $hasTen');
  
  // Check if any match condition
  bool hasEven = await data().any((n) => n % 2 == 0);
  print('Has even: $hasEven');
  
  // Check if all match condition
  bool allPositive = await data().every((n) => n > 0);
  print('All positive: $allPositive');
}
```

These methods consume the entire stream to provide aggregate information.  
They're useful for validation, counting, and existence checks before or  
after stream processing operations.  

## Stream Join and ToList

Streams can be collected into lists or joined into strings, converting  
asynchronous sequences into synchronous collections.  

```dart
import 'dart:async';

Stream<String> words() async* {
  for (String word in ['Hello', 'there!', 'Welcome', 'to', 'Dart']) {
    await Future.delayed(Duration(milliseconds: 200));
    yield word;
  }
}

Stream<int> numbers() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(milliseconds: 150));
    yield i;
  }
}

void main() async {
  // Convert to list
  List<String> wordList = await words().toList();
  print('List: $wordList');
  
  List<int> numberList = await numbers().toList();
  print('Numbers: $numberList');
  
  // Join into string
  String sentence = await words().join(' ');
  print('Joined: $sentence');
  
  String csv = await numbers().map((n) => n.toString()).join(',');
  print('CSV: $csv');
}
```

ToList collects all events into a List, while join concatenates string  
representations. These methods bridge async and sync code, enabling  
traditional collection operations on stream data.  

## Stream Timeout

Timeout operations add time constraints to streams, throwing errors or  
providing fallback values when events don't arrive within limits.  

```dart
import 'dart:async';

Stream<int> slowStream() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(milliseconds: i * 400));
    yield i;
  }
}

Stream<int> consistentStream() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(milliseconds: 200));
    yield i;
  }
}

void main() async {
  // Timeout with error
  print('With timeout (will fail):');
  try {
    await for (int value in slowStream().timeout(
      Duration(seconds: 1),
    )) {
      print('  $value');
    }
  } catch (e) {
    print('  Timeout error: ${e.runtimeType}');
  }
  
  // Timeout with replacement event
  print('\nWith timeout replacement:');
  await for (int value in slowStream().timeout(
    Duration(seconds: 1),
    onTimeout: (sink) {
      print('  Timeout! Using fallback');
      sink.add(-1);
      sink.close();
    },
  )) {
    print('  $value');
  }
}
```

Timeouts enforce timing constraints on streams. They're essential for  
preventing indefinite waits, implementing SLAs, and providing user feedback  
when operations take too long.  

## Combining Streams with Manual Merging

Multiple streams can be merged into one, interleaving events as they occur  
from different sources for unified processing.  

```dart
import 'dart:async';

Stream<String> stream1() async* {
  for (int i = 1; i <= 3; i++) {
    await Future.delayed(Duration(milliseconds: 300));
    yield 'A$i';
  }
}

Stream<String> stream2() async* {
  for (int i = 1; i <= 3; i++) {
    await Future.delayed(Duration(milliseconds: 450));
    yield 'B$i';
  }
}

Stream<String> stream3() async* {
  for (int i = 1; i <= 3; i++) {
    await Future.delayed(Duration(milliseconds: 600));
    yield 'C$i';
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
  print('Merged streams:');
  
  await for (String value in merge([stream1(), stream2(), stream3()])) {
    print('  $value');
  }
  
  print('All streams complete');
}
```

Stream merging combines multiple sources into one output stream. Events  
appear in the order they're emitted, not by source. This is essential for  
aggregating data from multiple origins like sensors or API endpoints.  

## Stream.periodic for Timer-Based Events

Stream.periodic creates streams that emit events at regular intervals,  
useful for polling, animations, and scheduled operations.  

```dart
import 'dart:async';

void main() async {
  print('Periodic stream (5 events, 500ms interval):');
  
  Stream<int> periodic = Stream<int>.periodic(
    Duration(milliseconds: 500),
    (count) => count,
  ).take(5);
  
  await for (int count in periodic) {
    print('  Tick $count at ${DateTime.now().millisecondsSinceEpoch}');
  }
  
  print('\nCustom computation:');
  
  Stream<String> messages = Stream<String>.periodic(
    Duration(milliseconds: 400),
    (count) => 'Message ${count + 1}',
  ).take(4);
  
  await for (String msg in messages) {
    print('  $msg');
  }
}
```

Stream.periodic generates events at fixed intervals. The computation  
function receives the event count and returns the event value. This is  
ideal for heartbeats, progress updates, and time-based triggers.  

## Stream Buffering Pattern

Buffering collects events into batches before processing, reducing overhead  
when dealing with high-frequency events.  

```dart
import 'dart:async';

Stream<List<T>> buffer<T>(Stream<T> source, int bufferSize) async* {
  List<T> batch = [];
  
  await for (T event in source) {
    batch.add(event);
    
    if (batch.length >= bufferSize) {
      yield List.from(batch);
      batch.clear();
    }
  }
  
  // Yield remaining events
  if (batch.isNotEmpty) {
    yield batch;
  }
}

Stream<int> rapidEvents() async* {
  for (int i = 1; i <= 12; i++) {
    await Future.delayed(Duration(milliseconds: 100));
    yield i;
  }
}

void main() async {
  print('Buffered stream (batches of 4):');
  
  await for (List<int> batch in buffer(rapidEvents(), 4)) {
    print('  Batch: $batch');
  }
}
```

Buffering batches events for efficient processing. It's commonly used for  
database batch inserts, network packet aggregation, and reducing update  
frequency in UIs responding to high-frequency events.  

## Stream Debouncing

Debouncing delays event emission until a quiet period occurs, filtering  
out rapid successive events and emitting only the last one.  

```dart
import 'dart:async';

Stream<T> debounce<T>(Stream<T> source, Duration duration) async* {
  Timer? debounceTimer;
  T? lastValue;
  bool hasValue = false;
  StreamController<T> controller = StreamController<T>();
  
  source.listen(
    (value) {
      debounceTimer?.cancel();
      lastValue = value;
      hasValue = true;
      
      debounceTimer = Timer(duration, () {
        if (hasValue) {
          controller.add(lastValue as T);
          hasValue = false;
        }
      });
    },
    onDone: () {
      debounceTimer?.cancel();
      if (hasValue) {
        controller.add(lastValue as T);
      }
      controller.close();
    },
    onError: (error) => controller.addError(error),
  );
  
  yield* controller.stream;
}

Stream<String> rapidInput() async* {
  var inputs = ['h', 'he', 'hel', 'hell', 'hello'];
  for (String input in inputs) {
    await Future.delayed(Duration(milliseconds: 150));
    yield input;
  }
  
  await Future.delayed(Duration(milliseconds: 600));
  
  var moreInputs = ['w', 'wo', 'wor', 'worl', 'world'];
  for (String input in moreInputs) {
    await Future.delayed(Duration(milliseconds: 150));
    yield input;
  }
}

void main() async {
  print('Debounced stream (500ms):');
  
  await for (String value in debounce(rapidInput(), Duration(milliseconds: 500))) {
    print('  Search for: $value');
  }
}
```

Debouncing is essential for search-as-you-type, auto-save, and any scenario  
where rapid events should be collapsed to the final value. It prevents  
overwhelming APIs with unnecessary requests during continuous input.  

## Stream Throttling

Throttling limits event rate by emitting at most one event per time window,  
preventing excessive processing of high-frequency events.  

```dart
import 'dart:async';

Stream<T> throttle<T>(Stream<T> source, Duration duration) async* {
  DateTime? lastEmit;
  
  await for (T event in source) {
    DateTime now = DateTime.now();
    
    if (lastEmit == null || 
        now.difference(lastEmit) >= duration) {
      yield event;
      lastEmit = now;
    }
  }
}

Stream<int> rapidStream() async* {
  for (int i = 1; i <= 20; i++) {
    await Future.delayed(Duration(milliseconds: 100));
    yield i;
  }
}

void main() async {
  print('Throttled stream (500ms window):');
  
  Stopwatch sw = Stopwatch()..start();
  
  await for (int value in throttle(rapidStream(), Duration(milliseconds: 500))) {
    int elapsed = sw.elapsedMilliseconds;
    print('  $value at ${elapsed}ms');
  }
}
```

Throttling ensures a maximum event rate, useful for UI updates, API rate  
limiting, and preventing resource exhaustion. Unlike debouncing, throttling  
emits events periodically rather than waiting for quiet periods.  

## Stream Distinct with Key Selector

Advanced distinct operations can use custom equality or key selection for  
more sophisticated duplicate detection.  

```dart
import 'dart:async';

class User {
  final int id;
  final String name;
  
  User(this.id, this.name);
  
  @override
  String toString() => 'User($id, $name)';
}

Stream<T> distinctBy<T, K>(
  Stream<T> source,
  K Function(T) keySelector,
) async* {
  Set<K> seenKeys = {};
  
  await for (T event in source) {
    K key = keySelector(event);
    
    if (!seenKeys.contains(key)) {
      seenKeys.add(key);
      yield event;
    }
  }
}

Stream<User> users() async* {
  var userList = [
    User(1, 'Alice'),
    User(2, 'Bob'),
    User(1, 'Alice Updated'),
    User(3, 'Charlie'),
    User(2, 'Bob Updated'),
  ];
  
  for (User user in userList) {
    await Future.delayed(Duration(milliseconds: 200));
    yield user;
  }
}

void main() async {
  print('Distinct users by ID:');
  
  await for (User user in distinctBy(users(), (u) => u.id)) {
    print('  $user');
  }
}
```

Custom distinct operations enable sophisticated deduplication based on  
specific properties or computed keys. This is essential for filtering  
duplicate entities based on business logic rather than simple equality.  

## Stream Zip Pattern

Zipping combines corresponding elements from multiple streams into pairs  
or tuples, synchronizing multiple data sources.  

```dart
import 'dart:async';

Stream<(T1, T2)> zip<T1, T2>(Stream<T1> stream1, Stream<T2> stream2) async* {
  StreamIterator<T1> iter1 = StreamIterator(stream1);
  StreamIterator<T2> iter2 = StreamIterator(stream2);
  
  while (await iter1.moveNext() && await iter2.moveNext()) {
    yield (iter1.current, iter2.current);
  }
}

Stream<int> numbers() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(milliseconds: 200));
    yield i;
  }
}

Stream<String> letters() async* {
  for (String letter in ['A', 'B', 'C', 'D', 'E']) {
    await Future.delayed(Duration(milliseconds: 250));
    yield letter;
  }
}

void main() async {
  print('Zipped streams:');
  
  await for (var (num, letter) in zip(numbers(), letters())) {
    print('  $num -> $letter');
  }
}
```

Zipping synchronizes multiple streams by pairing corresponding events. It's  
useful for correlating data from different sources, like pairing sensor  
readings or coordinating multi-source data processing.  

## Stream WithLatestFrom Pattern

WithLatestFrom combines events from a source stream with the latest value  
from another stream, useful for enriching events with context.  

```dart
import 'dart:async';

Stream<(T1, T2)> withLatestFrom<T1, T2>(
  Stream<T1> source,
  Stream<T2> other,
) async* {
  T2? latestOther;
  bool hasOther = false;
  
  StreamController<T1> sourceController = StreamController<T1>();
  StreamController<T2> otherController = StreamController<T2>();
  
  other.listen((value) {
    latestOther = value;
    hasOther = true;
  });
  
  await for (T1 sourceValue in source) {
    if (hasOther) {
      yield (sourceValue, latestOther as T2);
    }
  }
}

Stream<String> events() async* {
  await Future.delayed(Duration(milliseconds: 500));
  yield 'Event1';
  await Future.delayed(Duration(milliseconds: 500));
  yield 'Event2';
  await Future.delayed(Duration(milliseconds: 500));
  yield 'Event3';
}

Stream<int> counters() async* {
  for (int i = 1; i <= 10; i++) {
    await Future.delayed(Duration(milliseconds: 200));
    yield i;
  }
}

void main() async {
  print('Events with latest counter:');
  
  await for (var (event, counter) in withLatestFrom(events(), counters())) {
    print('  $event at counter $counter');
  }
}
```

WithLatestFrom enriches primary events with latest context from secondary  
streams. It's ideal for adding current state, user context, or configuration  
values to events without polling or manual state management.  

## User Input Stream Pattern

Modeling user input as streams enables reactive UIs that respond  
elegantly to user interactions with validation and transformation.  

```dart
import 'dart:async';

class UserInputHandler {
  final StreamController<String> _inputController = 
      StreamController<String>.broadcast();
  
  Stream<String> get inputStream => _inputController.stream;
  
  Stream<String> get validatedInput => inputStream
      .map((input) => input.trim())
      .where((input) => input.isNotEmpty)
      .distinct();
  
  Stream<int> get inputLength => inputStream
      .map((input) => input.length);
  
  Stream<bool> get isValid => inputStream
      .map((input) => input.length >= 3 && input.length <= 20);
  
  void addInput(String input) {
    _inputController.add(input);
  }
  
  void dispose() {
    _inputController.close();
  }
}

void main() async {
  UserInputHandler handler = UserInputHandler();
  
  // Listen to validated input
  handler.validatedInput.listen(
    (input) => print('Valid input: $input'),
  );
  
  // Listen to length changes
  handler.inputLength.listen(
    (length) => print('Length: $length'),
  );
  
  // Listen to validation status
  handler.isValid.listen(
    (valid) => print('Is valid: $valid'),
  );
  
  // Simulate user input
  handler.addInput('Hi');
  await Future.delayed(Duration(milliseconds: 100));
  
  handler.addInput('Hello');
  await Future.delayed(Duration(milliseconds: 100));
  
  handler.addInput('Hello there!');
  await Future.delayed(Duration(milliseconds: 100));
  
  handler.dispose();
}
```

User input streams enable reactive validation, auto-save, and real-time  
feedback. Multiple listeners can respond to the same input events for  
validation, character counting, and state updates simultaneously.  

## Search Stream with Debouncing

Search functionality benefits from debouncing to minimize API calls while  
providing responsive results as users type.  

```dart
import 'dart:async';

class SearchService {
  final StreamController<String> _queryController = 
      StreamController<String>.broadcast();
  
  Stream<List<String>> get searchResults => _queryController.stream
      .transform(_DebounceTransformer(Duration(milliseconds: 500)))
      .where((query) => query.length >= 2)
      .asyncMap((query) => _performSearch(query));
  
  void search(String query) {
    _queryController.add(query);
  }
  
  Future<List<String>> _performSearch(String query) async {
    print('Searching for: $query');
    await Future.delayed(Duration(milliseconds: 300));
    
    // Simulated search results
    return [
      'Result for $query #1',
      'Result for $query #2',
      'Result for $query #3',
    ];
  }
  
  void dispose() {
    _queryController.close();
  }
}

class _DebounceTransformer<T> extends StreamTransformerBase<T, T> {
  final Duration duration;
  
  _DebounceTransformer(this.duration);
  
  @override
  Stream<T> bind(Stream<T> stream) {
    StreamController<T> controller = StreamController<T>();
    Timer? debounceTimer;
    
    stream.listen(
      (value) {
        debounceTimer?.cancel();
        debounceTimer = Timer(duration, () {
          controller.add(value);
        });
      },
      onDone: () => controller.close(),
      onError: (error) => controller.addError(error),
    );
    
    return controller.stream;
  }
}

void main() async {
  SearchService service = SearchService();
  
  service.searchResults.listen(
    (results) {
      print('Results received:');
      for (String result in results) {
        print('  - $result');
      }
    },
  );
  
  // Simulate rapid typing
  service.search('d');
  await Future.delayed(Duration(milliseconds: 100));
  service.search('da');
  await Future.delayed(Duration(milliseconds: 100));
  service.search('dar');
  await Future.delayed(Duration(milliseconds: 100));
  service.search('dart');
  
  await Future.delayed(Duration(seconds: 2));
  
  service.search('str');
  await Future.delayed(Duration(milliseconds: 100));
  service.search('stre');
  await Future.delayed(Duration(milliseconds: 100));
  service.search('strea');
  await Future.delayed(Duration(milliseconds: 100));
  service.search('stream');
  
  await Future.delayed(Duration(seconds: 2));
  
  service.dispose();
}
```

Debounced search reduces server load by only searching after typing pauses.  
This pattern is essential for autocomplete, search suggestions, and any  
feature responding to rapid user input.  

## Network Request Stream

Modeling network requests as streams enables reactive data loading with  
error handling, retries, and progress tracking.  

```dart
import 'dart:async';

class NetworkRequest<T> {
  final String url;
  final Future<T> Function() fetcher;
  
  NetworkRequest(this.url, this.fetcher);
}

class NetworkService {
  Stream<T> fetch<T>(NetworkRequest<T> request) async* {
    int retries = 0;
    const maxRetries = 3;
    
    while (retries <= maxRetries) {
      try {
        print('Fetching ${request.url} (attempt ${retries + 1})');
        T result = await request.fetcher();
        yield result;
        return;
      } catch (e) {
        retries++;
        if (retries > maxRetries) {
          throw Exception('Failed after $maxRetries retries: $e');
        }
        print('Retry in ${retries * 2} seconds...');
        await Future.delayed(Duration(seconds: retries * 2));
      }
    }
  }
  
  Stream<List<T>> fetchMultiple<T>(List<NetworkRequest<T>> requests) async* {
    for (NetworkRequest<T> request in requests) {
      await for (T result in fetch(request)) {
        yield [result];
      }
    }
  }
}

// Simulated API call
Future<Map<String, dynamic>> fetchUserData(int userId) async {
  await Future.delayed(Duration(milliseconds: 500));
  
  // Simulate occasional failures
  if (userId == 2) {
    throw Exception('Network error for user $userId');
  }
  
  return {'id': userId, 'name': 'User $userId', 'active': true};
}

void main() async {
  NetworkService service = NetworkService();
  
  // Single request
  print('Single request:');
  NetworkRequest<Map<String, dynamic>> request = NetworkRequest(
    '/api/user/1',
    () => fetchUserData(1),
  );
  
  await for (Map<String, dynamic> data in service.fetch(request)) {
    print('  Received: $data');
  }
  
  // Multiple requests
  print('\nMultiple requests:');
  List<NetworkRequest<Map<String, dynamic>>> requests = [
    NetworkRequest('/api/user/1', () => fetchUserData(1)),
    NetworkRequest('/api/user/3', () => fetchUserData(3)),
  ];
  
  await for (List<Map<String, dynamic>> batch in service.fetchMultiple(requests)) {
    print('  Batch received: $batch');
  }
}
```

Network streams enable elegant handling of async API calls with built-in  
retry logic, error handling, and progress tracking. This pattern scales  
from simple fetches to complex multi-request orchestration.  

## Reactive State Management

Streams provide the foundation for reactive state management, enabling  
components to respond automatically to state changes.  

```dart
import 'dart:async';

class AppState {
  int _counter = 0;
  String _status = 'idle';
  
  final StreamController<int> _counterController = 
      StreamController<int>.broadcast();
  final StreamController<String> _statusController = 
      StreamController<String>.broadcast();
  
  Stream<int> get counterStream => _counterController.stream;
  Stream<String> get statusStream => _statusController.stream;
  
  int get counter => _counter;
  String get status => _status;
  
  void increment() {
    _counter++;
    _counterController.add(_counter);
    _updateStatus('incremented');
  }
  
  void decrement() {
    _counter--;
    _counterController.add(_counter);
    _updateStatus('decremented');
  }
  
  void reset() {
    _counter = 0;
    _counterController.add(_counter);
    _updateStatus('reset');
  }
  
  void _updateStatus(String newStatus) {
    _status = newStatus;
    _statusController.add(_status);
  }
  
  void dispose() {
    _counterController.close();
    _statusController.close();
  }
}

void main() async {
  AppState state = AppState();
  
  // Component 1: Display counter
  state.counterStream.listen(
    (count) => print('Counter display: $count'),
  );
  
  // Component 2: Display status
  state.statusStream.listen(
    (status) => print('Status: $status'),
  );
  
  // Component 3: Alert on milestones
  state.counterStream
      .where((count) => count % 5 == 0 && count != 0)
      .listen(
        (count) => print('MILESTONE: Reached $count!'),
      );
  
  // Simulate user interactions
  state.increment();
  await Future.delayed(Duration(milliseconds: 100));
  
  state.increment();
  await Future.delayed(Duration(milliseconds: 100));
  
  state.increment();
  await Future.delayed(Duration(milliseconds: 100));
  
  state.increment();
  await Future.delayed(Duration(milliseconds: 100));
  
  state.increment();
  await Future.delayed(Duration(milliseconds: 100));
  
  state.decrement();
  await Future.delayed(Duration(milliseconds: 100));
  
  state.reset();
  await Future.delayed(Duration(milliseconds: 100));
  
  state.dispose();
}
```

Stream-based state management enables reactive UIs where multiple components  
respond automatically to state changes. This pattern eliminates manual  
notification logic and creates loosely coupled, testable components.  

## Event Bus Pattern

An event bus uses broadcast streams to decouple application components,  
enabling publish-subscribe communication without direct dependencies.  

```dart
import 'dart:async';

abstract class AppEvent {
  final DateTime timestamp;
  
  AppEvent() : timestamp = DateTime.now();
}

class UserLoggedIn extends AppEvent {
  final String username;
  UserLoggedIn(this.username);
}

class UserLoggedOut extends AppEvent {}

class DataUpdated extends AppEvent {
  final String dataId;
  DataUpdated(this.dataId);
}

class EventBus {
  final StreamController<AppEvent> _controller = 
      StreamController<AppEvent>.broadcast();
  
  Stream<AppEvent> get stream => _controller.stream;
  
  Stream<T> on<T extends AppEvent>() {
    return _controller.stream.where((event) => event is T).cast<T>();
  }
  
  void fire(AppEvent event) {
    _controller.add(event);
  }
  
  void dispose() {
    _controller.close();
  }
}

void main() async {
  EventBus bus = EventBus();
  
  // Analytics service listens to all events
  bus.stream.listen(
    (event) => print('Analytics: ${event.runtimeType} at ${event.timestamp}'),
  );
  
  // Auth service listens to login/logout
  bus.on<UserLoggedIn>().listen(
    (event) => print('Auth: User ${event.username} logged in'),
  );
  
  bus.on<UserLoggedOut>().listen(
    (event) => print('Auth: User logged out'),
  );
  
  // Data service listens to data updates
  bus.on<DataUpdated>().listen(
    (event) => print('Data: Updated ${event.dataId}'),
  );
  
  // Fire events
  bus.fire(UserLoggedIn('Alice'));
  await Future.delayed(Duration(milliseconds: 100));
  
  bus.fire(DataUpdated('doc123'));
  await Future.delayed(Duration(milliseconds: 100));
  
  bus.fire(DataUpdated('doc456'));
  await Future.delayed(Duration(milliseconds: 100));
  
  bus.fire(UserLoggedOut());
  await Future.delayed(Duration(milliseconds: 100));
  
  bus.dispose();
}
```

Event buses decouple application components by routing events through a  
central stream. Components can publish and subscribe to events without  
knowing about each other, improving modularity and testability.  

## Stream Backpressure Handling

Backpressure occurs when producers generate data faster than consumers  
process it. Proper handling prevents memory issues and maintains stability.  

```dart
import 'dart:async';

class BackpressureController<T> {
  final StreamController<T> _controller;
  final int maxBufferSize;
  final List<T> _buffer = [];
  bool _isPaused = false;
  
  BackpressureController({this.maxBufferSize = 10})
      : _controller = StreamController<T>(
          onListen: () => print('Listener attached'),
          onPause: () => print('Paused'),
          onResume: () => print('Resumed'),
          onCancel: () => print('Cancelled'),
        );
  
  Stream<T> get stream => _controller.stream;
  
  Future<void> add(T value) async {
    if (_isPaused) {
      if (_buffer.length < maxBufferSize) {
        _buffer.add(value);
        print('Buffered: $value (buffer size: ${_buffer.length})');
      } else {
        print('Buffer full, dropping: $value');
      }
    } else {
      _controller.add(value);
    }
  }
  
  void pause() {
    _isPaused = true;
  }
  
  void resume() {
    _isPaused = false;
    print('Draining buffer...');
    
    while (_buffer.isNotEmpty) {
      _controller.add(_buffer.removeAt(0));
    }
  }
  
  void close() {
    _controller.close();
  }
}

void main() async {
  BackpressureController<int> controller = 
      BackpressureController<int>(maxBufferSize: 5);
  
  // Slow consumer
  controller.stream.listen((value) async {
    print('Processing: $value');
    await Future.delayed(Duration(milliseconds: 500));
  });
  
  // Fast producer
  for (int i = 1; i <= 12; i++) {
    await controller.add(i);
    await Future.delayed(Duration(milliseconds: 100));
    
    if (i == 6) {
      controller.pause();
      await Future.delayed(Duration(seconds: 1));
      controller.resume();
    }
  }
  
  await Future.delayed(Duration(seconds: 3));
  controller.close();
}
```

Backpressure handling prevents overwhelming slow consumers. Buffering,  
pausing, and dropping strategies maintain system stability when producers  
and consumers operate at different speeds.  

## Stream Caching Pattern

Caching stream results avoids redundant computations and network calls,  
improving performance for expensive operations with shared consumers.  

```dart
import 'dart:async';

class CachedStream<T> {
  Stream<T>? _cachedStream;
  List<T>? _cachedData;
  bool _isLoading = false;
  
  Future<Stream<T>> getStream(Future<List<T>> Function() dataFetcher) async {
    if (_cachedData != null) {
      print('Returning cached data');
      return Stream.fromIterable(_cachedData!);
    }
    
    if (_isLoading) {
      print('Already loading, waiting...');
      while (_isLoading) {
        await Future.delayed(Duration(milliseconds: 100));
      }
      return Stream.fromIterable(_cachedData!);
    }
    
    _isLoading = true;
    print('Fetching fresh data');
    
    try {
      _cachedData = await dataFetcher();
      _isLoading = false;
      return Stream.fromIterable(_cachedData!);
    } catch (e) {
      _isLoading = false;
      rethrow;
    }
  }
  
  void invalidate() {
    print('Cache invalidated');
    _cachedData = null;
  }
}

Future<List<String>> fetchExpensiveData() async {
  print('  Performing expensive operation...');
  await Future.delayed(Duration(seconds: 1));
  return ['Data1', 'Data2', 'Data3'];
}

void main() async {
  CachedStream<String> cache = CachedStream<String>();
  
  // First access - fetches data
  print('First request:');
  Stream<String> stream1 = await cache.getStream(fetchExpensiveData);
  await for (String item in stream1) {
    print('  $item');
  }
  
  await Future.delayed(Duration(milliseconds: 500));
  
  // Second access - uses cache
  print('\nSecond request:');
  Stream<String> stream2 = await cache.getStream(fetchExpensiveData);
  await for (String item in stream2) {
    print('  $item');
  }
  
  // Invalidate and fetch again
  cache.invalidate();
  
  print('\nAfter invalidation:');
  Stream<String> stream3 = await cache.getStream(fetchExpensiveData);
  await for (String item in stream3) {
    print('  $item');
  }
}
```

Stream caching optimizes repeated access to expensive data sources. This  
pattern is essential for API responses, computed data, and any scenario  
where multiple components need the same data.  

## StreamQueue for Manual Control

StreamQueue provides pull-based stream consumption with manual control  
over event timing and batching, complementing push-based streams.  

```dart
import 'dart:async';

Stream<int> dataSource() async* {
  for (int i = 1; i <= 10; i++) {
    await Future.delayed(Duration(milliseconds: 200));
    yield i;
  }
}

class StreamQueue<T> {
  final Stream<T> _source;
  final List<T> _buffer = [];
  StreamSubscription<T>? _subscription;
  bool _isDone = false;
  
  StreamQueue(this._source);
  
  Future<void> _ensureSubscribed() async {
    if (_subscription == null) {
      Completer completer = Completer();
      _subscription = _source.listen(
        (event) => _buffer.add(event),
        onDone: () {
          _isDone = true;
          completer.complete();
        },
      );
      await Future.delayed(Duration(milliseconds: 100));
    }
  }
  
  Future<T?> next() async {
    await _ensureSubscribed();
    
    while (_buffer.isEmpty && !_isDone) {
      await Future.delayed(Duration(milliseconds: 50));
    }
    
    if (_buffer.isEmpty) return null;
    return _buffer.removeAt(0);
  }
  
  Future<List<T>> take(int count) async {
    List<T> result = [];
    
    for (int i = 0; i < count; i++) {
      T? value = await next();
      if (value == null) break;
      result.add(value);
    }
    
    return result;
  }
  
  void cancel() {
    _subscription?.cancel();
  }
}

void main() async {
  StreamQueue<int> queue = StreamQueue(dataSource());
  
  // Pull events manually
  print('Pulling first event:');
  int? first = await queue.next();
  print('  $first');
  
  await Future.delayed(Duration(milliseconds: 500));
  
  print('\nPulling batch of 3:');
  List<int> batch = await queue.take(3);
  print('  $batch');
  
  await Future.delayed(Duration(seconds: 1));
  
  print('\nPulling remaining:');
  while (true) {
    int? value = await queue.next();
    if (value == null) break;
    print('  $value');
  }
  
  queue.cancel();
}
```

StreamQueue enables pull-based consumption where consumers control timing.  
This is useful for rate-limited processing, manual batching, and scenarios  
requiring fine-grained control over event consumption.  

## Stream Error Recovery Strategies

Different error recovery strategies handle failures based on requirements,  
from simple logging to sophisticated retry with exponential backoff.  

```dart
import 'dart:async';
import 'dart:math';

Stream<int> unreliableStream() async* {
  Random random = Random();
  
  for (int i = 1; i <= 10; i++) {
    await Future.delayed(Duration(milliseconds: 200));
    
    if (random.nextDouble() < 0.3) {
      throw Exception('Random error at $i');
    }
    
    yield i;
  }
}

// Strategy 1: Log and continue
Stream<T> logErrors<T>(Stream<T> source) {
  return source.handleError((error) {
    print('Error logged: $error');
  });
}

// Strategy 2: Replace with default
Stream<T> withDefault<T>(Stream<T> source, T defaultValue) async* {
  await for (T value in source.handleError((error) {
    print('Error occurred, using default');
  })) {
    yield value;
  }
}

// Strategy 3: Retry with exponential backoff
Stream<T> retryWithBackoff<T>(
  Future<Stream<T>> Function() streamFactory,
  int maxRetries,
) async* {
  int attempt = 0;
  
  while (attempt <= maxRetries) {
    try {
      await for (T value in await streamFactory()) {
        yield value;
      }
      return;
    } catch (e) {
      attempt++;
      if (attempt > maxRetries) {
        print('Max retries exceeded');
        rethrow;
      }
      
      int delayMs = pow(2, attempt).toInt() * 100;
      print('Retry $attempt after ${delayMs}ms');
      await Future.delayed(Duration(milliseconds: delayMs));
    }
  }
}

void main() async {
  print('Strategy 1: Log errors');
  await for (int value in logErrors(unreliableStream())) {
    print('  $value');
  }
  
  print('\nStrategy 2: Default value');
  await for (int value in withDefault(unreliableStream(), -1)) {
    print('  $value');
  }
}
```

Error recovery strategies vary based on requirements. Logging maintains  
visibility, defaults ensure continuity, and retries handle transient  
failures. Choosing the right strategy depends on error types and SLAs.  

## Stream Multiplexing

Multiplexing routes events from multiple streams through a single channel  
with identification, enabling efficient multi-source communication.  

```dart
import 'dart:async';

class StreamMessage<T> {
  final String sourceId;
  final T data;
  
  StreamMessage(this.sourceId, this.data);
  
  @override
  String toString() => 'Message($sourceId: $data)';
}

class StreamMultiplexer<T> {
  final StreamController<StreamMessage<T>> _controller = 
      StreamController<StreamMessage<T>>.broadcast();
  
  Stream<StreamMessage<T>> get multiplexedStream => _controller.stream;
  
  void addSource(String sourceId, Stream<T> source) {
    source.listen(
      (data) => _controller.add(StreamMessage(sourceId, data)),
      onError: (error) => _controller.addError(error),
    );
  }
  
  Stream<T> demultiplex(String sourceId) {
    return _controller.stream
        .where((msg) => msg.sourceId == sourceId)
        .map((msg) => msg.data);
  }
  
  void close() {
    _controller.close();
  }
}

Stream<int> source1() async* {
  for (int i = 1; i <= 3; i++) {
    await Future.delayed(Duration(milliseconds: 300));
    yield i;
  }
}

Stream<String> source2() async* {
  for (String s in ['A', 'B', 'C']) {
    await Future.delayed(Duration(milliseconds: 400));
    yield s;
  }
}

void main() async {
  StreamMultiplexer<dynamic> mux = StreamMultiplexer<dynamic>();
  
  // Add sources
  mux.addSource('numbers', source1());
  mux.addSource('letters', source2());
  
  // Listen to multiplexed stream
  print('Multiplexed stream:');
  mux.multiplexedStream.listen(
    (msg) => print('  $msg'),
  );
  
  await Future.delayed(Duration(seconds: 2));
  
  // Demultiplex specific source
  print('\nDemultiplexed numbers only:');
  mux.demultiplex('numbers').listen(
    (value) => print('  $value'),
  );
  
  await Future.delayed(Duration(seconds: 2));
  
  mux.close();
}
```

Multiplexing combines multiple streams into one channel with source  
identification, enabling efficient routing and selective consumption.  
This pattern is essential for protocol implementation and multi-source data.  

## Stream Rate Limiting

Rate limiting controls event emission frequency to comply with external  
constraints like API rate limits or resource availability.  

```dart
import 'dart:async';
import 'dart:collection';

class RateLimiter<T> {
  final Duration interval;
  final int maxBurst;
  final Queue<Completer<void>> _queue = Queue<Completer<void>>();
  int _tokens;
  Timer? _refillTimer;
  
  RateLimiter({
    required this.interval,
    this.maxBurst = 1,
  }) : _tokens = maxBurst {
    _startRefill();
  }
  
  void _startRefill() {
    _refillTimer = Timer.periodic(interval, (_) {
      if (_tokens < maxBurst) {
        _tokens++;
        if (_queue.isNotEmpty) {
          _queue.removeFirst().complete();
        }
      }
    });
  }
  
  Future<void> acquire() async {
    if (_tokens > 0) {
      _tokens--;
      return;
    }
    
    Completer<void> completer = Completer<void>();
    _queue.add(completer);
    return completer.future;
  }
  
  Stream<T> limit(Stream<T> source) async* {
    await for (T event in source) {
      await acquire();
      yield event;
    }
  }
  
  void dispose() {
    _refillTimer?.cancel();
  }
}

Stream<int> rapidEvents() async* {
  for (int i = 1; i <= 10; i++) {
    await Future.delayed(Duration(milliseconds: 100));
    yield i;
  }
}

void main() async {
  RateLimiter<int> limiter = RateLimiter<int>(
    interval: Duration(milliseconds: 500),
    maxBurst: 2,
  );
  
  print('Rate limited stream:');
  Stopwatch sw = Stopwatch()..start();
  
  await for (int value in limiter.limit(rapidEvents())) {
    int elapsed = sw.elapsedMilliseconds;
    print('  $value at ${elapsed}ms');
  }
  
  limiter.dispose();
}
```

Rate limiting ensures compliance with external rate constraints. Token  
bucket algorithms allow bursts while maintaining average rates, essential  
for API clients and resource-constrained operations.  

## Stream Batching with Time Windows

Time-based batching collects events within time windows, enabling efficient  
batch processing regardless of event frequency variations.  

```dart
import 'dart:async';

Stream<List<T>> batchByTime<T>(
  Stream<T> source,
  Duration windowDuration,
) async* {
  List<T> batch = [];
  Timer? timer;
  StreamController<List<T>> controller = StreamController<List<T>>();
  
  void emitBatch() {
    if (batch.isNotEmpty) {
      controller.add(List.from(batch));
      batch.clear();
    }
  }
  
  source.listen(
    (event) {
      batch.add(event);
      
      timer?.cancel();
      timer = Timer(windowDuration, emitBatch);
    },
    onDone: () {
      timer?.cancel();
      emitBatch();
      controller.close();
    },
    onError: (error) => controller.addError(error),
  );
  
  yield* controller.stream;
}

Stream<int> variableRateEvents() async* {
  // Rapid burst
  for (int i = 1; i <= 3; i++) {
    await Future.delayed(Duration(milliseconds: 100));
    yield i;
  }
  
  await Future.delayed(Duration(milliseconds: 600));
  
  // Another burst
  for (int i = 4; i <= 6; i++) {
    await Future.delayed(Duration(milliseconds: 100));
    yield i;
  }
  
  await Future.delayed(Duration(milliseconds: 800));
  
  // Final events
  yield 7;
  await Future.delayed(Duration(milliseconds: 200));
  yield 8;
}

void main() async {
  print('Time-windowed batches (500ms):');
  
  await for (List<int> batch in batchByTime(
    variableRateEvents(),
    Duration(milliseconds: 500),
  )) {
    print('  Batch: $batch');
  }
}
```

Time-based batching groups events by temporal proximity rather than count.  
This creates natural batches for bursty traffic and ensures timely  
processing even with variable event rates.  

## Stream Sampling

Sampling reduces stream frequency by emitting only periodic samples,  
useful for high-frequency sensors or reducing update rates.  

```dart
import 'dart:async';

Stream<T> sample<T>(Stream<T> source, Duration interval) async* {
  T? lastValue;
  bool hasValue = false;
  
  Timer? sampleTimer;
  StreamController<T> controller = StreamController<T>();
  
  source.listen(
    (value) {
      lastValue = value;
      hasValue = true;
    },
    onDone: () {
      sampleTimer?.cancel();
      controller.close();
    },
    onError: (error) => controller.addError(error),
  );
  
  sampleTimer = Timer.periodic(interval, (_) {
    if (hasValue) {
      controller.add(lastValue as T);
    }
  });
  
  yield* controller.stream;
}

Stream<double> highFrequencySensor() async* {
  double value = 0.0;
  
  for (int i = 0; i < 50; i++) {
    await Future.delayed(Duration(milliseconds: 50));
    value += 0.1;
    yield value;
  }
}

void main() async {
  print('Original high-frequency stream (50ms):');
  int count = 0;
  await for (double value in highFrequencySensor().take(10)) {
    print('  ${value.toStringAsFixed(1)}');
    count++;
  }
  print('  (showing first 10 of 50)');
  
  print('\nSampled stream (300ms):');
  await for (double value in sample(
    highFrequencySensor(),
    Duration(milliseconds: 300),
  )) {
    print('  ${value.toStringAsFixed(1)}');
  }
}
```

Sampling reduces data volume while preserving temporal characteristics.  
It's essential for sensor data, real-time monitoring, and any scenario  
where full-frequency updates exceed processing capacity or requirements.  

## Stream Memoization

Memoization caches stream transformations to avoid redundant computations  
when the same transformation is applied multiple times.  

```dart
import 'dart:async';

class MemoizedStream<T, R> {
  final Map<T, R> _cache = {};
  final Future<R> Function(T) _transformer;
  
  MemoizedStream(this._transformer);
  
  Stream<R> transform(Stream<T> source) async* {
    await for (T value in source) {
      if (_cache.containsKey(value)) {
        print('Cache hit for: $value');
        yield _cache[value]!;
      } else {
        print('Computing for: $value');
        R result = await _transformer(value);
        _cache[value] = result;
        yield result;
      }
    }
  }
  
  void clearCache() {
    _cache.clear();
  }
}

Future<String> expensiveTransform(int value) async {
  await Future.delayed(Duration(milliseconds: 500));
  return 'Processed: $value';
}

Stream<int> dataWithDuplicates() async* {
  var values = [1, 2, 3, 2, 4, 1, 5, 3];
  for (int value in values) {
    await Future.delayed(Duration(milliseconds: 100));
    yield value;
  }
}

void main() async {
  MemoizedStream<int, String> memoizer = 
      MemoizedStream<int, String>(expensiveTransform);
  
  print('Stream with memoization:');
  
  await for (String result in memoizer.transform(dataWithDuplicates())) {
    print('  $result');
  }
}
```

Memoization caches expensive transformations based on input values,  
dramatically improving performance when processing streams with duplicate  
values or when the same stream is processed multiple times.  

## Stream Windowing

Windowing groups consecutive events into fixed-size or sliding windows,  
enabling analysis of event sequences and patterns.  

```dart
import 'dart:async';

Stream<List<T>> windowFixed<T>(Stream<T> source, int size) async* {
  List<T> window = [];
  
  await for (T event in source) {
    window.add(event);
    
    if (window.length == size) {
      yield List.from(window);
      window.clear();
    }
  }
  
  if (window.isNotEmpty) {
    yield window;
  }
}

Stream<List<T>> windowSliding<T>(Stream<T> source, int size) async* {
  List<T> window = [];
  
  await for (T event in source) {
    window.add(event);
    
    if (window.length > size) {
      window.removeAt(0);
    }
    
    if (window.length == size) {
      yield List.from(window);
    }
  }
}

Stream<int> sequence() async* {
  for (int i = 1; i <= 10; i++) {
    await Future.delayed(Duration(milliseconds: 150));
    yield i;
  }
}

void main() async {
  print('Fixed windows (size 3):');
  await for (List<int> window in windowFixed(sequence(), 3)) {
    print('  $window');
  }
  
  print('\nSliding windows (size 3):');
  await for (List<int> window in windowSliding(sequence(), 3)) {
    print('  $window');
  }
}
```

Windowing enables sequential pattern analysis, moving averages, and  
temporal correlation detection. Fixed windows partition data while sliding  
windows provide overlapping views for continuous analysis.  

## Stream Fork Pattern

Forking duplicates a stream into multiple independent streams, each with  
its own processing pipeline and consumer.  

```dart
import 'dart:async';

class StreamFork<T> {
  final Stream<T> _source;
  final List<StreamController<T>> _controllers = [];
  StreamSubscription<T>? _subscription;
  
  StreamFork(this._source);
  
  Stream<T> fork() {
    StreamController<T> controller = StreamController<T>();
    _controllers.add(controller);
    
    if (_subscription == null) {
      _subscription = _source.listen(
        (event) {
          for (var ctrl in _controllers) {
            ctrl.add(event);
          }
        },
        onError: (error) {
          for (var ctrl in _controllers) {
            ctrl.addError(error);
          }
        },
        onDone: () {
          for (var ctrl in _controllers) {
            ctrl.close();
          }
        },
      );
    }
    
    return controller.stream;
  }
  
  void dispose() {
    _subscription?.cancel();
    for (var ctrl in _controllers) {
      ctrl.close();
    }
  }
}

Stream<int> originalStream() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(milliseconds: 300));
    yield i;
  }
}

void main() async {
  StreamFork<int> fork = StreamFork(originalStream());
  
  // Fork 1: Double the values
  Stream<int> fork1 = fork.fork().map((n) => n * 2);
  fork1.listen((value) => print('Fork1 (doubled): $value'));
  
  // Fork 2: Filter evens
  Stream<int> fork2 = fork.fork().where((n) => n % 2 == 0);
  fork2.listen((value) => print('Fork2 (evens): $value'));
  
  // Fork 3: Convert to strings
  Stream<String> fork3 = fork.fork().map((n) => 'Number: $n');
  fork3.listen((value) => print('Fork3 (strings): $value'));
  
  await Future.delayed(Duration(seconds: 2));
  
  fork.dispose();
}
```

Stream forking creates independent processing branches from a single  
source. Each fork can apply different transformations without affecting  
others, enabling parallel processing paths with shared input.  

## Stream Synchronization

Synchronization coordinates multiple streams to ensure events are processed  
in specific orders or combinations based on business logic.  

```dart
import 'dart:async';

class StreamSynchronizer<T> {
  final List<Stream<T>> _sources;
  final StreamController<List<T>> _controller = StreamController<List<T>>();
  final List<T?> _latest;
  final List<bool> _hasValue;
  
  StreamSynchronizer(this._sources)
      : _latest = List<T?>.filled(_sources.length, null),
        _hasValue = List<bool>.filled(_sources.length, false) {
    _initialize();
  }
  
  void _initialize() {
    for (int i = 0; i < _sources.length; i++) {
      _sources[i].listen(
        (value) {
          _latest[i] = value;
          _hasValue[i] = true;
          
          if (_hasValue.every((has) => has)) {
            _controller.add(List<T>.from(_latest.map((v) => v!)));
          }
        },
        onError: (error) => _controller.addError(error),
        onDone: () {
          if (_sources.every((s) => s == _sources[i])) {
            _controller.close();
          }
        },
      );
    }
  }
  
  Stream<List<T>> get synchronized => _controller.stream;
}

Stream<int> stream1() async* {
  for (int i in [1, 4, 7]) {
    await Future.delayed(Duration(milliseconds: 300));
    yield i;
  }
}

Stream<int> stream2() async* {
  for (int i in [2, 5, 8]) {
    await Future.delayed(Duration(milliseconds: 400));
    yield i;
  }
}

Stream<int> stream3() async* {
  for (int i in [3, 6, 9]) {
    await Future.delayed(Duration(milliseconds: 500));
    yield i;
  }
}

void main() async {
  print('Synchronized streams:');
  
  StreamSynchronizer<int> sync = StreamSynchronizer([
    stream1(),
    stream2(),
    stream3(),
  ]);
  
  await for (List<int> values in sync.synchronized) {
    print('  Combined: $values');
  }
}
```

Stream synchronization ensures all sources have emitted values before  
processing. This is critical for combining sensor data, coordinating  
distributed systems, and ensuring data consistency across sources.  

## Stream Replay Pattern

Replay patterns cache and replay stream events to late subscribers,  
ensuring all subscribers receive complete event history.  

```dart
import 'dart:async';

class ReplayStream<T> {
  final Stream<T> _source;
  final int maxReplaySize;
  final List<T> _replayBuffer = [];
  final StreamController<T> _controller = StreamController<T>.broadcast();
  StreamSubscription<T>? _subscription;
  
  ReplayStream(this._source, {this.maxReplaySize = 10}) {
    _subscription = _source.listen(
      (event) {
        _replayBuffer.add(event);
        if (_replayBuffer.length > maxReplaySize) {
          _replayBuffer.removeAt(0);
        }
        _controller.add(event);
      },
      onError: (error) => _controller.addError(error),
      onDone: () => _controller.close(),
    );
  }
  
  Stream<T> get stream async* {
    // Replay buffered events
    for (T event in _replayBuffer) {
      yield event;
    }
    
    // Then stream new events
    yield* _controller.stream;
  }
  
  void dispose() {
    _subscription?.cancel();
    _controller.close();
  }
}

Stream<int> dataStream() async* {
  for (int i = 1; i <= 8; i++) {
    await Future.delayed(Duration(milliseconds: 200));
    yield i;
  }
}

void main() async {
  ReplayStream<int> replay = ReplayStream(dataStream(), maxReplaySize: 5);
  
  // Early subscriber
  replay.stream.listen(
    (value) => print('Early: $value'),
  );
  
  // Wait and add late subscriber
  await Future.delayed(Duration(milliseconds: 800));
  
  print('Late subscriber joining:');
  replay.stream.listen(
    (value) => print('Late: $value'),
  );
  
  await Future.delayed(Duration(seconds: 2));
  
  replay.dispose();
}
```

Replay streams ensure late subscribers receive recent events, critical  
for state synchronization, catch-up scenarios, and ensuring all components  
have consistent views of event history.  

## Stream Circuit Breaker

Circuit breakers prevent cascading failures by temporarily blocking  
operations after error thresholds, giving systems time to recover.  

```dart
import 'dart:async';

enum CircuitState { closed, open, halfOpen }

class CircuitBreaker<T> {
  CircuitState _state = CircuitState.closed;
  int _failureCount = 0;
  final int failureThreshold;
  final Duration resetTimeout;
  Timer? _resetTimer;
  
  CircuitBreaker({
    this.failureThreshold = 3,
    this.resetTimeout = const Duration(seconds: 5),
  });
  
  Stream<T> protect(Stream<T> source) async* {
    await for (T event in source) {
      if (_state == CircuitState.open) {
        throw Exception('Circuit breaker is OPEN');
      }
      
      try {
        yield event;
        _onSuccess();
      } catch (e) {
        _onFailure();
        rethrow;
      }
    }
  }
  
  void _onSuccess() {
    _failureCount = 0;
    if (_state == CircuitState.halfOpen) {
      _state = CircuitState.closed;
      print('Circuit breaker: CLOSED');
    }
  }
  
  void _onFailure() {
    _failureCount++;
    print('Circuit breaker: Failure $_failureCount/$failureThreshold');
    
    if (_failureCount >= failureThreshold) {
      _open();
    }
  }
  
  void _open() {
    _state = CircuitState.open;
    print('Circuit breaker: OPEN');
    
    _resetTimer?.cancel();
    _resetTimer = Timer(resetTimeout, () {
      _state = CircuitState.halfOpen;
      _failureCount = 0;
      print('Circuit breaker: HALF-OPEN');
    });
  }
}

Stream<int> unreliableService() async* {
  for (int i = 1; i <= 10; i++) {
    await Future.delayed(Duration(milliseconds: 300));
    
    if (i >= 3 && i <= 5) {
      throw Exception('Service error at $i');
    }
    
    yield i;
  }
}

void main() async {
  CircuitBreaker<int> breaker = CircuitBreaker<int>(
    failureThreshold: 2,
    resetTimeout: Duration(seconds: 2),
  );
  
  try {
    await for (int value in breaker.protect(unreliableService())) {
      print('Received: $value');
    }
  } catch (e) {
    print('Stream failed: $e');
  }
}
```

Circuit breakers protect systems from cascading failures by failing fast  
when error thresholds are exceeded. They're essential for resilient  
distributed systems and preventing resource exhaustion.  

## Best Practices Summary

Stream programming requires careful consideration of memory, errors, and  
lifecycle management. Following best practices ensures robust applications.  

```dart
import 'dart:async';

// Practice 1: Always close StreamControllers
class GoodController {
  final StreamController<int> _controller = StreamController<int>();
  
  Stream<int> get stream => _controller.stream;
  
  void add(int value) => _controller.add(value);
  
  // Always provide dispose/close method
  void dispose() {
    _controller.close();
  }
}

// Practice 2: Cancel subscriptions
void demonstrateSubscriptionCancellation() async {
  Stream<int> infiniteStream = Stream<int>.periodic(
    Duration(milliseconds: 100),
    (count) => count,
  );
  
  StreamSubscription<int> subscription = infiniteStream.listen(
    (value) => print('Value: $value'),
  );
  
  await Future.delayed(Duration(milliseconds: 500));
  
  // Always cancel when done
  subscription.cancel();
}

// Practice 3: Handle errors appropriately
Stream<int> robustStream() async* {
  try {
    for (int i = 1; i <= 5; i++) {
      await Future.delayed(Duration(milliseconds: 200));
      
      if (i == 3) {
        throw Exception('Expected error');
      }
      
      yield i;
    }
  } catch (e) {
    print('Error in stream: $e');
    // Either rethrow or handle gracefully
  }
}

// Practice 4: Use broadcast streams appropriately
class EventBusExample {
  final StreamController<String> _controller = 
      StreamController<String>.broadcast(); // Use broadcast for multiple listeners
  
  Stream<String> get events => _controller.stream;
  
  void fireEvent(String event) => _controller.add(event);
  
  void dispose() => _controller.close();
}

// Practice 5: Avoid memory leaks with proper cleanup
class StreamManager {
  final List<StreamSubscription> _subscriptions = [];
  
  void addSubscription(StreamSubscription subscription) {
    _subscriptions.add(subscription);
  }
  
  void disposeAll() {
    for (var subscription in _subscriptions) {
      subscription.cancel();
    }
    _subscriptions.clear();
  }
}

void main() async {
  print('Best practices demonstration:');
  
  // Good controller usage
  GoodController controller = GoodController();
  controller.stream.listen((value) => print('  Controller: $value'));
  controller.add(1);
  controller.add(2);
  await Future.delayed(Duration(milliseconds: 100));
  controller.dispose(); // Always dispose
  
  // Subscription cancellation
  await demonstrateSubscriptionCancellation();
  
  // Robust error handling
  print('\nRobust stream:');
  await for (int value in robustStream()) {
    print('  $value');
  }
}
```

Key best practices: always close controllers and cancel subscriptions to  
prevent memory leaks; handle errors appropriately; use broadcast streams  
for multiple listeners; implement proper lifecycle management; and avoid  
blocking operations in stream callbacks.  

## Performance Considerations

Stream performance depends on proper buffering, avoiding blocking  
operations, and choosing appropriate patterns for workload characteristics.  

```dart
import 'dart:async';

// Anti-pattern: Blocking operations in callbacks
void badPerformance() async {
  Stream<int> stream = Stream<int>.periodic(
    Duration(milliseconds: 100),
    (count) => count,
  ).take(5);
  
  stream.listen((value) {
    // DON'T: Blocking operation
    // This delays processing of subsequent events
    print('Processing $value');
  });
}

// Good pattern: Async operations with proper concurrency
void goodPerformance() async {
  Stream<int> stream = Stream<int>.periodic(
    Duration(milliseconds: 100),
    (count) => count,
  ).take(5);
  
  // Use asyncMap for async operations
  await for (int result in stream.asyncMap((value) async {
    // Async operations don't block the stream
    await Future.delayed(Duration(milliseconds: 50));
    return value * 2;
  })) {
    print('Processed: $result');
  }
}

// Buffering for efficiency
Stream<List<T>> efficientBatching<T>(
  Stream<T> source,
  int batchSize,
) async* {
  List<T> buffer = [];
  
  await for (T event in source) {
    buffer.add(event);
    
    if (buffer.length >= batchSize) {
      yield List.from(buffer);
      buffer.clear();
    }
  }
  
  if (buffer.isNotEmpty) {
    yield buffer;
  }
}

void main() async {
  print('Bad performance example:');
  badPerformance();
  
  await Future.delayed(Duration(seconds: 1));
  
  print('\nGood performance example:');
  await goodPerformance();
  
  print('\nEfficient batching:');
  Stream<int> source = Stream.fromIterable(List.generate(10, (i) => i + 1));
  await for (List<int> batch in efficientBatching(source, 3)) {
    print('Batch: $batch');
  }
}
```

Performance optimization requires avoiding blocking operations in stream  
callbacks, using appropriate buffering strategies, and choosing patterns  
that match workload characteristics. Always profile before optimizing.  

## Common Pitfalls to Avoid

Understanding common mistakes helps developers avoid subtle bugs and  
performance issues in stream-based applications.  

```dart
import 'dart:async';

// Pitfall 1: Listening to single-subscription streams multiple times
void pitfall1() {
  Stream<int> stream = Stream.fromIterable([1, 2, 3]);
  
  // First listener works
  stream.listen((value) => print('Listener 1: $value'));
  
  // Second listener throws error
  // stream.listen((value) => print('Listener 2: $value')); // BAD!
  
  // Solution: Use asBroadcastStream() or create new stream
  Stream<int> broadcast = Stream.fromIterable([1, 2, 3]).asBroadcastStream();
  broadcast.listen((value) => print('Broadcast 1: $value'));
  broadcast.listen((value) => print('Broadcast 2: $value'));
}

// Pitfall 2: Not cancelling subscriptions
void pitfall2() async {
  Stream<int> infiniteStream = Stream<int>.periodic(
    Duration(milliseconds: 100),
    (count) => count,
  );
  
  StreamSubscription subscription = infiniteStream.listen(
    (value) => print('Value: $value'),
  );
  
  await Future.delayed(Duration(milliseconds: 300));
  
  // MUST cancel to prevent memory leak
  subscription.cancel();
}

// Pitfall 3: Forgetting to handle errors
void pitfall3() async {
  Stream<int> faultyStream = Stream.fromFuture(
    Future.error('Something failed'),
  );
  
  // BAD: No error handler - unhandled exception
  // await for (int value in faultyStream) {
  //   print(value);
  // }
  
  // GOOD: Handle errors
  try {
    await for (int value in faultyStream) {
      print(value);
    }
  } catch (e) {
    print('Caught error: $e');
  }
}

// Pitfall 4: Blocking in stream transformations
void pitfall4() async {
  Stream<int> source = Stream.fromIterable([1, 2, 3, 4, 5]);
  
  // BAD: Blocking operation
  // source.map((n) {
  //   Thread.sleep(1000); // Blocks entire stream
  //   return n * 2;
  // });
  
  // GOOD: Use asyncMap for async operations
  await for (int value in source.asyncMap((n) async {
    await Future.delayed(Duration(milliseconds: 100));
    return n * 2;
  })) {
    print('Value: $value');
  }
}

void main() async {
  print('Demonstrating common pitfalls:');
  
  print('\nPitfall 1: Multiple listeners');
  pitfall1();
  
  await Future.delayed(Duration(milliseconds: 500));
  
  print('\nPitfall 2: Not cancelling subscriptions');
  await pitfall2();
  
  print('\nPitfall 3: Missing error handlers');
  await pitfall3();
  
  print('\nPitfall 4: Blocking transformations');
  await pitfall4();
}
```

Common pitfalls include multiple listeners on single-subscription streams,  
forgetting to cancel subscriptions, missing error handlers, and blocking  
operations in transformations. Understanding these helps avoid subtle bugs.  

## Stream Completion Detection

Detecting when all operations complete is crucial for cleanup and  
coordination in complex stream pipelines with multiple stages.  

```dart
import 'dart:async';

class CompletionTracker {
  final List<Completer<void>> _completers = [];
  
  Future<void> trackStream(Stream stream) {
    Completer<void> completer = Completer<void>();
    _completers.add(completer);
    
    stream.listen(
      (_) {}, // Process events
      onDone: () => completer.complete(),
      onError: (error) => completer.completeError(error),
    );
    
    return completer.future;
  }
  
  Future<void> waitForAll() {
    return Future.wait(_completers.map((c) => c.future));
  }
}

Stream<int> stream1() async* {
  for (int i = 1; i <= 3; i++) {
    await Future.delayed(Duration(milliseconds: 300));
    yield i;
  }
  print('Stream 1 completed');
}

Stream<String> stream2() async* {
  for (String s in ['A', 'B', 'C', 'D']) {
    await Future.delayed(Duration(milliseconds: 250));
    yield s;
  }
  print('Stream 2 completed');
}

Stream<double> stream3() async* {
  for (double d in [1.1, 2.2]) {
    await Future.delayed(Duration(milliseconds: 500));
    yield d;
  }
  print('Stream 3 completed');
}

void main() async {
  CompletionTracker tracker = CompletionTracker();
  
  print('Starting multiple streams:');
  
  tracker.trackStream(stream1());
  tracker.trackStream(stream2());
  tracker.trackStream(stream3());
  
  await tracker.waitForAll();
  
  print('All streams completed!');
}
```

Completion tracking enables coordination of multiple async operations,  
ensuring all work completes before proceeding. This is essential for  
cleanup, resource release, and multi-stage pipeline coordination.  

## Stream Cancellation Propagation

Proper cancellation propagation ensures resources are released throughout  
the entire stream chain when subscription is cancelled.  

```dart
import 'dart:async';

class CancellableStream<T> {
  final Stream<T> _source;
  final List<void Function()> _onCancelCallbacks = [];
  
  CancellableStream(this._source);
  
  void onCancel(void Function() callback) {
    _onCancelCallbacks.add(callback);
  }
  
  Stream<T> get stream {
    StreamController<T> controller = StreamController<T>();
    StreamSubscription<T>? subscription;
    
    controller.onListen = () {
      subscription = _source.listen(
        (data) => controller.add(data),
        onError: (error) => controller.addError(error),
        onDone: () => controller.close(),
      );
    };
    
    controller.onCancel = () {
      subscription?.cancel();
      for (var callback in _onCancelCallbacks) {
        callback();
      }
    };
    
    return controller.stream;
  }
}

Stream<int> resourceStream() async* {
  print('Opening resource');
  
  try {
    for (int i = 1; i <= 10; i++) {
      await Future.delayed(Duration(milliseconds: 200));
      yield i;
    }
  } finally {
    print('Closing resource in finally');
  }
}

void main() async {
  CancellableStream<int> cancellable = CancellableStream(resourceStream());
  
  cancellable.onCancel(() {
    print('Cancellation callback: Cleanup performed');
  });
  
  StreamSubscription<int> subscription = cancellable.stream.listen(
    (value) => print('Received: $value'),
  );
  
  // Cancel after receiving a few events
  await Future.delayed(Duration(milliseconds: 600));
  
  print('Cancelling subscription');
  await subscription.cancel();
  
  await Future.delayed(Duration(milliseconds: 300));
  print('Done');
}
```

Cancellation propagation ensures clean resource release when streams are  
cancelled. Properly implemented cancellation prevents memory leaks and  
ensures external resources like files, sockets, and timers are released.  

## Advanced Stream Composition

Complex stream compositions combine multiple patterns to create  
sophisticated data processing pipelines for real-world applications.  

```dart
import 'dart:async';

class DataPipeline<T> {
  final Stream<T> source;
  final List<Stream<T> Function(Stream<T>)> _transformations = [];
  
  DataPipeline(this.source);
  
  DataPipeline<T> filter(bool Function(T) predicate) {
    _transformations.add((stream) => stream.where(predicate));
    return this;
  }
  
  DataPipeline<R> map<R>(R Function(T) mapper) {
    var pipeline = DataPipeline<R>(
      _build().map(mapper),
    );
    return pipeline;
  }
  
  DataPipeline<T> distinct() {
    _transformations.add((stream) => stream.distinct());
    return this;
  }
  
  DataPipeline<T> take(int count) {
    _transformations.add((stream) => stream.take(count));
    return this;
  }
  
  DataPipeline<T> handleErrors(Function(Object) handler) {
    _transformations.add((stream) => stream.handleError(handler));
    return this;
  }
  
  Stream<T> _build() {
    Stream<T> result = source;
    for (var transformation in _transformations) {
      result = transformation(result);
    }
    return result;
  }
  
  Stream<T> build() => _build();
  
  Future<List<T>> toList() => _build().toList();
  
  Future<void> forEach(void Function(T) action) => _build().forEach(action);
}

Stream<int> dataSource() async* {
  var data = [1, 5, 2, 8, 5, 3, 9, 2, 7, 1, 6, 4];
  for (int value in data) {
    await Future.delayed(Duration(milliseconds: 150));
    yield value;
  }
}

void main() async {
  print('Complex pipeline composition:');
  
  DataPipeline<int> pipeline = DataPipeline(dataSource())
      .filter((n) => n > 2)           // Only values > 2
      .distinct()                      // Remove duplicates
      .take(5);                        // Take first 5
  
  DataPipeline<String> formatted = pipeline
      .map((n) => 'Value: $n');       // Format as strings
  
  await formatted.forEach((item) => print('  $item'));
  
  print('\nDirect to list:');
  List<int> results = await DataPipeline(dataSource())
      .filter((n) => n % 2 == 0)
      .distinct()
      .toList();
  
  print('  Even values: $results');
}
```

Advanced composition patterns enable building complex, reusable data  
pipelines with declarative syntax. This approach creates maintainable  
code for sophisticated stream processing requirements in production systems.  
