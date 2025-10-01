# Dart Isolates

Isolates are Dart's approach to concurrency and parallelism, providing  
isolated execution contexts with independent memory heaps. Unlike threads,  
isolates do not share memory, which eliminates race conditions and the need  
for locks, making concurrent programming safer and more predictable.  

Each isolate has its own event loop and memory, communicating through  
message passing via ports. This architecture enables true parallel execution  
on multi-core processors while maintaining memory safety. Isolates are  
fundamental for building high-performance Flutter and Dart applications that  
need to perform CPU-intensive tasks without blocking the UI thread.  

Dart 3.9 provides powerful APIs for isolate management including Isolate.run  
for simple parallel tasks, Isolate.spawn for custom isolate creation, and  
sophisticated error handling and lifecycle management capabilities.  

## Basic Isolate with Isolate.run

The Isolate.run method provides a simple way to execute code in a separate  
isolate. It handles isolate creation, message passing, and cleanup  
automatically, making it ideal for straightforward parallel tasks.  

```dart
import 'dart:isolate';

void main() async {
  // Simple computation in isolate
  int result = await Isolate.run(() {
    int sum = 0;
    for (int i = 1; i <= 1000000; i++) {
      sum += i;
    }
    return sum;
  });
  
  print('Sum: $result');
  
  // With parameter passing
  String message = await Isolate.run(() {
    return 'Hello there! from isolate';
  });
  
  print(message);
}
```

Isolate.run simplifies parallel execution by abstracting isolate management  
details. The function executes in a separate isolate and returns the result  
through a Future, automatically handling cleanup when the computation  
completes.  

## Computing Heavy Task in Isolate

CPU-intensive operations should run in isolates to prevent blocking the main  
thread. This example demonstrates processing a computationally expensive  
task without affecting responsiveness.  

```dart
import 'dart:isolate';

int fibonacci(int n) {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
}

void main() async {
  print('Starting heavy computation...');
  
  var stopwatch = Stopwatch()..start();
  
  // Run in isolate
  int result = await Isolate.run(() => fibonacci(42));
  
  stopwatch.stop();
  
  print('Fibonacci(42) = $result');
  print('Time: ${stopwatch.elapsedMilliseconds}ms');
  print('Main thread remained responsive');
}
```

Running computationally expensive operations like recursive Fibonacci  
calculations in isolates prevents UI freezing in Flutter applications. The  
main isolate continues processing events while the worker isolate performs  
the calculation.  

## Passing Data to Isolates

Isolates communicate through message passing, and data must be transferable  
between isolates. This includes primitive types, lists, maps, and certain  
objects that can be copied.  

```dart
import 'dart:isolate';

class DataPacket {
  final String id;
  final List<int> values;
  final Map<String, dynamic> metadata;
  
  DataPacket(this.id, this.values, this.metadata);
  
  @override
  String toString() => 'DataPacket($id, ${values.length} values)';
}

void main() async {
  // Pass simple types
  int doubled = await Isolate.run(() => 42 * 2);
  print('Doubled: $doubled');
  
  // Pass collections
  List<int> sorted = await Isolate.run(() {
    List<int> numbers = [5, 2, 8, 1, 9, 3];
    numbers.sort();
    return numbers;
  });
  print('Sorted: $sorted');
  
  // Pass complex data
  Map<String, int> result = await Isolate.run(() {
    return {
      'sum': 100,
      'count': 10,
      'average': 10,
    };
  });
  print('Result: $result');
}
```

Data passed between isolates is copied, not shared. Supported types include  
numbers, booleans, strings, lists, maps, and transferable objects. Large  
data structures incur copying overhead, so consider data size when designing  
isolate communication.  

## Isolate.spawn with SendPort and ReceivePort

For more control over isolate lifecycle and bidirectional communication,  
Isolate.spawn combined with SendPort and ReceivePort provides low-level  
message passing capabilities.  

```dart
import 'dart:isolate';

void isolateEntry(SendPort sendPort) {
  // Worker isolate entry point
  int result = 0;
  for (int i = 1; i <= 100; i++) {
    result += i;
  }
  
  // Send result back to main isolate
  sendPort.send(result);
}

void main() async {
  // Create ReceivePort to get messages
  ReceivePort receivePort = ReceivePort();
  
  // Spawn isolate with our entry point
  await Isolate.spawn(isolateEntry, receivePort.sendPort);
  
  // Wait for result
  var result = await receivePort.first;
  print('Sum from isolate: $result');
  
  // Clean up
  receivePort.close();
}
```

Isolate.spawn creates a new isolate with a specified entry function. The  
SendPort allows the worker isolate to send messages back to the main  
isolate. ReceivePort.first waits for the first message and automatically  
closes the port.  

## Bidirectional Communication

Establishing two-way communication between isolates enables interactive  
patterns where the main isolate sends requests and receives responses from  
worker isolates.  

```dart
import 'dart:isolate';

void workerIsolate(SendPort mainSendPort) async {
  // Create receive port for this isolate
  ReceivePort workerReceivePort = ReceivePort();
  
  // Send our send port to main isolate
  mainSendPort.send(workerReceivePort.sendPort);
  
  // Listen for messages
  await for (var message in workerReceivePort) {
    if (message == 'shutdown') {
      workerReceivePort.close();
      break;
    }
    
    // Process and respond
    int squared = message * message;
    mainSendPort.send(squared);
  }
}

void main() async {
  ReceivePort mainReceivePort = ReceivePort();
  
  // Spawn worker
  await Isolate.spawn(workerIsolate, mainReceivePort.sendPort);
  
  // Get worker's send port
  SendPort workerSendPort = await mainReceivePort.first as SendPort;
  
  // Send messages to worker
  workerSendPort.send(5);
  workerSendPort.send(10);
  workerSendPort.send(15);
  
  // Receive responses
  int count = 0;
  await for (var response in mainReceivePort) {
    print('Received: $response');
    count++;
    
    if (count == 3) {
      workerSendPort.send('shutdown');
      break;
    }
  }
  
  mainReceivePort.close();
  print('Communication complete');
}
```

Bidirectional communication establishes a message exchange pattern where both  
isolates can send and receive messages. Each isolate maintains its own  
ReceivePort and shares its SendPort with the other isolate for message  
routing.  

## Multiple Isolates in Parallel

Creating multiple worker isolates enables parallel processing of independent  
tasks, maximizing CPU utilization on multi-core systems.  

```dart
import 'dart:isolate';

int computeChunk(List<int> numbers) {
  return numbers.fold(0, (sum, n) => sum + n * n);
}

void chunkWorker(List<dynamic> args) {
  SendPort sendPort = args[0];
  List<int> chunk = args[1];
  
  int result = computeChunk(chunk);
  sendPort.send(result);
}

void main() async {
  List<int> data = List.generate(1000, (i) => i + 1);
  
  // Split data into chunks
  int chunkSize = 250;
  List<List<int>> chunks = [];
  for (int i = 0; i < data.length; i += chunkSize) {
    chunks.add(data.sublist(
      i, 
      i + chunkSize < data.length ? i + chunkSize : data.length
    ));
  }
  
  // Create isolate for each chunk
  List<Future<int>> futures = [];
  
  for (var chunk in chunks) {
    ReceivePort receivePort = ReceivePort();
    await Isolate.spawn(chunkWorker, [receivePort.sendPort, chunk]);
    futures.add(receivePort.first.then((result) {
      receivePort.close();
      return result as int;
    }));
  }
  
  // Wait for all results
  List<int> results = await Future.wait(futures);
  int total = results.fold(0, (sum, result) => sum + result);
  
  print('Processed ${chunks.length} chunks in parallel');
  print('Total sum of squares: $total');
}
```

Parallel processing with multiple isolates divides work across CPU cores.  
Each isolate processes its chunk independently, and results are collected  
using Future.wait. This pattern maximizes throughput for data-parallel  
computations.  

## Error Handling in Isolates

Isolates can encounter errors during execution. Proper error handling  
ensures robust applications that gracefully manage failures in worker  
isolates.  

```dart
import 'dart:isolate';

void faultyWorker(SendPort sendPort) {
  try {
    // Simulate work that might fail
    List<int> numbers = [1, 2, 3];
    int value = numbers[10]; // Index out of bounds
    
    sendPort.send(value);
  } catch (e) {
    sendPort.send('Error: $e');
  }
}

void main() async {
  // Using Isolate.run with try-catch
  try {
    await Isolate.run(() {
      throw Exception('Something went wrong in isolate');
    });
  } catch (e) {
    print('Caught error from Isolate.run: $e');
  }
  
  // Using spawn with error handling
  ReceivePort receivePort = ReceivePort();
  ReceivePort errorPort = ReceivePort();
  
  await Isolate.spawn(
    faultyWorker,
    receivePort.sendPort,
    onError: errorPort.sendPort,
  );
  
  // Listen for both messages and errors
  receivePort.listen((message) {
    print('Received: $message');
    receivePort.close();
    errorPort.close();
  });
  
  errorPort.listen((error) {
    print('Error port received: $error');
    receivePort.close();
    errorPort.close();
  });
  
  await Future.delayed(Duration(seconds: 1));
}
```

Isolate.run automatically propagates exceptions to the caller. With  
Isolate.spawn, the onError parameter captures uncaught errors. Worker  
isolates should also implement try-catch blocks to handle expected errors  
and send error messages through normal channels.  

## Isolate Error Port

The error port provides a dedicated channel for capturing uncaught errors  
and exceptions that occur in worker isolates, separate from normal message  
flow.  

```dart
import 'dart:isolate';

void riskyWorker(int input) {
  if (input < 0) {
    throw ArgumentError('Negative input not allowed');
  }
  
  // Simulate processing
  int result = 100 ~/ input;
  print('Result: $result');
}

void main() async {
  ReceivePort errorPort = ReceivePort();
  
  // Configure error handling
  errorPort.listen((errorData) {
    print('Error captured:');
    print('  Message: ${errorData[0]}');
    print('  Stack trace: ${errorData[1]}');
    errorPort.close();
  });
  
  try {
    // Spawn with error port
    await Isolate.spawn(
      riskyWorker,
      0, // This will cause division by zero
      onError: errorPort.sendPort,
    );
  } catch (e) {
    print('Spawn error: $e');
  }
  
  await Future.delayed(Duration(seconds: 1));
}
```

The error port receives a list containing the error message and stack trace  
when an uncaught exception occurs in the isolate. This separation allows  
distinguishing between normal results and error conditions in long-running  
worker isolates.  

## Isolate Exit Port

The exit port notifies when an isolate terminates, either normally or due to  
error. This enables cleanup and resource management in supervising isolates.  

```dart
import 'dart:isolate';

void shortLivedWorker(SendPort sendPort) {
  sendPort.send('Starting work');
  
  // Do some work
  int sum = 0;
  for (int i = 0; i < 1000; i++) {
    sum += i;
  }
  
  sendPort.send('Work complete: $sum');
  // Isolate exits here
}

void main() async {
  ReceivePort receivePort = ReceivePort();
  ReceivePort exitPort = ReceivePort();
  
  exitPort.listen((message) {
    print('Isolate exited with message: $message');
    exitPort.close();
  });
  
  Isolate isolate = await Isolate.spawn(
    shortLivedWorker,
    receivePort.sendPort,
    onExit: exitPort.sendPort,
  );
  
  await for (var message in receivePort) {
    print('Received: $message');
  }
  
  receivePort.close();
  await Future.delayed(Duration(milliseconds: 100));
}
```

The exit port receives a final message when the isolate terminates. This is  
useful for tracking isolate lifecycle, performing cleanup, or restarting  
failed workers. The exit message can be customized or null if not specified.  

## Pausing and Resuming Isolates

Isolates can be paused and resumed to control execution flow, useful for  
implementing backpressure or coordinating multiple workers.  

```dart
import 'dart:isolate';

void controllableWorker(SendPort sendPort) async {
  int counter = 0;
  
  while (counter < 20) {
    counter++;
    sendPort.send('Count: $counter');
    await Future.delayed(Duration(milliseconds: 100));
  }
  
  sendPort.send('Done');
}

void main() async {
  ReceivePort receivePort = ReceivePort();
  
  Isolate isolate = await Isolate.spawn(
    controllableWorker,
    receivePort.sendPort,
  );
  
  int messageCount = 0;
  
  receivePort.listen((message) {
    print(message);
    messageCount++;
    
    // Pause after 5 messages
    if (messageCount == 5) {
      print('Pausing isolate...');
      Capability capability = isolate.pause();
      
      // Resume after delay
      Future.delayed(Duration(seconds: 2), () {
        print('Resuming isolate...');
        isolate.resume(capability);
      });
    }
    
    if (message == 'Done') {
      receivePort.close();
      isolate.kill();
    }
  });
}
```

Pausing an isolate suspends its execution and returns a Capability that must  
be used to resume it. This mechanism enables flow control, preventing worker  
isolates from overwhelming the main isolate with messages.  


## Killing Isolates

Isolates can be terminated forcefully using the kill method, which is  
necessary for cleanup and resource management when workers are no longer  
needed.  

```dart
import 'dart:isolate';

void endlessWorker(SendPort sendPort) async {
  int count = 0;
  while (true) {
    count++;
    sendPort.send('Message $count');
    await Future.delayed(Duration(milliseconds: 500));
  }
}

void main() async {
  ReceivePort receivePort = ReceivePort();
  
  Isolate isolate = await Isolate.spawn(
    endlessWorker,
    receivePort.sendPort,
  );
  
  int messageCount = 0;
  
  receivePort.listen((message) {
    print(message);
    messageCount++;
    
    if (messageCount >= 5) {
      print('Killing isolate...');
      isolate.kill(priority: Isolate.immediate);
      receivePort.close();
    }
  });
  
  await Future.delayed(Duration(seconds: 5));
  print('Main isolate exiting');
}
```

The kill method immediately terminates an isolate. Priority can be set to  
'immediate' for urgent termination or 'beforeNextEvent' for graceful  
shutdown. Always close associated ports after killing isolates to free  
resources.  

## Long-Running Isolate with Command Pattern

Long-running isolates that process multiple commands implement a message  
loop pattern, staying alive to handle various requests over time.  

```dart
import 'dart:isolate';

class Command {
  final String type;
  final dynamic data;
  final SendPort? replyPort;
  
  Command(this.type, this.data, [this.replyPort]);
}

void commandProcessor(SendPort mainSendPort) async {
  ReceivePort commandPort = ReceivePort();
  mainSendPort.send(commandPort.sendPort);
  
  await for (var message in commandPort) {
    if (message is Command) {
      switch (message.type) {
        case 'square':
          int result = message.data * message.data;
          message.replyPort?.send(result);
          break;
          
        case 'sum':
          List<int> numbers = message.data;
          int sum = numbers.fold(0, (a, b) => a + b);
          message.replyPort?.send(sum);
          break;
          
        case 'exit':
          commandPort.close();
          return;
          
        default:
          message.replyPort?.send('Unknown command: ${message.type}');
      }
    }
  }
}

void main() async {
  ReceivePort mainPort = ReceivePort();
  
  await Isolate.spawn(commandProcessor, mainPort.sendPort);
  SendPort commandSender = await mainPort.first as SendPort;
  
  // Send various commands
  ReceivePort replyPort1 = ReceivePort();
  commandSender.send(Command('square', 7, replyPort1.sendPort));
  print('Square result: ${await replyPort1.first}');
  replyPort1.close();
  
  ReceivePort replyPort2 = ReceivePort();
  commandSender.send(Command('sum', [1, 2, 3, 4, 5], replyPort2.sendPort));
  print('Sum result: ${await replyPort2.first}');
  replyPort2.close();
  
  // Shutdown
  commandSender.send(Command('exit', null));
  mainPort.close();
}
```

The command pattern enables flexible worker isolates that perform different  
operations based on message type. Each command carries its own reply port  
for sending results back, supporting asynchronous request-response  
interactions.  

## Isolate Pool Pattern

A pool of worker isolates enables efficient task distribution and load  
balancing across multiple workers, maximizing resource utilization.  

```dart
import 'dart:isolate';
import 'dart:async';

class WorkerPool {
  final int size;
  final List<SendPort> _workers = [];
  final List<bool> _available = [];
  int _nextWorker = 0;
  
  WorkerPool(this.size);
  
  Future<void> initialize() async {
    for (int i = 0; i < size; i++) {
      ReceivePort receivePort = ReceivePort();
      await Isolate.spawn(_workerEntry, receivePort.sendPort);
      SendPort sendPort = await receivePort.first as SendPort;
      _workers.add(sendPort);
      _available.add(true);
    }
  }
  
  static void _workerEntry(SendPort mainSendPort) async {
    ReceivePort workerPort = ReceivePort();
    mainSendPort.send(workerPort.sendPort);
    
    await for (var message in workerPort) {
      if (message is List && message.length == 2) {
        int value = message[0];
        SendPort replyPort = message[1];
        
        // Simulate work
        await Future.delayed(Duration(milliseconds: 100));
        int result = value * value;
        
        replyPort.send(result);
      }
    }
  }
  
  Future<int> execute(int value) async {
    // Simple round-robin distribution
    SendPort worker = _workers[_nextWorker];
    _nextWorker = (_nextWorker + 1) % size;
    
    ReceivePort replyPort = ReceivePort();
    worker.send([value, replyPort.sendPort]);
    
    int result = await replyPort.first as int;
    replyPort.close();
    
    return result;
  }
}

void main() async {
  WorkerPool pool = WorkerPool(3);
  await pool.initialize();
  
  print('Processing tasks with pool of ${pool.size} workers...');
  
  List<Future<int>> tasks = [];
  for (int i = 1; i <= 10; i++) {
    tasks.add(pool.execute(i));
  }
  
  List<int> results = await Future.wait(tasks);
  print('Results: $results');
}
```

Worker pools distribute tasks across multiple isolates, preventing any  
single worker from becoming a bottleneck. Round-robin or more sophisticated  
scheduling algorithms can be implemented for optimal load distribution.  

## Priority Queue for Isolate Tasks

Implementing priority-based task scheduling ensures critical tasks receive  
preferential treatment when multiple tasks compete for isolate resources.  

```dart
import 'dart:isolate';
import 'dart:collection';

class PriorityTask implements Comparable<PriorityTask> {
  final int priority;
  final Function() work;
  final SendPort replyPort;
  
  PriorityTask(this.priority, this.work, this.replyPort);
  
  @override
  int compareTo(PriorityTask other) => other.priority - priority;
}

void priorityWorker(SendPort mainSendPort) async {
  ReceivePort workerPort = ReceivePort();
  mainSendPort.send(workerPort.sendPort);
  
  PriorityQueue<PriorityTask> taskQueue = PriorityQueue<PriorityTask>();
  bool processing = false;
  
  Future<void> processTasks() async {
    if (processing || taskQueue.isEmpty) return;
    
    processing = true;
    while (taskQueue.isNotEmpty) {
      PriorityTask task = taskQueue.removeFirst();
      var result = task.work();
      task.replyPort.send(result);
      await Future.delayed(Duration.zero); // Yield
    }
    processing = false;
  }
  
  await for (var message in workerPort) {
    if (message is PriorityTask) {
      taskQueue.add(message);
      processTasks();
    } else if (message == 'shutdown') {
      workerPort.close();
      break;
    }
  }
}

void main() async {
  ReceivePort mainPort = ReceivePort();
  
  await Isolate.spawn(priorityWorker, mainPort.sendPort);
  SendPort workerSendPort = await mainPort.first as SendPort;
  
  // Create tasks with different priorities
  List<Future> results = [];
  
  for (int i = 0; i < 5; i++) {
    ReceivePort replyPort = ReceivePort();
    int priority = i % 2 == 0 ? 10 : 1; // Alternate high/low priority
    
    // Note: Can't pass functions, so we use predefined operations
    workerSendPort.send(PriorityTask(
      priority,
      () => 'Task $i (priority $priority)',
      replyPort.sendPort,
    ));
    
    results.add(replyPort.first.then((result) {
      print('Completed: $result');
      replyPort.close();
    }));
  }
  
  await Future.wait(results);
  workerSendPort.send('shutdown');
  mainPort.close();
}
```

Priority queues ensure high-priority tasks execute before lower-priority  
ones. This pattern is essential for systems where task importance varies,  
such as UI frameworks responding to user input versus background  
processing.  

## Streaming Data Through Isolates

Streaming large datasets through isolates enables processing data in chunks  
without loading everything into memory simultaneously.  

```dart
import 'dart:isolate';
import 'dart:async';

void streamProcessor(SendPort mainSendPort) async {
  ReceivePort receivePort = ReceivePort();
  mainSendPort.send(receivePort.sendPort);
  
  await for (var message in receivePort) {
    if (message == 'DONE') {
      mainSendPort.send('COMPLETE');
      receivePort.close();
      break;
    }
    
    if (message is int) {
      // Process each item
      int processed = message * message;
      mainSendPort.send(processed);
    }
  }
}

void main() async {
  ReceivePort mainPort = ReceivePort();
  
  await Isolate.spawn(streamProcessor, mainPort.sendPort);
  SendPort workerPort = await mainPort.first as SendPort;
  
  // Stream processing
  StreamController<int> inputController = StreamController<int>();
  List<int> results = [];
  
  // Listen for processed results
  mainPort.listen((message) {
    if (message == 'COMPLETE') {
      print('Stream processing complete');
      print('Results: $results');
      mainPort.close();
    } else if (message is int) {
      results.add(message);
    }
  });
  
  // Send stream of data
  for (int i = 1; i <= 10; i++) {
    workerPort.send(i);
    await Future.delayed(Duration(milliseconds: 50));
  }
  
  workerPort.send('DONE');
  
  await Future.delayed(Duration(seconds: 2));
}
```

Streaming through isolates processes data incrementally, maintaining low  
memory footprint for large datasets. This pattern suits scenarios like log  
processing, data transformation pipelines, or real-time analytics.  

## Isolate with Timeout

Implementing timeouts for isolate operations prevents indefinite waiting and  
enables handling of stuck or slow workers.  

```dart
import 'dart:isolate';
import 'dart:async';

void slowWorker(SendPort sendPort) async {
  // Simulate slow work
  await Future.delayed(Duration(seconds: 5));
  sendPort.send('Work complete');
}

void main() async {
  print('Starting task with timeout...');
  
  try {
    // Using Isolate.run with timeout
    String result = await Isolate.run(
      () async {
        await Future.delayed(Duration(seconds: 3));
        return 'Task finished';
      },
    ).timeout(
      Duration(seconds: 2),
      onTimeout: () => throw TimeoutException('Task took too long'),
    );
    
    print('Result: $result');
  } catch (e) {
    print('Error: $e');
  }
  
  // Manual timeout with Isolate.spawn
  ReceivePort receivePort = ReceivePort();
  Isolate isolate = await Isolate.spawn(slowWorker, receivePort.sendPort);
  
  try {
    var result = await receivePort.first.timeout(
      Duration(seconds: 2),
      onTimeout: () {
        print('Timeout occurred, killing isolate');
        isolate.kill();
        receivePort.close();
        return 'TIMEOUT';
      },
    );
    
    print('Worker result: $result');
  } catch (e) {
    print('Worker error: $e');
  }
}
```

Timeouts prevent resource leaks from hung workers. The timeout method on  
Futures enables specifying maximum wait time, with optional callbacks for  
cleanup. Always kill timed-out isolates to free system resources.  

## Computing Platform Features

Accessing platform-specific information like processor count helps optimize  
isolate usage based on available hardware resources.  

```dart
import 'dart:isolate';
import 'dart:io';

void main() async {
  // Get platform information
  int processors = Platform.numberOfProcessors;
  print('Number of processors: $processors');
  print('Operating system: ${Platform.operatingSystem}');
  print('Operating system version: ${Platform.operatingSystemVersion}');
  
  // Determine optimal isolate count
  int optimalIsolates = processors - 1; // Leave one for main isolate
  if (optimalIsolates < 1) optimalIsolates = 1;
  
  print('Recommended worker isolates: $optimalIsolates');
  
  // Create optimal number of workers
  List<int> data = List.generate(1000, (i) => i + 1);
  int chunkSize = data.length ~/ optimalIsolates;
  
  List<Future<int>> tasks = [];
  
  for (int i = 0; i < optimalIsolates; i++) {
    int start = i * chunkSize;
    int end = (i == optimalIsolates - 1) ? data.length : start + chunkSize;
    List<int> chunk = data.sublist(start, end);
    
    tasks.add(Isolate.run(() {
      return chunk.fold(0, (sum, n) => sum + n);
    }));
  }
  
  List<int> results = await Future.wait(tasks);
  int total = results.fold(0, (a, b) => a + b);
  
  print('Processed with $optimalIsolates isolates: total = $total');
}
```

Platform.numberOfProcessors reveals available CPU cores, guiding decisions  
about isolate count. Creating more isolates than processors provides no  
benefit and may degrade performance due to context switching overhead.  

## Isolate Debugging Names

Assigning debug names to isolates aids in profiling, logging, and debugging  
complex applications with multiple concurrent workers.  

```dart
import 'dart:isolate';
import 'dart:developer';

void namedWorker(List<dynamic> args) {
  String name = args[0];
  SendPort sendPort = args[1];
  
  // Set debug name for this isolate
  Service.getIsolateID(Isolate.current).then((id) {
    print('Worker "$name" has isolate ID: $id');
  });
  
  // Perform work
  int result = 0;
  for (int i = 1; i <= 1000; i++) {
    result += i;
  }
  
  sendPort.send('$name completed: $result');
}

void main() async {
  print('Main isolate starting workers...');
  
  List<String> workerNames = ['Alpha', 'Beta', 'Gamma'];
  List<Future<String>> results = [];
  
  for (String name in workerNames) {
    ReceivePort receivePort = ReceivePort();
    
    await Isolate.spawn(
      namedWorker,
      [name, receivePort.sendPort],
      debugName: 'Worker-$name', // Debug name for profiling
    );
    
    results.add(receivePort.first.then((msg) {
      receivePort.close();
      return msg as String;
    }));
  }
  
  List<String> completions = await Future.wait(results);
  
  for (String completion in completions) {
    print(completion);
  }
}
```

Debug names appear in development tools and crash reports, making it easier  
to identify which isolate experienced issues. This is particularly valuable  
in production debugging of complex multi-isolate applications.  

## Transferable Types

Understanding which types can be transferred between isolates is crucial for  
designing efficient message-passing protocols.  

```dart
import 'dart:isolate';
import 'dart:typed_data';

void typeTestWorker(SendPort sendPort) {
  // Receive various types
  print('Worker ready to receive data');
}

void main() async {
  // Primitive types (copied)
  int number = await Isolate.run(() => 42);
  double decimal = await Isolate.run(() => 3.14);
  bool flag = await Isolate.run(() => true);
  String text = await Isolate.run(() => 'Hello there!');
  
  print('Primitives: $number, $decimal, $flag, $text');
  
  // Collections (deep copied)
  List<int> list = await Isolate.run(() => [1, 2, 3, 4, 5]);
  Map<String, int> map = await Isolate.run(() => {'a': 1, 'b': 2});
  Set<String> set = await Isolate.run(() => {'x', 'y', 'z'});
  
  print('Collections: $list, $map, $set');
  
  // Typed data (efficiently transferred)
  Uint8List bytes = await Isolate.run(() {
    return Uint8List.fromList([1, 2, 3, 4, 5]);
  });
  
  print('Bytes: $bytes');
  
  // Null and enums
  String? nullable = await Isolate.run(() => null);
  
  print('Nullable: $nullable');
  
  // Records (Dart 3+)
  (int, String) record = await Isolate.run(() => (100, 'test'));
  
  print('Record: $record');
}
```

Primitive types, collections, typed data arrays, and records can be  
transferred between isolates. Objects are deep-copied, which can be  
expensive for large structures. Functions and certain platform-specific  
objects cannot be transferred.  

## Isolate.run vs Isolate.spawn Comparison

Understanding when to use Isolate.run versus Isolate.spawn helps choose the  
right abstraction for different concurrency patterns.  

```dart
import 'dart:isolate';

// Worker for spawn approach
void spawnWorker(SendPort sendPort) {
  int result = List.generate(1000000, (i) => i).fold(0, (a, b) => a + b);
  sendPort.send(result);
}

void main() async {
  var stopwatch = Stopwatch();
  
  // Isolate.run - simple, automatic cleanup
  print('Testing Isolate.run...');
  stopwatch.start();
  
  int runResult = await Isolate.run(() {
    return List.generate(1000000, (i) => i).fold(0, (a, b) => a + b);
  });
  
  stopwatch.stop();
  print('Isolate.run result: $runResult');
  print('Time: ${stopwatch.elapsedMilliseconds}ms');
  
  // Isolate.spawn - manual control, reusable
  print('\nTesting Isolate.spawn...');
  stopwatch.reset();
  stopwatch.start();
  
  ReceivePort receivePort = ReceivePort();
  await Isolate.spawn(spawnWorker, receivePort.sendPort);
  int spawnResult = await receivePort.first as int;
  receivePort.close();
  
  stopwatch.stop();
  print('Isolate.spawn result: $spawnResult');
  print('Time: ${stopwatch.elapsedMilliseconds}ms');
  
  print('\nComparison:');
  print('- Isolate.run: Simple, automatic cleanup, one-shot tasks');
  print('- Isolate.spawn: Manual control, reusable, long-running workers');
}
```

Isolate.run simplifies one-shot parallel tasks with automatic lifecycle  
management. Isolate.spawn provides fine-grained control for long-running  
workers, bidirectional communication, and custom lifecycle management.  
Choose based on task duration and complexity requirements.  

## JSON Processing in Isolates

Processing large JSON datasets in isolates prevents UI blocking while  
parsing or serializing complex data structures.  

```dart
import 'dart:isolate';
import 'dart:convert';

void main() async {
  // Generate large JSON data
  Map<String, dynamic> largeData = {
    'users': List.generate(10000, (i) => {
      'id': i,
      'name': 'User $i',
      'email': 'user$i@example.com',
      'active': i % 2 == 0,
      'score': i * 10,
    }),
    'metadata': {
      'version': '1.0',
      'timestamp': DateTime.now().toIso8601String(),
    },
  };
  
  print('Processing ${largeData['users'].length} users...');
  
  var stopwatch = Stopwatch()..start();
  
  // Encode in isolate
  String jsonString = await Isolate.run(() {
    return jsonEncode(largeData);
  });
  
  stopwatch.stop();
  print('Encoded to JSON: ${jsonString.length} bytes');
  print('Encoding time: ${stopwatch.elapsedMilliseconds}ms');
  
  // Decode in isolate
  stopwatch.reset();
  stopwatch.start();
  
  Map<String, dynamic> decoded = await Isolate.run(() {
    return jsonDecode(jsonString) as Map<String, dynamic>;
  });
  
  stopwatch.stop();
  print('Decoded users: ${decoded['users'].length}');
  print('Decoding time: ${stopwatch.elapsedMilliseconds}ms');
  
  // Process data in isolate
  int activeCount = await Isolate.run(() {
    List users = decoded['users'];
    return users.where((u) => u['active'] == true).length;
  });
  
  print('Active users: $activeCount');
}
```

Large JSON operations can block the main thread, causing UI freezes in  
Flutter applications. Processing JSON in isolates maintains responsiveness  
while handling substantial data serialization and deserialization tasks.  


## Image Processing in Isolates

Image manipulation operations are CPU-intensive and should run in isolates  
to maintain application responsiveness during transformations.  

```dart
import 'dart:isolate';
import 'dart:typed_data';

class ImageData {
  final int width;
  final int height;
  final Uint8List pixels;
  
  ImageData(this.width, this.height, this.pixels);
}

Uint8List grayscaleFilter(Uint8List pixels) {
  // Convert RGBA to grayscale
  Uint8List result = Uint8List(pixels.length);
  
  for (int i = 0; i < pixels.length; i += 4) {
    int r = pixels[i];
    int g = pixels[i + 1];
    int b = pixels[i + 2];
    int a = pixels[i + 3];
    
    // Luminance formula
    int gray = (0.299 * r + 0.587 * g + 0.114 * b).round();
    
    result[i] = gray;
    result[i + 1] = gray;
    result[i + 2] = gray;
    result[i + 3] = a;
  }
  
  return result;
}

void main() async {
  // Simulate image data (100x100 RGBA)
  int width = 100;
  int height = 100;
  Uint8List pixels = Uint8List(width * height * 4);
  
  // Fill with sample data
  for (int i = 0; i < pixels.length; i += 4) {
    pixels[i] = (i ~/ 4) % 256;     // R
    pixels[i + 1] = ((i ~/ 4) * 2) % 256; // G
    pixels[i + 2] = ((i ~/ 4) * 3) % 256; // B
    pixels[i + 3] = 255;            // A
  }
  
  print('Processing ${width}x$height image...');
  
  var stopwatch = Stopwatch()..start();
  
  Uint8List processed = await Isolate.run(() {
    return grayscaleFilter(pixels);
  });
  
  stopwatch.stop();
  
  print('Image processed in ${stopwatch.elapsedMilliseconds}ms');
  print('Output size: ${processed.length} bytes');
}
```

Image processing operations like filtering, resizing, or format conversion  
benefit from isolate execution. The typed data arrays (Uint8List) transfer  
efficiently between isolates, making this pattern performant for graphics  
operations.  

## Cryptographic Operations in Isolates

Cryptographic operations are computationally expensive and should execute in  
isolates to prevent blocking during encryption, hashing, or key generation.  

```dart
import 'dart:isolate';
import 'dart:convert';
import 'dart:typed_data';

class CryptoTask {
  final String operation;
  final String data;
  
  CryptoTask(this.operation, this.data);
}

String simpleHash(String input) {
  // Simple hash simulation (NOT for production use)
  int hash = 0;
  for (int i = 0; i < input.length; i++) {
    hash = ((hash << 5) - hash) + input.codeUnitAt(i);
    hash = hash & hash; // Convert to 32-bit integer
  }
  return hash.toRadixString(16).padLeft(8, '0');
}

String caesarCipher(String text, int shift) {
  return text.split('').map((char) {
    int code = char.codeUnitAt(0);
    if (code >= 65 && code <= 90) {
      // Uppercase
      return String.fromCharCode(((code - 65 + shift) % 26) + 65);
    } else if (code >= 97 && code <= 122) {
      // Lowercase
      return String.fromCharCode(((code - 97 + shift) % 26) + 97);
    }
    return char;
  }).join();
}

void main() async {
  String sensitiveData = 'This is confidential information that needs processing';
  
  print('Original: $sensitiveData');
  
  // Hash in isolate
  String hashed = await Isolate.run(() {
    return simpleHash(sensitiveData);
  });
  
  print('Hash: $hashed');
  
  // Encrypt in isolate
  String encrypted = await Isolate.run(() {
    return caesarCipher(sensitiveData, 13);
  });
  
  print('Encrypted: $encrypted');
  
  // Decrypt in isolate
  String decrypted = await Isolate.run(() {
    return caesarCipher(encrypted, 13); // Caesar cipher is symmetric
  });
  
  print('Decrypted: $decrypted');
  
  // Batch processing
  List<String> dataList = List.generate(100, (i) => 'Data item $i');
  
  var stopwatch = Stopwatch()..start();
  
  List<String> hashes = await Isolate.run(() {
    return dataList.map((item) => simpleHash(item)).toList();
  });
  
  stopwatch.stop();
  
  print('\nProcessed ${hashes.length} hashes in ${stopwatch.elapsedMilliseconds}ms');
}
```

Cryptographic operations should always run in isolates to prevent UI  
freezing and ensure responsive applications. This is especially important  
for operations on large datasets or complex encryption algorithms.  

## Database Operations in Isolates

Heavy database queries and data processing should occur in isolates to keep  
the main thread responsive during data-intensive operations.  

```dart
import 'dart:isolate';

class DatabaseQuery {
  final String table;
  final Map<String, dynamic> filters;
  
  DatabaseQuery(this.table, this.filters);
}

class DatabaseRecord {
  final int id;
  final String name;
  final int value;
  
  DatabaseRecord(this.id, this.name, this.value);
  
  @override
  String toString() => 'Record($id, $name, $value)';
}

List<DatabaseRecord> simulateDatabase() {
  return List.generate(10000, (i) => DatabaseRecord(
    i,
    'Record $i',
    i * 100,
  ));
}

List<DatabaseRecord> queryRecords(DatabaseQuery query) {
  List<DatabaseRecord> allRecords = simulateDatabase();
  
  // Apply filters
  List<DatabaseRecord> filtered = allRecords.where((record) {
    if (query.filters.containsKey('minValue')) {
      if (record.value < query.filters['minValue']) return false;
    }
    if (query.filters.containsKey('maxValue')) {
      if (record.value > query.filters['maxValue']) return false;
    }
    return true;
  }).toList();
  
  return filtered;
}

void main() async {
  print('Executing database query in isolate...');
  
  var stopwatch = Stopwatch()..start();
  
  List<DatabaseRecord> results = await Isolate.run(() {
    DatabaseQuery query = DatabaseQuery('records', {
      'minValue': 50000,
      'maxValue': 100000,
    });
    
    return queryRecords(query);
  });
  
  stopwatch.stop();
  
  print('Query returned ${results.length} records');
  print('Time: ${stopwatch.elapsedMilliseconds}ms');
  print('First result: ${results.first}');
  print('Last result: ${results.last}');
  
  // Aggregation query
  Map<String, int> stats = await Isolate.run(() {
    List<DatabaseRecord> allRecords = simulateDatabase();
    
    int sum = 0;
    int max = 0;
    int min = 999999999;
    
    for (var record in allRecords) {
      sum += record.value;
      if (record.value > max) max = record.value;
      if (record.value < min) min = record.value;
    }
    
    return {
      'count': allRecords.length,
      'sum': sum,
      'average': sum ~/ allRecords.length,
      'max': max,
      'min': min,
    };
  });
  
  print('\nStatistics:');
  stats.forEach((key, value) {
    print('  $key: $value');
  });
}
```

Database operations involving large result sets or complex computations  
benefit from isolate execution. This prevents blocking the UI during data  
processing and maintains application responsiveness.  

## File Processing in Isolates

Reading and processing large files should occur in isolates to avoid  
blocking during I/O-intensive operations.  

```dart
import 'dart:isolate';
import 'dart:io';

class FileStats {
  final int lines;
  final int words;
  final int characters;
  final int bytes;
  
  FileStats(this.lines, this.words, this.characters, this.bytes);
  
  @override
  String toString() {
    return 'Lines: $lines, Words: $words, Chars: $characters, Bytes: $bytes';
  }
}

FileStats analyzeFile(String content) {
  int lines = '\n'.allMatches(content).length + 1;
  int words = content.split(RegExp(r'\s+')).where((w) => w.isNotEmpty).length;
  int characters = content.length;
  int bytes = content.codeUnits.length;
  
  return FileStats(lines, words, characters, bytes);
}

void main() async {
  // Create temporary file for testing
  Directory tempDir = Directory.systemTemp;
  File testFile = File('${tempDir.path}/test_isolate_file.txt');
  
  // Generate large file content
  StringBuffer content = StringBuffer();
  for (int i = 0; i < 10000; i++) {
    content.writeln('This is line $i with some sample text for testing.');
  }
  
  await testFile.writeAsString(content.toString());
  
  print('Processing file: ${testFile.path}');
  
  var stopwatch = Stopwatch()..start();
  
  // Read and analyze in isolate
  FileStats stats = await Isolate.run(() {
    String fileContent = File(testFile.path).readAsStringSync();
    return analyzeFile(fileContent);
  });
  
  stopwatch.stop();
  
  print('File analysis: $stats');
  print('Processing time: ${stopwatch.elapsedMilliseconds}ms');
  
  // Clean up
  await testFile.delete();
  
  // Process multiple files
  List<File> files = [];
  for (int i = 0; i < 5; i++) {
    File f = File('${tempDir.path}/batch_$i.txt');
    await f.writeAsString('Sample content for file $i\n' * 1000);
    files.add(f);
  }
  
  print('\nProcessing ${files.length} files in parallel...');
  
  stopwatch.reset();
  stopwatch.start();
  
  List<Future<FileStats>> tasks = files.map((file) {
    return Isolate.run(() {
      String content = file.readAsStringSync();
      return analyzeFile(content);
    });
  }).toList();
  
  List<FileStats> allStats = await Future.wait(tasks);
  
  stopwatch.stop();
  
  print('Processed ${allStats.length} files in ${stopwatch.elapsedMilliseconds}ms');
  
  // Clean up batch files
  for (var file in files) {
    await file.delete();
  }
}
```

File I/O operations can be slow, especially for large files. Processing in  
isolates keeps the application responsive. Multiple files can be processed  
in parallel across different isolates for improved throughput.  

## Network Request Processing in Isolates

Processing network response data in isolates prevents blocking while parsing  
or transforming large payloads.  

```dart
import 'dart:isolate';
import 'dart:convert';

class ApiResponse {
  final int statusCode;
  final Map<String, dynamic> data;
  
  ApiResponse(this.statusCode, this.data);
}

Map<String, dynamic> processResponse(String jsonData) {
  Map<String, dynamic> parsed = jsonDecode(jsonData);
  
  // Simulate complex processing
  if (parsed.containsKey('items')) {
    List items = parsed['items'];
    
    // Transform data
    List<Map<String, dynamic>> processed = items.map((item) {
      return {
        'id': item['id'],
        'name': item['name']?.toString().toUpperCase() ?? 'UNKNOWN',
        'processed': true,
        'timestamp': DateTime.now().toIso8601String(),
      };
    }).toList();
    
    return {
      'processed_items': processed,
      'count': processed.length,
      'success': true,
    };
  }
  
  return {'success': false, 'error': 'Invalid data structure'};
}

void main() async {
  // Simulate API response
  String mockResponse = jsonEncode({
    'items': List.generate(5000, (i) => {
      'id': i,
      'name': 'Item $i',
      'category': 'Category ${i % 10}',
      'price': i * 10.5,
    }),
  });
  
  print('Processing API response with ${mockResponse.length} bytes...');
  
  var stopwatch = Stopwatch()..start();
  
  Map<String, dynamic> result = await Isolate.run(() {
    return processResponse(mockResponse);
  });
  
  stopwatch.stop();
  
  print('Processing complete:');
  print('  Success: ${result['success']}');
  print('  Items processed: ${result['count']}');
  print('  Time: ${stopwatch.elapsedMilliseconds}ms');
  
  // Parallel processing of multiple responses
  List<String> responses = List.generate(3, (i) {
    return jsonEncode({
      'items': List.generate(1000, (j) => {
        'id': j,
        'name': 'Batch $i Item $j',
      }),
    });
  });
  
  print('\nProcessing ${responses.length} responses in parallel...');
  
  stopwatch.reset();
  stopwatch.start();
  
  List<Future<Map<String, dynamic>>> tasks = responses.map((response) {
    return Isolate.run(() => processResponse(response));
  }).toList();
  
  List<Map<String, dynamic>> results = await Future.wait(tasks);
  
  stopwatch.stop();
  
  int totalItems = results.fold(0, (sum, r) => sum + (r['count'] as int));
  print('Processed $totalItems total items in ${stopwatch.elapsedMilliseconds}ms');
}
```

Network response processing, especially for large JSON payloads, benefits  
from isolate execution. This prevents UI freezing while parsing and  
transforming data received from APIs.  

## Background Task Scheduler

Implementing a background task scheduler using isolates enables periodic or  
scheduled task execution without blocking the main application.  

```dart
import 'dart:isolate';
import 'dart:async';

class ScheduledTask {
  final String name;
  final Duration interval;
  final Function() work;
  
  ScheduledTask(this.name, this.interval, this.work);
}

void taskScheduler(SendPort mainSendPort) async {
  ReceivePort receivePort = ReceivePort();
  mainSendPort.send(receivePort.sendPort);
  
  Map<String, Timer> timers = {};
  
  await for (var message in receivePort) {
    if (message is List && message.length == 3) {
      String command = message[0];
      String taskName = message[1];
      
      if (command == 'schedule') {
        int intervalMs = message[2];
        
        // Cancel existing timer if any
        timers[taskName]?.cancel();
        
        // Create new periodic timer
        timers[taskName] = Timer.periodic(
          Duration(milliseconds: intervalMs),
          (timer) {
            mainSendPort.send('Task "$taskName" executed');
          },
        );
        
        mainSendPort.send('Task "$taskName" scheduled');
      } else if (command == 'cancel') {
        timers[taskName]?.cancel();
        timers.remove(taskName);
        mainSendPort.send('Task "$taskName" cancelled');
      } else if (command == 'shutdown') {
        timers.values.forEach((timer) => timer.cancel());
        receivePort.close();
        mainSendPort.send('Scheduler shutdown');
        break;
      }
    }
  }
}

void main() async {
  ReceivePort mainPort = ReceivePort();
  
  await Isolate.spawn(taskScheduler, mainPort.sendPort);
  SendPort schedulerPort = await mainPort.first as SendPort;
  
  // Schedule tasks
  schedulerPort.send(['schedule', 'task1', 500]);
  schedulerPort.send(['schedule', 'task2', 1000]);
  
  // Listen for task executions
  int executionCount = 0;
  
  mainPort.listen((message) {
    print(message);
    
    if (message.toString().contains('executed')) {
      executionCount++;
      
      // Stop after several executions
      if (executionCount >= 10) {
        schedulerPort.send(['shutdown', '', 0]);
      }
    }
  });
  
  await Future.delayed(Duration(seconds: 6));
  print('Main isolate exiting');
}
```

Background schedulers enable periodic task execution without blocking the  
main isolate. This pattern is useful for polling, periodic cleanup,  
monitoring, or any recurring background operation.  

## Memory-Intensive Computations

Memory-intensive operations benefit from isolate isolation, as each isolate  
has its own heap, preventing memory pressure on the main isolate.  

```dart
import 'dart:isolate';
import 'dart:typed_data';

class MatrixData {
  final int rows;
  final int cols;
  final Float64List data;
  
  MatrixData(this.rows, this.cols, this.data);
  
  double get(int row, int col) => data[row * cols + col];
  void set(int row, int col, double value) => data[row * cols + col] = value;
}

MatrixData multiplyMatrices(MatrixData a, MatrixData b) {
  if (a.cols != b.rows) {
    throw ArgumentError('Invalid matrix dimensions');
  }
  
  MatrixData result = MatrixData(
    a.rows,
    b.cols,
    Float64List(a.rows * b.cols),
  );
  
  for (int i = 0; i < a.rows; i++) {
    for (int j = 0; j < b.cols; j++) {
      double sum = 0;
      for (int k = 0; k < a.cols; k++) {
        sum += a.get(i, k) * b.get(k, j);
      }
      result.set(i, j, sum);
    }
  }
  
  return result;
}

void main() async {
  print('Creating large matrices...');
  
  // Create 100x100 matrices
  int size = 100;
  
  MatrixData matrixA = MatrixData(
    size,
    size,
    Float64List.fromList(
      List.generate(size * size, (i) => (i % 10).toDouble())
    ),
  );
  
  MatrixData matrixB = MatrixData(
    size,
    size,
    Float64List.fromList(
      List.generate(size * size, (i) => ((i % 10) + 1).toDouble())
    ),
  );
  
  print('Multiplying matrices in isolate...');
  
  var stopwatch = Stopwatch()..start();
  
  MatrixData result = await Isolate.run(() {
    return multiplyMatrices(matrixA, matrixB);
  });
  
  stopwatch.stop();
  
  print('Matrix multiplication complete');
  print('Result dimensions: ${result.rows}x${result.cols}');
  print('Sample values: ${result.get(0, 0)}, ${result.get(1, 1)}');
  print('Time: ${stopwatch.elapsedMilliseconds}ms');
  
  // Verify memory is released
  print('Isolate completed, memory released');
}
```

Memory-intensive computations like matrix operations, large array  
manipulations, or data structure transformations benefit from isolate  
isolation. When the isolate completes, its memory is released, preventing  
memory accumulation in the main isolate.  

## Isolate Communication Patterns

Different communication patterns suit different concurrency scenarios.  
Understanding these patterns helps design robust multi-isolate systems.  

```dart
import 'dart:isolate';
import 'dart:async';

// Request-Response Pattern
class Request {
  final int id;
  final String operation;
  final dynamic data;
  final SendPort replyPort;
  
  Request(this.id, this.operation, this.data, this.replyPort);
}

void requestResponseWorker(SendPort mainSendPort) async {
  ReceivePort receivePort = ReceivePort();
  mainSendPort.send(receivePort.sendPort);
  
  await for (var message in receivePort) {
    if (message is Request) {
      // Process request
      dynamic result;
      
      switch (message.operation) {
        case 'add':
          result = message.data[0] + message.data[1];
          break;
        case 'multiply':
          result = message.data[0] * message.data[1];
          break;
        default:
          result = 'Unknown operation';
      }
      
      // Send response
      message.replyPort.send({
        'id': message.id,
        'result': result,
      });
    } else if (message == 'exit') {
      receivePort.close();
      break;
    }
  }
}

// Fire-and-Forget Pattern
void fireAndForgetWorker(List<dynamic> args) {
  String task = args[0];
  int duration = args[1];
  
  // Simulate work
  int result = 0;
  for (int i = 0; i < duration; i++) {
    result += i;
  }
  
  print('Task "$task" completed: $result');
}

// Pub-Sub Pattern
void pubSubWorker(SendPort mainSendPort) async {
  ReceivePort receivePort = ReceivePort();
  mainSendPort.send(receivePort.sendPort);
  
  List<SendPort> subscribers = [];
  
  await for (var message in receivePort) {
    if (message is Map) {
      String action = message['action'];
      
      if (action == 'subscribe') {
        subscribers.add(message['port']);
        print('Subscriber added');
      } else if (action == 'publish') {
        for (var sub in subscribers) {
          sub.send(message['data']);
        }
      } else if (action == 'exit') {
        receivePort.close();
        break;
      }
    }
  }
}

void main() async {
  // Request-Response Pattern
  print('Request-Response Pattern:');
  ReceivePort mainPort = ReceivePort();
  await Isolate.spawn(requestResponseWorker, mainPort.sendPort);
  SendPort workerPort = await mainPort.first as SendPort;
  
  ReceivePort replyPort = ReceivePort();
  workerPort.send(Request(1, 'add', [10, 20], replyPort.sendPort));
  Map response = await replyPort.first as Map;
  print('Response: ${response['result']}');
  
  workerPort.send('exit');
  mainPort.close();
  replyPort.close();
  
  // Fire-and-Forget Pattern
  print('\nFire-and-Forget Pattern:');
  await Isolate.spawn(fireAndForgetWorker, ['background-task', 10000]);
  print('Task launched, continuing...');
  
  await Future.delayed(Duration(milliseconds: 100));
  
  // Pub-Sub Pattern
  print('\nPub-Sub Pattern:');
  ReceivePort pubSubMain = ReceivePort();
  await Isolate.spawn(pubSubWorker, pubSubMain.sendPort);
  SendPort pubSubPort = await pubSubMain.first as SendPort;
  
  // Create subscribers
  ReceivePort sub1 = ReceivePort();
  ReceivePort sub2 = ReceivePort();
  
  sub1.listen((data) => print('Subscriber 1 received: $data'));
  sub2.listen((data) => print('Subscriber 2 received: $data'));
  
  pubSubPort.send({'action': 'subscribe', 'port': sub1.sendPort});
  pubSubPort.send({'action': 'subscribe', 'port': sub2.sendPort});
  
  await Future.delayed(Duration(milliseconds: 100));
  
  // Publish messages
  pubSubPort.send({'action': 'publish', 'data': 'Hello there!'});
  
  await Future.delayed(Duration(milliseconds: 100));
  
  pubSubPort.send({'action': 'exit'});
  sub1.close();
  sub2.close();
  pubSubMain.close();
}
```

Different communication patterns serve different needs: request-response for  
interactive queries, fire-and-forget for independent tasks, and pub-sub for  
event broadcasting. Choose patterns based on coordination requirements.  


## Actor Model Implementation

The actor model provides a high-level abstraction for concurrent programming  
using isolated state and message passing between actors.  

```dart
import 'dart:isolate';
import 'dart:async';

class ActorMessage {
  final String type;
  final dynamic payload;
  final SendPort? replyTo;
  
  ActorMessage(this.type, this.payload, [this.replyTo]);
}

class Actor {
  late SendPort _sendPort;
  final ReceivePort _receivePort = ReceivePort();
  
  Future<void> spawn(Function(ActorMessage) messageHandler) async {
    await Isolate.spawn(_actorEntry, {
      'mainPort': _receivePort.sendPort,
      'handler': messageHandler,
    });
    
    _sendPort = await _receivePort.first as SendPort;
  }
  
  static void _actorEntry(Map<String, dynamic> args) async {
    SendPort mainPort = args['mainPort'];
    ReceivePort actorPort = ReceivePort();
    
    mainPort.send(actorPort.sendPort);
    
    // Note: Can't pass function directly, so simplified version
    await for (var message in actorPort) {
      if (message is ActorMessage) {
        if (message.type == 'stop') {
          actorPort.close();
          break;
        }
        
        // Process message (simplified)
        message.replyTo?.send('Processed: ${message.payload}');
      }
    }
  }
  
  void send(ActorMessage message) {
    _sendPort.send(message);
  }
  
  void stop() {
    _sendPort.send(ActorMessage('stop', null));
    _receivePort.close();
  }
}

void main() async {
  Actor actor = Actor();
  await actor.spawn((message) {
    print('Actor received: ${message.type} - ${message.payload}');
  });
  
  ReceivePort replyPort = ReceivePort();
  
  actor.send(ActorMessage('process', 'data1', replyPort.sendPort));
  actor.send(ActorMessage('process', 'data2', replyPort.sendPort));
  
  int replies = 0;
  replyPort.listen((reply) {
    print('Reply: $reply');
    replies++;
    
    if (replies >= 2) {
      actor.stop();
      replyPort.close();
    }
  });
  
  await Future.delayed(Duration(seconds: 1));
}
```

The actor model encapsulates state and behavior in actors that communicate  
exclusively through messages. This eliminates shared state and race  
conditions, making concurrent systems easier to reason about and maintain.  

## Pipeline Pattern for Data Processing

Pipelines chain multiple isolates to process data through sequential stages,  
enabling efficient stream processing.  

```dart
import 'dart:isolate';
import 'dart:async';

class PipelineStage {
  final String name;
  final Function(dynamic) transform;
  
  PipelineStage(this.name, this.transform);
}

void stageWorker(List<dynamic> args) async {
  SendPort mainSendPort = args[0];
  String stageName = args[1];
  ReceivePort stagePort = ReceivePort();
  
  mainSendPort.send(stagePort.sendPort);
  
  await for (var message in stagePort) {
    if (message == 'STOP') {
      stagePort.close();
      break;
    }
    
    // Apply transformation based on stage
    dynamic result;
    if (stageName == 'double') {
      result = message * 2;
    } else if (stageName == 'square') {
      result = message * message;
    } else if (stageName == 'add10') {
      result = message + 10;
    }
    
    mainSendPort.send(result);
  }
}

void main() async {
  print('Creating 3-stage pipeline: double -> square -> add10');
  
  // Create stages
  List<String> stageNames = ['double', 'square', 'add10'];
  List<SendPort> stagePorts = [];
  List<ReceivePort> outputPorts = [];
  
  for (String name in stageNames) {
    ReceivePort outputPort = ReceivePort();
    outputPorts.add(outputPort);
    
    await Isolate.spawn(stageWorker, [outputPort.sendPort, name]);
    SendPort stagePort = await outputPort.first as SendPort;
    stagePorts.add(stagePort);
  }
  
  // Process data through pipeline
  List<int> inputData = [1, 2, 3, 4, 5];
  List<int> results = [];
  
  for (int value in inputData) {
    int current = value;
    
    // Stage 1: double
    stagePorts[0].send(current);
    current = await outputPorts[0].first as int;
    
    // Stage 2: square
    stagePorts[1].send(current);
    current = await outputPorts[1].first as int;
    
    // Stage 3: add10
    stagePorts[2].send(current);
    current = await outputPorts[2].first as int;
    
    results.add(current);
    print('$value -> $current');
  }
  
  // Cleanup
  for (var port in stagePorts) {
    port.send('STOP');
  }
  
  for (var port in outputPorts) {
    port.close();
  }
  
  print('Pipeline results: $results');
}
```

Pipeline patterns decompose complex processing into stages, each running in  
its own isolate. This enables parallel execution where each stage processes  
different data items simultaneously, maximizing throughput.  

## Producer-Consumer Pattern

The producer-consumer pattern uses isolates to separate data generation from  
consumption, enabling efficient buffering and processing.  

```dart
import 'dart:isolate';
import 'dart:async';

void producer(SendPort consumerPort) async {
  print('Producer started');
  
  for (int i = 1; i <= 10; i++) {
    // Simulate data generation
    await Future.delayed(Duration(milliseconds: 100));
    
    consumerPort.send('Data-$i');
    print('Produced: Data-$i');
  }
  
  consumerPort.send('DONE');
  print('Producer finished');
}

void consumer(SendPort mainSendPort) async {
  ReceivePort receivePort = ReceivePort();
  mainSendPort.send(receivePort.sendPort);
  
  print('Consumer started');
  int count = 0;
  
  await for (var message in receivePort) {
    if (message == 'DONE') {
      mainSendPort.send('Consumer finished: $count items');
      receivePort.close();
      break;
    }
    
    // Simulate processing
    await Future.delayed(Duration(milliseconds: 150));
    print('Consumed: $message');
    count++;
  }
}

void main() async {
  ReceivePort mainPort = ReceivePort();
  
  // Start consumer
  await Isolate.spawn(consumer, mainPort.sendPort);
  SendPort consumerPort = await mainPort.first as SendPort;
  
  // Start producer
  await Isolate.spawn(producer, consumerPort);
  
  // Wait for completion
  await for (var message in mainPort) {
    print(message);
    mainPort.close();
    break;
  }
}
```

Producer-consumer patterns decouple data generation from processing,  
allowing each to operate at its own pace. Messages naturally buffer in the  
receive port, providing automatic backpressure management.  

## Work Stealing Pattern

Work stealing allows idle isolates to take tasks from busy isolates,  
improving load balance and CPU utilization.  

```dart
import 'dart:isolate';
import 'dart:async';
import 'dart:collection';

class Task {
  final int id;
  final int workload;
  
  Task(this.id, this.workload);
}

void worker(List<dynamic> args) async {
  int workerId = args[0];
  SendPort mainPort = args[1];
  ReceivePort workerPort = ReceivePort();
  
  mainPort.send(workerPort.sendPort);
  
  Queue<Task> localQueue = Queue<Task>();
  
  await for (var message in workerPort) {
    if (message is Task) {
      localQueue.add(message);
    } else if (message == 'PROCESS') {
      while (localQueue.isNotEmpty) {
        Task task = localQueue.removeFirst();
        
        // Simulate work
        await Future.delayed(Duration(milliseconds: task.workload));
        
        mainPort.send('Worker $workerId completed task ${task.id}');
      }
      
      mainPort.send('Worker $workerId idle');
    } else if (message == 'STOP') {
      workerPort.close();
      break;
    }
  }
}

void main() async {
  int numWorkers = 3;
  List<SendPort> workerPorts = [];
  ReceivePort mainPort = ReceivePort();
  
  // Create workers
  for (int i = 0; i < numWorkers; i++) {
    await Isolate.spawn(worker, [i, mainPort.sendPort]);
    SendPort workerPort = await mainPort.first as SendPort;
    workerPorts.add(workerPort);
  }
  
  // Distribute tasks (uneven distribution to demonstrate concept)
  List<Task> tasks = [
    Task(1, 100),
    Task(2, 200),
    Task(3, 50),
    Task(4, 300),
    Task(5, 100),
    Task(6, 150),
  ];
  
  // Give more work to first worker
  workerPorts[0].send(tasks[0]);
  workerPorts[0].send(tasks[1]);
  workerPorts[0].send(tasks[3]);
  
  workerPorts[1].send(tasks[2]);
  
  workerPorts[2].send(tasks[4]);
  workerPorts[2].send(tasks[5]);
  
  // Start processing
  for (var port in workerPorts) {
    port.send('PROCESS');
  }
  
  // Collect results
  int completedTasks = 0;
  
  mainPort.listen((message) {
    print(message);
    
    if (message.toString().contains('completed')) {
      completedTasks++;
      
      if (completedTasks == tasks.length) {
        for (var port in workerPorts) {
          port.send('STOP');
        }
        mainPort.close();
      }
    }
  });
  
  await Future.delayed(Duration(seconds: 3));
}
```

Work stealing improves load distribution when task execution times vary  
unpredictably. Idle workers can request tasks from busy workers, preventing  
some workers from being overwhelmed while others sit idle.  

## Hierarchical Isolate Structure

Creating hierarchical isolate structures enables building scalable systems  
with supervisor-worker relationships.  

```dart
import 'dart:isolate';
import 'dart:async';

void supervisor(SendPort mainSendPort) async {
  ReceivePort supervisorPort = ReceivePort();
  mainSendPort.send(supervisorPort.sendPort);
  
  List<SendPort> workers = [];
  int workerCount = 3;
  
  // Spawn worker isolates
  for (int i = 0; i < workerCount; i++) {
    ReceivePort workerMainPort = ReceivePort();
    
    await Isolate.spawn((SendPort port) async {
      ReceivePort workerPort = ReceivePort();
      port.send(workerPort.sendPort);
      
      await for (var message in workerPort) {
        if (message is int) {
          // Process work
          int result = message * message;
          port.send(result);
        } else if (message == 'STOP') {
          workerPort.close();
          break;
        }
      }
    }, workerMainPort.sendPort);
    
    SendPort workerPort = await workerMainPort.first as SendPort;
    workers.add(workerPort);
    
    // Listen to worker results
    workerMainPort.listen((result) {
      mainSendPort.send('Worker result: $result');
    });
  }
  
  mainSendPort.send('Supervisor ready with $workerCount workers');
  
  // Distribute work to workers
  await for (var message in supervisorPort) {
    if (message is List && message[0] == 'WORK') {
      List<int> tasks = message[1];
      
      for (int i = 0; i < tasks.length; i++) {
        int workerIndex = i % workers.length;
        workers[workerIndex].send(tasks[i]);
      }
    } else if (message == 'SHUTDOWN') {
      for (var worker in workers) {
        worker.send('STOP');
      }
      supervisorPort.close();
      mainSendPort.send('Supervisor shutdown');
      break;
    }
  }
}

void main() async {
  ReceivePort mainPort = ReceivePort();
  
  await Isolate.spawn(supervisor, mainPort.sendPort);
  SendPort supervisorPort = await mainPort.first as SendPort;
  
  mainPort.listen((message) {
    print(message);
  });
  
  await Future.delayed(Duration(milliseconds: 100));
  
  // Send work through supervisor
  supervisorPort.send(['WORK', [1, 2, 3, 4, 5, 6, 7, 8, 9]]);
  
  await Future.delayed(Duration(seconds: 1));
  
  // Shutdown
  supervisorPort.send('SHUTDOWN');
  
  await Future.delayed(Duration(milliseconds: 500));
  mainPort.close();
}
```

Hierarchical structures enable building fault-tolerant systems where  
supervisors monitor and manage worker isolates. This pattern is fundamental  
in frameworks like Erlang/OTP and can be adapted to Dart applications.  

## Isolate State Management

Managing state across multiple isolates requires careful design to maintain  
consistency while avoiding shared memory.  

```dart
import 'dart:isolate';
import 'dart:async';

class StateUpdate {
  final String key;
  final dynamic value;
  
  StateUpdate(this.key, this.value);
}

class StateQuery {
  final String key;
  final SendPort replyPort;
  
  StateQuery(this.key, this.replyPort);
}

void stateManager(SendPort mainSendPort) async {
  ReceivePort managerPort = ReceivePort();
  mainSendPort.send(managerPort.sendPort);
  
  Map<String, dynamic> state = {};
  
  await for (var message in managerPort) {
    if (message is StateUpdate) {
      state[message.key] = message.value;
      mainSendPort.send('Updated: ${message.key} = ${message.value}');
    } else if (message is StateQuery) {
      dynamic value = state[message.key];
      message.replyPort.send(value);
    } else if (message is Map && message['command'] == 'snapshot') {
      SendPort replyPort = message['replyPort'];
      replyPort.send(Map.from(state));
    } else if (message == 'STOP') {
      managerPort.close();
      break;
    }
  }
}

void main() async {
  ReceivePort mainPort = ReceivePort();
  
  await Isolate.spawn(stateManager, mainPort.sendPort);
  SendPort managerPort = await mainPort.first as SendPort;
  
  mainPort.listen((message) {
    print(message);
  });
  
  // Update state
  managerPort.send(StateUpdate('user', 'Alice'));
  managerPort.send(StateUpdate('score', 100));
  managerPort.send(StateUpdate('level', 5));
  
  await Future.delayed(Duration(milliseconds: 100));
  
  // Query state
  ReceivePort queryPort1 = ReceivePort();
  managerPort.send(StateQuery('user', queryPort1.sendPort));
  var user = await queryPort1.first;
  print('Queried user: $user');
  queryPort1.close();
  
  ReceivePort queryPort2 = ReceivePort();
  managerPort.send(StateQuery('score', queryPort2.sendPort));
  var score = await queryPort2.first;
  print('Queried score: $score');
  queryPort2.close();
  
  // Get full snapshot
  ReceivePort snapshotPort = ReceivePort();
  managerPort.send({'command': 'snapshot', 'replyPort': snapshotPort.sendPort});
  Map<String, dynamic> snapshot = await snapshotPort.first as Map<String, dynamic>;
  print('State snapshot: $snapshot');
  snapshotPort.close();
  
  // Cleanup
  managerPort.send('STOP');
  await Future.delayed(Duration(milliseconds: 100));
  mainPort.close();
}
```

Centralized state management in a dedicated isolate provides a single source  
of truth. Multiple worker isolates can query and update state through  
message passing, maintaining consistency without shared memory.  

## Isolate Fault Tolerance

Implementing fault tolerance mechanisms enables systems to recover from  
isolate failures and continue operation.  

```dart
import 'dart:isolate';
import 'dart:async';

class SupervisedWorker {
  final String name;
  Isolate? isolate;
  SendPort? sendPort;
  ReceivePort? receivePort;
  int restartCount = 0;
  
  SupervisedWorker(this.name);
  
  Future<void> start() async {
    receivePort = ReceivePort();
    ReceivePort errorPort = ReceivePort();
    ReceivePort exitPort = ReceivePort();
    
    errorPort.listen((error) {
      print('Worker $name error: $error');
      _handleFailure();
    });
    
    exitPort.listen((message) {
      print('Worker $name exited: $message');
    });
    
    isolate = await Isolate.spawn(
      _workerEntry,
      receivePort!.sendPort,
      onError: errorPort.sendPort,
      onExit: exitPort.sendPort,
      debugName: 'Worker-$name',
    );
    
    sendPort = await receivePort!.first as SendPort;
    print('Worker $name started');
  }
  
  static void _workerEntry(SendPort mainPort) async {
    ReceivePort workerPort = ReceivePort();
    mainPort.send(workerPort.sendPort);
    
    await for (var message in workerPort) {
      if (message == 'FAIL') {
        throw Exception('Simulated failure');
      } else if (message == 'STOP') {
        workerPort.close();
        break;
      } else {
        mainPort.send('Processed: $message');
      }
    }
  }
  
  void _handleFailure() {
    print('Handling failure for worker $name');
    
    if (restartCount < 3) {
      restartCount++;
      print('Restarting worker $name (attempt $restartCount)');
      
      receivePort?.close();
      isolate?.kill();
      
      Future.delayed(Duration(seconds: 1), () {
        start();
      });
    } else {
      print('Worker $name exceeded restart limit');
    }
  }
  
  void send(dynamic message) {
    sendPort?.send(message);
  }
  
  void stop() {
    sendPort?.send('STOP');
    receivePort?.close();
    isolate?.kill();
  }
}

void main() async {
  SupervisedWorker worker = SupervisedWorker('Resilient-1');
  await worker.start();
  
  worker.receivePort?.listen((message) {
    print('Received: $message');
  });
  
  // Send normal work
  worker.send('Task-1');
  await Future.delayed(Duration(milliseconds: 100));
  
  // Trigger failure
  print('\nTriggering failure...');
  worker.send('FAIL');
  
  // Wait for restart
  await Future.delayed(Duration(seconds: 2));
  
  // Send more work after restart
  worker.send('Task-2');
  
  await Future.delayed(Duration(seconds: 1));
  
  worker.stop();
}
```

Fault tolerance involves detecting failures and implementing recovery  
strategies like restart with exponential backoff. Supervised workers can  
automatically restart after failures, ensuring system resilience.  

## Isolate Performance Monitoring

Monitoring isolate performance helps identify bottlenecks and optimize  
resource utilization in concurrent systems.  

```dart
import 'dart:isolate';
import 'dart:async';

class PerformanceMetrics {
  final int tasksCompleted;
  final int tasksQueued;
  final Duration avgProcessingTime;
  final DateTime timestamp;
  
  PerformanceMetrics(
    this.tasksCompleted,
    this.tasksQueued,
    this.avgProcessingTime,
    this.timestamp,
  );
  
  @override
  String toString() {
    return 'Completed: $tasksCompleted, Queued: $tasksQueued, '
           'Avg time: ${avgProcessingTime.inMilliseconds}ms';
  }
}

void monitoredWorker(SendPort mainSendPort) async {
  ReceivePort workerPort = ReceivePort();
  mainSendPort.send(workerPort.sendPort);
  
  int tasksCompleted = 0;
  int tasksQueued = 0;
  List<int> processingTimes = [];
  
  Timer.periodic(Duration(seconds: 1), (timer) {
    Duration avgTime = processingTimes.isEmpty
        ? Duration.zero
        : Duration(
            milliseconds: processingTimes.reduce((a, b) => a + b) ~/ 
                          processingTimes.length
          );
    
    PerformanceMetrics metrics = PerformanceMetrics(
      tasksCompleted,
      tasksQueued,
      avgTime,
      DateTime.now(),
    );
    
    mainSendPort.send(metrics);
  });
  
  await for (var message in workerPort) {
    if (message == 'STOP') {
      workerPort.close();
      break;
    }
    
    tasksQueued++;
    
    Stopwatch sw = Stopwatch()..start();
    
    // Simulate work
    await Future.delayed(Duration(milliseconds: 50 + (message % 100)));
    
    sw.stop();
    processingTimes.add(sw.elapsedMilliseconds);
    
    if (processingTimes.length > 100) {
      processingTimes.removeAt(0);
    }
    
    tasksCompleted++;
    tasksQueued--;
    
    mainSendPort.send('Task $message completed');
  }
}

void main() async {
  ReceivePort mainPort = ReceivePort();
  
  await Isolate.spawn(monitoredWorker, mainPort.sendPort);
  SendPort workerPort = await mainPort.first as SendPort;
  
  print('Performance monitoring started\n');
  
  mainPort.listen((message) {
    if (message is PerformanceMetrics) {
      print('[${message.timestamp.toIso8601String()}] $message');
    }
  });
  
  // Send tasks
  for (int i = 0; i < 50; i++) {
    workerPort.send(i);
    await Future.delayed(Duration(milliseconds: 20));
  }
  
  await Future.delayed(Duration(seconds: 5));
  
  workerPort.send('STOP');
  await Future.delayed(Duration(milliseconds: 100));
  mainPort.close();
}
```

Performance monitoring tracks metrics like task completion rate, queue  
depth, and processing times. This data helps identify performance issues,  
optimize worker count, and ensure system responsiveness.  

## Resource Pooling with Isolates

Resource pools manage limited resources across isolates, preventing resource  
exhaustion and ensuring fair allocation.  

```dart
import 'dart:isolate';
import 'dart:async';
import 'dart:collection';

class Resource {
  final int id;
  bool inUse = false;
  
  Resource(this.id);
}

class ResourceRequest {
  final int requestId;
  final SendPort replyPort;
  
  ResourceRequest(this.requestId, this.replyPort);
}

void resourcePool(SendPort mainSendPort) async {
  ReceivePort poolPort = ReceivePort();
  mainSendPort.send(poolPort.sendPort);
  
  List<Resource> resources = List.generate(3, (i) => Resource(i));
  Queue<ResourceRequest> waitingRequests = Queue<ResourceRequest>();
  
  void allocateResource() {
    if (waitingRequests.isEmpty) return;
    
    for (var resource in resources) {
      if (!resource.inUse && waitingRequests.isNotEmpty) {
        resource.inUse = true;
        
        ResourceRequest request = waitingRequests.removeFirst();
        request.replyPort.send({
          'resourceId': resource.id,
          'requestId': request.requestId,
        });
        
        mainSendPort.send('Allocated resource ${resource.id} '
                         'to request ${request.requestId}');
      }
    }
  }
  
  await for (var message in poolPort) {
    if (message is ResourceRequest) {
      waitingRequests.add(message);
      allocateResource();
    } else if (message is Map && message['action'] == 'release') {
      int resourceId = message['resourceId'];
      
      Resource resource = resources.firstWhere((r) => r.id == resourceId);
      resource.inUse = false;
      
      mainSendPort.send('Released resource $resourceId');
      allocateResource();
    } else if (message == 'STOP') {
      poolPort.close();
      break;
    }
  }
}

void main() async {
  ReceivePort mainPort = ReceivePort();
  
  await Isolate.spawn(resourcePool, mainPort.sendPort);
  SendPort poolPort = await mainPort.first as SendPort;
  
  mainPort.listen((message) {
    print(message);
  });
  
  // Request more resources than available
  List<ReceivePort> replyPorts = [];
  
  for (int i = 0; i < 5; i++) {
    ReceivePort replyPort = ReceivePort();
    replyPorts.add(replyPort);
    
    poolPort.send(ResourceRequest(i, replyPort.sendPort));
    
    replyPort.listen((allocation) {
      print('Request $i received resource ${allocation['resourceId']}');
      
      // Release after use
      Future.delayed(Duration(seconds: 1), () {
        poolPort.send({
          'action': 'release',
          'resourceId': allocation['resourceId'],
        });
      });
    });
  }
  
  await Future.delayed(Duration(seconds: 4));
  
  poolPort.send('STOP');
  for (var port in replyPorts) {
    port.close();
  }
  mainPort.close();
}
```

Resource pools manage limited resources like database connections, file  
handles, or network sockets. The pool ensures fair allocation and prevents  
resource exhaustion when multiple isolates compete for shared resources.  

## Cascading Failures Prevention

Implementing circuit breakers and bulkheads prevents failures in one isolate  
from cascading to others in the system.  

```dart
import 'dart:isolate';
import 'dart:async';

enum CircuitState { closed, open, halfOpen }

class CircuitBreaker {
  CircuitState state = CircuitState.closed;
  int failureCount = 0;
  int successCount = 0;
  DateTime? lastFailureTime;
  final int failureThreshold = 3;
  final Duration timeout = Duration(seconds: 5);
  
  bool shouldAllow() {
    if (state == CircuitState.closed) {
      return true;
    } else if (state == CircuitState.open) {
      if (lastFailureTime != null &&
          DateTime.now().difference(lastFailureTime!) > timeout) {
        state = CircuitState.halfOpen;
        return true;
      }
      return false;
    } else { // halfOpen
      return true;
    }
  }
  
  void recordSuccess() {
    if (state == CircuitState.halfOpen) {
      successCount++;
      if (successCount >= 2) {
        state = CircuitState.closed;
        failureCount = 0;
        successCount = 0;
      }
    } else {
      failureCount = 0;
    }
  }
  
  void recordFailure() {
    lastFailureTime = DateTime.now();
    failureCount++;
    
    if (failureCount >= failureThreshold) {
      state = CircuitState.open;
    }
  }
}

void protectedWorker(SendPort mainSendPort) async {
  ReceivePort workerPort = ReceivePort();
  mainSendPort.send(workerPort.sendPort);
  
  CircuitBreaker breaker = CircuitBreaker();
  int requestCount = 0;
  
  await for (var message in workerPort) {
    if (message == 'STOP') {
      workerPort.close();
      break;
    }
    
    requestCount++;
    
    if (!breaker.shouldAllow()) {
      mainSendPort.send('Request $requestCount rejected (circuit open)');
      continue;
    }
    
    try {
      // Simulate unreliable operation
      if (message == 'FAIL') {
        throw Exception('Operation failed');
      }
      
      await Future.delayed(Duration(milliseconds: 50));
      breaker.recordSuccess();
      mainSendPort.send('Request $requestCount succeeded');
    } catch (e) {
      breaker.recordFailure();
      mainSendPort.send('Request $requestCount failed: ${breaker.state}');
    }
  }
}

void main() async {
  ReceivePort mainPort = ReceivePort();
  
  await Isolate.spawn(protectedWorker, mainPort.sendPort);
  SendPort workerPort = await mainPort.first as SendPort;
  
  mainPort.listen((message) {
    print(message);
  });
  
  // Send requests
  workerPort.send('OK');
  await Future.delayed(Duration(milliseconds: 100));
  
  // Trigger failures
  for (int i = 0; i < 5; i++) {
    workerPort.send('FAIL');
    await Future.delayed(Duration(milliseconds: 100));
  }
  
  // Try more requests (should be rejected)
  for (int i = 0; i < 3; i++) {
    workerPort.send('OK');
    await Future.delayed(Duration(milliseconds: 100));
  }
  
  // Wait for circuit to reset
  print('\nWaiting for circuit breaker timeout...');
  await Future.delayed(Duration(seconds: 6));
  
  // Should work again
  workerPort.send('OK');
  workerPort.send('OK');
  
  await Future.delayed(Duration(milliseconds: 200));
  
  workerPort.send('STOP');
  mainPort.close();
}
```

Circuit breakers prevent cascading failures by temporarily blocking requests  
to failing services. This gives failing components time to recover and  
prevents overload that could spread to other parts of the system.  


## Load Balancer Pattern

Load balancers distribute work across multiple worker isolates based on  
current load, optimizing resource utilization.  

```dart
import 'dart:isolate';
import 'dart:async';

class WorkerInfo {
  final int id;
  final SendPort sendPort;
  int activeTasks = 0;
  
  WorkerInfo(this.id, this.sendPort);
}

void loadBalancerWorker(int workerId, SendPort mainSendPort) async {
  ReceivePort workerPort = ReceivePort();
  mainSendPort.send(workerPort.sendPort);
  
  await for (var message in workerPort) {
    if (message is Map) {
      int taskId = message['taskId'];
      int workload = message['workload'];
      SendPort replyPort = message['replyPort'];
      
      // Simulate work
      await Future.delayed(Duration(milliseconds: workload));
      
      replyPort.send({
        'workerId': workerId,
        'taskId': taskId,
        'result': 'Completed by worker $workerId',
      });
    } else if (message == 'STOP') {
      workerPort.close();
      break;
    }
  }
}

void main() async {
  int numWorkers = 4;
  List<WorkerInfo> workers = [];
  ReceivePort mainPort = ReceivePort();
  
  // Create workers
  print('Creating $numWorkers workers...');
  for (int i = 0; i < numWorkers; i++) {
    ReceivePort workerMainPort = ReceivePort();
    
    await Isolate.spawn(
      (args) => loadBalancerWorker(args[0], args[1]),
      [i, workerMainPort.sendPort],
    );
    
    SendPort workerPort = await workerMainPort.first as SendPort;
    workers.add(WorkerInfo(i, workerPort));
    
    workerMainPort.close();
  }
  
  // Function to select least loaded worker
  WorkerInfo selectWorker() {
    workers.sort((a, b) => a.activeTasks.compareTo(b.activeTasks));
    return workers.first;
  }
  
  // Process results
  ReceivePort resultPort = ReceivePort();
  
  resultPort.listen((result) {
    int workerId = result['workerId'];
    int taskId = result['taskId'];
    
    // Update worker load
    WorkerInfo worker = workers.firstWhere((w) => w.id == workerId);
    worker.activeTasks--;
    
    print('Task $taskId completed by worker $workerId '
          '(load: ${worker.activeTasks})');
  });
  
  // Submit tasks
  print('\nSubmitting tasks...\n');
  for (int i = 0; i < 20; i++) {
    WorkerInfo selectedWorker = selectWorker();
    selectedWorker.activeTasks++;
    
    print('Assigning task $i to worker ${selectedWorker.id} '
          '(load: ${selectedWorker.activeTasks})');
    
    selectedWorker.sendPort.send({
      'taskId': i,
      'workload': 100 + (i % 5) * 50,
      'replyPort': resultPort.sendPort,
    });
    
    await Future.delayed(Duration(milliseconds: 50));
  }
  
  // Wait for completion
  await Future.delayed(Duration(seconds: 5));
  
  // Cleanup
  for (var worker in workers) {
    worker.sendPort.send('STOP');
  }
  
  resultPort.close();
  mainPort.close();
  
  print('\nFinal worker loads:');
  for (var worker in workers) {
    print('Worker ${worker.id}: ${worker.activeTasks} active tasks');
  }
}
```

Load balancers monitor worker utilization and route tasks to the least  
loaded workers. This ensures even distribution and prevents hot spots where  
some workers are overwhelmed while others are idle.  

## Rate Limiting with Isolates

Rate limiting controls the flow of messages to isolates, preventing  
overload and ensuring stable system performance.  

```dart
import 'dart:isolate';
import 'dart:async';

class RateLimiter {
  final int maxRequestsPerSecond;
  final List<DateTime> requestTimes = [];
  
  RateLimiter(this.maxRequestsPerSecond);
  
  bool allowRequest() {
    DateTime now = DateTime.now();
    DateTime cutoff = now.subtract(Duration(seconds: 1));
    
    // Remove old requests
    requestTimes.removeWhere((time) => time.isBefore(cutoff));
    
    if (requestTimes.length < maxRequestsPerSecond) {
      requestTimes.add(now);
      return true;
    }
    
    return false;
  }
}

void rateLimitedWorker(SendPort mainSendPort) async {
  ReceivePort workerPort = ReceivePort();
  mainSendPort.send(workerPort.sendPort);
  
  RateLimiter limiter = RateLimiter(5); // 5 requests per second
  int accepted = 0;
  int rejected = 0;
  
  await for (var message in workerPort) {
    if (message == 'STOP') {
      mainSendPort.send('Final stats - Accepted: $accepted, Rejected: $rejected');
      workerPort.close();
      break;
    }
    
    if (limiter.allowRequest()) {
      accepted++;
      mainSendPort.send('Request $message accepted');
    } else {
      rejected++;
      mainSendPort.send('Request $message rejected (rate limit exceeded)');
    }
  }
}

void main() async {
  ReceivePort mainPort = ReceivePort();
  
  await Isolate.spawn(rateLimitedWorker, mainPort.sendPort);
  SendPort workerPort = await mainPort.first as SendPort;
  
  mainPort.listen((message) {
    print(message);
  });
  
  // Send requests faster than rate limit
  print('Sending 20 requests rapidly...\n');
  for (int i = 0; i < 20; i++) {
    workerPort.send(i);
    await Future.delayed(Duration(milliseconds: 100));
  }
  
  await Future.delayed(Duration(milliseconds: 500));
  
  workerPort.send('STOP');
  await Future.delayed(Duration(milliseconds: 100));
  mainPort.close();
}
```

Rate limiting protects isolates from overload by restricting the number of  
requests processed per time window. This prevents resource exhaustion and  
maintains predictable performance under varying load conditions.  

## Deadlock Prevention

Preventing deadlocks in multi-isolate systems requires careful message  
ordering and timeout mechanisms.  

```dart
import 'dart:isolate';
import 'dart:async';

void resourceHolder(List<dynamic> args) async {
  String name = args[0];
  SendPort mainSendPort = args[1];
  ReceivePort receivePort = ReceivePort();
  
  mainSendPort.send(receivePort.sendPort);
  
  bool locked = false;
  
  await for (var message in receivePort) {
    if (message is Map) {
      String action = message['action'];
      SendPort replyPort = message['replyPort'];
      
      if (action == 'lock') {
        if (!locked) {
          locked = true;
          replyPort.send({'success': true, 'resource': name});
        } else {
          replyPort.send({'success': false, 'resource': name});
        }
      } else if (action == 'unlock') {
        locked = false;
        replyPort.send({'success': true, 'resource': name});
      }
    } else if (message == 'STOP') {
      receivePort.close();
      break;
    }
  }
}

void main() async {
  // Create two resources
  ReceivePort mainPort1 = ReceivePort();
  ReceivePort mainPort2 = ReceivePort();
  
  await Isolate.spawn(resourceHolder, ['ResourceA', mainPort1.sendPort]);
  await Isolate.spawn(resourceHolder, ['ResourceB', mainPort2.sendPort]);
  
  SendPort resourceA = await mainPort1.first as SendPort;
  SendPort resourceB = await mainPort2.first as SendPort;
  
  mainPort1.close();
  mainPort2.close();
  
  // Demonstrate deadlock prevention with timeouts
  print('Attempting to acquire resources with timeout...\n');
  
  Future<Map?> acquireResource(SendPort resource, Duration timeout) async {
    ReceivePort replyPort = ReceivePort();
    
    resource.send({
      'action': 'lock',
      'replyPort': replyPort.sendPort,
    });
    
    try {
      Map result = await replyPort.first.timeout(timeout) as Map;
      replyPort.close();
      return result;
    } catch (e) {
      print('Timeout acquiring resource');
      replyPort.close();
      return null;
    }
  }
  
  Future<void> releaseResource(SendPort resource) async {
    ReceivePort replyPort = ReceivePort();
    
    resource.send({
      'action': 'unlock',
      'replyPort': replyPort.sendPort,
    });
    
    await replyPort.first;
    replyPort.close();
  }
  
  // Acquire resources with timeout to prevent deadlock
  Map? lockA = await acquireResource(resourceA, Duration(seconds: 2));
  if (lockA?['success'] == true) {
    print('Acquired ${lockA?['resource']}');
    
    Map? lockB = await acquireResource(resourceB, Duration(seconds: 2));
    if (lockB?['success'] == true) {
      print('Acquired ${lockB?['resource']}');
      
      // Critical section
      print('Performing work with both resources...');
      await Future.delayed(Duration(milliseconds: 500));
      
      // Release in reverse order
      await releaseResource(resourceB);
      print('Released ${lockB?['resource']}');
    }
    
    await releaseResource(resourceA);
    print('Released ${lockA?['resource']}');
  }
  
  // Cleanup
  resourceA.send('STOP');
  resourceB.send('STOP');
  
  print('\nDeadlock prevention successful');
}
```

Deadlock prevention uses timeouts when acquiring resources and ensures  
consistent lock ordering. Timeout mechanisms prevent indefinite waiting,  
allowing recovery when resources are unavailable.  

## Event Sourcing with Isolates

Event sourcing maintains system state through an immutable log of events  
processed in isolates, enabling audit trails and time-travel debugging.  

```dart
import 'dart:isolate';
import 'dart:async';

class Event {
  final String type;
  final Map<String, dynamic> data;
  final DateTime timestamp;
  
  Event(this.type, this.data) : timestamp = DateTime.now();
  
  @override
  String toString() => '$type @ ${timestamp.toIso8601String()}: $data';
}

void eventStore(SendPort mainSendPort) async {
  ReceivePort storePort = ReceivePort();
  mainSendPort.send(storePort.sendPort);
  
  List<Event> events = [];
  Map<String, dynamic> currentState = {'balance': 0};
  
  await for (var message in storePort) {
    if (message is Event) {
      events.add(message);
      
      // Apply event to state
      switch (message.type) {
        case 'deposit':
          currentState['balance'] += message.data['amount'];
          break;
        case 'withdraw':
          currentState['balance'] -= message.data['amount'];
          break;
      }
      
      mainSendPort.send('Event applied: ${message.type}, '
                       'Balance: ${currentState['balance']}');
    } else if (message is Map && message['command'] == 'query') {
      SendPort replyPort = message['replyPort'];
      replyPort.send(Map.from(currentState));
    } else if (message is Map && message['command'] == 'history') {
      SendPort replyPort = message['replyPort'];
      replyPort.send(List<Event>.from(events));
    } else if (message == 'STOP') {
      storePort.close();
      break;
    }
  }
}

void main() async {
  ReceivePort mainPort = ReceivePort();
  
  await Isolate.spawn(eventStore, mainPort.sendPort);
  SendPort storePort = await mainPort.first as SendPort;
  
  mainPort.listen((message) {
    print(message);
  });
  
  // Generate events
  print('Generating events...\n');
  
  storePort.send(Event('deposit', {'amount': 100}));
  await Future.delayed(Duration(milliseconds: 100));
  
  storePort.send(Event('deposit', {'amount': 50}));
  await Future.delayed(Duration(milliseconds: 100));
  
  storePort.send(Event('withdraw', {'amount': 30}));
  await Future.delayed(Duration(milliseconds: 100));
  
  storePort.send(Event('withdraw', {'amount': 20}));
  await Future.delayed(Duration(milliseconds: 100));
  
  // Query current state
  print('\nQuerying state...');
  ReceivePort queryPort = ReceivePort();
  storePort.send({'command': 'query', 'replyPort': queryPort.sendPort});
  Map state = await queryPort.first as Map;
  print('Current state: $state');
  queryPort.close();
  
  // Get event history
  print('\nEvent history:');
  ReceivePort historyPort = ReceivePort();
  storePort.send({'command': 'history', 'replyPort': historyPort.sendPort});
  List<Event> history = await historyPort.first as List<Event>;
  for (var event in history) {
    print('  $event');
  }
  historyPort.close();
  
  storePort.send('STOP');
  await Future.delayed(Duration(milliseconds: 100));
  mainPort.close();
}
```

Event sourcing stores all state changes as events, enabling reconstruction  
of state at any point in time. Processing events in isolates ensures  
consistency and allows scaling event processing independently.  

## CQRS Pattern with Isolates

Command Query Responsibility Segregation separates read and write operations  
into different isolates for optimized performance and scalability.  

```dart
import 'dart:isolate';
import 'dart:async';

class Command {
  final String type;
  final Map<String, dynamic> data;
  
  Command(this.type, this.data);
}

class Query {
  final String type;
  final SendPort replyPort;
  
  Query(this.type, this.replyPort);
}

// Write side - handles commands
void commandHandler(SendPort mainSendPort) async {
  ReceivePort commandPort = ReceivePort();
  mainSendPort.send(commandPort.sendPort);
  
  Map<String, dynamic> writeModel = {'users': [], 'nextId': 1};
  
  await for (var message in commandPort) {
    if (message is Command) {
      switch (message.type) {
        case 'createUser':
          Map user = {
            'id': writeModel['nextId'],
            'name': message.data['name'],
            'email': message.data['email'],
          };
          writeModel['users'].add(user);
          writeModel['nextId']++;
          
          mainSendPort.send({
            'type': 'userCreated',
            'user': user,
          });
          break;
          
        case 'updateUser':
          List users = writeModel['users'];
          int index = users.indexWhere((u) => u['id'] == message.data['id']);
          if (index != -1) {
            users[index]['name'] = message.data['name'];
            mainSendPort.send({
              'type': 'userUpdated',
              'user': users[index],
            });
          }
          break;
      }
    } else if (message == 'STOP') {
      commandPort.close();
      break;
    }
  }
}

// Read side - handles queries
void queryHandler(SendPort mainSendPort) async {
  ReceivePort queryPort = ReceivePort();
  mainSendPort.send(queryPort.sendPort);
  
  Map<String, dynamic> readModel = {'users': [], 'userCount': 0};
  
  await for (var message in queryPort) {
    if (message is Query) {
      switch (message.type) {
        case 'allUsers':
          message.replyPort.send(readModel['users']);
          break;
          
        case 'userCount':
          message.replyPort.send(readModel['userCount']);
          break;
      }
    } else if (message is Map && message['type'] == 'userCreated') {
      readModel['users'].add(message['user']);
      readModel['userCount']++;
    } else if (message is Map && message['type'] == 'userUpdated') {
      List users = readModel['users'];
      int index = users.indexWhere((u) => u['id'] == message['user']['id']);
      if (index != -1) {
        users[index] = message['user'];
      }
    } else if (message == 'STOP') {
      queryPort.close();
      break;
    }
  }
}

void main() async {
  // Start command handler
  ReceivePort commandMainPort = ReceivePort();
  await Isolate.spawn(commandHandler, commandMainPort.sendPort);
  SendPort commandPort = await commandMainPort.first as SendPort;
  
  // Start query handler
  ReceivePort queryMainPort = ReceivePort();
  await Isolate.spawn(queryHandler, queryMainPort.sendPort);
  SendPort queryPort = await queryMainPort.first as SendPort;
  
  // Forward events from command handler to query handler
  commandMainPort.listen((event) {
    print('Event: ${event['type']}');
    queryPort.send(event);
  });
  
  print('CQRS system started\n');
  
  // Execute commands
  commandPort.send(Command('createUser', {
    'name': 'Alice',
    'email': 'alice@example.com',
  }));
  
  await Future.delayed(Duration(milliseconds: 100));
  
  commandPort.send(Command('createUser', {
    'name': 'Bob',
    'email': 'bob@example.com',
  }));
  
  await Future.delayed(Duration(milliseconds: 100));
  
  // Execute queries
  print('\nQuerying data...');
  
  ReceivePort countReply = ReceivePort();
  queryPort.send(Query('userCount', countReply.sendPort));
  int count = await countReply.first as int;
  print('User count: $count');
  countReply.close();
  
  ReceivePort usersReply = ReceivePort();
  queryPort.send(Query('allUsers', usersReply.sendPort));
  List users = await usersReply.first as List;
  print('Users: $users');
  usersReply.close();
  
  // Update and query again
  commandPort.send(Command('updateUser', {
    'id': 1,
    'name': 'Alice Updated',
  }));
  
  await Future.delayed(Duration(milliseconds: 100));
  
  ReceivePort usersReply2 = ReceivePort();
  queryPort.send(Query('allUsers', usersReply2.sendPort));
  List updatedUsers = await usersReply2.first as List;
  print('Updated users: $updatedUsers');
  usersReply2.close();
  
  // Cleanup
  commandPort.send('STOP');
  queryPort.send('STOP');
  commandMainPort.close();
  queryMainPort.close();
}
```

CQRS separates command processing from query handling, allowing independent  
optimization of each. Write operations use a normalized model while read  
operations use denormalized projections optimized for query performance.  

## Reactive Streams with Isolates

Implementing reactive streams with isolates enables backpressure-aware  
processing of continuous data flows.  

```dart
import 'dart:isolate';
import 'dart:async';

void streamProcessor(SendPort mainSendPort) async {
  ReceivePort processorPort = ReceivePort();
  mainSendPort.send(processorPort.sendPort);
  
  StreamController<int> controller = StreamController<int>();
  
  // Process stream with transformation
  controller.stream
      .map((value) => value * 2)
      .where((value) => value > 10)
      .listen((value) {
    mainSendPort.send('Processed: $value');
  });
  
  await for (var message in processorPort) {
    if (message == 'STOP') {
      await controller.close();
      processorPort.close();
      break;
    } else if (message is int) {
      controller.add(message);
    }
  }
}

void main() async {
  ReceivePort mainPort = ReceivePort();
  
  await Isolate.spawn(streamProcessor, mainPort.sendPort);
  SendPort processorPort = await mainPort.first as SendPort;
  
  mainPort.listen((message) {
    print(message);
  });
  
  print('Streaming data to processor...\n');
  
  // Send data stream
  for (int i = 1; i <= 20; i++) {
    processorPort.send(i);
    await Future.delayed(Duration(milliseconds: 50));
  }
  
  await Future.delayed(Duration(milliseconds: 200));
  
  processorPort.send('STOP');
  await Future.delayed(Duration(milliseconds: 100));
  mainPort.close();
}
```

Reactive streams with backpressure enable efficient processing of continuous  
data flows. Isolates process streams independently, preventing slow  
consumers from blocking producers and maintaining system responsiveness.  

## Distributed Tracing for Isolates

Implementing distributed tracing helps debug and monitor complex multi-  
isolate systems by tracking request flows across isolate boundaries.  

```dart
import 'dart:isolate';
import 'dart:async';

class Trace {
  final String traceId;
  final String spanId;
  final String operation;
  final DateTime timestamp;
  final String isolateName;
  
  Trace(this.traceId, this.spanId, this.operation, this.isolateName)
      : timestamp = DateTime.now();
  
  @override
  String toString() {
    return '[$isolateName] $traceId/$spanId - $operation @ '
           '${timestamp.toIso8601String()}';
  }
}

void tracedWorker(List<dynamic> args) async {
  String workerName = args[0];
  SendPort mainSendPort = args[1];
  ReceivePort workerPort = ReceivePort();
  
  mainSendPort.send(workerPort.sendPort);
  
  await for (var message in workerPort) {
    if (message is Map && message.containsKey('traceId')) {
      String traceId = message['traceId'];
      String spanId = '${workerName}-${DateTime.now().millisecondsSinceEpoch}';
      
      mainSendPort.send(Trace(traceId, spanId, 'received', workerName));
      
      // Simulate work
      await Future.delayed(Duration(milliseconds: 100));
      
      mainSendPort.send(Trace(traceId, spanId, 'processed', workerName));
      
      int result = message['value'] * 2;
      mainSendPort.send({
        'traceId': traceId,
        'result': result,
      });
    } else if (message == 'STOP') {
      workerPort.close();
      break;
    }
  }
}

void main() async {
  List<String> workerNames = ['Worker-A', 'Worker-B', 'Worker-C'];
  List<SendPort> workers = [];
  ReceivePort mainPort = ReceivePort();
  
  // Create traced workers
  for (String name in workerNames) {
    ReceivePort workerMainPort = ReceivePort();
    
    await Isolate.spawn(tracedWorker, [name, workerMainPort.sendPort]);
    SendPort workerPort = await workerMainPort.first as SendPort;
    workers.add(workerPort);
    
    workerMainPort.close();
  }
  
  List<Trace> traces = [];
  
  mainPort.listen((message) {
    if (message is Trace) {
      traces.add(message);
    } else if (message is Map && message.containsKey('result')) {
      print('Result for trace ${message['traceId']}: ${message['result']}');
    }
  });
  
  // Send traced requests
  print('Sending traced requests...\n');
  
  for (int i = 0; i < 3; i++) {
    String traceId = 'trace-${i + 1}';
    
    workers[i].send({
      'traceId': traceId,
      'value': (i + 1) * 10,
    });
  }
  
  await Future.delayed(Duration(seconds: 1));
  
  // Display traces
  print('\nDistributed trace log:');
  for (var trace in traces) {
    print(trace);
  }
  
  // Cleanup
  for (var worker in workers) {
    worker.send('STOP');
  }
  
  mainPort.close();
}
```

Distributed tracing tracks operations across isolate boundaries by  
propagating trace IDs. This enables understanding request flow, identifying  
bottlenecks, and debugging complex interactions in multi-isolate systems.  

## Graceful Shutdown Pattern

Implementing graceful shutdown ensures work completes and resources are  
properly released before isolates terminate.  

```dart
import 'dart:isolate';
import 'dart:async';

void gracefulWorker(SendPort mainSendPort) async {
  ReceivePort workerPort = ReceivePort();
  mainSendPort.send(workerPort.sendPort);
  
  bool shutdownRequested = false;
  List<Completer> activeWork = [];
  
  Future<void> doWork(int taskId) async {
    Completer completer = Completer();
    activeWork.add(completer);
    
    mainSendPort.send('Starting task $taskId');
    await Future.delayed(Duration(milliseconds: 500));
    mainSendPort.send('Completed task $taskId');
    
    activeWork.remove(completer);
    completer.complete();
  }
  
  await for (var message in workerPort) {
    if (message == 'SHUTDOWN') {
      shutdownRequested = true;
      mainSendPort.send('Shutdown requested, finishing active work...');
      
      // Wait for active work to complete
      if (activeWork.isNotEmpty) {
        await Future.wait(activeWork.map((c) => c.future));
      }
      
      mainSendPort.send('All work completed, shutting down');
      workerPort.close();
      break;
    } else if (message is int && !shutdownRequested) {
      doWork(message);
    } else if (shutdownRequested) {
      mainSendPort.send('Rejecting task $message (shutdown in progress)');
    }
  }
}

void main() async {
  ReceivePort mainPort = ReceivePort();
  
  await Isolate.spawn(gracefulWorker, mainPort.sendPort);
  SendPort workerPort = await mainPort.first as SendPort;
  
  mainPort.listen((message) {
    print(message);
  });
  
  print('Starting tasks...\n');
  
  // Send several tasks
  for (int i = 1; i <= 5; i++) {
    workerPort.send(i);
    await Future.delayed(Duration(milliseconds: 100));
  }
  
  // Request shutdown while work is in progress
  await Future.delayed(Duration(milliseconds: 300));
  print('\nRequesting graceful shutdown...\n');
  workerPort.send('SHUTDOWN');
  
  // Try sending more work after shutdown requested
  await Future.delayed(Duration(milliseconds: 100));
  workerPort.send(99);
  
  await Future.delayed(Duration(seconds: 3));
  mainPort.close();
}
```

Graceful shutdown allows in-flight work to complete before termination. The  
worker stops accepting new work, completes active tasks, releases resources,  
and then exits cleanly without data loss or corruption.  

## Best Practices Summary

Understanding best practices ensures efficient and maintainable isolate-based  
concurrent systems.  

```dart
import 'dart:isolate';
import 'dart:io';

void main() async {
  print('Dart Isolates Best Practices Summary\n');
  
  // 1. Use Isolate.run for simple tasks
  print('1. Use Isolate.run for simple, one-shot parallel tasks');
  int result1 = await Isolate.run(() => List.generate(1000, (i) => i).reduce((a, b) => a + b));
  print('   Result: $result1\n');
  
  // 2. Match isolate count to CPU cores
  print('2. Match isolate count to available CPU cores');
  int cores = Platform.numberOfProcessors;
  int optimalWorkers = cores > 1 ? cores - 1 : 1;
  print('   CPU cores: $cores, Recommended workers: $optimalWorkers\n');
  
  // 3. Transfer data efficiently
  print('3. Use efficient data types for message passing');
  print('   - Prefer primitive types and collections');
  print('   - Use TypedData for binary data');
  print('   - Avoid large object graphs\n');
  
  // 4. Handle errors properly
  print('4. Implement robust error handling');
  print('   - Use try-catch in worker isolates');
  print('   - Configure onError ports');
  print('   - Monitor isolate health\n');
  
  // 5. Manage lifecycle
  print('5. Properly manage isolate lifecycle');
  print('   - Close ports when done');
  print('   - Kill isolates that are no longer needed');
  print('   - Implement graceful shutdown\n');
  
  // 6. Avoid shared state
  print('6. Never share mutable state between isolates');
  print('   - Use message passing exclusively');
  print('   - Copy data, don\'t share references');
  print('   - Design for immutability\n');
  
  // 7. Monitor performance
  print('7. Monitor and profile isolate performance');
  print('   - Track task completion times');
  print('   - Monitor queue depths');
  print('   - Measure throughput and latency\n');
  
  // 8. Design communication patterns
  print('8. Choose appropriate communication patterns');
  print('   - Request-response for queries');
  print('   - Fire-and-forget for independent tasks');
  print('   - Pub-sub for event broadcasting');
  print('   - Streaming for continuous data\n');
  
  // 9. Implement backpressure
  print('9. Implement backpressure mechanisms');
  print('   - Pause/resume isolates when needed');
  print('   - Use rate limiting');
  print('   - Monitor queue sizes\n');
  
  // 10. Test thoroughly
  print('10. Test isolate interactions thoroughly');
  print('    - Unit test worker logic');
  print('    - Integration test message passing');
  print('    - Load test concurrent scenarios');
  print('    - Test error and timeout handling\n');
  
  print('Following these practices ensures robust, performant, and');
  print('maintainable concurrent Dart applications.');
}
```

Best practices cover choosing the right abstraction level, efficient data  
transfer, proper resource management, robust error handling, and appropriate  
communication patterns. Following these guidelines ensures scalable and  
maintainable isolate-based systems.  

## Isolate for ML Model Inference

Machine learning model inference is CPU-intensive and benefits significantly  
from isolate-based parallel execution.  

```dart
import 'dart:isolate';
import 'dart:typed_data';

class ModelInput {
  final Float32List features;
  final int inputSize;
  
  ModelInput(this.features, this.inputSize);
}

class ModelOutput {
  final Float32List predictions;
  final double confidence;
  
  ModelOutput(this.predictions, this.confidence);
}

ModelOutput runInference(ModelInput input) {
  // Simulate ML inference computation
  Float32List predictions = Float32List(10);
  
  for (int i = 0; i < predictions.length; i++) {
    double sum = 0.0;
    for (int j = 0; j < input.inputSize; j++) {
      sum += input.features[j] * (i + 1) * 0.01;
    }
    predictions[i] = sum;
  }
  
  double maxPrediction = predictions.reduce((a, b) => a > b ? a : b);
  double confidence = maxPrediction / predictions.reduce((a, b) => a + b);
  
  return ModelOutput(predictions, confidence);
}

void main() async {
  print('Running ML inference in isolates...\n');
  
  // Create batch of inputs
  List<ModelInput> inputs = List.generate(10, (i) {
    return ModelInput(
      Float32List.fromList(List.generate(100, (j) => (i + j) * 0.1)),
      100,
    );
  });
  
  var stopwatch = Stopwatch()..start();
  
  // Parallel inference
  List<Future<ModelOutput>> inferences = inputs.map((input) {
    return Isolate.run(() => runInference(input));
  }).toList();
  
  List<ModelOutput> results = await Future.wait(inferences);
  
  stopwatch.stop();
  
  print('Processed ${results.length} inferences in parallel');
  print('Time: ${stopwatch.elapsedMilliseconds}ms');
  print('Average confidence: ${results.map((r) => r.confidence).reduce((a, b) => a + b) / results.length}');
}
```

ML inference operations should always run in isolates to prevent UI  
blocking. Batching multiple inference requests across isolates maximizes  
throughput on multi-core systems.  

## Web Scraping with Isolates

Web scraping benefits from parallel execution when processing multiple pages  
or extracting data from HTML content.  

```dart
import 'dart:isolate';

class ScrapingTask {
  final String url;
  final String selector;
  
  ScrapingTask(this.url, this.selector);
}

class ScrapedData {
  final String url;
  final List<String> items;
  final int itemCount;
  
  ScrapedData(this.url, this.items) : itemCount = items.length;
}

ScrapedData scrapeContent(ScrapingTask task) {
  // Simulate HTML parsing and data extraction
  List<String> items = [];
  
  // Mock data extraction
  for (int i = 0; i < 20; i++) {
    items.add('Item $i from ${task.url}');
  }
  
  // Simulate processing time
  int sum = 0;
  for (int i = 0; i < 1000000; i++) {
    sum += i;
  }
  
  return ScrapedData(task.url, items);
}

void main() async {
  List<ScrapingTask> tasks = [
    ScrapingTask('https://example.com/page1', '.item'),
    ScrapingTask('https://example.com/page2', '.product'),
    ScrapingTask('https://example.com/page3', '.article'),
    ScrapingTask('https://example.com/page4', '.post'),
    ScrapingTask('https://example.com/page5', '.card'),
  ];
  
  print('Scraping ${tasks.length} pages in parallel...\n');
  
  var stopwatch = Stopwatch()..start();
  
  List<Future<ScrapedData>> futures = tasks.map((task) {
    return Isolate.run(() => scrapeContent(task));
  }).toList();
  
  List<ScrapedData> results = await Future.wait(futures);
  
  stopwatch.stop();
  
  int totalItems = results.fold(0, (sum, data) => sum + data.itemCount);
  
  print('Scraped $totalItems total items from ${results.length} pages');
  print('Time: ${stopwatch.elapsedMilliseconds}ms');
  
  for (var data in results) {
    print('${data.url}: ${data.itemCount} items');
  }
}
```

Web scraping with isolates enables parallel processing of multiple pages,  
significantly reducing total execution time. HTML parsing and data  
extraction are CPU-intensive and benefit from concurrent execution.  

## Video Processing with Isolates

Video processing operations like frame extraction, filtering, or encoding  
are computationally expensive and ideal for isolate-based parallelization.  

```dart
import 'dart:isolate';
import 'dart:typed_data';

class VideoFrame {
  final int frameNumber;
  final int width;
  final int height;
  final Uint8List pixels;
  
  VideoFrame(this.frameNumber, this.width, this.height, this.pixels);
}

VideoFrame processFrame(VideoFrame frame) {
  // Simulate frame processing (e.g., apply filter)
  Uint8List processedPixels = Uint8List(frame.pixels.length);
  
  for (int i = 0; i < frame.pixels.length; i += 4) {
    // Simple grayscale conversion
    int r = frame.pixels[i];
    int g = frame.pixels[i + 1];
    int b = frame.pixels[i + 2];
    int gray = ((r + g + b) ~/ 3);
    
    processedPixels[i] = gray;
    processedPixels[i + 1] = gray;
    processedPixels[i + 2] = gray;
    processedPixels[i + 3] = frame.pixels[i + 3];
  }
  
  return VideoFrame(frame.frameNumber, frame.width, frame.height, processedPixels);
}

void main() async {
  // Simulate video frames
  List<VideoFrame> frames = List.generate(60, (i) {
    int width = 640;
    int height = 480;
    Uint8List pixels = Uint8List(width * height * 4);
    
    // Fill with sample data
    for (int j = 0; j < pixels.length; j++) {
      pixels[j] = (i + j) % 256;
    }
    
    return VideoFrame(i, width, height, pixels);
  });
  
  print('Processing ${frames.length} video frames...\n');
  
  var stopwatch = Stopwatch()..start();
  
  // Process frames in parallel
  List<Future<VideoFrame>> futures = frames.map((frame) {
    return Isolate.run(() => processFrame(frame));
  }).toList();
  
  List<VideoFrame> processedFrames = await Future.wait(futures);
  
  stopwatch.stop();
  
  print('Processed ${processedFrames.length} frames');
  print('Time: ${stopwatch.elapsedMilliseconds}ms');
  print('FPS: ${(processedFrames.length / stopwatch.elapsedMilliseconds * 1000).toStringAsFixed(2)}');
}
```

Video processing benefits immensely from parallel execution across multiple  
isolates. Each frame can be processed independently, enabling real-time or  
near-real-time video filtering on multi-core systems.  

## Audio Processing Pipeline

Audio processing operations like filtering, mixing, or effects application  
can be distributed across isolates for efficient processing.  

```dart
import 'dart:isolate';
import 'dart:typed_data';
import 'dart:math' as math;

class AudioBuffer {
  final Float32List samples;
  final int sampleRate;
  final int channels;
  
  AudioBuffer(this.samples, this.sampleRate, this.channels);
}

AudioBuffer applyLowPassFilter(AudioBuffer buffer, double cutoffFreq) {
  Float32List filtered = Float32List(buffer.samples.length);
  double rc = 1.0 / (cutoffFreq * 2 * math.pi);
  double dt = 1.0 / buffer.sampleRate;
  double alpha = dt / (rc + dt);
  
  filtered[0] = buffer.samples[0];
  
  for (int i = 1; i < buffer.samples.length; i++) {
    filtered[i] = filtered[i - 1] + alpha * (buffer.samples[i] - filtered[i - 1]);
  }
  
  return AudioBuffer(filtered, buffer.sampleRate, buffer.channels);
}

void main() async {
  // Generate test audio (1 second at 44100 Hz)
  int sampleRate = 44100;
  int duration = 1;
  int numSamples = sampleRate * duration;
  
  Float32List samples = Float32List(numSamples);
  for (int i = 0; i < numSamples; i++) {
    samples[i] = math.sin(2 * math.pi * 440 * i / sampleRate);
  }
  
  AudioBuffer audioBuffer = AudioBuffer(samples, sampleRate, 1);
  
  print('Processing audio with low-pass filter...\n');
  
  var stopwatch = Stopwatch()..start();
  
  AudioBuffer filtered = await Isolate.run(() {
    return applyLowPassFilter(audioBuffer, 1000.0);
  });
  
  stopwatch.stop();
  
  print('Processed ${filtered.samples.length} samples');
  print('Sample rate: ${filtered.sampleRate} Hz');
  print('Time: ${stopwatch.elapsedMilliseconds}ms');
  print('Processing rate: ${(filtered.samples.length / stopwatch.elapsedMilliseconds).toStringAsFixed(0)} samples/ms');
}
```

Audio processing operations on large buffers benefit from isolate execution.  
Real-time audio applications can process different channels or effects in  
separate isolates to maintain low latency.  

## Batch Document Processing

Processing multiple documents or reports in parallel using isolates  
significantly reduces total processing time for batch operations.  

```dart
import 'dart:isolate';

class Document {
  final String id;
  final String content;
  final Map<String, dynamic> metadata;
  
  Document(this.id, this.content, this.metadata);
}

class ProcessedDocument {
  final String id;
  final int wordCount;
  final int characterCount;
  final List<String> keywords;
  final String summary;
  
  ProcessedDocument(
    this.id,
    this.wordCount,
    this.characterCount,
    this.keywords,
    this.summary,
  );
}

ProcessedDocument processDocument(Document doc) {
  int wordCount = doc.content.split(RegExp(r'\s+')).length;
  int characterCount = doc.content.length;
  
  // Extract keywords (simplified)
  List<String> words = doc.content.toLowerCase().split(RegExp(r'\W+'));
  Map<String, int> frequency = {};
  
  for (String word in words) {
    if (word.length > 4) {
      frequency[word] = (frequency[word] ?? 0) + 1;
    }
  }
  
  List<String> keywords = frequency.entries
      .where((e) => e.value > 1)
      .map((e) => e.key)
      .take(5)
      .toList();
  
  // Create summary (first sentence)
  String summary = doc.content.split('.').first;
  if (summary.length > 100) {
    summary = summary.substring(0, 100) + '...';
  }
  
  return ProcessedDocument(
    doc.id,
    wordCount,
    characterCount,
    keywords,
    summary,
  );
}

void main() async {
  // Create batch of documents
  List<Document> documents = List.generate(20, (i) {
    return Document(
      'doc-$i',
      'This is document $i. It contains important information about processing. '
      'The document processing system handles multiple formats. '
      'Processing documents efficiently requires parallel execution. ' * 10,
      {'author': 'System', 'version': '1.0'},
    );
  });
  
  print('Processing ${documents.length} documents in parallel...\n');
  
  var stopwatch = Stopwatch()..start();
  
  List<Future<ProcessedDocument>> futures = documents.map((doc) {
    return Isolate.run(() => processDocument(doc));
  }).toList();
  
  List<ProcessedDocument> results = await Future.wait(futures);
  
  stopwatch.stop();
  
  int totalWords = results.fold(0, (sum, doc) => sum + doc.wordCount);
  
  print('Processed ${results.length} documents');
  print('Total words: $totalWords');
  print('Time: ${stopwatch.elapsedMilliseconds}ms');
  print('Average: ${totalWords ~/ results.length} words per document');
  
  print('\nSample processed document:');
  var sample = results.first;
  print('ID: ${sample.id}');
  print('Words: ${sample.wordCount}');
  print('Keywords: ${sample.keywords}');
}
```

Batch document processing with isolates enables efficient handling of large  
document sets. Text analysis, keyword extraction, and summarization are  
CPU-intensive operations that parallelize well.  

## Compression and Decompression

Data compression and decompression are CPU-intensive operations that benefit  
significantly from parallel execution in isolates.  

```dart
import 'dart:isolate';
import 'dart:typed_data';

class CompressionResult {
  final Uint8List compressed;
  final int originalSize;
  final int compressedSize;
  final double ratio;
  
  CompressionResult(this.compressed, this.originalSize)
      : compressedSize = compressed.length,
        ratio = compressed.length / originalSize;
}

CompressionResult simpleCompress(Uint8List data) {
  // Simple run-length encoding simulation
  List<int> compressed = [];
  
  int i = 0;
  while (i < data.length) {
    int count = 1;
    int value = data[i];
    
    while (i + count < data.length && 
           data[i + count] == value && 
           count < 255) {
      count++;
    }
    
    compressed.add(count);
    compressed.add(value);
    i += count;
  }
  
  return CompressionResult(
    Uint8List.fromList(compressed),
    data.length,
  );
}

void main() async {
  // Generate test data
  List<Uint8List> datasets = List.generate(10, (i) {
    Uint8List data = Uint8List(10000);
    for (int j = 0; j < data.length; j++) {
      data[j] = (j ~/ 10) % 256; // Repeating pattern
    }
    return data;
  });
  
  print('Compressing ${datasets.length} datasets in parallel...\n');
  
  var stopwatch = Stopwatch()..start();
  
  List<Future<CompressionResult>> futures = datasets.map((data) {
    return Isolate.run(() => simpleCompress(data));
  }).toList();
  
  List<CompressionResult> results = await Future.wait(futures);
  
  stopwatch.stop();
  
  int totalOriginal = results.fold(0, (sum, r) => sum + r.originalSize);
  int totalCompressed = results.fold(0, (sum, r) => sum + r.compressedSize);
  
  print('Compressed ${results.length} datasets');
  print('Original size: $totalOriginal bytes');
  print('Compressed size: $totalCompressed bytes');
  print('Compression ratio: ${(totalCompressed / totalOriginal * 100).toStringAsFixed(2)}%');
  print('Time: ${stopwatch.elapsedMilliseconds}ms');
}
```

Compression and decompression operations are CPU-bound and parallelize  
effectively. Large files can be split into chunks and compressed in parallel  
across multiple isolates for improved throughput.  

## Genetic Algorithm with Isolates

Genetic algorithms benefit from parallel fitness evaluation across  
populations, making isolates ideal for evolutionary computation.  

```dart
import 'dart:isolate';
import 'dart:math' as math;

class Individual {
  final List<double> genes;
  double fitness;
  
  Individual(this.genes) : fitness = 0.0;
}

class Population {
  final List<Individual> individuals;
  
  Population(this.individuals);
}

double evaluateFitness(Individual individual) {
  // Fitness function: maximize sum while keeping values near 0.5
  double sum = 0.0;
  for (double gene in individual.genes) {
    sum += gene - (gene - 0.5).abs();
  }
  return sum;
}

Population evaluatePopulation(Population population) {
  for (var individual in population.individuals) {
    individual.fitness = evaluateFitness(individual);
  }
  return population;
}

void main() async {
  int populationSize = 1000;
  int geneCount = 50;
  math.Random random = math.Random();
  
  // Create initial population
  List<Individual> individuals = List.generate(populationSize, (_) {
    return Individual(
      List.generate(geneCount, (_) => random.nextDouble())
    );
  });
  
  print('Evaluating population of $populationSize individuals...\n');
  
  // Split population into chunks for parallel evaluation
  int numIsolates = 4;
  int chunkSize = populationSize ~/ numIsolates;
  
  var stopwatch = Stopwatch()..start();
  
  List<Future<Population>> futures = [];
  
  for (int i = 0; i < numIsolates; i++) {
    int start = i * chunkSize;
    int end = (i == numIsolates - 1) ? populationSize : start + chunkSize;
    
    List<Individual> chunk = individuals.sublist(start, end);
    
    futures.add(Isolate.run(() {
      return evaluatePopulation(Population(chunk));
    }));
  }
  
  List<Population> results = await Future.wait(futures);
  
  stopwatch.stop();
  
  // Collect evaluated individuals
  List<Individual> evaluated = [];
  for (var pop in results) {
    evaluated.addAll(pop.individuals);
  }
  
  // Find best individual
  evaluated.sort((a, b) => b.fitness.compareTo(a.fitness));
  Individual best = evaluated.first;
  
  print('Population evaluated');
  print('Time: ${stopwatch.elapsedMilliseconds}ms');
  print('Best fitness: ${best.fitness.toStringAsFixed(4)}');
  print('Average fitness: ${(evaluated.map((i) => i.fitness).reduce((a, b) => a + b) / evaluated.length).toStringAsFixed(4)}');
}
```

Genetic algorithms with parallel fitness evaluation across isolates  
significantly speed up evolutionary computation. Each isolate evaluates a  
portion of the population independently, enabling efficient exploration.  

## Monte Carlo Simulation

Monte Carlo simulations involve running many independent trials, making them  
naturally suited for parallel execution across isolates.  

```dart
import 'dart:isolate';
import 'dart:math' as math;

class SimulationResult {
  final int trials;
  final int successes;
  final double probability;
  
  SimulationResult(this.trials, this.successes)
      : probability = successes / trials;
}

SimulationResult runMonteCarloTrials(int numTrials) {
  math.Random random = math.Random();
  int successes = 0;
  
  for (int i = 0; i < numTrials; i++) {
    // Simulate: estimate pi by random points in unit square
    double x = random.nextDouble();
    double y = random.nextDouble();
    
    // Check if point is in quarter circle
    if (x * x + y * y <= 1.0) {
      successes++;
    }
  }
  
  return SimulationResult(numTrials, successes);
}

void main() async {
  int totalTrials = 10000000;
  int numIsolates = 4;
  int trialsPerIsolate = totalTrials ~/ numIsolates;
  
  print('Running Monte Carlo simulation to estimate π...');
  print('Total trials: $totalTrials across $numIsolates isolates\n');
  
  var stopwatch = Stopwatch()..start();
  
  List<Future<SimulationResult>> futures = List.generate(numIsolates, (_) {
    return Isolate.run(() => runMonteCarloTrials(trialsPerIsolate));
  });
  
  List<SimulationResult> results = await Future.wait(futures);
  
  stopwatch.stop();
  
  // Aggregate results
  int totalSuccesses = results.fold(0, (sum, r) => sum + r.successes);
  double estimatedPi = 4.0 * totalSuccesses / totalTrials;
  double error = (estimatedPi - math.pi).abs();
  
  print('Simulation complete');
  print('Time: ${stopwatch.elapsedMilliseconds}ms');
  print('Estimated π: ${estimatedPi.toStringAsFixed(6)}');
  print('Actual π: ${math.pi.toStringAsFixed(6)}');
  print('Error: ${error.toStringAsFixed(6)}');
  print('Accuracy: ${((1 - error / math.pi) * 100).toStringAsFixed(2)}%');
}
```

Monte Carlo simulations parallelize perfectly across isolates since each  
trial is independent. This enables running millions of simulations quickly  
for accurate statistical estimates.  

## Ray Tracing Renderer

Ray tracing is highly parallelizable, with each pixel's calculation  
independent of others, making it ideal for isolate-based rendering.  

```dart
import 'dart:isolate';
import 'dart:math' as math;
import 'dart:typed_data';

class Ray {
  final List<double> origin;
  final List<double> direction;
  
  Ray(this.origin, this.direction);
}

class Scene {
  final int width;
  final int height;
  
  Scene(this.width, this.height);
}

Uint8List renderTile(Map<String, dynamic> args) {
  int startY = args['startY'];
  int endY = args['endY'];
  int width = args['width'];
  int height = args['height'];
  
  int tileHeight = endY - startY;
  Uint8List pixels = Uint8List(width * tileHeight * 4);
  
  for (int y = startY; y < endY; y++) {
    for (int x = 0; x < width; x++) {
      // Simple ray tracing simulation
      double u = x / width;
      double v = (height - y) / height;
      
      // Compute color based on position
      int r = (u * 255).toInt();
      int g = (v * 255).toInt();
      int b = ((math.sin(u * 10) + math.cos(v * 10)) * 127 + 128).toInt();
      
      int pixelIndex = ((y - startY) * width + x) * 4;
      pixels[pixelIndex] = r;
      pixels[pixelIndex + 1] = g;
      pixels[pixelIndex + 2] = b;
      pixels[pixelIndex + 3] = 255;
    }
  }
  
  return pixels;
}

void main() async {
  int width = 800;
  int height = 600;
  int numTiles = 4;
  int tileHeight = height ~/ numTiles;
  
  print('Ray tracing scene: ${width}x$height');
  print('Using $numTiles tiles\n');
  
  var stopwatch = Stopwatch()..start();
  
  List<Future<Uint8List>> futures = [];
  
  for (int i = 0; i < numTiles; i++) {
    int startY = i * tileHeight;
    int endY = (i == numTiles - 1) ? height : startY + tileHeight;
    
    futures.add(Isolate.run(() => renderTile({
      'startY': startY,
      'endY': endY,
      'width': width,
      'height': height,
    })));
  }
  
  List<Uint8List> tiles = await Future.wait(futures);
  
  stopwatch.stop();
  
  // Combine tiles
  int totalPixels = width * height;
  print('Rendered $totalPixels pixels');
  print('Time: ${stopwatch.elapsedMilliseconds}ms');
  print('Pixels/second: ${(totalPixels / stopwatch.elapsedMilliseconds * 1000).toStringAsFixed(0)}');
}
```

Ray tracing is embarrassingly parallel - each pixel can be computed  
independently. Dividing the image into tiles and rendering each in a  
separate isolate maximizes CPU utilization for fast rendering.  

## Blockchain Mining Simulation

Cryptocurrency mining involves repeated hashing, which parallelizes  
perfectly across isolates for maximum throughput.  

```dart
import 'dart:isolate';
import 'dart:convert';
import 'dart:typed_data';

class Block {
  final int index;
  final String previousHash;
  final String data;
  int nonce;
  
  Block(this.index, this.previousHash, this.data) : nonce = 0;
  
  String calculateHash() {
    String input = '$index$previousHash$data$nonce';
    List<int> bytes = utf8.encode(input);
    int hash = 0;
    for (int byte in bytes) {
      hash = ((hash << 5) - hash + byte) & 0xFFFFFFFF;
    }
    return hash.toRadixString(16).padLeft(8, '0');
  }
}

Map<String, dynamic> mineBlock(Map<String, dynamic> args) {
  Block block = Block(
    args['index'],
    args['previousHash'],
    args['data'],
  );
  
  int startNonce = args['startNonce'];
  int range = args['range'];
  int difficulty = args['difficulty'];
  String target = '0' * difficulty;
  
  block.nonce = startNonce;
  
  for (int i = 0; i < range; i++) {
    String hash = block.calculateHash();
    
    if (hash.startsWith(target)) {
      return {
        'found': true,
        'nonce': block.nonce,
        'hash': hash,
      };
    }
    
    block.nonce++;
  }
  
  return {'found': false};
}

void main() async {
  Block block = Block(1, '0000000000000000', 'Transaction data');
  int difficulty = 5; // Number of leading zeros
  int numMiners = 4;
  int rangePerMiner = 1000000;
  
  print('Mining block with difficulty $difficulty');
  print('Using $numMiners parallel miners\n');
  
  var stopwatch = Stopwatch()..start();
  
  List<Future<Map<String, dynamic>>> futures = [];
  
  for (int i = 0; i < numMiners; i++) {
    futures.add(Isolate.run(() => mineBlock({
      'index': block.index,
      'previousHash': block.previousHash,
      'data': block.data,
      'startNonce': i * rangePerMiner,
      'range': rangePerMiner,
      'difficulty': difficulty,
    })));
  }
  
  Map<String, dynamic>? solution;
  
  for (var future in futures) {
    var result = await future;
    if (result['found'] == true) {
      solution = result;
      break;
    }
  }
  
  stopwatch.stop();
  
  if (solution != null) {
    print('Block mined successfully!');
    print('Nonce: ${solution['nonce']}');
    print('Hash: ${solution['hash']}');
    print('Time: ${stopwatch.elapsedMilliseconds}ms');
  } else {
    print('Block not mined in search space');
  }
}
```

Blockchain mining is perfectly suited for parallel execution. Multiple  
isolates can search different nonce ranges simultaneously, dramatically  
increasing the hash rate and reducing time to find valid blocks.  

## Scientific Computing Pipeline

Complex scientific computations often involve multiple stages that can be  
parallelized using isolates for maximum efficiency.  

```dart
import 'dart:isolate';
import 'dart:math' as math;

class DataPoint {
  final double x;
  final double y;
  final double z;
  
  DataPoint(this.x, this.y, this.z);
}

class Analysis {
  final double mean;
  final double stdDev;
  final double min;
  final double max;
  
  Analysis(this.mean, this.stdDev, this.min, this.max);
}

Analysis analyzeData(List<DataPoint> points) {
  double sum = 0.0;
  double min = double.infinity;
  double max = double.negativeInfinity;
  
  for (var point in points) {
    double magnitude = math.sqrt(point.x * point.x + 
                                 point.y * point.y + 
                                 point.z * point.z);
    sum += magnitude;
    if (magnitude < min) min = magnitude;
    if (magnitude > max) max = magnitude;
  }
  
  double mean = sum / points.length;
  
  double variance = 0.0;
  for (var point in points) {
    double magnitude = math.sqrt(point.x * point.x + 
                                 point.y * point.y + 
                                 point.z * point.z);
    variance += (magnitude - mean) * (magnitude - mean);
  }
  
  double stdDev = math.sqrt(variance / points.length);
  
  return Analysis(mean, stdDev, min, max);
}

void main() async {
  // Generate scientific data
  math.Random random = math.Random();
  List<DataPoint> dataset = List.generate(100000, (_) {
    return DataPoint(
      random.nextDouble() * 100,
      random.nextDouble() * 100,
      random.nextDouble() * 100,
    );
  });
  
  print('Analyzing ${dataset.length} data points...\n');
  
  // Split dataset for parallel analysis
  int numPartitions = 4;
  int partitionSize = dataset.length ~/ numPartitions;
  
  var stopwatch = Stopwatch()..start();
  
  List<Future<Analysis>> futures = [];
  
  for (int i = 0; i < numPartitions; i++) {
    int start = i * partitionSize;
    int end = (i == numPartitions - 1) ? dataset.length : start + partitionSize;
    
    List<DataPoint> partition = dataset.sublist(start, end);
    
    futures.add(Isolate.run(() => analyzeData(partition)));
  }
  
  List<Analysis> results = await Future.wait(futures);
  
  stopwatch.stop();
  
  // Combine results
  double overallMean = results.map((a) => a.mean).reduce((a, b) => a + b) / results.length;
  double overallMin = results.map((a) => a.min).reduce((a, b) => a < b ? a : b);
  double overallMax = results.map((a) => a.max).reduce((a, b) => a > b ? a : b);
  
  print('Analysis complete');
  print('Time: ${stopwatch.elapsedMilliseconds}ms');
  print('Mean magnitude: ${overallMean.toStringAsFixed(2)}');
  print('Min magnitude: ${overallMin.toStringAsFixed(2)}');
  print('Max magnitude: ${overallMax.toStringAsFixed(2)}');
}
```

Scientific computing pipelines benefit from isolate-based parallelization  
for data analysis, simulations, and numerical computations. Large datasets  
can be partitioned and processed independently for improved performance.  

## Real-Time Data Aggregation

Real-time data aggregation from multiple sources can be efficiently handled  
using isolates for concurrent processing.  

```dart
import 'dart:isolate';
import 'dart:async';

class DataSource {
  final String name;
  final int interval;
  
  DataSource(this.name, this.interval);
}

void dataCollector(List<dynamic> args) async {
  String sourceName = args[0];
  int interval = args[1];
  SendPort sendPort = args[2];
  
  for (int i = 0; i < 10; i++) {
    await Future.delayed(Duration(milliseconds: interval));
    
    // Simulate data collection
    Map<String, dynamic> data = {
      'source': sourceName,
      'timestamp': DateTime.now().toIso8601String(),
      'value': (i + 1) * 10,
      'sequence': i,
    };
    
    sendPort.send(data);
  }
  
  sendPort.send({'source': sourceName, 'complete': true});
}

void main() async {
  List<DataSource> sources = [
    DataSource('Sensor-A', 100),
    DataSource('Sensor-B', 150),
    DataSource('Sensor-C', 80),
    DataSource('Sensor-D', 200),
  ];
  
  ReceivePort aggregatorPort = ReceivePort();
  Map<String, List<Map<String, dynamic>>> collectedData = {};
  int completedSources = 0;
  
  print('Starting real-time data aggregation from ${sources.length} sources\n');
  
  aggregatorPort.listen((data) {
    if (data is Map && data.containsKey('complete')) {
      completedSources++;
      print('${data['source']} completed');
      
      if (completedSources == sources.length) {
        aggregatorPort.close();
        
        print('\nAggregation complete');
        int totalPoints = collectedData.values.fold(
          0, 
          (sum, list) => sum + list.length
        );
        print('Total data points: $totalPoints');
        
        collectedData.forEach((source, points) {
          print('$source: ${points.length} points');
        });
      }
    } else if (data is Map) {
      String source = data['source'];
      collectedData.putIfAbsent(source, () => []);
      collectedData[source]!.add(data);
      print('Received from ${data['source']}: ${data['value']}');
    }
  });
  
  // Spawn collectors for each source
  for (var source in sources) {
    await Isolate.spawn(
      dataCollector,
      [source.name, source.interval, aggregatorPort.sendPort],
    );
  }
  
  await Future.delayed(Duration(seconds: 3));
}
```

Real-time data aggregation with isolates enables concurrent collection from  
multiple sources. Each source operates independently, with data aggregated  
centrally for analysis and reporting.  
