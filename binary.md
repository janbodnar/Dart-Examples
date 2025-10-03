# Binary Data Manipulation in Dart

Binary data manipulation is a fundamental aspect of computer programming that  
deals with data at the byte level, allowing developers to work directly with  
the raw binary representation of information. In Dart, binary data manipulation  
is essential for many real-world applications including network protocols,  
file formats, cryptography, data compression, and system-level programming.  

Unlike high-level string or JSON processing, binary data manipulation requires  
understanding how data is represented in memory and on disk. This involves  
working with bytes, bit patterns, endianness, and various encoding schemes.  
Dart provides excellent support for binary operations through the `dart:io`  
library for file operations and `dart:typed_data` for efficient byte arrays.  

The ability to manipulate binary data efficiently is crucial for performance-  
critical applications, especially those dealing with large datasets, real-time  
processing, or resource-constrained environments. Dart's strong typing and  
efficient memory management make it ideal for binary manipulation while  
maintaining safety and performance.  

Common use cases for binary data manipulation in Dart include implementing  
network protocols like TCP/IP, HTTP/2, or custom binary protocols, parsing  
and generating file formats such as images, documents, or databases, performing  
cryptographic operations, data compression and decompression, embedded systems  
programming, and interfacing with hardware devices or native platforms.  

Dart's approach to binary data centers around the `Uint8List` type from  
`dart:typed_data`, which represents an efficient, fixed-length list of 8-bit  
unsigned integers. This type serves as the foundation for most binary operations,  
providing a flexible and efficient way to work with raw data. The language also  
offers powerful libraries like `dart:io`, `dart:convert`, and `dart:typed_data`  
that provide high-level abstractions for common binary manipulation tasks.  

Understanding binary data manipulation in Dart opens doors to implementing  
efficient data structures, optimizing memory usage, creating custom serialization  
formats, and building high-performance applications for both server-side and  
client-side platforms including Flutter mobile applications.  

This comprehensive guide contains detailed Dart code examples demonstrating  
working with binary data. Each example showcases practical use cases, best  
practices, and educational concepts for binary data manipulation in Dart.  

## Table of Contents

1. [Basic Byte Operations and Uint8List](#basic-byte-operations-and-uint8list)
2. [Reading and Writing Binary Files](#reading-and-writing-binary-files)
3. [Bitwise Operations and Bit Manipulation](#bitwise-operations-and-bit-manipulation)
4. [Encoding and Decoding](#encoding-and-decoding)
5. [Buffer Operations and Management](#buffer-operations-and-management)
6. [Endianness Handling](#endianness-handling)
7. [Advanced Binary Manipulation](#advanced-binary-manipulation)
8. [Real-world Binary Data Scenarios](#real-world-binary-data-scenarios)

---

## Basic Byte Operations and Uint8List

### Creating and Working with Uint8List

Uint8List is the foundation of binary data manipulation in Dart, providing  
an efficient way to work with sequences of bytes.  

```dart
import 'dart:typed_data';

void main() {
  // Creating Uint8List in different ways
  
  // Method 1: From string using codeUnits
  var data1 = Uint8List.fromList('Hello there!'.codeUnits);
  print('Method 1: $data1');
  print('As string: ${String.fromCharCodes(data1)}');
  
  // Method 2: Using constructor with specified length
  var data2 = Uint8List(5);
  data2.setAll(0, 'Dart!'.codeUnits);
  print('Method 2: $data2');
  
  // Method 3: Creating from hex values
  var data3 = Uint8List.fromList([0x48, 0x65, 0x6C, 0x6C, 0x6F]); // "Hello"
  print('Method 3: $data3 -> ${String.fromCharCodes(data3)}');
  
  // Method 4: Using builder pattern with List
  var builder = <int>[];
  builder.add(0x44); // 'D'
  builder.add(0x61); // 'a'
  builder.add(0x72); // 'r'
  builder.add(0x74); // 't'
  var data4 = Uint8List.fromList(builder);
  print('Method 4: ${String.fromCharCodes(data4)}');
}
```

This example demonstrates four fundamental approaches to creating Uint8List  
objects in Dart. The first method converts a string to bytes using codeUnits,  
which is the most common approach when working with text data. The second  
method uses the Uint8List constructor to pre-allocate a list with specific  
length, which is efficient for performance-critical code.  

The third method creates a Uint8List using hexadecimal literals, which is  
useful when working with binary protocols or when you need to specify exact  
byte values. The fourth method shows incremental construction using a regular  
List builder, demonstrating how to build byte arrays dynamically before  
converting them to the more efficient Uint8List type.  

### Uint8List Manipulation and Copying

Understanding how Uint8List behaves during copying and modification is  
crucial for avoiding data corruption and memory issues.  

```dart
import 'dart:typed_data';

void main() {
  var original = Uint8List.fromList('Original Data'.codeUnits);
  print('Original: ${String.fromCharCodes(original)}');
  
  // Shallow copy vs deep copy
  var shallow = original; // shares underlying data
  var deep = Uint8List.fromList(original); // creates new copy
  
  // Modify original
  original[0] = 'M'.codeUnitAt(0);
  
  print('After modifying original[0]:');
  print('Original: ${String.fromCharCodes(original)}');
  print('Shallow:  ${String.fromCharCodes(shallow)}');  // Changed!
  print('Deep:     ${String.fromCharCodes(deep)}');     // Unchanged
  
  // Sublist operations (creates a new list)
  var data = Uint8List.fromList('Hello there!'.codeUnits);
  var sublist1 = data.sublist(0, 5);    // "Hello"
  var sublist2 = data.sublist(6);       // "there!"
  var sublist3 = data.sublist(0);       // Full copy
  
  print('\nSublist operations:');
  print('sublist1: ${String.fromCharCodes(sublist1)}');
  print('sublist2: ${String.fromCharCodes(sublist2)}');
  print('sublist3: ${String.fromCharCodes(sublist3)}');
  
  // Length information
  print('\nLength information:');
  print('data.length: ${data.length}');
  print('sublist1.length: ${sublist1.length}');
}
```

This example demonstrates the critical difference between shallow and deep  
copies in Dart. When assigning a Uint8List to another variable, both variables  
reference the same underlying data, so modifications affect both. To create  
an independent copy, use `Uint8List.fromList()` or the `sublist()` method.  

The sublist method always creates a new Uint8List containing the specified  
range of elements. This is different from some languages where slice operations  
might create views into the original data. Understanding this behavior is  
essential for memory management and preventing unintended data modifications.  

### Converting Between Types and Uint8List

Dart provides various methods to convert between different data types and  
byte representations.  

```dart
import 'dart:typed_data';

void main() {
  // String to bytes and back
  print('String conversions:');
  var text = 'Hello there!';
  var bytes = Uint8List.fromList(text.codeUnits);
  print('String to bytes: $bytes');
  var backToString = String.fromCharCodes(bytes);
  print('Bytes to string: $backToString');
  
  // Integer to bytes (manual conversion)
  print('\nInteger conversions:');
  var number = 256;
  var intBytes = Uint8List(4);
  intBytes[0] = (number >> 24) & 0xFF;
  intBytes[1] = (number >> 16) & 0xFF;
  intBytes[2] = (number >> 8) & 0xFF;
  intBytes[3] = number & 0xFF;
  print('Integer $number to bytes: $intBytes');
  
  // Using ByteData for structured conversions
  print('\nUsing ByteData:');
  var buffer = Uint8List(8).buffer;
  var byteData = ByteData.view(buffer);
  
  // Write different types
  byteData.setInt32(0, 42);
  byteData.setFloat32(4, 3.14);
  
  // Read back
  print('Int32 at 0: ${byteData.getInt32(0)}');
  print('Float32 at 4: ${byteData.getFloat32(4)}');
  
  // List of integers to Uint8List
  print('\nList conversions:');
  var regularList = [72, 101, 108, 108, 111]; // "Hello"
  var typedList = Uint8List.fromList(regularList);
  print('List to Uint8List: ${String.fromCharCodes(typedList)}');
}
```

This example demonstrates various type conversions essential for binary data  
manipulation. String to byte conversion uses the codeUnits property, which  
provides UTF-16 code units. For ASCII text, this gives the expected byte values.  

The ByteData class provides a powerful interface for reading and writing  
structured binary data with explicit type control. It's particularly useful  
when working with binary protocols or file formats that specify exact data  
types and byte offsets. ByteData handles endianness and type conversions  
automatically, making it safer than manual bit manipulation.  

### Uint8List Search and Comparison

Searching and comparing byte sequences is common in binary data processing,  
such as finding patterns or validating data integrity.  

```dart
import 'dart:typed_data';

void main() {
  var data = Uint8List.fromList('Hello there! Hello again!'.codeUnits);
  var pattern = Uint8List.fromList('Hello'.codeUnits);
  
  // Find first occurrence
  print('Searching for pattern:');
  var index = _findPattern(data, pattern);
  print('First occurrence at index: $index');
  
  // Find all occurrences
  var allIndices = _findAllPatterns(data, pattern);
  print('All occurrences at indices: $allIndices');
  
  // Compare two byte sequences
  var data1 = Uint8List.fromList([1, 2, 3, 4, 5]);
  var data2 = Uint8List.fromList([1, 2, 3, 4, 5]);
  var data3 = Uint8List.fromList([1, 2, 3, 4, 6]);
  
  print('\nComparisons:');
  print('data1 equals data2: ${_bytesEqual(data1, data2)}');
  print('data1 equals data3: ${_bytesEqual(data1, data3)}');
  
  // Check if starts with pattern
  var startsWithHello = _startsWith(data, pattern);
  print('\nStarts with "Hello": $startsWithHello');
}

int _findPattern(Uint8List data, Uint8List pattern) {
  for (var i = 0; i <= data.length - pattern.length; i++) {
    var match = true;
    for (var j = 0; j < pattern.length; j++) {
      if (data[i + j] != pattern[j]) {
        match = false;
        break;
      }
    }
    if (match) return i;
  }
  return -1;
}

List<int> _findAllPatterns(Uint8List data, Uint8List pattern) {
  var indices = <int>[];
  for (var i = 0; i <= data.length - pattern.length; i++) {
    var match = true;
    for (var j = 0; j < pattern.length; j++) {
      if (data[i + j] != pattern[j]) {
        match = false;
        break;
      }
    }
    if (match) indices.add(i);
  }
  return indices;
}

bool _bytesEqual(Uint8List a, Uint8List b) {
  if (a.length != b.length) return false;
  for (var i = 0; i < a.length; i++) {
    if (a[i] != b[i]) return false;
  }
  return true;
}

bool _startsWith(Uint8List data, Uint8List pattern) {
  if (data.length < pattern.length) return false;
  for (var i = 0; i < pattern.length; i++) {
    if (data[i] != pattern[i]) return false;
  }
  return true;
}
```

This example demonstrates common pattern matching and comparison operations  
on byte sequences. Pattern finding is essential for parsing binary protocols,  
detecting file signatures, and extracting data from binary streams.  

The helper functions show how to implement efficient byte-level searching  
and comparison. While these operations could be done using list methods,  
understanding the byte-level implementation is valuable for optimizing  
performance-critical code and working with large binary datasets.  

### Uint8List Modification and Transformation

Modifying and transforming byte sequences is fundamental to binary data  
processing, including operations like XOR encryption and data masking.  

```dart
import 'dart:typed_data';

void main() {
  // In-place modification
  print('In-place modifications:');
  var data = Uint8List.fromList('hello'.codeUnits);
  print('Original: ${String.fromCharCodes(data)}');
  
  // Convert to uppercase (simple ASCII)
  for (var i = 0; i < data.length; i++) {
    if (data[i] >= 97 && data[i] <= 122) {
      data[i] = data[i] - 32;
    }
  }
  print('Uppercase: ${String.fromCharCodes(data)}');
  
  // XOR transformation
  print('\nXOR transformation:');
  var message = Uint8List.fromList('Secret'.codeUnits);
  var key = 0x5A;
  print('Original: ${String.fromCharCodes(message)}');
  
  // Encrypt
  var encrypted = Uint8List(message.length);
  for (var i = 0; i < message.length; i++) {
    encrypted[i] = message[i] ^ key;
  }
  print('Encrypted bytes: $encrypted');
  
  // Decrypt
  var decrypted = Uint8List(encrypted.length);
  for (var i = 0; i < encrypted.length; i++) {
    decrypted[i] = encrypted[i] ^ key;
  }
  print('Decrypted: ${String.fromCharCodes(decrypted)}');
  
  // Byte reversal
  print('\nByte reversal:');
  var original = Uint8List.fromList([1, 2, 3, 4, 5]);
  var reversed = Uint8List.fromList(original.reversed.toList());
  print('Original: $original');
  print('Reversed: $reversed');
  
  // Fill with pattern
  print('\nFill with pattern:');
  var buffer = Uint8List(10);
  buffer.fillRange(0, 10, 0xFF);
  print('Filled with 0xFF: $buffer');
}
```

This example demonstrates various byte transformation techniques. In-place  
modification shows how to alter bytes directly, useful for operations like  
case conversion or data normalization. The XOR transformation demonstrates  
a simple encryption technique that's reversible (XOR is its own inverse).  

Byte reversal and pattern filling are common operations in binary data  
processing. The fillRange method efficiently sets multiple bytes to the  
same value, useful for initializing buffers or creating patterns. These  
transformations form the building blocks for more complex binary operations.  

### Working with Individual Bytes

Understanding how to manipulate individual bytes and extract specific  
information is essential for low-level binary operations.  

```dart
import 'dart:typed_data';

void main() {
  // Byte value analysis
  print('Byte value analysis:');
  var byte = 0xA5; // Binary: 10100101
  
  print('Byte: 0x${byte.toRadixString(16).padLeft(2, '0')}');
  print('Binary: ${byte.toRadixString(2).padLeft(8, '0')}');
  print('Decimal: $byte');
  
  // Extract individual bits
  print('\nBit extraction:');
  for (var i = 7; i >= 0; i--) {
    var bit = (byte >> i) & 1;
    print('Bit $i: $bit');
  }
  
  // Set specific bits
  print('\nBit manipulation:');
  var value = 0x00;
  value |= (1 << 0);  // Set bit 0
  value |= (1 << 2);  // Set bit 2
  value |= (1 << 7);  // Set bit 7
  print('After setting bits 0, 2, 7: 0x${value.toRadixString(16)}');
  print('Binary: ${value.toRadixString(2).padLeft(8, '0')}');
  
  // Clear specific bits
  value &= ~(1 << 2);  // Clear bit 2
  print('After clearing bit 2: 0x${value.toRadixString(16)}');
  print('Binary: ${value.toRadixString(2).padLeft(8, '0')}');
  
  // Toggle bits
  value ^= (1 << 0);  // Toggle bit 0
  print('After toggling bit 0: 0x${value.toRadixString(16)}');
  print('Binary: ${value.toRadixString(2).padLeft(8, '0')}');
  
  // Check if bit is set
  var isBit7Set = (value & (1 << 7)) != 0;
  print('Is bit 7 set? $isBit7Set');
  
  // Count set bits (population count)
  print('\nCounting set bits:');
  var testByte = 0xAA; // 10101010
  var count = _countSetBits(testByte);
  print('Byte: 0x${testByte.toRadixString(16)} has $count set bits');
}

int _countSetBits(int byte) {
  var count = 0;
  while (byte != 0) {
    count += byte & 1;
    byte >>= 1;
  }
  return count;
}
```

This example demonstrates fundamental bit manipulation techniques essential  
for working with binary data. Understanding how to set, clear, toggle, and  
test individual bits is crucial for implementing flags, bit masks, and  
low-level protocols.  

The toRadixString method provides convenient conversion between decimal,  
hexadecimal, and binary representations, making it easier to visualize and  
debug binary data. The population count function (counting set bits) is  
a common algorithm in binary processing used in checksums, data compression,  
and cryptography.  

### Uint8List Growing and Shrinking

Dynamic byte arrays that grow and shrink are common in applications that  
process streaming data or build binary structures incrementally.  

```dart
import 'dart:typed_data';

void main() {
  // Using List builder for dynamic growth
  print('Dynamic byte array building:');
  var builder = <int>[];
  
  // Append individual bytes
  builder.add(0x48); // H
  builder.add(0x65); // e
  builder.add(0x6C); // l
  builder.add(0x6C); // l
  builder.add(0x6F); // o
  
  print('After adding bytes: ${String.fromCharCodes(builder)}');
  
  // Append multiple bytes
  builder.addAll([0x20, 0x74, 0x68, 0x65, 0x72, 0x65]); // " there"
  print('After adding more: ${String.fromCharCodes(builder)}');
  
  // Convert to Uint8List when done
  var data = Uint8List.fromList(builder);
  print('Final Uint8List: ${String.fromCharCodes(data)}');
  
  // Shrinking by creating sublist
  print('\nShrinking operations:');
  var truncated = data.sublist(0, 5); // Keep only "Hello"
  print('Truncated: ${String.fromCharCodes(truncated)}');
  
  // Remove specific bytes by filtering
  var filtered = Uint8List.fromList(
    data.where((byte) => byte != 0x20).toList() // Remove spaces
  );
  print('Without spaces: ${String.fromCharCodes(filtered)}');
  
  // Pre-allocated buffer with growth strategy
  print('\nPre-allocated buffer:');
  var capacity = 10;
  var buffer = Uint8List(capacity);
  var length = 0;
  
  void addByte(int byte) {
    if (length >= buffer.length) {
      // Double the capacity
      var newBuffer = Uint8List(buffer.length * 2);
      newBuffer.setRange(0, buffer.length, buffer);
      buffer = newBuffer;
    }
    buffer[length++] = byte;
  }
  
  for (var i = 0; i < 15; i++) {
    addByte(65 + i); // Add 'A', 'B', 'C', etc.
  }
  
  var result = buffer.sublist(0, length);
  print('Grown buffer: ${String.fromCharCodes(result)}');
  print('Final capacity: ${buffer.length}');
}
```

This example demonstrates strategies for working with dynamic byte arrays.  
While Uint8List has a fixed length, you can use a regular List<int> as a  
builder and convert to Uint8List when needed. This is efficient for building  
data incrementally without knowing the final size.  

The pre-allocated buffer pattern shows how to implement a growth strategy  
similar to other languages' dynamic arrays. This is useful for high-performance  
scenarios where you want to minimize allocations while still allowing growth.  

### Memory-Efficient Byte Operations

Optimizing memory usage is critical when processing large binary files or  
working with resource-constrained environments.  

```dart
import 'dart:typed_data';

void main() {
  // Reusing buffers
  print('Buffer reuse pattern:');
  var buffer = Uint8List(1024); // Reusable buffer
  
  void processChunk(List<int> source, int offset, int length) {
    // Copy data to buffer for processing
    for (var i = 0; i < length; i++) {
      buffer[i] = source[offset + i];
    }
    // Process buffer (example: calculate checksum)
    var sum = 0;
    for (var i = 0; i < length; i++) {
      sum = (sum + buffer[i]) & 0xFF;
    }
    print('Chunk checksum: 0x${sum.toRadixString(16)}');
  }
  
  var data = Uint8List.fromList(List.generate(3000, (i) => i & 0xFF));
  
  // Process in chunks using same buffer
  for (var i = 0; i < data.length; i += 1024) {
    var chunkSize = (i + 1024 > data.length) ? data.length - i : 1024;
    processChunk(data, i, chunkSize);
  }
  
  // View pattern - no copy
  print('\nBuffer views (zero-copy):');
  var largeData = Uint8List(100);
  largeData.fillRange(0, 100, 0xAA);
  
  // Create view without copying
  var view = Uint8List.view(largeData.buffer, 10, 20);
  print('View length: ${view.length}');
  
  // Modifying view modifies original
  view[0] = 0xFF;
  print('Original at index 10: 0x${largeData[10].toRadixString(16)}');
  
  // ByteData view for structured access
  print('\nByteData view:');
  var structured = Uint8List(16);
  var byteDataView = ByteData.view(structured.buffer);
  
  byteDataView.setUint32(0, 0x12345678);
  byteDataView.setUint32(4, 0xABCDEF00);
  
  print('Structured data: $structured');
}
```

This example demonstrates memory-efficient patterns for binary data processing.  
Buffer reuse is essential when processing large files in chunks, as it avoids  
allocating new memory for each chunk. The pattern shown is common in stream  
processing and file parsing.  

The view pattern using Uint8List.view creates a window into existing data  
without copying, enabling zero-copy operations. This is particularly useful  
when working with large buffers where you need to access different sections.  
ByteData.view provides structured access to the same underlying data.  

### Uint8List Sorting and Ordering

Sorting byte sequences is useful for creating indices, normalizing data,  
and implementing binary search algorithms.  

```dart
import 'dart:typed_data';

void main() {
  // Sort bytes
  print('Sorting bytes:');
  var data = Uint8List.fromList([5, 2, 8, 1, 9, 3, 7, 4, 6]);
  print('Original: $data');
  
  var sorted = Uint8List.fromList(data.toList()..sort());
  print('Sorted: $sorted');
  
  // Sort in descending order
  var descending = Uint8List.fromList(
    data.toList()..sort((a, b) => b.compareTo(a))
  );
  print('Descending: $descending');
  
  // Sort byte sequences lexicographically
  print('\nLexicographic sorting:');
  var sequences = [
    Uint8List.fromList([1, 2, 3]),
    Uint8List.fromList([1, 2, 1]),
    Uint8List.fromList([1, 3, 0]),
    Uint8List.fromList([1, 2, 2]),
  ];
  
  print('Original sequences:');
  for (var seq in sequences) {
    print('  $seq');
  }
  
  sequences.sort((a, b) {
    var minLen = a.length < b.length ? a.length : b.length;
    for (var i = 0; i < minLen; i++) {
      if (a[i] != b[i]) return a[i].compareTo(b[i]);
    }
    return a.length.compareTo(b.length);
  });
  
  print('Sorted sequences:');
  for (var seq in sequences) {
    print('  $seq');
  }
  
  // Binary search on sorted data
  print('\nBinary search:');
  var sortedData = Uint8List.fromList([1, 3, 5, 7, 9, 11, 13, 15]);
  print('Sorted data: $sortedData');
  
  var target = 7;
  var index = _binarySearch(sortedData, target);
  print('Found $target at index: $index');
  
  var notFound = 6;
  var notFoundIndex = _binarySearch(sortedData, notFound);
  print('Search for $notFound: $notFoundIndex');
}

int _binarySearch(Uint8List data, int target) {
  var left = 0;
  var right = data.length - 1;
  
  while (left <= right) {
    var mid = (left + right) ~/ 2;
    
    if (data[mid] == target) {
      return mid;
    } else if (data[mid] < target) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }
  
  return -1; // Not found
}
```

This example demonstrates sorting operations on byte data. Simple byte sorting  
uses Dart's built-in sort method after converting to a list. For more complex  
sorting like lexicographic ordering of byte sequences, custom comparators  
are needed.  

Binary search on sorted byte data is an efficient way to find specific values  
in large datasets. The algorithm has O(log n) complexity, making it much faster  
than linear search for large arrays. Understanding these sorting and searching  
patterns is essential for implementing efficient binary data structures.  

### Advanced Uint8List Patterns

Advanced patterns combine multiple techniques for solving complex binary  
data manipulation problems.  

```dart
import 'dart:typed_data';

void main() {
  // Circular buffer implementation
  print('Circular buffer:');
  var circularBuffer = _CircularBuffer(5);
  
  for (var i = 0; i < 8; i++) {
    circularBuffer.write(i);
    print('After writing $i: ${circularBuffer.data}');
  }
  
  // Checksum calculation
  print('\nChecksum calculation:');
  var data = Uint8List.fromList('Hello there!'.codeUnits);
  var checksum8 = _calculateChecksum8(data);
  var checksum16 = _calculateChecksum16(data);
  print('Data: ${String.fromCharCodes(data)}');
  print('8-bit checksum: 0x${checksum8.toRadixString(16)}');
  print('16-bit checksum: 0x${checksum16.toRadixString(16)}');
  
  // Data interleaving
  print('\nData interleaving:');
  var channel1 = Uint8List.fromList([1, 2, 3, 4]);
  var channel2 = Uint8List.fromList([5, 6, 7, 8]);
  var interleaved = _interleave(channel1, channel2);
  print('Channel 1: $channel1');
  print('Channel 2: $channel2');
  print('Interleaved: $interleaved');
  
  var deinterleaved = _deinterleave(interleaved);
  print('Deinterleaved:');
  print('  Channel 1: ${deinterleaved[0]}');
  print('  Channel 2: ${deinterleaved[1]}');
  
  // Run-length encoding
  print('\nRun-length encoding:');
  var repeated = Uint8List.fromList([1, 1, 1, 2, 2, 3, 3, 3, 3]);
  print('Original: $repeated');
  var encoded = _runLengthEncode(repeated);
  print('Encoded: $encoded');
  var decoded = _runLengthDecode(encoded);
  print('Decoded: $decoded');
}

class _CircularBuffer {
  final Uint8List data;
  int _writePos = 0;
  
  _CircularBuffer(int size) : data = Uint8List(size);
  
  void write(int byte) {
    data[_writePos] = byte;
    _writePos = (_writePos + 1) % data.length;
  }
}

int _calculateChecksum8(Uint8List data) {
  var sum = 0;
  for (var byte in data) {
    sum = (sum + byte) & 0xFF;
  }
  return sum;
}

int _calculateChecksum16(Uint8List data) {
  var sum = 0;
  for (var byte in data) {
    sum = (sum + byte) & 0xFFFF;
  }
  return sum;
}

Uint8List _interleave(Uint8List a, Uint8List b) {
  var result = Uint8List(a.length + b.length);
  for (var i = 0; i < a.length; i++) {
    result[i * 2] = a[i];
    result[i * 2 + 1] = b[i];
  }
  return result;
}

List<Uint8List> _deinterleave(Uint8List data) {
  var half = data.length ~/ 2;
  var a = Uint8List(half);
  var b = Uint8List(half);
  
  for (var i = 0; i < half; i++) {
    a[i] = data[i * 2];
    b[i] = data[i * 2 + 1];
  }
  
  return [a, b];
}

Uint8List _runLengthEncode(Uint8List data) {
  var encoded = <int>[];
  var i = 0;
  
  while (i < data.length) {
    var value = data[i];
    var count = 1;
    
    while (i + count < data.length && data[i + count] == value && count < 255) {
      count++;
    }
    
    encoded.add(count);
    encoded.add(value);
    i += count;
  }
  
  return Uint8List.fromList(encoded);
}

Uint8List _runLengthDecode(Uint8List encoded) {
  var decoded = <int>[];
  
  for (var i = 0; i < encoded.length; i += 2) {
    var count = encoded[i];
    var value = encoded[i + 1];
    
    for (var j = 0; j < count; j++) {
      decoded.add(value);
    }
  }
  
  return Uint8List.fromList(decoded);
}
```

This example demonstrates advanced patterns commonly used in binary data  
processing. The circular buffer is useful for streaming data, fixed-size  
logs, or sliding window algorithms. It efficiently reuses memory by wrapping  
around when reaching the end.  

Checksums provide data integrity verification, with 8-bit and 16-bit variants  
shown. Data interleaving is common in multimedia processing where multiple  
channels need to be combined. Run-length encoding demonstrates a simple  
compression technique effective for data with repeated sequences.  

## Reading and Writing Binary Files

### Basic Binary File Reading

Reading binary files is fundamental for processing images, audio, video,  
and other non-text formats. Dart provides simple async methods for reading  
entire files or parts of them.  

```dart
import 'dart:io';
import 'dart:typed_data';

void main() async {
  // Create a sample binary file first
  var sampleFile = File('sample.bin');
  await sampleFile.writeAsBytes(
    Uint8List.fromList([0x00, 0x01, 0x02, 0x03, 0x04, 0x05])
  );
  
  // Method 1: Read entire file as bytes
  print('1. Reading entire file:');
  var data1 = await sampleFile.readAsBytes();
  print('Read ${data1.length} bytes: $data1');
  print('Hex: ${data1.map((b) => b.toRadixString(16).padLeft(2, '0')).join(' ')}');
  
  // Method 2: Read file synchronously (blocking)
  print('\n2. Synchronous reading:');
  var data2 = sampleFile.readAsBytesSync();
  print('Read ${data2.length} bytes synchronously');
  
  // Method 3: Read specific number of bytes
  print('\n3. Reading specific bytes:');
  var raf = await sampleFile.open(mode: FileMode.read);
  var buffer = Uint8List(3);
  await raf.readInto(buffer);
  print('First 3 bytes: $buffer');
  await raf.close();
  
  // Method 4: Check file exists before reading
  print('\n4. Safe reading with existence check:');
  var testFile = File('nonexistent.bin');
  if (await testFile.exists()) {
    var data = await testFile.readAsBytes();
    print('File data: $data');
  } else {
    print('File does not exist');
  }
  
  // Cleanup
  await sampleFile.delete();
}
```

This example demonstrates various approaches to reading binary files in Dart.  
The asynchronous readAsBytes method is preferred for production code as it  
doesn't block the event loop. For small files or CLI applications, the  
synchronous variant may be acceptable.  

Using RandomAccessFile with readInto provides fine-grained control over  
how much data to read and where to store it. This is efficient when you  
only need part of a file or want to process it in chunks. Always check  
file existence before reading to avoid exceptions.  

### Binary File Writing with Different Methods

Binary file writing supports various approaches depending on your use  
case, from simple writes to high-performance buffered output.  

```dart
import 'dart:io';
import 'dart:typed_data';

void main() async {
  // Method 1: Write entire byte array at once
  print('1. Writing entire byte array:');
  var data1 = Uint8List.fromList([0x00, 0x01, 0x02, 0x03, 0x04, 0x05]);
  var file1 = File('output1.bin');
  await file1.writeAsBytes(data1);
  print('Wrote ${data1.length} bytes to output1.bin');
  
  // Method 2: Synchronous writing
  print('\n2. Synchronous writing:');
  var data2 = Uint8List.fromList([0x10, 0x20, 0x30, 0x40]);
  var file2 = File('output2.bin');
  file2.writeAsBytesSync(data2);
  print('Wrote ${data2.length} bytes synchronously');
  
  // Method 3: Append to existing file
  print('\n3. Appending to file:');
  var appendData = Uint8List.fromList([0x50, 0x60]);
  await file2.writeAsBytes(appendData, mode: FileMode.append);
  var verifyData = await file2.readAsBytes();
  print('File now contains ${verifyData.length} bytes: $verifyData');
  
  // Method 4: Using RandomAccessFile for control
  print('\n4. Using RandomAccessFile:');
  var file3 = File('output3.bin');
  var raf = await file3.open(mode: FileMode.write);
  
  // Write header
  await raf.writeFrom(Uint8List.fromList([0xFF, 0xFE]));
  
  // Write data
  await raf.writeFrom(Uint8List.fromList([1, 2, 3, 4, 5]));
  
  // Write footer
  await raf.writeFrom(Uint8List.fromList([0xFD, 0xFC]));
  
  await raf.close();
  print('Wrote structured data to output3.bin');
  
  // Method 5: Stream writing
  print('\n5. Stream writing:');
  var file4 = File('output4.bin');
  var sink = file4.openWrite();
  
  for (var i = 0; i < 10; i++) {
    sink.add([i * 10]);
  }
  
  await sink.close();
  print('Wrote stream data to output4.bin');
  
  // Verify and cleanup
  var sizes = await Future.wait([
    file1.length(),
    file2.length(),
    file3.length(),
    file4.length(),
  ]);
  
  print('\nFile sizes:');
  print('  output1.bin: ${sizes[0]} bytes');
  print('  output2.bin: ${sizes[1]} bytes');
  print('  output3.bin: ${sizes[2]} bytes');
  print('  output4.bin: ${sizes[3]} bytes');
  
  // Cleanup
  await file1.delete();
  await file2.delete();
  await file3.delete();
  await file4.delete();
}
```

This example shows five different methods for writing binary files. The  
simple writeAsBytes method is most common and suitable for writing complete  
data at once. The append mode allows adding data to existing files without  
overwriting.  

RandomAccessFile provides the most control, allowing you to write data at  
specific positions and build files incrementally. Stream writing with openWrite  
is efficient for generating large files or writing data as it becomes available,  
preventing memory overflow.  

### Random Access Binary File Operations

Random access allows reading and writing at specific file positions,  
essential for databases, binary formats with headers, and large file editing.  

```dart
import 'dart:io';
import 'dart:typed_data';

void main() async {
  var file = File('random_access.bin');
  
  // Create a file with initial data
  print('Creating file with initial data:');
  var initialData = Uint8List(100);
  for (var i = 0; i < 100; i++) {
    initialData[i] = i;
  }
  await file.writeAsBytes(initialData);
  print('Created file with 100 bytes');
  
  // Open for random access
  var raf = await file.open(mode: FileMode.append);
  
  // Read from specific position
  print('\nReading from specific positions:');
  await raf.setPosition(50);
  var buffer = Uint8List(5);
  await raf.readInto(buffer);
  print('Bytes at position 50-54: $buffer');
  
  // Write at specific position
  print('\nWriting at specific position:');
  await raf.setPosition(10);
  await raf.writeFrom(Uint8List.fromList([0xFF, 0xFE, 0xFD]));
  print('Wrote 3 bytes at position 10');
  
  // Read modified data
  await raf.setPosition(10);
  buffer = Uint8List(3);
  await raf.readInto(buffer);
  print('Modified bytes at position 10: $buffer');
  
  // Get current position
  var currentPos = await raf.position();
  print('Current position: $currentPos');
  
  // Get file length
  var fileLen = await raf.length();
  print('File length: $fileLen');
  
  // Truncate file
  print('\nTruncating file:');
  await raf.truncate(50);
  var newLen = await raf.length();
  print('New file length: $newLen');
  
  await raf.close();
  
  // Verify final content
  var finalData = await file.readAsBytes();
  print('Final data (first 20 bytes): ${finalData.sublist(0, 20)}');
  
  // Cleanup
  await file.delete();
}
```

This example demonstrates random access file operations using RandomAccessFile.  
The setPosition method allows jumping to any byte offset in the file, enabling  
non-sequential reading and writing. This is essential for file formats with  
headers, indices, or when updating specific records.  

The truncate method allows shrinking files to a specific size, useful for  
removing trailing data or implementing file-based data structures. Random  
access is particularly valuable for databases, log file rotation, and binary  
file format implementations.  

### Binary File Streaming and Pipes

Streaming enables processing large files without loading them entirely into  
memory, essential for handling files larger than available RAM.  

```dart
import 'dart:io';
import 'dart:typed_data';
import 'dart:async';

void main() async {
  // Create a large test file
  print('Creating test file:');
  var sourceFile = File('large_source.bin');
  var sink = sourceFile.openWrite();
  
  for (var i = 0; i < 10000; i++) {
    sink.add([i & 0xFF]);
  }
  await sink.close();
  print('Created source file with ${await sourceFile.length()} bytes');
  
  // Stream reading and processing
  print('\nStreaming file processing:');
  var destFile = File('processed.bin');
  var outputSink = destFile.openWrite();
  
  var bytesProcessed = 0;
  await for (var chunk in sourceFile.openRead()) {
    // Process chunk (example: XOR with 0x55)
    var processed = Uint8List.fromList(chunk);
    for (var i = 0; i < processed.length; i++) {
      processed[i] ^= 0x55;
    }
    
    outputSink.add(processed);
    bytesProcessed += chunk.length;
    
    if (bytesProcessed % 2000 == 0) {
      print('Processed $bytesProcessed bytes...');
    }
  }
  
  await outputSink.close();
  print('Total bytes processed: $bytesProcessed');
  
  // Verify result
  var destSize = await destFile.length();
  print('Destination file size: $destSize bytes');
  
  // Stream with transformation
  print('\nStream with transformation:');
  var transformedFile = File('transformed.bin');
  var transformSink = transformedFile.openWrite();
  
  var stream = sourceFile.openRead();
  await stream
      .map((chunk) {
        // Transform: increment each byte
        return Uint8List.fromList(
          chunk.map((byte) => (byte + 1) & 0xFF).toList()
        );
      })
      .pipe(transformSink);
  
  print('Transformed file size: ${await transformedFile.length()} bytes');
  
  // Cleanup
  await sourceFile.delete();
  await destFile.delete();
  await transformedFile.delete();
}
```

This example demonstrates stream-based file processing, which is memory-  
efficient for large files. The openRead method returns a Stream<List<int>>  
that emits chunks of data, allowing processing without loading the entire  
file into memory.  

The map and pipe operations enable declarative stream transformations.  
This pattern is ideal for file conversion, encryption, compression, or any  
operation that processes data sequentially. Streams automatically handle  
backpressure, preventing memory overflow.  

### Reading Structured Binary Data

Many binary formats have structured layouts with headers, fixed-size records,  
and specific data types. ByteData provides efficient access to structured data.  

```dart
import 'dart:io';
import 'dart:typed_data';

void main() async {
  // Create a file with structured data
  print('Creating structured binary file:');
  var file = File('structured.bin');
  var buffer = ByteData(32);
  
  // Header (16 bytes)
  buffer.setUint32(0, 0x4D4F4F46, Endian.big);  // Magic: 'MOOF'
  buffer.setUint16(4, 1);                        // Version
  buffer.setUint16(6, 0);                        // Flags
  buffer.setUint64(8, DateTime.now().millisecondsSinceEpoch);
  
  // Data record (16 bytes)
  buffer.setInt32(16, -12345);
  buffer.setFloat32(20, 3.14159);
  buffer.setUint32(24, 0xDEADBEEF);
  buffer.setUint32(28, 42);
  
  await file.writeAsBytes(buffer.buffer.asUint8List());
  print('Created structured file');
  
  // Read and parse structured data
  print('\nReading structured data:');
  var data = await file.readAsBytes();
  var reader = ByteData.view(data.buffer);
  
  // Parse header
  var magic = reader.getUint32(0, Endian.big);
  var version = reader.getUint16(4);
  var flags = reader.getUint16(6);
  var timestamp = reader.getUint64(8);
  
  print('Header:');
  print('  Magic: 0x${magic.toRadixString(16).toUpperCase()}');
  print('  Version: $version');
  print('  Flags: $flags');
  print('  Timestamp: $timestamp');
  
  // Parse data record
  var int32Value = reader.getInt32(16);
  var floatValue = reader.getFloat32(20);
  var uint32Value = reader.getUint32(24);
  var counter = reader.getUint32(28);
  
  print('Data:');
  print('  Int32: $int32Value');
  print('  Float32: $floatValue');
  print('  Uint32: 0x${uint32Value.toRadixString(16).toUpperCase()}');
  print('  Counter: $counter');
  
  // Cleanup
  await file.delete();
}
```

This example demonstrates reading and writing structured binary data using  
ByteData. This is essential for implementing binary file formats, network  
protocols, or interfacing with native libraries. ByteData handles type  
conversions and endianness automatically.  

The example shows a simple file format with a header and data record, similar  
to many real-world formats. Using specific offsets and data types ensures  
cross-platform compatibility and precise control over the binary layout.  

### File Copying with Progress and Checksums

Copying large binary files efficiently while tracking progress and verifying  
integrity is a common requirement.  

```dart
import 'dart:io';
import 'dart:typed_data';

void main() async {
  // Create source file
  print('Creating source file:');
  var sourceFile = File('source_large.bin');
  var sourceSink = sourceFile.openWrite();
  
  var checksum = 0;
  for (var i = 0; i < 50000; i++) {
    var byte = i & 0xFF;
    sourceSink.add([byte]);
    checksum = (checksum + byte) & 0xFFFFFFFF;
  }
  await sourceSink.close();
  
  var sourceSize = await sourceFile.length();
  print('Created source file: $sourceSize bytes');
  print('Source checksum: 0x${checksum.toRadixString(16)}');
  
  // Copy with progress tracking
  print('\nCopying file with progress:');
  var destFile = File('destination.bin');
  var destSink = destFile.openWrite();
  
  var bytesCopied = 0;
  var destChecksum = 0;
  
  await for (var chunk in sourceFile.openRead()) {
    destSink.add(chunk);
    bytesCopied += chunk.length;
    
    // Calculate checksum
    for (var byte in chunk) {
      destChecksum = (destChecksum + byte) & 0xFFFFFFFF;
    }
    
    // Report progress
    var progress = (bytesCopied / sourceSize * 100).toStringAsFixed(1);
    if (bytesCopied % 10000 == 0 || bytesCopied == sourceSize) {
      print('Progress: $progress% ($bytesCopied / $sourceSize bytes)');
    }
  }
  
  await destSink.close();
  
  // Verify copy
  print('\nVerifying copy:');
  var destSize = await destFile.length();
  print('Destination size: $destSize bytes');
  print('Destination checksum: 0x${destChecksum.toRadixString(16)}');
  print('Checksums match: ${checksum == destChecksum}');
  print('Sizes match: ${sourceSize == destSize}');
  
  // Cleanup
  await sourceFile.delete();
  await destFile.delete();
}
```

This example demonstrates copying files with progress tracking and checksum  
validation. Progress tracking is important for user feedback during long  
operations. Checksums ensure data integrity by detecting corruption during  
the copy process.  

The streaming approach processes the file in chunks, preventing memory overflow  
with large files. The checksum is calculated incrementally as data is copied,  
avoiding the need to read the file again for verification.  

### Memory-Mapped File Operations

Memory-mapped operations simulate accessing files as if they were in memory,  
useful for large files and databases requiring random access.  

```dart
import 'dart:io';
import 'dart:typed_data';

void main() async {
  // Create a large file for memory-mapped access simulation
  print('Creating data file:');
  var dataFile = File('memmap_data.bin');
  var buffer = Uint8List(1024);
  
  // Fill with pattern
  for (var i = 0; i < buffer.length; i++) {
    buffer[i] = i & 0xFF;
  }
  
  await dataFile.writeAsBytes(buffer);
  print('Created ${buffer.length} byte file');
  
  // Simulate memory-mapped access with RandomAccessFile
  print('\nSimulated memory-mapped operations:');
  var raf = await dataFile.open(mode: FileMode.append);
  
  // Read at various positions (like memory access)
  var positions = [0, 100, 200, 500, 800];
  var readBuffer = Uint8List(4);
  
  for (var pos in positions) {
    await raf.setPosition(pos);
    await raf.readInto(readBuffer);
    
    var value = (readBuffer[0] << 24) | (readBuffer[1] << 16) |
                (readBuffer[2] << 8) | readBuffer[3];
    print('Position $pos: ${readBuffer.join(', ')} (combined: $value)');
  }
  
  // Pattern-based modifications
  await raf.setPosition(512);
  var pattern = Uint8List.fromList([0xFF, 0xAA, 0x55, 0x00]);
  await raf.writeFrom(pattern);
  
  print('Modified pattern at position 512');
  
  await raf.close();
  
  // Verify modifications
  var modifiedData = await dataFile.readAsBytes();
  print('Verification at 512-515: ${modifiedData.sublist(512, 516).join(', ')}');
  
  // Cleanup
  await dataFile.delete();
}
```

This example simulates memory-mapped file access using RandomAccessFile.  
While Dart doesn't have direct memory-mapping APIs like some languages,  
RandomAccessFile provides similar functionality for random access patterns.  

The example demonstrates reading and writing at arbitrary positions without  
loading the entire file, useful for database-like operations, binary search  
in large files, or implementing custom file formats with indices.  

### Concurrent File Processing

Processing multiple files concurrently can significantly improve performance  
for I/O-bound operations.  

```dart
import 'dart:io';
import 'dart:typed_data';
import 'dart:async';

void main() async {
  // Create multiple test files
  print('Creating test files:');
  var files = <File>[];
  
  for (var i = 0; i < 5; i++) {
    var file = File('test_$i.bin');
    var data = Uint8List(1000);
    for (var j = 0; j < 1000; j++) {
      data[j] = (i * 1000 + j) & 0xFF;
    }
    await file.writeAsBytes(data);
    files.add(file);
  }
  
  print('Created ${files.length} files');
  
  // Process files concurrently
  print('\nProcessing files concurrently:');
  var startTime = DateTime.now();
  
  var results = await Future.wait(
    files.map((file) => _processFile(file))
  );
  
  var endTime = DateTime.now();
  var duration = endTime.difference(startTime);
  
  print('\nResults:');
  for (var i = 0; i < results.length; i++) {
    print('File $i: checksum = 0x${results[i].toRadixString(16)}');
  }
  print('Processing took: ${duration.inMilliseconds}ms');
  
  // Cleanup
  for (var file in files) {
    await file.delete();
  }
}

Future<int> _processFile(File file) async {
  var data = await file.readAsBytes();
  var checksum = 0;
  
  // Simulate some processing
  for (var byte in data) {
    checksum = (checksum + byte) & 0xFFFFFFFF;
  }
  
  // Simulate some work
  await Future.delayed(Duration(milliseconds: 100));
  
  return checksum;
}
```

This example demonstrates concurrent file processing using Future.wait,  
which processes multiple files simultaneously. This approach is efficient  
for I/O-bound operations where the CPU spends time waiting for disk access.  

Concurrent processing can dramatically reduce total processing time when  
dealing with multiple files. However, be mindful of system resources and  
the number of concurrent operations to avoid overwhelming the file system.  

### Binary File Compression and Decompression

Compressing binary data reduces storage space and network bandwidth,  
essential for efficient data management.  

```dart
import 'dart:io';
import 'dart:typed_data';
import 'dart:convert';

void main() async {
  // Create test data
  print('Creating test data:');
  var originalData = Uint8List(10000);
  
  // Fill with compressible pattern
  for (var i = 0; i < originalData.length; i++) {
    originalData[i] = (i % 256);
  }
  
  var originalFile = File('original.bin');
  await originalFile.writeAsBytes(originalData);
  print('Original size: ${originalData.length} bytes');
  
  // Compress using GZIP
  print('\nCompressing with GZIP:');
  var compressedFile = File('compressed.gz');
  var gzipEncoder = GZipCodec();
  
  var compressed = gzipEncoder.encode(originalData);
  await compressedFile.writeAsBytes(compressed);
  
  var compressedSize = await compressedFile.length();
  var ratio = ((1 - compressedSize / originalData.length) * 100).toStringAsFixed(1);
  print('Compressed size: $compressedSize bytes');
  print('Compression ratio: $ratio%');
  
  // Decompress
  print('\nDecompressing:');
  var compressedBytes = await compressedFile.readAsBytes();
  var decompressed = gzipEncoder.decode(compressedBytes);
  
  print('Decompressed size: ${decompressed.length} bytes');
  
  // Verify integrity
  var match = true;
  for (var i = 0; i < originalData.length; i++) {
    if (originalData[i] != decompressed[i]) {
      match = false;
      break;
    }
  }
  print('Data integrity: ${match ? 'PASSED' : 'FAILED'}');
  
  // Stream compression
  print('\nStream compression:');
  var streamCompressedFile = File('stream_compressed.gz');
  var sink = streamCompressedFile.openWrite();
  
  sink.add(gzipEncoder.encode(originalData));
  await sink.close();
  
  print('Stream compressed size: ${await streamCompressedFile.length()} bytes');
  
  // Cleanup
  await originalFile.delete();
  await compressedFile.delete();
  await streamCompressedFile.delete();
}
```

This example demonstrates binary file compression using GZIP, a widely-  
supported compression format. Compression is valuable for reducing storage  
requirements and network transfer times, especially for data with patterns  
or redundancy.  

The dart:convert library provides GZipCodec for compression and decompression.  
Stream-based compression is available for processing large files without  
loading them entirely into memory. Always verify data integrity after  
decompression to ensure no corruption occurred.  

### File Format Detection and Validation

Detecting file types and validating file integrity is crucial for security  
and robust file handling.  

```dart
import 'dart:io';
import 'dart:typed_data';

void main() async {
  // Create files with different signatures
  await _createTestFiles();
  
  // Detect file formats
  print('Detecting file formats:');
  var pngFile = File('test.png');
  var jpgFile = File('test.jpg');
  var pdfFile = File('test.pdf');
  var zipFile = File('test.zip');
  var unknownFile = File('test.unknown');
  
  await _detectAndPrint(pngFile);
  await _detectAndPrint(jpgFile);
  await _detectAndPrint(pdfFile);
  await _detectAndPrint(zipFile);
  await _detectAndPrint(unknownFile);
  
  // Validate PNG structure
  print('\nValidating PNG structure:');
  var isValid = await _validatePNG(pngFile);
  print('PNG validation: ${isValid ? 'PASSED' : 'FAILED'}');
  
  // Cleanup
  await pngFile.delete();
  await jpgFile.delete();
  await pdfFile.delete();
  await zipFile.delete();
  await unknownFile.delete();
}

Future<void> _createTestFiles() async {
  // PNG signature
  await File('test.png').writeAsBytes(
    Uint8List.fromList([0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A])
  );
  
  // JPEG signature
  await File('test.jpg').writeAsBytes(
    Uint8List.fromList([0xFF, 0xD8, 0xFF, 0xE0])
  );
  
  // PDF signature
  await File('test.pdf').writeAsBytes(
    Uint8List.fromList([0x25, 0x50, 0x44, 0x46])
  );
  
  // ZIP signature
  await File('test.zip').writeAsBytes(
    Uint8List.fromList([0x50, 0x4B, 0x03, 0x04])
  );
  
  // Unknown
  await File('test.unknown').writeAsBytes(
    Uint8List.fromList([0x00, 0x01, 0x02, 0x03])
  );
}

Future<void> _detectAndPrint(File file) async {
  var type = await _detectFileType(file);
  print('${file.path}: $type');
}

Future<String> _detectFileType(File file) async {
  if (!await file.exists()) {
    return 'File not found';
  }
  
  var data = await file.readAsBytes();
  if (data.length < 4) {
    return 'Unknown (file too small)';
  }
  
  // Check PNG
  if (data.length >= 8 &&
      data[0] == 0x89 && data[1] == 0x50 &&
      data[2] == 0x4E && data[3] == 0x47) {
    return 'PNG image';
  }
  
  // Check JPEG
  if (data[0] == 0xFF && data[1] == 0xD8 && data[2] == 0xFF) {
    return 'JPEG image';
  }
  
  // Check PDF
  if (data[0] == 0x25 && data[1] == 0x50 &&
      data[2] == 0x44 && data[3] == 0x46) {
    return 'PDF document';
  }
  
  // Check ZIP
  if (data[0] == 0x50 && data[1] == 0x4B &&
      data[2] == 0x03 && data[3] == 0x04) {
    return 'ZIP archive';
  }
  
  return 'Unknown format';
}

Future<bool> _validatePNG(File file) async {
  var data = await file.readAsBytes();
  
  // Check minimum size
  if (data.length < 8) return false;
  
  // Validate PNG signature
  var signature = [0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A];
  for (var i = 0; i < 8; i++) {
    if (data[i] != signature[i]) return false;
  }
  
  return true;
}
```

This example demonstrates file type detection using magic numbers (file  
signatures). Magic numbers are specific byte sequences at the beginning  
of files that identify their format. This is more reliable than using  
file extensions, which can be changed.  

File validation ensures files conform to expected formats, important for  
security (preventing malicious files) and robustness (handling corrupt data).  
The example shows basic PNG validation, but real-world validation would  
check additional structure like chunks and CRC checksums.  

