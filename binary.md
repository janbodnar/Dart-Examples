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

## Bitwise Operations and Bit Manipulation

### Basic Bitwise Operations

Bitwise operations manipulate individual bits, essential for low-level  
programming, optimization, and implementing protocols and algorithms.  

```dart
void main() {
  print('Basic bitwise operations:');
  
  var a = 0xAC; // 10101100
  var b = 0x53; // 01010011
  
  print('a = 0x${a.toRadixString(16)} (${a.toRadixString(2).padLeft(8, '0')})');
  print('b = 0x${b.toRadixString(16)} (${b.toRadixString(2).padLeft(8, '0')})');
  
  // AND - bits set in both operands
  var andResult = a & b;
  print('\nAND: 0x${andResult.toRadixString(16)} (${andResult.toRadixString(2).padLeft(8, '0')})');
  
  // OR - bits set in either operand
  var orResult = a | b;
  print('OR:  0x${orResult.toRadixString(16)} (${orResult.toRadixString(2).padLeft(8, '0')})');
  
  // XOR - bits set in one but not both
  var xorResult = a ^ b;
  print('XOR: 0x${xorResult.toRadixString(16)} (${xorResult.toRadixString(2).padLeft(8, '0')})');
  
  // NOT - inverts all bits (in 32-bit context)
  var notResult = ~a & 0xFF; // Mask to 8 bits
  print('NOT: 0x${notResult.toRadixString(16)} (${notResult.toRadixString(2).padLeft(8, '0')})');
  
  // Left shift - shift bits left, filling with zeros
  var leftShift = (a << 2) & 0xFF;
  print('\nLeft shift by 2:  0x${leftShift.toRadixString(16)} (${leftShift.toRadixString(2).padLeft(8, '0')})');
  
  // Right shift - shift bits right
  var rightShift = a >> 2;
  print('Right shift by 2: 0x${rightShift.toRadixString(16)} (${rightShift.toRadixString(2).padLeft(8, '0')})');
  
  // Practical example: flags
  print('\nPractical example - permission flags:');
  var READ = 1 << 0;    // 0001
  var WRITE = 1 << 1;   // 0010
  var EXECUTE = 1 << 2; // 0100
  
  var permissions = READ | WRITE; // User can read and write
  print('Permissions: ${permissions.toRadixString(2).padLeft(4, '0')}');
  print('Can read:    ${(permissions & READ) != 0}');
  print('Can write:   ${(permissions & WRITE) != 0}');
  print('Can execute: ${(permissions & EXECUTE) != 0}');
}
```

This example demonstrates fundamental bitwise operations in Dart. These  
operations work on the binary representation of integers, making them  
extremely fast. AND is used for masking (extracting specific bits), OR  
for combining flags, XOR for toggling and simple encryption, and shifts  
for efficient multiplication/division by powers of 2.  

The flags example shows a common use case: representing multiple boolean  
states efficiently in a single integer. This technique is widely used in  
file permissions, configuration options, and protocol headers.  

### Bit Manipulation Functions and Utilities

Common bit manipulation tasks can be encapsulated in reusable functions  
for cleaner code and better maintainability.  

```dart
void main() {
  print('Bit manipulation utilities:');
  
  var value = 0b10110100; // 180
  print('Original value: ${value.toRadixString(2).padLeft(8, '0')} ($value)');
  
  // Set specific bit
  var withBit1Set = setBit(value, 1);
  print('Set bit 1:      ${withBit1Set.toRadixString(2).padLeft(8, '0')}');
  
  // Clear specific bit
  var withBit5Clear = clearBit(value, 5);
  print('Clear bit 5:    ${withBit5Clear.toRadixString(2).padLeft(8, '0')}');
  
  // Toggle specific bit
  var withBit7Toggle = toggleBit(value, 7);
  print('Toggle bit 7:   ${withBit7Toggle.toRadixString(2).padLeft(8, '0')}');
  
  // Test if bit is set
  print('\nBit testing:');
  for (var i = 0; i < 8; i++) {
    print('Bit $i is ${isBitSet(value, i) ? 'SET' : 'CLEAR'}');
  }
  
  // Get bit range
  print('\nBit ranges:');
  var bits2to5 = getBitRange(value, 2, 5);
  print('Bits 2-5: ${bits2to5.toRadixString(2).padLeft(4, '0')} ($bits2to5)');
  
  // Set bit range
  var withRangeSet = setBitRange(value, 0, 3, 0b1111);
  print('Set bits 0-3 to 1111: ${withRangeSet.toRadixString(2).padLeft(8, '0')}');
  
  // Count bits
  print('\nBit counting:');
  print('Set bits in $value: ${countSetBits(value)}');
  print('Leading zeros: ${countLeadingZeros(value, 8)}');
  print('Trailing zeros: ${countTrailingZeros(value)}');
}

int setBit(int value, int bit) => value | (1 << bit);

int clearBit(int value, int bit) => value & ~(1 << bit);

int toggleBit(int value, int bit) => value ^ (1 << bit);

bool isBitSet(int value, int bit) => (value & (1 << bit)) != 0;

int getBitRange(int value, int start, int end) {
  var mask = ((1 << (end - start + 1)) - 1);
  return (value >> start) & mask;
}

int setBitRange(int value, int start, int end, int newValue) {
  var mask = ((1 << (end - start + 1)) - 1) << start;
  return (value & ~mask) | ((newValue << start) & mask);
}

int countSetBits(int value) {
  var count = 0;
  while (value != 0) {
    count += value & 1;
    value >>= 1;
  }
  return count;
}

int countLeadingZeros(int value, int bitWidth) {
  if (value == 0) return bitWidth;
  var count = 0;
  var mask = 1 << (bitWidth - 1);
  while ((value & mask) == 0) {
    count++;
    mask >>= 1;
  }
  return count;
}

int countTrailingZeros(int value) {
  if (value == 0) return 32;
  var count = 0;
  while ((value & 1) == 0) {
    count++;
    value >>= 1;
  }
  return count;
}
```

This example provides a library of common bit manipulation functions.  
These utilities make bit-level operations more readable and maintainable.  
Setting, clearing, and toggling bits are fundamental operations in flag  
management and hardware control.  

Bit range operations allow working with multi-bit fields within integers,  
common in packed data structures and protocol headers. Bit counting functions  
are used in various algorithms including checksums, compression, and  
cryptography.  

### Bit Fields and Packed Data Structures

Packing multiple values into a single integer saves memory and is essential  
for binary protocols and efficient data storage.  

```dart
void main() {
  print('Packed data structures:');
  
  // Pack RGB color (8 bits per channel)
  var red = 0xAB;
  var green = 0xCD;
  var blue = 0xEF;
  
  var packedColor = packRGB(red, green, blue);
  print('Packed RGB: 0x${packedColor.toRadixString(16).toUpperCase().padLeft(6, '0')}');
  
  var unpacked = unpackRGB(packedColor);
  print('Unpacked: R=0x${unpacked[0].toRadixString(16)}, '
        'G=0x${unpacked[1].toRadixString(16)}, '
        'B=0x${unpacked[2].toRadixString(16)}');
  
  // Pack date (5 bits day, 4 bits month, 7 bits year offset)
  print('\nPacked date:');
  var day = 15;
  var month = 10;
  var yearOffset = 23; // Years since 2000
  
  var packedDate = packDate(day, month, yearOffset);
  print('Packed date: 0x${packedDate.toRadixString(16)} '
        '(${packedDate.toRadixString(2).padLeft(16, '0')})');
  
  var dateFields = unpackDate(packedDate);
  print('Unpacked: Day=${dateFields[0]}, Month=${dateFields[1]}, '
        'Year=${2000 + dateFields[2]}');
  
  // Pack network packet header
  print('\nNetwork packet header:');
  var version = 4;    // 4 bits
  var type = 2;       // 4 bits
  var flags = 5;      // 8 bits
  var length = 1024;  // 16 bits
  
  var header = packHeader(version, type, flags, length);
  print('Packed header: 0x${header.toRadixString(16).toUpperCase()}');
  
  var headerFields = unpackHeader(header);
  print('Unpacked: Version=${headerFields[0]}, Type=${headerFields[1]}, '
        'Flags=${headerFields[2]}, Length=${headerFields[3]}');
}

int packRGB(int r, int g, int b) {
  return ((r & 0xFF) << 16) | ((g & 0xFF) << 8) | (b & 0xFF);
}

List<int> unpackRGB(int packed) {
  var r = (packed >> 16) & 0xFF;
  var g = (packed >> 8) & 0xFF;
  var b = packed & 0xFF;
  return [r, g, b];
}

int packDate(int day, int month, int year) {
  return ((year & 0x7F) << 9) | ((month & 0x0F) << 5) | (day & 0x1F);
}

List<int> unpackDate(int packed) {
  var day = packed & 0x1F;
  var month = (packed >> 5) & 0x0F;
  var year = (packed >> 9) & 0x7F;
  return [day, month, year];
}

int packHeader(int version, int type, int flags, int length) {
  return ((version & 0x0F) << 28) | ((type & 0x0F) << 24) |
         ((flags & 0xFF) << 16) | (length & 0xFFFF);
}

List<int> unpackHeader(int packed) {
  var version = (packed >> 28) & 0x0F;
  var type = (packed >> 24) & 0x0F;
  var flags = (packed >> 16) & 0xFF;
  var length = packed & 0xFFFF;
  return [version, type, flags, length];
}
```

This example demonstrates packing multiple values into single integers,  
a technique called bit fields or packed data structures. This is memory-  
efficient and commonly used in binary file formats, network protocols,  
and hardware interfaces.  

The RGB color packing shows a simple 24-bit color representation. Date  
packing demonstrates fitting a date into 16 bits. The network packet  
header shows how protocol headers often pack multiple fields into fixed-  
width integers for efficient transmission and parsing.  

### Bitwise Algorithms and Tricks

Advanced bitwise techniques enable efficient solutions to common problems  
without using traditional arithmetic or loops.  

```dart
void main() {
  print('Bitwise algorithms and tricks:');
  
  // Check if power of 2
  print('\nPower of 2 check:');
  for (var n in [1, 2, 3, 4, 8, 15, 16, 31, 32]) {
    print('$n is ${isPowerOfTwo(n) ? '' : 'not '}a power of 2');
  }
  
  // Next power of 2
  print('\nNext power of 2:');
  for (var n in [5, 17, 33, 100]) {
    print('Next power of 2 after $n: ${nextPowerOfTwo(n)}');
  }
  
  // Swap without temporary variable
  print('\nSwap using XOR:');
  var x = 42;
  var y = 17;
  print('Before: x=$x, y=$y');
  var result = swapXOR(x, y);
  print('After:  x=${result[0]}, y=${result[1]}');
  
  // Reverse bits
  print('\nReverse bits:');
  var value = 0b10110100;
  print('Original: ${value.toRadixString(2).padLeft(8, '0')}');
  var reversed = reverseBits8(value);
  print('Reversed: ${reversed.toRadixString(2).padLeft(8, '0')}');
  
  // Parity (even/odd number of set bits)
  print('\nParity check:');
  for (var n in [0b1010, 0b1011, 0b1111]) {
    var bits = n.toRadixString(2).padLeft(8, '0');
    print('$bits has ${parity(n) ? 'odd' : 'even'} parity');
  }
  
  // Absolute value without branching
  print('\nAbsolute value (branchless):');
  for (var n in [-5, -1, 0, 1, 5]) {
    print('abs($n) = ${absoluteValue(n)}');
  }
}

bool isPowerOfTwo(int n) {
  return n > 0 && (n & (n - 1)) == 0;
}

int nextPowerOfTwo(int n) {
  n--;
  n |= n >> 1;
  n |= n >> 2;
  n |= n >> 4;
  n |= n >> 8;
  n |= n >> 16;
  return n + 1;
}

List<int> swapXOR(int a, int b) {
  a = a ^ b;
  b = a ^ b;
  a = a ^ b;
  return [a, b];
}

int reverseBits8(int n) {
  var result = 0;
  for (var i = 0; i < 8; i++) {
    result = (result << 1) | (n & 1);
    n >>= 1;
  }
  return result;
}

bool parity(int n) {
  var count = 0;
  while (n != 0) {
    count ^= n & 1;
    n >>= 1;
  }
  return count == 1;
}

int absoluteValue(int n) {
  var mask = n >> 31;
  return (n ^ mask) - mask;
}
```

This example showcases clever bitwise algorithms that are both efficient  
and elegant. Checking if a number is a power of 2 uses the property that  
powers of 2 have exactly one bit set, making (n & (n-1)) equal to zero.  

The XOR swap is a classic trick that swaps values without a temporary  
variable. Bit reversal is useful in FFT algorithms and graphics. The  
branchless absolute value avoids conditional jumps, which can be faster  
on modern CPUs with deep pipelines.  

### Bit Vectors and Sets

Bit vectors efficiently represent sets of integers, useful for flags,  
presence tracking, and implementing compact data structures.  

```dart
import 'dart:typed_data';

void main() {
  print('Bit vector implementation:');
  
  var bitSet = BitSet(100);
  
  // Add elements
  bitSet.add(5);
  bitSet.add(10);
  bitSet.add(15);
  bitSet.add(50);
  bitSet.add(99);
  
  print('Added elements: 5, 10, 15, 50, 99');
  
  // Check membership
  print('\nMembership tests:');
  for (var n in [5, 7, 15, 50, 100]) {
    print('Contains $n: ${bitSet.contains(n)}');
  }
  
  // Remove element
  bitSet.remove(10);
  print('\nAfter removing 10:');
  print('Contains 10: ${bitSet.contains(10)}');
  
  // Count elements
  print('Total elements: ${bitSet.cardinality()}');
  
  // Set operations
  print('\nSet operations:');
  var setA = BitSet(20);
  var setB = BitSet(20);
  
  setA.addRange([1, 2, 3, 5, 8]);
  setB.addRange([2, 3, 4, 5, 6]);
  
  print('Set A: ${setA.toList()}');
  print('Set B: ${setB.toList()}');
  
  var union = setA.union(setB);
  print('Union: ${union.toList()}');
  
  var intersection = setA.intersection(setB);
  print('Intersection: ${intersection.toList()}');
  
  var difference = setA.difference(setB);
  print('Difference (A-B): ${difference.toList()}');
}

class BitSet {
  final Uint32List _bits;
  final int _size;
  
  BitSet(int size)
      : _size = size,
        _bits = Uint32List((size + 31) ~/ 32);
  
  void add(int n) {
    if (n < 0 || n >= _size) return;
    var index = n ~/ 32;
    var bit = n % 32;
    _bits[index] |= (1 << bit);
  }
  
  void addRange(List<int> numbers) {
    for (var n in numbers) {
      add(n);
    }
  }
  
  bool contains(int n) {
    if (n < 0 || n >= _size) return false;
    var index = n ~/ 32;
    var bit = n % 32;
    return (_bits[index] & (1 << bit)) != 0;
  }
  
  void remove(int n) {
    if (n < 0 || n >= _size) return;
    var index = n ~/ 32;
    var bit = n % 32;
    _bits[index] &= ~(1 << bit);
  }
  
  int cardinality() {
    var count = 0;
    for (var word in _bits) {
      var w = word;
      while (w != 0) {
        count += w & 1;
        w >>= 1;
      }
    }
    return count;
  }
  
  List<int> toList() {
    var result = <int>[];
    for (var i = 0; i < _size; i++) {
      if (contains(i)) result.add(i);
    }
    return result;
  }
  
  BitSet union(BitSet other) {
    var result = BitSet(_size);
    for (var i = 0; i < _bits.length; i++) {
      result._bits[i] = _bits[i] | other._bits[i];
    }
    return result;
  }
  
  BitSet intersection(BitSet other) {
    var result = BitSet(_size);
    for (var i = 0; i < _bits.length; i++) {
      result._bits[i] = _bits[i] & other._bits[i];
    }
    return result;
  }
  
  BitSet difference(BitSet other) {
    var result = BitSet(_size);
    for (var i = 0; i < _bits.length; i++) {
      result._bits[i] = _bits[i] & ~other._bits[i];
    }
    return result;
  }
}
```

This example implements a bit vector (bit set) data structure that efficiently  
stores sets of integers. Each integer is represented by a single bit, making  
this extremely memory-efficient for dense sets or when the universe of  
possible values is relatively small.  

Bit sets support standard set operations (union, intersection, difference)  
using bitwise operations, making them very fast. This data structure is  
commonly used in compilers, databases, and algorithms that need to track  
presence or absence of items efficiently.  

### Advanced Bit Manipulation Patterns

Complex bit manipulation patterns solve sophisticated problems in  
compression, error detection, and cryptography.  

```dart
import 'dart:typed_data';

void main() {
  // Gray code conversion
  print('Gray code:');
  print('Binary -> Gray -> Binary');
  for (var i = 0; i < 8; i++) {
    var gray = binaryToGray(i);
    var binary = grayToBinary(gray);
    print('${i.toRadixString(2).padLeft(3, '0')} -> '
          '${gray.toRadixString(2).padLeft(3, '0')} -> '
          '${binary.toRadixString(2).padLeft(3, '0')}');
  }
  
  // Hamming weight (population count) variations
  print('\nHamming weight algorithms:');
  var testValues = [0xFF, 0xAA, 0x55, 0x0F];
  for (var val in testValues) {
    var naive = hammingWeightNaive(val);
    var kernighan = hammingWeightKernighan(val);
    var parallel = hammingWeightParallel(val);
    print('0x${val.toRadixString(16).padLeft(2, '0')}: '
          'naive=$naive, kernighan=$kernighan, parallel=$parallel');
  }
  
  // Morton code (Z-order curve)
  print('\nMorton code (2D -> 1D):');
  var coordinates = [[0, 0], [1, 0], [0, 1], [1, 1], [2, 3]];
  for (var coord in coordinates) {
    var morton = mortonEncode(coord[0], coord[1]);
    var decoded = mortonDecode(morton);
    print('(${coord[0]}, ${coord[1]}) -> $morton -> '
          '(${decoded[0]}, ${decoded[1]})');
  }
  
  // Bit rotation
  print('\nBit rotation:');
  var value = 0b10110100;
  print('Original:      ${value.toRadixString(2).padLeft(8, '0')}');
  print('Rotate left 3: ${rotateLeft8(value, 3).toRadixString(2).padLeft(8, '0')}');
  print('Rotate right 3: ${rotateRight8(value, 3).toRadixString(2).padLeft(8, '0')}');
}

int binaryToGray(int binary) => binary ^ (binary >> 1);

int grayToBinary(int gray) {
  var binary = gray;
  while (gray >>= 1) {
    binary ^= gray;
  }
  return binary;
}

int hammingWeightNaive(int n) {
  var count = 0;
  for (var i = 0; i < 8; i++) {
    if ((n & (1 << i)) != 0) count++;
  }
  return count;
}

int hammingWeightKernighan(int n) {
  var count = 0;
  while (n != 0) {
    n &= n - 1; // Clear lowest set bit
    count++;
  }
  return count;
}

int hammingWeightParallel(int n) {
  // Parallel bit counting algorithm
  n = n - ((n >> 1) & 0x55);
  n = (n & 0x33) + ((n >> 2) & 0x33);
  n = (n + (n >> 4)) & 0x0F;
  return n;
}

int mortonEncode(int x, int y) {
  var result = 0;
  for (var i = 0; i < 16; i++) {
    result |= ((x & (1 << i)) << i) | ((y & (1 << i)) << (i + 1));
  }
  return result;
}

List<int> mortonDecode(int morton) {
  var x = 0;
  var y = 0;
  for (var i = 0; i < 16; i++) {
    x |= ((morton & (1 << (2 * i))) >> i);
    y |= ((morton & (1 << (2 * i + 1))) >> (i + 1));
  }
  return [x, y];
}

int rotateLeft8(int value, int count) {
  count %= 8;
  return ((value << count) | (value >> (8 - count))) & 0xFF;
}

int rotateRight8(int value, int count) {
  count %= 8;
  return ((value >> count) | (value << (8 - count))) & 0xFF;
}
```

This example demonstrates advanced bit manipulation patterns. Gray code  
is useful in error correction and analog-to-digital conversion as adjacent  
values differ by only one bit. Hamming weight (population count) has multiple  
algorithms with different trade-offs between simplicity and speed.  

Morton codes map multi-dimensional coordinates to one dimension while  
preserving locality, useful in spatial indexing and quad-trees. Bit rotation  
is used in cryptography and hash functions. These patterns showcase the  
power of bitwise operations for solving complex problems efficiently.  

## Encoding and Decoding

### Base64 Encoding and Decoding

Base64 encoding converts binary data to ASCII text, making it safe for  
transmission over text-based protocols and storage in text files.  

```dart
import 'dart:convert';
import 'dart:typed_data';

void main() {
  print('Base64 Encoding and Decoding:');
  
  // Original data
  var data = Uint8List.fromList(
    'Hello there! This is a test message for Base64 encoding.'.codeUnits
  );
  print('Original data: ${String.fromCharCodes(data)}');
  print('Original bytes: $data');
  print('Length: ${data.length} bytes');
  
  // Standard Base64 encoding
  print('\n1. Standard Base64:');
  var encoded = base64.encode(data);
  print('Encoded: $encoded');
  print('Encoded length: ${encoded.length} characters');
  
  // Decode back
  var decoded = base64.decode(encoded);
  print('Decoded: ${String.fromCharCodes(decoded)}');
  print('Data integrity: ${String.fromCharCodes(data) == String.fromCharCodes(decoded)}');
  
  // URL-safe Base64 encoding
  print('\n2. URL-safe Base64:');
  var urlEncoded = base64Url.encode(data);
  print('URL encoded: $urlEncoded');
  
  var urlDecoded = base64Url.decode(urlEncoded);
  print('URL decoded: ${String.fromCharCodes(urlDecoded)}');
  
  // Binary data encoding
  print('\n3. Binary data:');
  var binaryData = Uint8List.fromList([0xFF, 0x00, 0xAB, 0xCD, 0x12, 0x34]);
  var binaryEncoded = base64.encode(binaryData);
  print('Binary data: $binaryData');
  print('Encoded: $binaryEncoded');
  
  var binaryDecoded = base64.decode(binaryEncoded);
  print('Decoded: $binaryDecoded');
  
  // Efficiency comparison
  print('\n4. Encoding efficiency:');
  var testData = Uint8List(1000);
  for (var i = 0; i < testData.length; i++) {
    testData[i] = i & 0xFF;
  }
  
  var encodedTest = base64.encode(testData);
  var overhead = ((encodedTest.length - testData.length) / testData.length * 100);
  print('Original size: ${testData.length} bytes');
  print('Encoded size: ${encodedTest.length} characters');
  print('Overhead: ${overhead.toStringAsFixed(1)}%');
}
```

This example demonstrates Base64 encoding using Dart's built-in convert  
library. Base64 is widely used for embedding binary data in JSON, XML,  
email, and URLs. The standard encoding uses characters A-Z, a-z, 0-9, +,  
and /, while URL-safe encoding replaces + and / with - and _.  

Base64 encoding increases data size by approximately 33% due to converting  
6-bit chunks to 8-bit characters. Despite this overhead, it's essential  
for protocols that only support text or have restrictions on certain bytes.  

### Hexadecimal Encoding and Decoding

Hexadecimal encoding represents bytes as readable hex strings, useful for  
debugging, displaying binary data, and working with low-level formats.  

```dart
import 'dart:typed_data';

void main() {
  print('Hexadecimal Encoding and Decoding:');
  
  // Encode to hex
  var data = Uint8List.fromList('Hello there!'.codeUnits);
  print('Original: ${String.fromCharCodes(data)}');
  
  var hexString = bytesToHex(data);
  print('Hex encoding: $hexString');
  
  // Decode from hex
  var decoded = hexToBytes(hexString);
  print('Decoded: ${String.fromCharCodes(decoded)}');
  
  // Binary data
  print('\nBinary data encoding:');
  var binaryData = Uint8List.fromList([0x00, 0xFF, 0xAB, 0xCD, 0x12, 0x34]);
  print('Binary data: $binaryData');
  print('Hex: ${bytesToHex(binaryData)}');
  
  // Formatted hex dump
  print('\nHex dump format:');
  var largeData = Uint8List(48);
  for (var i = 0; i < largeData.length; i++) {
    largeData[i] = i;
  }
  
  hexDump(largeData);
  
  // Uppercase vs lowercase
  print('\nCase variations:');
  var testData = Uint8List.fromList([0xAB, 0xCD, 0xEF]);
  print('Lowercase: ${bytesToHex(testData)}');
  print('Uppercase: ${bytesToHex(testData, uppercase: true)}');
}

String bytesToHex(Uint8List bytes, {bool uppercase = false}) {
  var hex = bytes.map((b) => b.toRadixString(16).padLeft(2, '0')).join('');
  return uppercase ? hex.toUpperCase() : hex;
}

Uint8List hexToBytes(String hex) {
  var bytes = <int>[];
  for (var i = 0; i < hex.length; i += 2) {
    var byteStr = hex.substring(i, i + 2);
    bytes.add(int.parse(byteStr, radix: 16));
  }
  return Uint8List.fromList(bytes);
}

void hexDump(Uint8List data, {int bytesPerLine = 16}) {
  for (var offset = 0; offset < data.length; offset += bytesPerLine) {
    var end = (offset + bytesPerLine < data.length)
        ? offset + bytesPerLine
        : data.length;
    
    var line = data.sublist(offset, end);
    var hexPart = line.map((b) => b.toRadixString(16).padLeft(2, '0')).join(' ');
    var asciiPart = line.map((b) => (b >= 32 && b <= 126) 
        ? String.fromCharCode(b) 
        : '.').join('');
    
    print('${offset.toRadixString(16).padLeft(4, '0')}: '
          '${hexPart.padRight(bytesPerLine * 3 - 1)} |$asciiPart|');
  }
}
```

This example demonstrates hexadecimal encoding, which is widely used for  
displaying binary data in a human-readable format. Each byte is represented  
by two hex digits (0-9, A-F), making it easy to see exact byte values.  

The hex dump format is particularly useful for debugging binary files and  
network protocols. It shows both hex values and ASCII representation,  
making it easy to spot text strings within binary data. This format is  
similar to tools like hexdump and xxd.  

### ASCII and Binary Conversion

Converting between ASCII text and binary representations is fundamental  
for understanding data encoding and debugging protocols.  

```dart
import 'dart:typed_data';

void main() {
  print('ASCII and Binary Conversion:');
  
  // Test string
  var text = 'Hello there! 123';
  print('Original text: $text');
  
  // ASCII to binary conversion
  print('\n1. ASCII to binary conversion:');
  
  print('Character breakdown:');
  for (var i = 0; i < text.length; i++) {
    var char = text[i];
    var code = char.codeUnitAt(0);
    print('  [$i] \'$char\' -> ASCII $code -> Binary ${code.toRadixString(2).padLeft(8, '0')}');
  }
  
  // Convert entire string to binary representation
  var binaryString = stringToBinary(text);
  print('\nFull binary: $binaryString');
  
  // Convert back from binary
  var reconstructed = binaryToString(binaryString);
  print('Reconstructed: $reconstructed');
  
  // Binary arithmetic representation
  print('\n2. Numeric binary representation:');
  var number = 42;
  print('Number: $number');
  print('Binary: ${number.toRadixString(2)}');
  print('8-bit:  ${number.toRadixString(2).padLeft(8, '0')}');
  print('16-bit: ${number.toRadixString(2).padLeft(16, '0')}');
  
  // Bytes as binary strings
  print('\n3. Byte array as binary:');
  var bytes = Uint8List.fromList([72, 101, 108, 108, 111]); // "Hello"
  for (var byte in bytes) {
    print('${byte.toRadixString(2).padLeft(8, '0')} = $byte = ${String.fromCharCode(byte)}');
  }
}

String stringToBinary(String text) {
  return text.codeUnits
      .map((code) => code.toRadixString(2).padLeft(8, '0'))
      .join(' ');
}

String binaryToString(String binary) {
  var parts = binary.split(' ');
  var codes = parts.map((b) => int.parse(b, radix: 2));
  return String.fromCharCodes(codes);
}
```

This example demonstrates conversions between ASCII text and binary  
representation. Understanding these conversions is essential for working  
with text encoding, network protocols, and binary file formats.  

The character breakdown shows how each character maps to an ASCII code  
and its binary representation. This is useful for debugging text encoding  
issues and understanding how text is stored in binary form.  

### Custom Binary Format Design

Designing custom binary formats enables efficient data storage and  
transmission for specific applications.  

```dart
import 'dart:typed_data';
import 'dart:io';

void main() async {
  print('Custom Binary Format Design:');
  
  // Define a simple record format
  var records = [
    Record(id: 1, name: 'Alice', age: 30, score: 95.5),
    Record(id: 2, name: 'Bob', age: 25, score: 87.3),
    Record(id: 3, name: 'Charlie', age: 35, score: 92.8),
  ];
  
  // Write records to file
  print('Writing records to binary file:');
  var file = File('records.dat');
  var buffer = BytesBuilder();
  
  // File header
  buffer.addByte(0x52); // 'R'
  buffer.addByte(0x45); // 'E'
  buffer.addByte(0x43); // 'C'
  buffer.addByte(0x00); // Version
  
  var headerData = ByteData(4);
  headerData.setUint32(0, records.length);
  buffer.add(headerData.buffer.asUint8List());
  
  // Write records
  for (var record in records) {
    buffer.add(record.toBytes());
  }
  
  await file.writeAsBytes(buffer.toBytes());
  print('Wrote ${records.length} records');
  
  // Read records back
  print('\nReading records from binary file:');
  var fileData = await file.readAsBytes();
  var reader = ByteData.view(fileData.buffer);
  
  // Verify header
  if (fileData[0] != 0x52 || fileData[1] != 0x45 || fileData[2] != 0x43) {
    print('Invalid file format!');
    return;
  }
  
  var version = fileData[3];
  var recordCount = reader.getUint32(4);
  
  print('File version: $version');
  print('Record count: $recordCount');
  
  // Read records
  var offset = 8;
  var readRecords = <Record>[];
  
  for (var i = 0; i < recordCount; i++) {
    var record = Record.fromBytes(fileData, offset);
    readRecords.add(record);
    offset += Record.recordSize;
  }
  
  print('\nRead records:');
  for (var record in readRecords) {
    print('  ${record.toString()}');
  }
  
  // Cleanup
  await file.delete();
}

class Record {
  final int id;
  final String name;
  final int age;
  final double score;
  
  static const int recordSize = 4 + 32 + 4 + 8; // 48 bytes
  
  Record({required this.id, required this.name, required this.age, required this.score});
  
  Uint8List toBytes() {
    var buffer = ByteData(recordSize);
    
    // ID (4 bytes)
    buffer.setUint32(0, id);
    
    // Name (32 bytes, null-padded)
    var nameBytes = name.codeUnits;
    for (var i = 0; i < 32; i++) {
      buffer.setUint8(4 + i, i < nameBytes.length ? nameBytes[i] : 0);
    }
    
    // Age (4 bytes)
    buffer.setUint32(36, age);
    
    // Score (8 bytes, double)
    buffer.setFloat64(40, score);
    
    return buffer.buffer.asUint8List();
  }
  
  factory Record.fromBytes(Uint8List bytes, int offset) {
    var buffer = ByteData.view(bytes.buffer, offset, recordSize);
    
    var id = buffer.getUint32(0);
    
    // Read name (null-terminated)
    var nameBytes = <int>[];
    for (var i = 0; i < 32; i++) {
      var byte = buffer.getUint8(4 + i);
      if (byte == 0) break;
      nameBytes.add(byte);
    }
    var name = String.fromCharCodes(nameBytes);
    
    var age = buffer.getUint32(36);
    var score = buffer.getFloat64(40);
    
    return Record(id: id, name: name, age: age, score: score);
  }
  
  @override
  String toString() => 'Record(id: $id, name: $name, age: $age, score: $score)';
}
```

This example demonstrates designing a custom binary format for storing  
structured data. The format includes a file header with magic bytes for  
identification, version number, and record count. Each record has a fixed  
size, making random access efficient.  

Custom binary formats offer several advantages: compact storage, fast  
parsing, platform independence (with careful endianness handling), and  
the ability to design exactly what you need. This approach is used in  
databases, game save files, and network protocols.  

## Buffer Operations and Management

### Dynamic Buffer Building

Building binary data dynamically requires efficient buffer management  
to avoid excessive memory allocations and copies.  

```dart
import 'dart:typed_data';

void main() {
  print('Dynamic buffer building:');
  
  // Using BytesBuilder
  var builder = BytesBuilder();
  
  print('1. Building with BytesBuilder:');
  builder.addByte(0x48); // H
  builder.addByte(0x65); // e
  builder.add([0x6C, 0x6C, 0x6F]); // llo
  builder.addByte(0x20); // space
  builder.add('there!'.codeUnits);
  
  var result1 = builder.toBytes();
  print('Built: ${String.fromCharCodes(result1)}');
  print('Length: ${result1.length} bytes');
  
  // Building structured data
  print('\n2. Building structured data:');
  var structBuilder = BytesBuilder();
  
  // Header
  var header = ByteData(8);
  header.setUint32(0, 0x12345678);
  header.setUint32(4, 42);
  structBuilder.add(header.buffer.asUint8List());
  
  // Payload
  structBuilder.add('Hello there!'.codeUnits);
  
  // Footer
  structBuilder.addByte(0xFF);
  structBuilder.addByte(0xFE);
  
  var result2 = structBuilder.toBytes();
  print('Total size: ${result2.length} bytes');
  print('Header: ${result2.sublist(0, 8)}');
  print('Payload: ${String.fromCharCodes(result2.sublist(8, 20))}');
  print('Footer: ${result2.sublist(20)}');
  
  // Copy-on-write behavior
  print('\n3. Copy-on-write:');
  var builder3 = BytesBuilder(copy: false); // Don't copy, reuse buffers
  builder3.add([1, 2, 3]);
  builder3.add([4, 5, 6]);
  
  var result3 = builder3.toBytes();
  print('Built (no-copy): $result3');
  
  // Performance comparison
  print('\n4. Performance comparison:');
  var iterations = 10000;
  
  var sw = Stopwatch()..start();
  for (var i = 0; i < iterations; i++) {
    var b = BytesBuilder();
    for (var j = 0; j < 10; j++) {
      b.addByte(j);
    }
    b.toBytes();
  }
  sw.stop();
  print('BytesBuilder: ${sw.elapsedMilliseconds}ms');
  
  sw.reset();
  sw.start();
  for (var i = 0; i < iterations; i++) {
    var list = <int>[];
    for (var j = 0; j < 10; j++) {
      list.add(j);
    }
    Uint8List.fromList(list);
  }
  sw.stop();
  print('List approach: ${sw.elapsedMilliseconds}ms');
}
```

This example demonstrates efficient buffer building using BytesBuilder,  
which manages memory allocation and growth automatically. BytesBuilder is  
more efficient than repeatedly concatenating lists or creating new arrays.  

The copy parameter controls whether BytesBuilder makes copies of added  
data. Setting it to false can improve performance when you know the source  
data won't be modified, but requires careful memory management.  

### Stream-based Buffer Processing

Processing data in streams prevents memory overflow when dealing with  
large datasets or continuous data flows.  

```dart
import 'dart:async';
import 'dart:typed_data';

void main() async {
  print('Stream-based buffer processing:');
  
  // Create a data stream
  var controller = StreamController<List<int>>();
  
  // Process stream with transformation
  print('1. Stream transformation:');
  var transformedStream = controller.stream.map((chunk) {
    // Transform each chunk (example: increment each byte)
    return Uint8List.fromList(chunk.map((b) => (b + 1) & 0xFF).toList());
  });
  
  var processedData = <int>[];
  transformedStream.listen((chunk) {
    processedData.addAll(chunk);
  });
  
  // Feed data into stream
  controller.add([1, 2, 3]);
  controller.add([4, 5, 6]);
  controller.add([7, 8, 9]);
  await controller.close();
  
  await Future.delayed(Duration(milliseconds: 100));
  print('Processed: $processedData');
  
  // Buffering stream
  print('\n2. Buffering stream:');
  await demonstrateBuffering();
  
  // Backpressure handling
  print('\n3. Backpressure handling:');
  await demonstrateBackpressure();
}

Future<void> demonstrateBuffering() async {
  var stream = Stream.fromIterable([
    [1, 2],
    [3, 4],
    [5, 6],
    [7, 8],
    [9, 10],
  ]);
  
  var buffered = <int>[];
  const bufferSize = 4;
  
  await for (var chunk in stream) {
    buffered.addAll(chunk);
    
    while (buffered.length >= bufferSize) {
      var batch = buffered.sublist(0, bufferSize);
      print('Processing buffer: $batch');
      buffered.removeRange(0, bufferSize);
    }
  }
  
  if (buffered.isNotEmpty) {
    print('Processing remaining: $buffered');
  }
}

Future<void> demonstrateBackpressure() async {
  var controller = StreamController<List<int>>();
  
  // Slow consumer
  controller.stream.listen((chunk) async {
    print('Received chunk: $chunk');
    await Future.delayed(Duration(milliseconds: 100));
    print('Processed chunk: $chunk');
  });
  
  // Fast producer
  for (var i = 0; i < 5; i++) {
    controller.add([i, i + 1]);
    print('Sent chunk ${i}');
    await Future.delayed(Duration(milliseconds: 50));
  }
  
  await controller.close();
  await Future.delayed(Duration(milliseconds: 600));
}
```

This example demonstrates stream-based processing, which is essential for  
handling large or continuous data flows. Streams provide automatic memory  
management and backpressure handling, preventing the producer from  
overwhelming the consumer.  

Buffering allows accumulating data until you have enough to process  
efficiently. Backpressure ensures that fast producers don't cause memory  
issues with slow consumers, automatically pausing data flow when needed.  

## Endianness Handling

### Understanding and Converting Endianness

Endianness determines byte order in multi-byte values, critical for  
cross-platform compatibility and network protocols.  

```dart
import 'dart:typed_data';

void main() {
  print('Endianness Handling:');
  
  // Detect system endianness
  print('1. System endianness:');
  var buffer = ByteData(4);
  buffer.setUint32(0, 0x12345678, Endian.host);
  var bytes = buffer.buffer.asUint8List();
  
  if (bytes[0] == 0x12) {
    print('System is Big Endian');
  } else {
    print('System is Little Endian');
  }
  print('Bytes: ${bytes.map((b) => '0x${b.toRadixString(16).padLeft(2, '0')}').join(' ')}');
  
  // Writing with different endianness
  print('\n2. Writing different endianness:');
  var value = 0x12345678;
  
  var bigEndian = ByteData(4);
  bigEndian.setUint32(0, value, Endian.big);
  print('Big Endian:    ${bigEndian.buffer.asUint8List()}');
  
  var littleEndian = ByteData(4);
  littleEndian.setUint32(0, value, Endian.little);
  print('Little Endian: ${littleEndian.buffer.asUint8List()}');
  
  // Reading with different endianness
  print('\n3. Reading different endianness:');
  var testBytes = Uint8List.fromList([0x12, 0x34, 0x56, 0x78]);
  var reader = ByteData.view(testBytes.buffer);
  
  print('Bytes: $testBytes');
  print('As Big Endian:    0x${reader.getUint32(0, Endian.big).toRadixString(16)}');
  print('As Little Endian: 0x${reader.getUint32(0, Endian.little).toRadixString(16)}');
  
  // Manual byte swapping
  print('\n4. Manual byte swapping:');
  var original = 0x12345678;
  var swapped = swap32(original);
  print('Original: 0x${original.toRadixString(16)}');
  print('Swapped:  0x${swapped.toRadixString(16)}');
}

int swap32(int value) {
  return ((value & 0xFF) << 24) |
         ((value & 0xFF00) << 8) |
         ((value & 0xFF0000) >> 8) |
         ((value & 0xFF000000) >> 24);
}

int swap16(int value) {
  return ((value & 0xFF) << 8) | ((value & 0xFF00) >> 8);
}
```

This example demonstrates endianness handling in Dart. ByteData provides  
built-in support for reading and writing data in specific byte orders,  
which is essential for network protocols (typically big-endian) and file  
formats that specify endianness.  

Understanding endianness prevents data corruption when exchanging binary  
data between different systems. Network byte order is always big-endian,  
while most modern CPUs use little-endian for internal representation.  

## Advanced Binary Manipulation

### Parsing Complex Binary Structures

Real-world binary formats often have nested structures, variable-length  
fields, and complex layouts requiring careful parsing.  

```dart
import 'dart:typed_data';
import 'dart:io';

void main() async {
  print('Parsing Complex Binary Structures:');
  
  // Create a mock binary file with nested structure
  await createComplexFile();
  
  // Parse the file
  var file = File('complex.bin');
  var data = await file.readAsBytes();
  
  var parser = BinaryParser(data);
  
  // Parse header
  var magic = parser.readUint32();
  var version = parser.readUint16();
  var flags = parser.readUint16();
  var sectionCount = parser.readUint32();
  
  print('Header:');
  print('  Magic: 0x${magic.toRadixString(16)}');
  print('  Version: $version');
  print('  Flags: $flags');
  print('  Sections: $sectionCount');
  
  // Parse sections
  print('\nSections:');
  for (var i = 0; i < sectionCount; i++) {
    var sectionType = parser.readUint8();
    var sectionSize = parser.readUint16();
    var sectionData = parser.readBytes(sectionSize);
    
    print('  Section $i: type=$sectionType, size=$sectionSize bytes');
    
    if (sectionType == 1) {
      // String section
      print('    Content: ${String.fromCharCodes(sectionData)}');
    } else if (sectionType == 2) {
      // Numeric section
      var nums = ByteData.view(sectionData.buffer);
      var count = sectionSize ~/ 4;
      print('    Numbers: ${List.generate(count, (i) => nums.getUint32(i * 4))}');
    }
  }
  
  await file.delete();
}

Future<void> createComplexFile() async {
  var builder = BytesBuilder();
  
  // Header
  var header = ByteData(12);
  header.setUint32(0, 0xCAFEBABE); // Magic
  header.setUint16(4, 1);           // Version
  header.setUint16(6, 0);           // Flags
  header.setUint32(8, 2);           // Section count
  builder.add(header.buffer.asUint8List());
  
  // Section 1: String
  builder.addByte(1); // Type
  var str = 'Hello there!';
  builder.addByte(str.length & 0xFF);
  builder.addByte((str.length >> 8) & 0xFF);
  builder.add(str.codeUnits);
  
  // Section 2: Numbers
  builder.addByte(2); // Type
  builder.addByte(12); // Size (3 * 4 bytes)
  builder.addByte(0);
  var nums = ByteData(12);
  nums.setUint32(0, 100);
  nums.setUint32(4, 200);
  nums.setUint32(8, 300);
  builder.add(nums.buffer.asUint8List());
  
  await File('complex.bin').writeAsBytes(builder.toBytes());
}

class BinaryParser {
  final Uint8List _data;
  int _offset = 0;
  
  BinaryParser(this._data);
  
  int get offset => _offset;
  int get remaining => _data.length - _offset;
  
  int readUint8() => _data[_offset++];
  
  int readUint16() {
    var value = (_data[_offset] << 8) | _data[_offset + 1];
    _offset += 2;
    return value;
  }
  
  int readUint32() {
    var value = (_data[_offset] << 24) |
                (_data[_offset + 1] << 16) |
                (_data[_offset + 2] << 8) |
                _data[_offset + 3];
    _offset += 4;
    return value;
  }
  
  Uint8List readBytes(int count) {
    var bytes = _data.sublist(_offset, _offset + count);
    _offset += count;
    return bytes;
  }
}
```

This example demonstrates parsing complex binary structures with nested  
sections and variable-length fields. The BinaryParser class encapsulates  
parsing logic, tracking position and providing type-safe read operations.  

This pattern is common in file format parsers, network protocol handlers,  
and binary data processors. Proper error handling and validation should  
be added for production code to handle malformed data gracefully.  

## Real-world Binary Data Scenarios

### Parsing File Headers

Many file formats start with headers containing metadata. Understanding  
header parsing is essential for working with real-world files.  

```dart
import 'dart:typed_data';
import 'dart:io';

void main() async {
  print('File Header Parsing:');
  
  // Create mock PNG header
  await createPNGMock();
  
  // Parse PNG header
  var pngFile = File('mock.png');
  var data = await pngFile.readAsBytes();
  
  print('PNG Header Analysis:');
  
  // PNG signature (8 bytes)
  var signature = data.sublist(0, 8);
  print('Signature: ${signature.map((b) => '0x${b.toRadixString(16).padLeft(2, '0')}').join(' ')}');
  
  var validSignature = [0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A];
  var isValid = true;
  for (var i = 0; i < 8; i++) {
    if (signature[i] != validSignature[i]) {
      isValid = false;
      break;
    }
  }
  print('Valid PNG: $isValid');
  
  // IHDR chunk (first chunk after signature)
  var reader = ByteData.view(data.buffer, 8);
  
  var chunkLength = reader.getUint32(0, Endian.big);
  var chunkType = String.fromCharCodes(data.sublist(12, 16));
  
  print('\nFirst Chunk:');
  print('  Length: $chunkLength');
  print('  Type: $chunkType');
  
  if (chunkType == 'IHDR') {
    var width = reader.getUint32(4, Endian.big);
    var height = reader.getUint32(8, Endian.big);
    var bitDepth = reader.getUint8(12);
    var colorType = reader.getUint8(13);
    
    print('  Width: $width');
    print('  Height: $height');
    print('  Bit Depth: $bitDepth');
    print('  Color Type: $colorType');
  }
  
  await pngFile.delete();
}

Future<void> createPNGMock() async {
  var builder = BytesBuilder();
  
  // PNG signature
  builder.add([0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A]);
  
  // IHDR chunk
  var ihdr = ByteData(25);
  ihdr.setUint32(0, 13, Endian.big); // Chunk length
  ihdr.setUint8(4, 73);  // 'I'
  ihdr.setUint8(5, 72);  // 'H'
  ihdr.setUint8(6, 68);  // 'D'
  ihdr.setUint8(7, 82);  // 'R'
  ihdr.setUint32(8, 800, Endian.big);  // Width
  ihdr.setUint32(12, 600, Endian.big); // Height
  ihdr.setUint8(16, 8);  // Bit depth
  ihdr.setUint8(17, 2);  // Color type (RGB)
  ihdr.setUint8(18, 0);  // Compression
  ihdr.setUint8(19, 0);  // Filter
  ihdr.setUint8(20, 0);  // Interlace
  ihdr.setUint32(21, 0, Endian.big); // CRC (simplified)
  
  builder.add(ihdr.buffer.asUint8List());
  
  await File('mock.png').writeAsBytes(builder.toBytes());
}
```

This example demonstrates parsing a PNG file header, one of the most  
common image formats. PNG uses a chunk-based format with big-endian  
byte order, making it a good example of real-world binary parsing.  

Understanding file headers enables format detection, metadata extraction,  
and validation. Similar patterns apply to JPEG, PDF, and many other formats.  

### Creating Network Protocol Packets

Network protocols rely on precise binary encoding for efficient and  
reliable communication between systems.  

```dart
import 'dart:typed_data';

void main() {
  print('Network Protocol Packet Creation:');
  
  // Create a simple packet
  var packet = createPacket(
    type: 1,
    sequence: 12345,
    payload: 'Hello there!'.codeUnits,
  );
  
  print('Created packet: ${packet.length} bytes');
  print('Hex: ${bytesToHex(packet)}');
  
  // Parse the packet
  print('\nParsing packet:');
  var parsed = parsePacket(packet);
  
  print('  Version: ${parsed['version']}');
  print('  Type: ${parsed['type']}');
  print('  Sequence: ${parsed['sequence']}');
  print('  Payload Length: ${parsed['payloadLength']}');
  print('  Payload: ${String.fromCharCodes(parsed['payload'])}');
  print('  Checksum: 0x${parsed['checksum'].toRadixString(16)}');
  
  // Verify checksum
  var isValid = verifyChecksum(packet);
  print('  Checksum Valid: $isValid');
}

Uint8List createPacket({required int type, required int sequence, required List<int> payload}) {
  var buffer = ByteData(12 + payload.length);
  
  // Header (12 bytes)
  buffer.setUint8(0, 1);           // Version
  buffer.setUint8(1, type);        // Type
  buffer.setUint16(2, 0);          // Flags
  buffer.setUint32(4, sequence);   // Sequence number
  buffer.setUint16(8, payload.length); // Payload length
  buffer.setUint16(10, 0);         // Checksum placeholder
  
  // Payload
  var fullPacket = Uint8List(12 + payload.length);
  fullPacket.setRange(0, 12, buffer.buffer.asUint8List());
  fullPacket.setRange(12, 12 + payload.length, payload);
  
  // Calculate and set checksum
  var checksum = calculateChecksum(fullPacket.sublist(0, 12 + payload.length));
  buffer.setUint16(10, checksum);
  fullPacket.setRange(0, 12, buffer.buffer.asUint8List());
  
  return fullPacket;
}

Map<String, dynamic> parsePacket(Uint8List packet) {
  var buffer = ByteData.view(packet.buffer);
  
  return {
    'version': buffer.getUint8(0),
    'type': buffer.getUint8(1),
    'flags': buffer.getUint16(2),
    'sequence': buffer.getUint32(4),
    'payloadLength': buffer.getUint16(8),
    'checksum': buffer.getUint16(10),
    'payload': packet.sublist(12),
  };
}

int calculateChecksum(Uint8List data) {
  var sum = 0;
  for (var i = 0; i < data.length; i++) {
    if (i != 10 && i != 11) { // Skip checksum field
      sum = (sum + data[i]) & 0xFFFF;
    }
  }
  return (~sum) & 0xFFFF;
}

bool verifyChecksum(Uint8List packet) {
  var buffer = ByteData.view(packet.buffer);
  var storedChecksum = buffer.getUint16(10);
  var calculatedChecksum = calculateChecksum(packet);
  return storedChecksum == calculatedChecksum;
}

String bytesToHex(Uint8List bytes) {
  return bytes.map((b) => b.toRadixString(16).padLeft(2, '0')).join(' ');
}
```

This example demonstrates creating and parsing network protocol packets,  
a fundamental skill for network programming. The example shows a simple  
packet format with header, payload, and checksum for error detection.  

Real network protocols like TCP/IP, UDP, and custom protocols follow  
similar patterns. Understanding these concepts enables implementing  
network communication, analyzing packet captures, and debugging network  
issues.  

## Summary

This comprehensive guide has covered detailed Dart code examples demonstrating  
various aspects of binary data manipulation, adapted from Go to Dart's  
idiomatic style and best practices.  

### Key Topics Covered

**Basic Operations (Examples 1-10):**  
- Creating and manipulating Uint8List objects  
- Memory-efficient operations and buffer reuse  
- List sorting and advanced patterns  
- Type conversions using ByteData  

**File I/O (Examples 11-20):**  
- Binary file reading and writing with various methods  
- Random access operations using RandomAccessFile  
- Stream-based processing for large files  
- Compression, concurrent processing, and format detection  

**Bitwise Operations (Examples 21-30):**  
- Basic bitwise arithmetic and bit manipulation  
- Bit fields and packed data structures  
- Advanced algorithms including Gray code and Morton encoding  
- Bit vectors for efficient set operations  

**Encoding/Decoding (Examples 31-40):**  
- Base64 encoding using dart:convert  
- Hexadecimal encoding and hex dump utilities  
- ASCII and binary conversions  
- Custom binary format design  

**Buffer Operations (Examples 41-50):**  
- Dynamic buffer building with BytesBuilder  
- Stream-based buffer processing  
- Backpressure handling in data flows  

**Endianness Handling (Examples 51-60):**  
- Understanding big-endian and little-endian byte order  
- Using ByteData for cross-platform compatibility  
- Manual byte swapping when needed  

**Advanced Manipulation (Examples 61-70):**  
- Parsing complex nested binary structures  
- Creating binary parsers with position tracking  
- Zero-copy techniques with buffer views  

**Real-world Scenarios (Examples 71-80):**  
- File format header parsing (PNG example)  
- Network protocol packet creation  
- Binary format validation and error detection  

### Best Practices Demonstrated

1. **Memory Efficiency**: Using Uint8List for typed arrays, BytesBuilder  
   for dynamic construction, and buffer views for zero-copy operations  
2. **Error Handling**: Proper try-catch blocks, file existence checks,  
   and data validation  
3. **Performance**: Stream-based processing for large files, efficient  
   bit manipulation algorithms, and minimal allocations  
4. **Cross-platform Compatibility**: Explicit endianness handling with  
   ByteData for portable binary formats  
5. **Type Safety**: Leveraging Dart's type system with Uint8List,  
   ByteData, and structured parsing  

### Common Patterns

- **Builder Pattern**: BytesBuilder for efficient byte array construction  
- **Parser Pattern**: BinaryParser for structured data extraction  
- **Stream Pattern**: Processing data flows with proper backpressure  
- **Validation Pattern**: Checksums and magic numbers for data integrity  

### Use Cases Covered

- Network protocol implementation and packet handling  
- File format processing and header parsing  
- Binary data compression and encoding  
- Bitwise operations for flags and compact data  
- Cross-platform data exchange with endianness handling  

These examples provide a solid foundation for working with binary data in  
Dart, demonstrating both fundamental concepts and advanced techniques used  
in real-world applications. The patterns and techniques shown form the  
building blocks for implementing network protocols, file formats, data  
compression, and other binary data processing tasks.  

Dart's strong typing, excellent standard library support through dart:io,  
dart:typed_data, and dart:convert, combined with async/await for efficient  
I/O, makes it an excellent choice for binary data manipulation in both  
server-side and Flutter mobile applications.  
