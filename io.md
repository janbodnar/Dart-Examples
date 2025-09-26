# Dart IO

Input/Output operations in Dart provide comprehensive functionality for  
interacting with the file system, reading and writing files, managing  
directories, and handling various data formats. The `dart:io` library  
offers both synchronous and asynchronous APIs for efficient file operations.  

Dart IO operations support different platforms and provide error handling  
mechanisms to ensure robust applications. These examples demonstrate  
file manipulation, directory operations, and advanced IO techniques  
essential for building real-world applications.  

## Reading Text Files

Text file reading is fundamental for processing configuration files,  
data imports, and content parsing. Dart provides simple methods for  
reading entire files or processing them line by line.  

```dart
import 'dart:io';

void main() async {
  // Read entire file as string
  var file = File('sample.txt');
  
  try {
    String contents = await file.readAsString();
    print('File contents:');
    print(contents);
    
    // Read file as lines
    List<String> lines = await file.readAsLines();
    print('\nFile has ${lines.length} lines');
    for (int i = 0; i < lines.length; i++) {
      print('Line ${i + 1}: ${lines[i]}');
    }
  } catch (e) {
    print('Error reading file: $e');
  }
}
```

This example demonstrates reading a text file both as a complete string  
and as individual lines. The asynchronous approach prevents blocking  
the application while reading large files, and error handling ensures  
graceful failure management.  


## Writing Text Files

Writing text files enables data persistence, logging, and configuration  
storage. Dart supports various writing modes including overwriting  
and appending to existing files.  

```dart
import 'dart:io';

void main() async {
  var file = File('output.txt');
  
  try {
    // Write string to file (overwrites existing content)
    await file.writeAsString('Hello there!\nWelcome to Dart IO.');
    print('File written successfully');
    
    // Append to existing file
    await file.writeAsString('\nThis line is appended.', 
                           mode: FileMode.append);
    
    // Write multiple lines
    var lines = ['Line 1', 'Line 2', 'Line 3'];
    await file.writeAsString(lines.join('\n') + '\n', 
                           mode: FileMode.append);
    
    print('Content appended successfully');
  } catch (e) {
    print('Error writing file: $e');
  }
}
```

File writing operations support different modes including write (overwrite),  
append, and writeOnly. The join method efficiently combines multiple lines  
with newline separators for batch writing operations.  


## Synchronous File Operations

Synchronous file operations provide immediate results but can block  
the application thread. They are useful for small files or when  
sequential processing is required.  

```dart
import 'dart:io';

void main() {
  var file = File('sync_example.txt');
  
  try {
    // Synchronous write
    file.writeAsStringSync('Synchronous write operation\n');
    print('File written synchronously');
    
    // Synchronous read
    String content = file.readAsStringSync();
    print('Read content: $content');
    
    // Check file properties
    print('File size: ${file.lengthSync()} bytes');
    print('Last modified: ${file.lastModifiedSync()}');
    
  } catch (e) {
    print('Error in synchronous operation: $e');
  }
}
```

Synchronous operations use the `Sync` suffix and return values directly  
rather than futures. They are suitable for small files where blocking  
behavior is acceptable, such as reading configuration files at startup.  


## Creating and Checking Files

File creation and existence checking are essential for data management  
and preventing conflicts. Dart provides methods to create files  
conditionally and verify their existence.  

```dart
import 'dart:io';

void main() async {
  var file = File('new_file.txt');
  
  // Check if file exists
  bool exists = await file.exists();
  print('File exists: $exists');
  
  if (!exists) {
    // Create file
    await file.create();
    print('File created');
  }
  
  // Create file recursively (including directories)
  var nestedFile = File('data/logs/app.log');
  await nestedFile.create(recursive: true);
  print('Nested file created with directories');
  
  // Check file properties
  var stat = await nestedFile.stat();
  print('File type: ${stat.type}');
  print('File size: ${stat.size} bytes');
  print('Modified: ${stat.modified}');
}
```

The create method can build directory structures recursively when needed.  
File statistics provide detailed information about size, modification time,  
and file type for comprehensive file management.  


## Deleting Files

File deletion removes files from the file system permanently.  
Proper error handling ensures applications handle missing files  
and permission issues gracefully.  

```dart
import 'dart:io';

void main() async {
  var file = File('temp_file.txt');
  
  // Create a temporary file
  await file.writeAsString('Temporary content');
  print('Temporary file created');
  
  // Check if file exists before deletion
  if (await file.exists()) {
    try {
      await file.delete();
      print('File deleted successfully');
    } catch (e) {
      print('Error deleting file: $e');
    }
  } else {
    print('File does not exist');
  }
  
  // Verify deletion
  bool stillExists = await file.exists();
  print('File still exists: $stillExists');
}
```

Always verify file existence before attempting deletion to avoid  
exceptions. The delete operation is permanent and cannot be undone,  
so careful consideration is required in production applications.  


## Directory Operations

Directory operations enable folder creation, listing contents, and  
navigation. These operations are fundamental for organizing files  
and implementing file managers.  

```dart
import 'dart:io';

void main() async {
  var directory = Directory('test_directory');
  
  // Create directory
  if (!await directory.exists()) {
    await directory.create();
    print('Directory created');
  }
  
  // Create nested directories
  var nestedDir = Directory('parent/child/grandchild');
  await nestedDir.create(recursive: true);
  print('Nested directories created');
  
  // List directory contents
  var currentDir = Directory.current;
  print('Current directory: ${currentDir.path}');
  
  await for (var entity in currentDir.list()) {
    if (entity is File) {
      print('File: ${entity.path}');
    } else if (entity is Directory) {
      print('Directory: ${entity.path}');
    }
  }
}
```

Directory creation supports recursive mode for building nested structures.  
The list method returns a stream of file system entities, allowing  
efficient processing of large directory contents.  


## Directory Listing and Filtering

Directory listing with filtering helps locate specific files and  
organize content. Dart provides pattern matching and custom  
filtering capabilities.  

```dart
import 'dart:io';

void main() async {
  var directory = Directory('.');
  
  // List all entities
  print('All entities:');
  var entities = await directory.list().toList();
  for (var entity in entities) {
    print('${entity.runtimeType}: ${entity.path}');
  }
  
  // Filter by file extension
  print('\nDart files:');
  var dartFiles = entities
      .where((entity) => entity is File && entity.path.endsWith('.dart'))
      .cast<File>();
  
  for (var file in dartFiles) {
    var size = await file.length();
    print('${file.path} - ${size} bytes');
  }
  
  // List directories only
  print('\nDirectories:');
  var directories = entities
      .where((entity) => entity is Directory)
      .cast<Directory>();
  
  for (var dir in directories) {
    print(dir.path);
  }
}
```

Stream processing with filtering enables efficient file system navigation.  
The cast method safely converts filtered results to specific types  
for accessing file-specific or directory-specific properties.  


## File Copying

File copying creates duplicates of existing files with optional  
directory creation. This operation is essential for backups  
and file distribution.  

```dart
import 'dart:io';

void main() async {
  var source = File('source.txt');
  var destination = File('backup/source_copy.txt');
  
  // Create source file if it doesn't exist
  if (!await source.exists()) {
    await source.writeAsString('Sample content for copying');
  }
  
  try {
    // Ensure destination directory exists
    await destination.parent.create(recursive: true);
    
    // Copy file
    await source.copy(destination.path);
    print('File copied successfully');
    
    // Verify copy
    var sourceSize = await source.length();
    var destSize = await destination.length();
    print('Source size: $sourceSize bytes');
    print('Destination size: $destSize bytes');
    
    // Read copied content to verify
    var copiedContent = await destination.readAsString();
    print('Copied content: $copiedContent');
    
  } catch (e) {
    print('Error copying file: $e');
  }
}
```

File copying preserves content exactly but creates a new file with  
potentially different metadata. The parent property provides access  
to the containing directory for recursive creation.  


## File Moving and Renaming

Moving files changes their location or name within the file system.  
This operation is atomic and more efficient than copying and deleting.  

```dart
import 'dart:io';

void main() async {
  var originalFile = File('original.txt');
  
  // Create original file
  await originalFile.writeAsString('Content to be moved');
  print('Original file created');
  
  try {
    // Rename file in same directory
    var renamedFile = await originalFile.rename('renamed.txt');
    print('File renamed to: ${renamedFile.path}');
    
    // Move to different directory
    var targetDir = Directory('moved_files');
    await targetDir.create();
    
    var movedFile = await renamedFile.rename('moved_files/final_name.txt');
    print('File moved to: ${movedFile.path}');
    
    // Verify original file no longer exists
    bool originalExists = await originalFile.exists();
    print('Original file exists: $originalExists');
    
    // Verify moved file exists
    bool movedExists = await movedFile.exists();
    print('Moved file exists: $movedExists');
    
  } catch (e) {
    print('Error moving file: $e');
  }
}
```

The rename method can both rename files and move them to different  
directories in a single operation. The original file reference becomes  
invalid after renaming, requiring use of the returned file object.  


## Binary File Operations

Binary file operations handle non-text data such as images, executables,  
and compressed files. These operations work with byte arrays for  
precise data manipulation.  

```dart
import 'dart:io';
import 'dart:typed_data';

void main() async {
  var binaryFile = File('binary_data.bin');
  
  // Create binary data
  var data = Uint8List.fromList([
    0xFF, 0x00, 0xFF, 0x00,  // Pattern data
    0x48, 0x65, 0x6C, 0x6C, 0x6F,  // "Hello" in ASCII
    0x01, 0x02, 0x03, 0x04
  ]);
  
  try {
    // Write binary data
    await binaryFile.writeAsBytes(data);
    print('Binary data written: ${data.length} bytes');
    
    // Read binary data
    Uint8List readData = await binaryFile.readAsBytes();
    print('Binary data read: ${readData.length} bytes');
    
    // Verify data integrity
    bool identical = true;
    for (int i = 0; i < data.length; i++) {
      if (data[i] != readData[i]) {
        identical = false;
        break;
      }
    }
    print('Data integrity check: ${identical ? 'PASSED' : 'FAILED'}');
    
    // Display hex representation
    var hexString = readData.map((b) => b.toRadixString(16).padLeft(2, '0')).join(' ');
    print('Hex representation: $hexString');
    
  } catch (e) {
    print('Error with binary operations: $e');
  }
}
```

Binary operations use Uint8List for precise byte manipulation.  
Hex representation provides human-readable display of binary data  
for debugging and verification purposes.  


## Stream-based File Reading

Stream-based reading processes large files efficiently without  
loading entire content into memory. This approach is ideal  
for processing log files and large datasets.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  var file = File('large_file.txt');
  
  // Create a sample large file
  var content = List.generate(1000, (i) => 'Line ${i + 1}: Sample content').join('\n');
  await file.writeAsString(content);
  
  try {
    // Read file as stream
    var stream = file.openRead();
    var lineStream = stream
        .transform(utf8.decoder)
        .transform(LineSplitter());
    
    int lineCount = 0;
    int wordCount = 0;
    
    await for (var line in lineStream) {
      lineCount++;
      wordCount += line.split(' ').length;
      
      // Process first 5 lines as example
      if (lineCount <= 5) {
        print('Processing: $line');
      }
    }
    
    print('\nFile processing complete:');
    print('Total lines: $lineCount');
    print('Total words: $wordCount');
    
  } catch (e) {
    print('Error reading stream: $e');
  }
}
```

Stream processing with transformers enables efficient text processing.  
The LineSplitter transformer converts byte streams to line-based  
streams for convenient text processing applications.  


## Stream-based File Writing

Stream-based writing enables efficient data output for large datasets  
and real-time logging. This approach prevents memory overflow  
when processing substantial amounts of data.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  var file = File('stream_output.txt');
  
  try {
    var sink = file.openWrite();
    
    // Write data in chunks
    for (int i = 1; i <= 100; i++) {
      var line = 'Processing item $i at ${DateTime.now()}\n';
      sink.write(line);
      
      // Demonstrate progress
      if (i % 20 == 0) {
        print('Written $i lines');
      }
    }
    
    // Write binary data to stream
    var binaryData = [0x48, 0x65, 0x6C, 0x6C, 0x6F]; // "Hello"
    sink.add(binaryData);
    sink.write('\n');
    
    // Important: close the sink
    await sink.close();
    print('Stream writing completed');
    
    // Verify written content
    var lines = await file.readAsLines();
    print('Total lines written: ${lines.length}');
    print('First line: ${lines.first}');
    print('Last line: ${lines.last}');
    
  } catch (e) {
    print('Error writing stream: $e');
  }
}
```

Stream writing requires explicit closing to ensure all data is flushed  
to disk. The IOSink interface supports both string and binary data  
for flexible output formatting.  


## Temporary Files and Directories

Temporary files provide safe storage for intermediate processing  
results and cache data. They are automatically cleaned up by  
the operating system when no longer needed.  

```dart
import 'dart:io';

void main() async {
  try {
    // Create temporary directory
    var tempDir = await Directory.systemTemp.createTemp('dart_app_');
    print('Temporary directory: ${tempDir.path}');
    
    // Create temporary file
    var tempFile = File('${tempDir.path}/temp_data.txt');
    await tempFile.writeAsString('Temporary processing data\n');
    
    // Use temporary file for processing
    await tempFile.writeAsString('Processing step 1\n', mode: FileMode.append);
    await tempFile.writeAsString('Processing step 2\n', mode: FileMode.append);
    
    // Read temporary results
    var content = await tempFile.readAsString();
    print('Temporary file content:\n$content');
    
    // Create another temporary file using system temp
    var systemTempFile = await File('${Directory.systemTemp.path}/system_temp_${DateTime.now().millisecondsSinceEpoch}.tmp')
        .create();
    await systemTempFile.writeAsString('System temporary file');
    
    print('System temp file: ${systemTempFile.path}');
    
    // Cleanup (optional - OS will handle it)
    await tempFile.delete();
    await systemTempFile.delete();
    await tempDir.delete();
    print('Temporary files cleaned up');
    
  } catch (e) {
    print('Error with temporary files: $e');
  }
}
```

Temporary directories use unique names to avoid conflicts. The system  
temporary directory provides a standard location for temporary files  
across different operating systems and platforms.  


## File Watching and Monitoring

File watching monitors file system changes in real-time, enabling  
applications to respond to file modifications, creations, and  
deletions automatically.  

```dart
import 'dart:io';
import 'dart:async';

void main() async {
  var directory = Directory('watched_folder');
  await directory.create();
  
  print('Starting file system watcher...');
  
  // Watch directory for changes
  var watcher = directory.watch();
  
  // Set up event listener
  StreamSubscription? subscription;
  subscription = watcher.listen((FileSystemEvent event) {
    print('Event: ${event.type} - ${event.path}');
    
    switch (event.type) {
      case FileSystemEvent.create:
        print('File/directory created: ${event.path}');
        break;
      case FileSystemEvent.modify:
        print('File modified: ${event.path}');
        break;
      case FileSystemEvent.delete:
        print('File/directory deleted: ${event.path}');
        break;
      case FileSystemEvent.move:
        if (event is FileSystemMoveEvent) {
          print('File moved from ${event.path} to ${event.destination}');
        }
        break;
    }
  });
  
  // Simulate file changes
  print('Creating test files...');
  await Future.delayed(Duration(seconds: 1));
  
  var testFile = File('${directory.path}/test.txt');
  await testFile.writeAsString('Initial content');
  
  await Future.delayed(Duration(seconds: 1));
  await testFile.writeAsString('Modified content');
  
  await Future.delayed(Duration(seconds: 1));
  await testFile.delete();
  
  // Clean up
  await Future.delayed(Duration(seconds: 2));
  await subscription.cancel();
  await directory.delete();
  print('File watching stopped');
}
```

File system watchers provide real-time notifications for file system  
changes. Different event types enable specific responses to various  
file operations, supporting live file monitoring applications.  


## Path Operations

Path operations provide cross-platform file system navigation  
and path manipulation. The `path` package offers comprehensive  
utilities for working with file system paths.  

```dart
import 'dart:io';

void main() async {
  // Current directory operations
  var currentDir = Directory.current;
  print('Current directory: ${currentDir.path}');
  
  // Path components
  var filePath = '${currentDir.path}/data/config/settings.txt';
  print('Full path: $filePath');
  
  // Extract path components manually
  var components = filePath.split(Platform.pathSeparator);
  print('Path components: $components');
  print('Filename: ${components.last}');
  print('Directory: ${components.sublist(0, components.length - 1).join(Platform.pathSeparator)}');
  
  // Platform-specific path handling
  print('Path separator: "${Platform.pathSeparator}"');
  print('Operating system: ${Platform.operatingSystem}');
  
  // Build paths programmatically
  var documentsPath = '${currentDir.path}${Platform.pathSeparator}documents';
  var configPath = '$documentsPath${Platform.pathSeparator}config.ini';
  print('Built path: $configPath');
  
  // Check if path is absolute
  print('Is absolute path: ${filePath.startsWith('/')}');
  
  // Create nested directory structure
  var nestedPath = '${currentDir.path}/level1/level2/level3';
  var nestedDir = Directory(nestedPath);
  await nestedDir.create(recursive: true);
  print('Created nested directories: $nestedPath');
  
  // Clean up
  await Directory('${currentDir.path}/level1').delete(recursive: true);
}
```

Platform-specific path separators ensure cross-platform compatibility.  
Path manipulation using string operations provides basic functionality  
for file system navigation and path construction.  


## File Information and Metadata

File metadata provides detailed information about files including  
size, permissions, timestamps, and file type. This information  
is essential for file management applications.  

```dart
import 'dart:io';

void main() async {
  var file = File('metadata_example.txt');
  
  // Create file with content
  await file.writeAsString('Sample file for metadata demonstration\n');
  await file.writeAsString('Additional content line\n', mode: FileMode.append);
  
  try {
    // Get file statistics
    var stat = await file.stat();
    
    print('File Information:');
    print('Path: ${file.path}');
    print('Absolute path: ${file.absolute.path}');
    print('Size: ${stat.size} bytes');
    print('Type: ${stat.type}');
    print('Modified: ${stat.modified}');
    print('Accessed: ${stat.accessed}');
    print('Changed: ${stat.changed}');
    print('Mode: ${stat.mode.toRadixString(8)}'); // Octal permissions
    
    // Check file properties
    bool exists = await file.exists();
    int length = await file.length();
    DateTime lastModified = await file.lastModified();
    
    print('\nDirect Properties:');
    print('Exists: $exists');
    print('Length: $length bytes');
    print('Last modified: $lastModified');
    
    // Parent directory information
    var parent = file.parent;
    print('\nParent Directory:');
    print('Path: ${parent.path}');
    print('Absolute path: ${parent.absolute.path}');
    
    var parentStat = await parent.stat();
    print('Parent modified: ${parentStat.modified}');
    
  } catch (e) {
    print('Error getting file information: $e');
  } finally {
    // Clean up
    if (await file.exists()) {
      await file.delete();
    }
  }
}
```

File statistics provide comprehensive metadata including timestamps  
and permissions. The parent property enables access to containing  
directory information for complete file system context.  


## Error Handling in File Operations

Robust error handling ensures applications gracefully manage file  
system errors such as permission issues, missing files, and  
disk space limitations.  

```dart
import 'dart:io';

void main() async {
  await demonstrateFileErrors();
  await demonstrateDirectoryErrors();
  await demonstratePermissionErrors();
}

Future<void> demonstrateFileErrors() async {
  print('=== File Operation Errors ===');
  
  // Reading non-existent file
  var nonExistentFile = File('does_not_exist.txt');
  try {
    await nonExistentFile.readAsString();
  } on FileSystemException catch (e) {
    print('File system error: ${e.message}');
    print('Error path: ${e.path}');
    print('OS error code: ${e.osError?.errorCode}');
  } catch (e) {
    print('Unexpected error: $e');
  }
  
  // Writing to read-only location (simulate)
  try {
    var readOnlyFile = File('/root/readonly.txt');
    await readOnlyFile.writeAsString('This should fail');
  } on FileSystemException catch (e) {
    print('Permission denied: ${e.message}');
  } catch (e) {
    print('Error writing to protected location: $e');
  }
}

Future<void> demonstrateDirectoryErrors() async {
  print('\n=== Directory Operation Errors ===');
  
  // Creating directory in non-existent parent
  try {
    var invalidDir = Directory('/non/existent/path/newdir');
    await invalidDir.create(); // Without recursive flag
  } on FileSystemException catch (e) {
    print('Directory creation failed: ${e.message}');
  }
  
  // Listing non-existent directory
  try {
    var missingDir = Directory('missing_directory');
    await missingDir.list().toList();
  } on FileSystemException catch (e) {
    print('Directory listing failed: ${e.message}');
  }
}

Future<void> demonstratePermissionErrors() async {
  print('\n=== Permission Handling ===');
  
  var testFile = File('permission_test.txt');
  
  try {
    // Create file successfully
    await testFile.writeAsString('Test content');
    print('File created successfully');
    
    // Attempt operations that might fail
    var content = await testFile.readAsString();
    print('File read successfully: ${content.length} characters');
    
  } on FileSystemException catch (e) {
    print('File system error occurred:');
    print('  Message: ${e.message}');
    print('  Path: ${e.path}');
    print('  OS Error: ${e.osError}');
  } on PathAccessException catch (e) {
    print('Path access denied: ${e.message}');
  } catch (e) {
    print('Unexpected error: $e');
  } finally {
    // Clean up in finally block
    try {
      if (await testFile.exists()) {
        await testFile.delete();
        print('Test file cleaned up');
      }
    } catch (e) {
      print('Failed to clean up test file: $e');
    }
  }
}
```

Specific exception types enable targeted error handling for different  
failure scenarios. The finally block ensures cleanup operations  
execute regardless of success or failure status.  


## Working with CSV Files

CSV file processing involves reading and writing structured data  
in comma-separated value format. This technique is essential  
for data import, export, and processing tasks.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  await writeCsvFile();
  await readCsvFile();
  await processCsvData();
}

Future<void> writeCsvFile() async {
  var csvFile = File('employees.csv');
  
  // CSV data
  var employees = [
    ['Name', 'Age', 'Department', 'Salary'],
    ['Alice Johnson', '28', 'Engineering', '75000'],
    ['Bob Smith', '35', 'Marketing', '65000'],
    ['Carol Brown', '42', 'Engineering', '85000'],
    ['David Wilson', '29', 'Sales', '58000'],
  ];
  
  // Write CSV content
  var csvContent = employees.map((row) => row.join(',')).join('\n');
  await csvFile.writeAsString(csvContent);
  print('CSV file created with ${employees.length} rows');
}

Future<void> readCsvFile() async {
  var csvFile = File('employees.csv');
  
  try {
    var lines = await csvFile.readAsLines();
    print('\n=== Reading CSV File ===');
    
    // Parse header
    var headers = lines.first.split(',');
    print('Headers: $headers');
    
    // Parse data rows
    for (int i = 1; i < lines.length; i++) {
      var values = lines[i].split(',');
      var employee = Map.fromIterables(headers, values);
      print('Employee ${i}: $employee');
    }
    
  } catch (e) {
    print('Error reading CSV: $e');
  }
}

Future<void> processCsvData() async {
  var csvFile = File('employees.csv');
  
  try {
    var lines = await csvFile.readAsLines();
    var headers = lines.first.split(',');
    
    // Calculate average salary
    var salaries = <double>[];
    for (int i = 1; i < lines.length; i++) {
      var values = lines[i].split(',');
      var salary = double.parse(values[3]);
      salaries.add(salary);
    }
    
    var averageSalary = salaries.reduce((a, b) => a + b) / salaries.length;
    print('\n=== CSV Data Analysis ===');
    print('Total employees: ${salaries.length}');
    print('Average salary: \$${averageSalary.toStringAsFixed(2)}');
    
    // Find highest paid employee
    var maxSalaryIndex = 0;
    for (int i = 1; i < salaries.length; i++) {
      if (salaries[i] > salaries[maxSalaryIndex]) {
        maxSalaryIndex = i;
      }
    }
    
    var topEmployee = lines[maxSalaryIndex + 1].split(',');
    print('Highest paid: ${topEmployee[0]} (\$${topEmployee[3]})');
    
  } catch (e) {
    print('Error processing CSV data: $e');
  }
}
```

CSV processing requires careful handling of data types and potential  
parsing errors. String splitting provides basic CSV parsing, while  
more complex scenarios may require specialized CSV libraries.  


## Working with JSON Files

JSON file operations enable structured data persistence and  
configuration management. Dart's built-in JSON support  
simplifies serialization and parsing operations.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  await writeJsonFile();
  await readJsonFile();
  await updateJsonFile();
}

Future<void> writeJsonFile() async {
  var jsonFile = File('config.json');
  
  // Create structured data
  var config = {
    'application': {
      'name': 'Dart IO Example',
      'version': '1.0.0',
      'debug': true
    },
    'database': {
      'host': 'localhost',
      'port': 5432,
      'name': 'app_db',
      'ssl': false
    },
    'features': ['logging', 'authentication', 'caching'],
    'limits': {
      'max_connections': 100,
      'timeout': 30.5,
      'retry_count': 3
    }
  };
  
  // Convert to JSON and write
  var jsonString = JsonEncoder.withIndent('  ').convert(config);
  await jsonFile.writeAsString(jsonString);
  print('JSON configuration file created');
}

Future<void> readJsonFile() async {
  var jsonFile = File('config.json');
  
  try {
    var jsonString = await jsonFile.readAsString();
    var config = jsonDecode(jsonString) as Map<String, dynamic>;
    
    print('\n=== Reading JSON Configuration ===');
    print('App name: ${config['application']['name']}');
    print('App version: ${config['application']['version']}');
    print('Debug mode: ${config['application']['debug']}');
    
    // Access nested data
    var database = config['database'] as Map<String, dynamic>;
    print('Database: ${database['host']}:${database['port']}');
    
    // Access arrays
    var features = config['features'] as List<dynamic>;
    print('Features: ${features.join(', ')}');
    
    // Access numeric values
    var limits = config['limits'] as Map<String, dynamic>;
    print('Max connections: ${limits['max_connections']}');
    print('Timeout: ${limits['timeout']} seconds');
    
  } catch (e) {
    print('Error reading JSON: $e');
  }
}

Future<void> updateJsonFile() async {
  var jsonFile = File('config.json');
  
  try {
    // Read existing configuration
    var jsonString = await jsonFile.readAsString();
    var config = jsonDecode(jsonString) as Map<String, dynamic>;
    
    // Update configuration
    config['application']['version'] = '1.1.0';
    config['application']['debug'] = false;
    (config['features'] as List).add('monitoring');
    
    // Add new section
    config['logging'] = {
      'level': 'info',
      'file': 'app.log',
      'rotate': true
    };
    
    // Write updated configuration
    var updatedJson = JsonEncoder.withIndent('  ').convert(config);
    await jsonFile.writeAsString(updatedJson);
    
    print('\n=== JSON Configuration Updated ===');
    print('Version updated to: ${config['application']['version']}');
    print('Debug mode: ${config['application']['debug']}');
    print('Added logging configuration');
    
  } catch (e) {
    print('Error updating JSON: $e');
  }
}
```

JSON operations use the dart:convert library for serialization and  
parsing. The JsonEncoder.withIndent creates human-readable formatted  
output suitable for configuration files and data storage.  


## Compressed File Operations

File compression reduces storage space and transfer time for large  
files. Dart supports gzip compression through the dart:io library  
for efficient data handling.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  await demonstrateGzipCompression();
  await demonstrateStreamCompression();
}

Future<void> demonstrateGzipCompression() async {
  var originalFile = File('large_text.txt');
  
  // Create large text content
  var content = List.generate(1000, (i) => 
      'This is line ${i + 1} with some repeated content to demonstrate compression. ' +
      'Lorem ipsum dolor sit amet, consectetur adipiscing elit. '
  ).join('\n');
  
  await originalFile.writeAsString(content);
  var originalSize = await originalFile.length();
  print('Original file size: $originalSize bytes');
  
  // Compress file
  var compressedFile = File('large_text.txt.gz');
  var input = originalFile.openRead();
  var output = compressedFile.openWrite();
  
  await input
      .transform(gzip.encoder)
      .pipe(output);
  
  var compressedSize = await compressedFile.length();
  var compressionRatio = (1 - compressedSize / originalSize) * 100;
  
  print('Compressed file size: $compressedSize bytes');
  print('Compression ratio: ${compressionRatio.toStringAsFixed(1)}%');
  
  // Decompress file
  var decompressedFile = File('decompressed.txt');
  var compressedInput = compressedFile.openRead();
  var decompressedOutput = decompressedFile.openWrite();
  
  await compressedInput
      .transform(gzip.decoder)
      .pipe(decompressedOutput);
  
  // Verify decompression
  var decompressedContent = await decompressedFile.readAsString();
  bool identical = content == decompressedContent;
  print('Decompression successful: $identical');
  
  // Clean up
  await originalFile.delete();
  await compressedFile.delete();
  await decompressedFile.delete();
}

Future<void> demonstrateStreamCompression() async {
  print('\n=== Stream-based Compression ===');
  
  var dataFile = File('stream_data.txt');
  var compressedFile = File('stream_data.gz');
  
  // Generate and compress data in stream
  var sink = compressedFile.openWrite();
  var gzipSink = gzip.encoder.startChunkedConversion(sink);
  
  // Write data in chunks
  for (int i = 0; i < 100; i++) {
    var chunk = 'Data chunk $i: ${DateTime.now()} - Some sample data content\n';
    gzipSink.add(utf8.encode(chunk));
  }
  
  gzipSink.close();
  await sink.close();
  
  print('Stream compression completed');
  
  // Read and decompress in stream
  var decompressedSink = dataFile.openWrite();
  await compressedFile
      .openRead()
      .transform(gzip.decoder)
      .transform(utf8.decoder)
      .transform(LineSplitter())
      .forEach((line) {
        decompressedSink.writeln('Processed: $line');
      });
  
  await decompressedSink.close();
  
  var lineCount = (await dataFile.readAsLines()).length;
  print('Decompressed $lineCount lines');
  
  // Clean up
  await dataFile.delete();
  await compressedFile.delete();
}
```

Gzip compression provides efficient data reduction with built-in  
stream support. Chunked conversion enables processing large files  
without loading entire content into memory.  


## Platform-specific File Operations

Platform-specific operations handle differences between operating  
systems for file permissions, paths, and system integration.  
Dart provides platform detection and adaptation capabilities.  

```dart
import 'dart:io';

void main() async {
  await demonstratePlatformInfo();
  await demonstratePathHandling();
  await demonstrateEnvironmentAccess();
}

Future<void> demonstratePlatformInfo() async {
  print('=== Platform Information ===');
  print('Operating System: ${Platform.operatingSystem}');
  print('Operating System Version: ${Platform.operatingSystemVersion}');
  print('Path Separator: "${Platform.pathSeparator}"');
  print('Line Terminator: "${Platform.lineTerminator.codeUnits}"');
  print('Number of Processors: ${Platform.numberOfProcessors}');
  print('Locale Name: ${Platform.localeName}');
  
  // Platform-specific behavior
  if (Platform.isWindows) {
    print('Running on Windows');
  } else if (Platform.isLinux) {
    print('Running on Linux');
  } else if (Platform.isMacOS) {
    print('Running on macOS');
  } else {
    print('Running on other platform');
  }
}

Future<void> demonstratePathHandling() async {
  print('\n=== Platform-specific Paths ===');
  
  var currentDir = Directory.current.path;
  print('Current directory: $currentDir');
  
  // Build platform-appropriate paths
  var configPath = currentDir + 
      Platform.pathSeparator + 
      'config' + 
      Platform.pathSeparator + 
      'app.conf';
  print('Config path: $configPath');
  
  // Home directory (platform-specific)
  String? homeDir;
  if (Platform.isWindows) {
    homeDir = Platform.environment['USERPROFILE'];
  } else {
    homeDir = Platform.environment['HOME'];
  }
  print('Home directory: ${homeDir ?? 'Not available'}');
  
  // Temporary directory
  var tempDir = Directory.systemTemp.path;
  print('System temp directory: $tempDir');
  
  // Create cross-platform file
  var crossPlatformFile = File(currentDir + 
      Platform.pathSeparator + 
      'cross_platform.txt');
  
  await crossPlatformFile.writeAsString(
      'Created on: ${Platform.operatingSystem}${Platform.lineTerminator}' +
      'Path separator: ${Platform.pathSeparator}${Platform.lineTerminator}' +
      'Timestamp: ${DateTime.now()}${Platform.lineTerminator}'
  );
  
  print('Cross-platform file created');
  
  // Clean up
  await crossPlatformFile.delete();
}

Future<void> demonstrateEnvironmentAccess() async {
  print('\n=== Environment Variables ===');
  
  // Access common environment variables
  var environment = Platform.environment;
  
  // Platform-specific important variables
  if (Platform.isWindows) {
    print('Windows User: ${environment['USERNAME']}');
    print('Windows Version: ${environment['OS']}');
    print('Program Files: ${environment['PROGRAMFILES']}');
  } else {
    print('Unix User: ${environment['USER']}');
    print('Shell: ${environment['SHELL']}');
    print('PATH entries: ${environment['PATH']?.split(':').length ?? 0}');
  }
  
  // Create platform-specific log file
  var logDir = Platform.isWindows 
      ? '${environment['TEMP']}'
      : '/tmp';
      
  var logFile = File('$logDir${Platform.pathSeparator}dart_app.log');
  
  await logFile.writeAsString(
      '[${DateTime.now()}] Application started on ${Platform.operatingSystem}${Platform.lineTerminator}' +
      '[${DateTime.now()}] User: ${environment['USER'] ?? environment['USERNAME']}${Platform.lineTerminator}' +
      '[${DateTime.now()}] Working directory: ${Directory.current.path}${Platform.lineTerminator}'
  );
  
  print('Platform-specific log created: ${logFile.path}');
  
  // Read and display log
  var logContent = await logFile.readAsString();
  print('Log content:');
  print(logContent);
  
  // Clean up
  await logFile.delete();
}
```

Platform detection enables adaptive behavior for different operating  
systems. Environment variables provide access to system configuration  
and user-specific information across platforms.  


## File Locking and Concurrent Access

File locking prevents concurrent access conflicts when multiple  
processes attempt to modify the same file. Proper locking  
ensures data integrity in multi-process environments.  

```dart
import 'dart:io';
import 'dart:async';
import 'dart:isolate';

void main() async {
  await demonstrateFileLocking();
  await demonstrateConcurrentAccess();
}

Future<void> demonstrateFileLocking() async {
  print('=== File Locking Demonstration ===');
  
  var sharedFile = File('shared_resource.txt');
  var lockFile = File('shared_resource.lock');
  
  // Initialize shared file
  await sharedFile.writeAsString('Initial content\n');
  
  try {
    // Acquire lock
    if (await lockFile.exists()) {
      print('File is locked by another process');
      return;
    }
    
    await lockFile.create();
    print('Lock acquired');
    
    // Simulate exclusive file access
    for (int i = 1; i <= 5; i++) {
      await sharedFile.writeAsString(
          'Update $i at ${DateTime.now()}\n', 
          mode: FileMode.append
      );
      await Future.delayed(Duration(milliseconds: 500));
      print('Updated shared file: step $i');
    }
    
  } finally {
    // Always release lock
    if (await lockFile.exists()) {
      await lockFile.delete();
      print('Lock released');
    }
  }
  
  // Display final content
  var content = await sharedFile.readAsString();
  print('Final shared file content:\n$content');
  
  // Clean up
  await sharedFile.delete();
}

Future<void> demonstrateConcurrentAccess() async {
  print('\n=== Concurrent Access Management ===');
  
  var counterFile = File('counter.txt');
  await counterFile.writeAsString('0');
  
  // Simulate multiple concurrent processes
  var futures = <Future>[];
  
  for (int processId = 1; processId <= 3; processId++) {
    futures.add(simulateProcess(processId, counterFile));
  }
  
  // Wait for all processes to complete
  await Future.wait(futures);
  
  // Read final counter value
  var finalValue = int.parse(await counterFile.readAsString());
  print('Final counter value: $finalValue');
  
  // Clean up
  await counterFile.delete();
}

Future<void> simulateProcess(int processId, File counterFile) async {
  for (int i = 0; i < 5; i++) {
    await incrementCounter(processId, counterFile);
    await Future.delayed(Duration(milliseconds: 100));
  }
}

Future<void> incrementCounter(int processId, File counterFile) async {
  var lockFile = File('counter.lock');
  
  // Wait for lock availability
  while (await lockFile.exists()) {
    await Future.delayed(Duration(milliseconds: 10));
  }
  
  try {
    // Acquire lock
    await lockFile.create();
    
    // Read current value
    var currentValue = int.parse(await counterFile.readAsString());
    
    // Simulate processing time
    await Future.delayed(Duration(milliseconds: 50));
    
    // Increment and write back
    var newValue = currentValue + 1;
    await counterFile.writeAsString(newValue.toString());
    
    print('Process $processId: $currentValue -> $newValue');
    
  } catch (e) {
    print('Process $processId error: $e');
  } finally {
    // Release lock
    try {
      await lockFile.delete();
    } catch (e) {
      // Lock file might already be deleted
    }
  }
}
```

File locking uses auxiliary lock files to coordinate access between  
processes. The try-finally pattern ensures locks are always released,  
preventing deadlock situations in concurrent environments.  


## Performance Optimization

Performance optimization in file operations involves choosing  
appropriate methods, buffer sizes, and access patterns for  
efficient data processing and minimal resource usage.  

```dart
import 'dart:io';
import 'dart:typed_data';

void main() async {
  await demonstrateReadPerformance();
  await demonstrateWritePerformance();
  await demonstrateBufferingStrategies();
}

Future<void> demonstrateReadPerformance() async {
  print('=== Read Performance Comparison ===');
  
  // Create test file with substantial content
  var testFile = File('performance_test.txt');
  var content = List.generate(10000, (i) => 
      'Line $i: This is a sample line with some content for testing. '
  ).join('\n');
  
  await testFile.writeAsString(content);
  var fileSize = await testFile.length();
  print('Test file size: $fileSize bytes');
  
  // Method 1: Read entire file as string
  var stopwatch = Stopwatch()..start();
  var fullContent = await testFile.readAsString();
  stopwatch.stop();
  print('Read as string: ${stopwatch.elapsedMilliseconds}ms');
  
  // Method 2: Read as lines
  stopwatch.reset();
  stopwatch.start();
  var lines = await testFile.readAsLines();
  stopwatch.stop();
  print('Read as lines (${lines.length} lines): ${stopwatch.elapsedMilliseconds}ms');
  
  // Method 3: Stream reading
  stopwatch.reset();
  stopwatch.start();
  var stream = testFile.openRead();
  var byteCount = 0;
  await for (var chunk in stream) {
    byteCount += chunk.length;
  }
  stopwatch.stop();
  print('Stream reading ($byteCount bytes): ${stopwatch.elapsedMilliseconds}ms');
  
  // Method 4: Buffered reading
  stopwatch.reset();
  stopwatch.start();
  var randomAccess = await testFile.open();
  var buffer = Uint8List(8192); // 8KB buffer
  var totalRead = 0;
  
  while (true) {
    var bytesRead = await randomAccess.readInto(buffer);
    if (bytesRead == 0) break;
    totalRead += bytesRead;
  }
  await randomAccess.close();
  stopwatch.stop();
  print('Buffered reading ($totalRead bytes): ${stopwatch.elapsedMilliseconds}ms');
  
  await testFile.delete();
}

Future<void> demonstrateWritePerformance() async {
  print('\n=== Write Performance Comparison ===');
  
  var testData = List.generate(5000, (i) => 'Data line $i with content\n');
  
  // Method 1: Single write operation
  var file1 = File('write_test1.txt');
  var stopwatch = Stopwatch()..start();
  await file1.writeAsString(testData.join());
  stopwatch.stop();
  print('Single write: ${stopwatch.elapsedMilliseconds}ms');
  
  // Method 2: Multiple append operations
  var file2 = File('write_test2.txt');
  stopwatch.reset();
  stopwatch.start();
  for (var line in testData) {
    await file2.writeAsString(line, mode: FileMode.append);
  }
  stopwatch.stop();
  print('Multiple appends: ${stopwatch.elapsedMilliseconds}ms');
  
  // Method 3: Stream writing
  var file3 = File('write_test3.txt');
  stopwatch.reset();
  stopwatch.start();
  var sink = file3.openWrite();
  for (var line in testData) {
    sink.write(line);
  }
  await sink.close();
  stopwatch.stop();
  print('Stream writing: ${stopwatch.elapsedMilliseconds}ms');
  
  // Method 4: Buffered writing
  var file4 = File('write_test4.txt');
  stopwatch.reset();
  stopwatch.start();
  var randomAccess = await file4.open(mode: FileMode.write);
  var buffer = testData.join().codeUnits;
  await randomAccess.writeFrom(buffer);
  await randomAccess.close();
  stopwatch.stop();
  print('Buffered writing: ${stopwatch.elapsedMilliseconds}ms');
  
  // Verify file sizes
  var sizes = await Future.wait([
    file1.length(),
    file2.length(), 
    file3.length(),
    file4.length()
  ]);
  print('File sizes: ${sizes.join(', ')} bytes');
  
  // Clean up
  await Future.wait([
    file1.delete(),
    file2.delete(),
    file3.delete(),
    file4.delete()
  ]);
}

Future<void> demonstrateBufferingStrategies() async {
  print('\n=== Buffering Strategies ===');
  
  var sourceFile = File('source_large.txt');
  
  // Create large source file
  var largeContent = List.generate(50000, (i) => 
      'Large file line $i with substantial content for buffer testing. '
  ).join('\n');
  await sourceFile.writeAsString(largeContent);
  
  var fileSize = await sourceFile.length();
  print('Source file size: $fileSize bytes');
  
  // Test different buffer sizes
  var bufferSizes = [1024, 4096, 8192, 16384, 65536]; // 1KB to 64KB
  
  for (var bufferSize in bufferSizes) {
    var destFile = File('buffered_copy_${bufferSize}.txt');
    
    var stopwatch = Stopwatch()..start();
    var source = await sourceFile.open();
    var dest = await destFile.open(mode: FileMode.write);
    var buffer = Uint8List(bufferSize);
    
    while (true) {
      var bytesRead = await source.readInto(buffer);
      if (bytesRead == 0) break;
      
      await dest.writeFrom(buffer, 0, bytesRead);
    }
    
    await source.close();
    await dest.close();
    stopwatch.stop();
    
    print('${bufferSize} byte buffer: ${stopwatch.elapsedMilliseconds}ms');
    await destFile.delete();
  }
  
  await sourceFile.delete();
}
```

Performance optimization requires balancing memory usage with speed.  
Larger buffers generally improve performance but consume more memory.  
Stream-based operations provide the best balance for large files.  


## Advanced File System Operations

Advanced file system operations include symbolic links, hard links,  
and file system traversal with complex filtering and processing  
capabilities for sophisticated file management tasks.  

```dart
import 'dart:io';

void main() async {
  await demonstrateFileSystemTraversal();
  await demonstrateAdvancedFiltering();
  await demonstrateBulkOperations();
}

Future<void> demonstrateFileSystemTraversal() async {
  print('=== File System Traversal ===');
  
  // Create test directory structure
  await createTestStructure();
  
  var rootDir = Directory('test_structure');
  
  // Recursive directory traversal
  print('Recursive traversal:');
  await traverseDirectory(rootDir, '');
  
  // Find files by pattern
  print('\nFind .txt files:');
  var txtFiles = await findFilesByExtension(rootDir, '.txt');
  for (var file in txtFiles) {
    var size = await file.length();
    print('${file.path} - $size bytes');
  }
  
  // Calculate directory sizes
  print('\nDirectory sizes:');
  var totalSize = await calculateDirectorySize(rootDir);
  print('Total size: $totalSize bytes');
  
  // Clean up
  await rootDir.delete(recursive: true);
}

Future<void> createTestStructure() async {
  var structure = [
    'test_structure/docs/readme.txt',
    'test_structure/docs/manual.pdf',
    'test_structure/src/main.dart',
    'test_structure/src/utils/helper.dart',
    'test_structure/src/models/user.dart',
    'test_structure/tests/unit_test.dart',
    'test_structure/config/app.json',
    'test_structure/logs/app.log',
  ];
  
  for (var filePath in structure) {
    var file = File(filePath);
    await file.create(recursive: true);
    await file.writeAsString('Sample content for ${file.path}');
  }
}

Future<void> traverseDirectory(Directory dir, String indent) async {
  try {
    await for (var entity in dir.list()) {
      if (entity is File) {
        var size = await entity.length();
        print('$indent📄 ${entity.path.split('/').last} ($size bytes)');
      } else if (entity is Directory) {
        print('$indent📁 ${entity.path.split('/').last}/');
        await traverseDirectory(entity, indent + '  ');
      }
    }
  } catch (e) {
    print('${indent}❌ Error accessing ${dir.path}: $e');
  }
}

Future<List<File>> findFilesByExtension(Directory dir, String extension) async {
  var files = <File>[];
  
  try {
    await for (var entity in dir.list(recursive: true)) {
      if (entity is File && entity.path.endsWith(extension)) {
        files.add(entity);
      }
    }
  } catch (e) {
    print('Error finding files: $e');
  }
  
  return files;
}

Future<int> calculateDirectorySize(Directory dir) async {
  var totalSize = 0;
  
  try {
    await for (var entity in dir.list(recursive: true)) {
      if (entity is File) {
        totalSize += await entity.length();
      }
    }
  } catch (e) {
    print('Error calculating size: $e');
  }
  
  return totalSize;
}

Future<void> demonstrateAdvancedFiltering() async {
  print('\n=== Advanced File Filtering ===');
  
  var testDir = Directory('filter_test');
  await testDir.create();
  
  // Create test files with different characteristics
  var testFiles = [
    ('small.txt', 'Small file'),
    ('medium.log', 'Medium sized file with more content here'),
    ('large.data', List.generate(1000, (i) => 'Large file line $i').join('\n')),
    ('config.json', '{"key": "value"}'),
    ('script.dart', 'void main() { print("Hello there!"); }'),
  ];
  
  for (var (name, content) in testFiles) {
    var file = File('${testDir.path}/$name');
    await file.writeAsString(content);
  }
  
  // Filter by size
  print('Files larger than 100 bytes:');
  await for (var entity in testDir.list()) {
    if (entity is File) {
      var size = await entity.length();
      if (size > 100) {
        print('${entity.path.split('/').last}: $size bytes');
      }
    }
  }
  
  // Filter by modification time
  print('\nFiles modified in last minute:');
  var oneMinuteAgo = DateTime.now().subtract(Duration(minutes: 1));
  
  await for (var entity in testDir.list()) {
    if (entity is File) {
      var modified = await entity.lastModified();
      if (modified.isAfter(oneMinuteAgo)) {
        print('${entity.path.split('/').last}: $modified');
      }
    }
  }
  
  // Clean up
  await testDir.delete(recursive: true);
}

Future<void> demonstrateBulkOperations() async {
  print('\n=== Bulk File Operations ===');
  
  var sourceDir = Directory('bulk_source');
  var targetDir = Directory('bulk_target');
  
  await sourceDir.create();
  await targetDir.create();
  
  // Create multiple source files
  var files = <File>[];
  for (int i = 1; i <= 10; i++) {
    var file = File('${sourceDir.path}/file_$i.txt');
    await file.writeAsString('Content of file $i\nLine 2\nLine 3');
    files.add(file);
  }
  
  print('Created ${files.length} source files');
  
  // Bulk copy operation
  var copyFutures = <Future>[];
  for (var file in files) {
    var fileName = file.path.split('/').last;
    var targetPath = '${targetDir.path}/$fileName';
    copyFutures.add(file.copy(targetPath));
  }
  
  await Future.wait(copyFutures);
  print('Bulk copy completed');
  
  // Verify copied files
  var copiedFiles = await targetDir.list().length;
  print('Copied files count: $copiedFiles');
  
  // Bulk rename operation
  var renameCount = 0;
  await for (var entity in targetDir.list()) {
    if (entity is File && entity.path.endsWith('.txt')) {
      var newName = entity.path.replaceAll('.txt', '_renamed.bak');
      await entity.rename(newName);
      renameCount++;
    }
  }
  print('Renamed $renameCount files');
  
  // Bulk deletion
  await sourceDir.delete(recursive: true);
  await targetDir.delete(recursive: true);
  print('Bulk cleanup completed');
}
```

Advanced file system operations combine multiple techniques for  
comprehensive file management. Recursive traversal and pattern matching  
enable sophisticated file discovery and processing workflows.  


## Random Access File Operations

Random access file operations enable reading and writing data at specific  
positions within files. This approach is essential for databases,  
indices, and binary file formats requiring precise positioning.  

```dart
import 'dart:io';
import 'dart:typed_data';

void main() async {
  var file = File('random_access.dat');
  
  try {
    // Create file with initial data
    var randomAccess = await file.open(mode: FileMode.write);
    
    // Write data at different positions
    var data1 = 'HEADER__';
    var data2 = 'MIDDLE__';
    var data3 = 'END_____';
    
    await randomAccess.writeString(data1);
    await randomAccess.setPosition(50);
    await randomAccess.writeString(data2);
    await randomAccess.setPosition(100);
    await randomAccess.writeString(data3);
    
    await randomAccess.close();
    
    // Read data from specific positions
    randomAccess = await file.open(mode: FileMode.read);
    
    // Read header
    await randomAccess.setPosition(0);
    var buffer = Uint8List(8);
    await randomAccess.readInto(buffer);
    var header = String.fromCharCodes(buffer);
    print('Header: $header');
    
    // Read middle section
    await randomAccess.setPosition(50);
    await randomAccess.readInto(buffer);
    var middle = String.fromCharCodes(buffer);
    print('Middle: $middle');
    
    // Read end section
    await randomAccess.setPosition(100);
    await randomAccess.readInto(buffer);
    var end = String.fromCharCodes(buffer);
    print('End: $end');
    
    // Get file information
    var length = await randomAccess.length();
    var position = await randomAccess.position();
    print('File length: $length bytes');
    print('Current position: $position');
    
    await randomAccess.close();
    
  } catch (e) {
    print('Error with random access: $e');
  } finally {
    if (await file.exists()) {
      await file.delete();
    }
  }
}
```

Random access operations provide precise control over file positioning  
and enable efficient data access patterns. The setPosition method allows  
jumping to any location within the file for targeted read and write  
operations.  


## File Permissions and Security

File permission management controls access rights and security for files  
and directories. Understanding permission systems is crucial for  
secure application deployment and data protection.  

```dart
import 'dart:io';

void main() async {
  var testFile = File('permission_demo.txt');
  var testDir = Directory('permission_dir');
  
  try {
    // Create test file and directory
    await testFile.writeAsString('Test content for permissions');
    await testDir.create();
    
    // Get file permissions
    var fileStat = await testFile.stat();
    var dirStat = await testDir.stat();
    
    print('=== File Permissions ===');
    print('File mode: ${fileStat.mode.toRadixString(8)}');
    print('File type: ${fileStat.type}');
    print('Directory mode: ${dirStat.mode.toRadixString(8)}');
    print('Directory type: ${dirStat.type}');
    
    // Check if file is readable/writable
    print('\n=== Permission Checks ===');
    
    // Test read access
    try {
      await testFile.readAsString();
      print('File is readable: YES');
    } catch (e) {
      print('File is readable: NO ($e)');
    }
    
    // Test write access
    try {
      await testFile.writeAsString('Additional content', mode: FileMode.append);
      print('File is writable: YES');
    } catch (e) {
      print('File is writable: NO ($e)');
    }
    
    // Platform-specific permission handling
    if (!Platform.isWindows) {
      print('\n=== Unix-style Permissions ===');
      var mode = fileStat.mode;
      
      // Extract permission bits
      var userRead = (mode & 0x100) != 0;
      var userWrite = (mode & 0x080) != 0;
      var userExecute = (mode & 0x040) != 0;
      
      var groupRead = (mode & 0x020) != 0;
      var groupWrite = (mode & 0x010) != 0;
      var groupExecute = (mode & 0x008) != 0;
      
      var otherRead = (mode & 0x004) != 0;
      var otherWrite = (mode & 0x002) != 0;
      var otherExecute = (mode & 0x001) != 0;
      
      print('User permissions: r${userRead ? 'r' : '-'}${userWrite ? 'w' : '-'}${userExecute ? 'x' : '-'}');
      print('Group permissions: ${groupRead ? 'r' : '-'}${groupWrite ? 'w' : '-'}${groupExecute ? 'x' : '-'}');
      print('Other permissions: ${otherRead ? 'r' : '-'}${otherWrite ? 'w' : '-'}${otherExecute ? 'x' : '-'}');
    }
    
    // Security considerations
    print('\n=== Security Notes ===');
    print('Always validate file paths to prevent directory traversal');
    print('Check permissions before performing operations');
    print('Use temporary directories for untrusted data');
    
  } catch (e) {
    print('Error checking permissions: $e');
  } finally {
    // Clean up
    if (await testFile.exists()) await testFile.delete();
    if (await testDir.exists()) await testDir.delete();
  }
}
```

Permission management varies by platform but follows similar principles.  
Unix-style systems use octal permission bits while Windows uses ACLs.  
Always verify permissions before attempting file operations.  


## File System Links and Shortcuts

File system links create references to files and directories without  
duplicating content. Understanding symbolic and hard links enables  
advanced file organization and space-efficient storage solutions.  

```dart
import 'dart:io';

void main() async {
  var originalFile = File('original_document.txt');
  var targetDir = Directory('links_demo');
  
  try {
    // Create original file and directory
    await originalFile.writeAsString('This is the original document content.\nLine 2 of content.');
    await targetDir.create();
    
    print('=== Creating Links ===');
    
    // Create symbolic link (if supported)
    var symbolicLink = Link('${targetDir.path}/symbolic_link.txt');
    try {
      await symbolicLink.create(originalFile.path);
      print('Symbolic link created: ${symbolicLink.path}');
      
      // Test symbolic link
      var linkTarget = await symbolicLink.target();
      print('Link target: $linkTarget');
      
      // Read through symbolic link
      var linkedFile = File(symbolicLink.path);
      var content = await linkedFile.readAsString();
      print('Content via link: ${content.split('\n').first}');
      
    } catch (e) {
      print('Symbolic links not supported: $e');
    }
    
    // Demonstrate link properties
    print('\n=== Link Properties ===');
    
    var entities = await targetDir.list().toList();
    for (var entity in entities) {
      var stat = await entity.stat();
      print('Entity: ${entity.path}');
      print('  Type: ${stat.type}');
      
      if (entity is Link) {
        try {
          var target = await entity.target();
          var exists = await entity.resolveSymbolicLinks();
          print('  Link target: $target');
          print('  Resolved path: $exists');
          
          // Check if target exists
          var targetFile = File(target);
          var targetExists = await targetFile.exists();
          print('  Target exists: $targetExists');
        } catch (e) {
          print('  Link error: $e');
        }
      }
    }
    
    // Demonstrate link behavior with file operations
    print('\n=== Link Behavior ===');
    
    if (entities.any((e) => e is Link)) {
      var link = entities.firstWhere((e) => e is Link) as Link;
      
      // Modify original file
      await originalFile.writeAsString('\nAdded via original file', mode: FileMode.append);
      
      // Read through link to verify changes
      var linkedFile = File(link.path);
      var updatedContent = await linkedFile.readAsString();
      print('Updated content via link:');
      print(updatedContent);
      
      // Delete original file and test link
      await originalFile.delete();
      
      try {
        await linkedFile.readAsString();
        print('Link still works after original deletion');
      } catch (e) {
        print('Link broken after original deletion: $e');
      }
    }
    
  } catch (e) {
    print('Error with links: $e');
  } finally {
    // Clean up
    try {
      if (await originalFile.exists()) await originalFile.delete();
      if (await targetDir.exists()) await targetDir.delete(recursive: true);
    } catch (e) {
      print('Cleanup error: $e');
    }
  }
}
```

File system links provide powerful file organization capabilities.  
Symbolic links create pointer-like references while hard links create  
multiple names for the same file content across the file system.  


## Memory-Mapped File Operations

Memory-mapped file operations enable treating files as memory regions  
for high-performance access. This technique is valuable for large files  
and applications requiring frequent random access patterns.  

```dart
import 'dart:io';
import 'dart:typed_data';

void main() async {
  await demonstrateMemoryMapping();
  await demonstrateMemoryMappedProcessing();
}

Future<void> demonstrateMemoryMapping() async {
  print('=== Memory-Mapped File Operations ===');
  
  var dataFile = File('mapped_data.bin');
  
  try {
    // Create file with binary data
    var initialData = Uint8List(1024);
    for (int i = 0; i < initialData.length; i++) {
      initialData[i] = i % 256;
    }
    
    await dataFile.writeAsBytes(initialData);
    print('Created binary file: ${initialData.length} bytes');
    
    // Open file for random access
    var randomAccess = await dataFile.open(mode: FileMode.append);
    
    // Simulate memory-mapped access patterns
    print('\n=== Simulated Memory Mapping ===');
    
    // Random access reads
    var positions = [0, 100, 200, 500, 800];
    var buffer = Uint8List(4);
    
    for (var pos in positions) {
      await randomAccess.setPosition(pos);
      await randomAccess.readInto(buffer);
      
      var value = (buffer[0] << 24) | (buffer[1] << 16) | (buffer[2] << 8) | buffer[3];
      print('Position $pos: ${buffer.join(', ')} (combined: $value)');
    }
    
    // Pattern-based modifications
    await randomAccess.setPosition(512);
    var pattern = Uint8List.fromList([0xFF, 0xAA, 0x55, 0x00]);
    await randomAccess.writeFrom(pattern);
    
    print('Modified pattern at position 512');
    
    await randomAccess.close();
    
    // Verify modifications
    var modifiedData = await dataFile.readAsBytes();
    print('Verification at 512-515: ${modifiedData.sublist(512, 516).join(', ')}');
    
  } catch (e) {
    print('Error with memory mapping: $e');
  } finally {
    if (await dataFile.exists()) await dataFile.delete();
  }
}

Future<void> demonstrateMemoryMappedProcessing() async {
  print('\n=== Memory-Mapped Processing Simulation ===');
  
  var indexFile = File('data_index.bin');
  
  try {
    // Create index file structure
    var recordSize = 32;
    var recordCount = 100;
    var indexData = Uint8List(recordCount * recordSize);
    
    // Initialize index records
    for (int i = 0; i < recordCount; i++) {
      var offset = i * recordSize;
      
      // Record ID (4 bytes)
      indexData[offset] = (i >> 24) & 0xFF;
      indexData[offset + 1] = (i >> 16) & 0xFF;
      indexData[offset + 2] = (i >> 8) & 0xFF;
      indexData[offset + 3] = i & 0xFF;
      
      // Timestamp simulation (8 bytes)
      var timestamp = DateTime.now().millisecondsSinceEpoch + i;
      for (int j = 0; j < 8; j++) {
        indexData[offset + 4 + j] = (timestamp >> (8 * (7 - j))) & 0xFF;
      }
      
      // Data size (4 bytes)
      var dataSize = 1000 + (i * 10);
      indexData[offset + 12] = (dataSize >> 24) & 0xFF;
      indexData[offset + 13] = (dataSize >> 16) & 0xFF;
      indexData[offset + 14] = (dataSize >> 8) & 0xFF;
      indexData[offset + 15] = dataSize & 0xFF;
      
      // Fill remaining with pattern
      for (int j = 16; j < recordSize; j++) {
        indexData[offset + j] = (i + j) % 256;
      }
    }
    
    await indexFile.writeAsBytes(indexData);
    print('Created index file: $recordCount records, ${indexData.length} bytes');
    
    // Simulate index lookups
    var randomAccess = await indexFile.open();
    var recordBuffer = Uint8List(recordSize);
    
    // Search for specific records
    var searchIds = [10, 25, 50, 75, 99];
    
    for (var searchId in searchIds) {
      var position = searchId * recordSize;
      await randomAccess.setPosition(position);
      await randomAccess.readInto(recordBuffer);
      
      // Parse record
      var id = (recordBuffer[0] << 24) | (recordBuffer[1] << 16) | 
               (recordBuffer[2] << 8) | recordBuffer[3];
      
      var dataSize = (recordBuffer[12] << 24) | (recordBuffer[13] << 16) | 
                     (recordBuffer[14] << 8) | recordBuffer[15];
      
      print('Record $searchId: ID=$id, DataSize=$dataSize');
    }
    
    // Performance comparison
    var stopwatch = Stopwatch()..start();
    
    // Sequential access
    await randomAccess.setPosition(0);
    for (int i = 0; i < recordCount; i++) {
      await randomAccess.readInto(recordBuffer);
      // Process record (simulated)
    }
    
    stopwatch.stop();
    print('\nSequential access: ${stopwatch.elapsedMicroseconds} microseconds');
    
    // Random access
    stopwatch.reset();
    stopwatch.start();
    
    for (int i = 0; i < recordCount; i += 10) {
      await randomAccess.setPosition(i * recordSize);
      await randomAccess.readInto(recordBuffer);
      // Process record (simulated)
    }
    
    stopwatch.stop();
    print('Random access: ${stopwatch.elapsedMicroseconds} microseconds');
    
    await randomAccess.close();
    
  } catch (e) {
    print('Error with memory-mapped processing: $e');
  } finally {
    if (await indexFile.exists()) await indexFile.delete();
  }
}
```

Memory-mapped file operations enable efficient data access patterns  
for large files. Random access capabilities combined with proper  
buffering strategies provide optimal performance for data-intensive  
applications.  


## File System Monitoring and Events

File system monitoring enables real-time detection of changes across  
multiple directories and files. Advanced monitoring supports complex  
filtering and event aggregation for comprehensive file system oversight.  

```dart
import 'dart:io';
import 'dart:async';

void main() async {
  await demonstrateAdvancedFileWatching();
  await demonstrateMultiDirectoryMonitoring();
}

Future<void> demonstrateAdvancedFileWatching() async {
  print('=== Advanced File System Monitoring ===');
  
  var monitoredDir = Directory('monitored_files');
  await monitoredDir.create();
  
  // Set up comprehensive file watcher
  var eventController = StreamController<FileSystemEvent>();
  var subscription = monitoredDir.watch(recursive: true).listen((event) {
    eventController.add(event);
  });
  
  // Event aggregation and filtering
  var eventCounts = <FileSystemEventType, int>{};
  var recentEvents = <String>[];
  
  var eventSubscription = eventController.stream.listen((event) {
    // Count events by type
    eventCounts[event.type] = (eventCounts[event.type] ?? 0) + 1;
    
    // Track recent events
    var eventDescription = '${event.type.toString().split('.').last}: ${event.path}';
    recentEvents.add(eventDescription);
    if (recentEvents.length > 10) {
      recentEvents.removeAt(0);
    }
    
    // Log significant events
    if (event.type == FileSystemEvent.create) {
      print('📄 Created: ${event.path.split('/').last}');
    } else if (event.type == FileSystemEvent.modify) {
      print('✏️  Modified: ${event.path.split('/').last}');
    } else if (event.type == FileSystemEvent.delete) {
      print('🗑️  Deleted: ${event.path.split('/').last}');
    } else if (event.type == FileSystemEvent.move && event is FileSystemMoveEvent) {
      print('📦 Moved: ${event.path} → ${event.destination}');
    }
  });
  
  // Simulate file system activity
  print('Simulating file system activity...');
  
  // Create multiple files
  for (int i = 1; i <= 5; i++) {
    var file = File('${monitoredDir.path}/test_file_$i.txt');
    await file.writeAsString('Initial content for file $i');
    await Future.delayed(Duration(milliseconds: 100));
  }
  
  // Create subdirectory with files
  var subdir = Directory('${monitoredDir.path}/subdirectory');
  await subdir.create();
  
  var subFile = File('${subdir.path}/nested_file.txt');
  await subFile.writeAsString('Nested file content');
  
  // Modify files
  for (int i = 1; i <= 3; i++) {
    var file = File('${monitoredDir.path}/test_file_$i.txt');
    await file.writeAsString('\nModification ${DateTime.now()}', mode: FileMode.append);
    await Future.delayed(Duration(milliseconds: 100));
  }
  
  // Move and rename operations
  var sourceFile = File('${monitoredDir.path}/test_file_4.txt');
  var movedFile = await sourceFile.rename('${monitoredDir.path}/renamed_file.txt');
  
  // Delete some files
  await File('${monitoredDir.path}/test_file_5.txt').delete();
  await movedFile.delete();
  
  // Allow events to process
  await Future.delayed(Duration(seconds: 1));
  
  // Display monitoring results
  print('\n=== Monitoring Results ===');
  print('Event counts:');
  eventCounts.forEach((type, count) {
    print('  ${type.toString().split('.').last}: $count');
  });
  
  print('\nRecent events:');
  for (var event in recentEvents.take(5)) {
    print('  $event');
  }
  
  // Clean up
  await eventSubscription.cancel();
  await subscription.cancel();
  await eventController.close();
  await monitoredDir.delete(recursive: true);
}

Future<void> demonstrateMultiDirectoryMonitoring() async {
  print('\n\n=== Multi-Directory Monitoring ===');
  
  var directories = [
    Directory('monitor1'),
    Directory('monitor2'),
    Directory('monitor3'),
  ];
  
  // Create directories
  for (var dir in directories) {
    await dir.create();
  }
  
  // Set up monitoring for multiple directories
  var allSubscriptions = <StreamSubscription>[];
  var globalEventCount = 0;
  
  for (var dir in directories) {
    var subscription = dir.watch().listen((event) {
      globalEventCount++;
      var dirName = dir.path;
      var fileName = event.path.split('/').last;
      var eventType = event.type.toString().split('.').last;
      
      print('[$dirName] $eventType: $fileName');
    });
    allSubscriptions.add(subscription);
  }
  
  // Simulate activity across directories
  print('Creating files across multiple directories...');
  
  for (int i = 0; i < directories.length; i++) {
    var dir = directories[i];
    
    // Create files in each directory
    for (int j = 1; j <= 3; j++) {
      var file = File('${dir.path}/file_$j.txt');
      await file.writeAsString('Content for ${dir.path}/file_$j.txt');
      await Future.delayed(Duration(milliseconds: 50));
    }
    
    // Create subdirectory
    var subDir = Directory('${dir.path}/subdir');
    await subDir.create();
    
    var nestedFile = File('${subDir.path}/nested.txt');
    await nestedFile.writeAsString('Nested in ${dir.path}');
  }
  
  await Future.delayed(Duration(milliseconds: 500));
  
  print('\nTotal events monitored: $globalEventCount');
  
  // Performance monitoring
  var startTime = DateTime.now();
  var testEventCount = 0;
  
  // Monitor performance during bulk operations
  var perfSubscription = directories[0].watch().listen((event) {
    testEventCount++;
  });
  
  // Bulk file creation for performance test
  for (int i = 0; i < 50; i++) {
    var file = File('${directories[0].path}/bulk_$i.txt');
    await file.writeAsString('Bulk file $i');
  }
  
  await Future.delayed(Duration(milliseconds: 200));
  
  var endTime = DateTime.now();
  var duration = endTime.difference(startTime);
  
  print('\nPerformance test:');
  print('  Duration: ${duration.inMilliseconds}ms');
  print('  Events: $testEventCount');
  print('  Events per second: ${(testEventCount / duration.inSeconds).toStringAsFixed(2)}');
  
  // Clean up
  await perfSubscription.cancel();
  for (var subscription in allSubscriptions) {
    await subscription.cancel();
  }
  
  for (var dir in directories) {
    await dir.delete(recursive: true);
  }
}
```

Advanced file system monitoring enables comprehensive oversight of  
file system changes. Event aggregation and filtering provide insights  
into application behavior and system activity patterns for debugging  
and optimization purposes.  


## Asynchronous File Processing Pipelines

Asynchronous file processing pipelines enable efficient handling of  
large datasets through stream composition and parallel processing.  
These patterns maximize throughput while maintaining resource control.  

```dart
import 'dart:io';
import 'dart:async';
import 'dart:convert';
import 'dart:isolate';

void main() async {
  await demonstrateStreamPipeline();
  await demonstrateParallelProcessing();
  await demonstrateBatchProcessing();
}

Future<void> demonstrateStreamPipeline() async {
  print('=== Asynchronous File Processing Pipeline ===');
  
  var inputFile = File('pipeline_input.txt');
  var outputFile = File('pipeline_output.txt');
  
  try {
    // Create input data
    var inputData = List.generate(10000, (i) => 
        'Record $i: ${DateTime.now().millisecondsSinceEpoch + i} data content here'
    ).join('\n');
    await inputFile.writeAsString(inputData);
    
    print('Created input file: ${(await inputFile.length())} bytes');
    
    // Build processing pipeline
    var stopwatch = Stopwatch()..start();
    var processedCount = 0;
    var errorCount = 0;
    
    var outputSink = outputFile.openWrite();
    
    await inputFile
        .openRead()
        .transform(utf8.decoder)
        .transform(LineSplitter())
        .where((line) => line.isNotEmpty)
        .asyncMap((line) async {
          // Simulate processing delay
          await Future.delayed(Duration(microseconds: 10));
          
          try {
            // Extract record number
            var parts = line.split(':');
            if (parts.length >= 2) {
              var recordNum = int.parse(parts[0].replaceAll('Record ', ''));
              
              // Process data (example: double the record number)
              var processed = 'Processed ${recordNum * 2}: ${parts[1].trim()}';
              return processed;
            }
            return null;
          } catch (e) {
            errorCount++;
            return null;
          }
        })
        .where((processed) => processed != null)
        .listen((processed) {
          outputSink.writeln(processed);
          processedCount++;
          
          if (processedCount % 1000 == 0) {
            print('Processed $processedCount records...');
          }
        })
        .asFuture();
    
    await outputSink.close();
    stopwatch.stop();
    
    print('\nPipeline Results:');
    print('  Processing time: ${stopwatch.elapsedMilliseconds}ms');
    print('  Records processed: $processedCount');
    print('  Errors encountered: $errorCount');
    print('  Output file size: ${await outputFile.length()} bytes');
    print('  Throughput: ${(processedCount / stopwatch.elapsedSeconds).toStringAsFixed(2)} records/sec');
    
  } catch (e) {
    print('Pipeline error: $e');
  } finally {
    if (await inputFile.exists()) await inputFile.delete();
    if (await outputFile.exists()) await outputFile.delete();
  }
}

Future<void> demonstrateParallelProcessing() async {
  print('\n=== Parallel File Processing ===');
  
  var inputFiles = <File>[];
  var outputFiles = <File>[];
  
  try {
    // Create multiple input files
    for (int i = 0; i < 4; i++) {
      var inputFile = File('parallel_input_$i.txt');
      var outputFile = File('parallel_output_$i.txt');
      
      var data = List.generate(2500, (j) => 
          'File $i Record $j: Sample data content for processing'
      ).join('\n');
      
      await inputFile.writeAsString(data);
      inputFiles.add(inputFile);
      outputFiles.add(outputFile);
    }
    
    print('Created ${inputFiles.length} input files');
    
    // Process files in parallel
    var stopwatch = Stopwatch()..start();
    
    var futures = <Future>[];
    for (int i = 0; i < inputFiles.length; i++) {
      futures.add(processFileAsync(inputFiles[i], outputFiles[i], i));
    }
    
    var results = await Future.wait(futures);
    stopwatch.stop();
    
    var totalProcessed = results.fold<int>(0, (sum, count) => sum + (count as int));
    
    print('\nParallel Processing Results:');
    print('  Total time: ${stopwatch.elapsedMilliseconds}ms');
    print('  Total records: $totalProcessed');
    print('  Average per file: ${(totalProcessed / inputFiles.length).toStringAsFixed(1)}');
    print('  Overall throughput: ${(totalProcessed / stopwatch.elapsedSeconds).toStringAsFixed(2)} records/sec');
    
  } catch (e) {
    print('Parallel processing error: $e');
  } finally {
    // Clean up
    for (var file in inputFiles) {
      if (await file.exists()) await file.delete();
    }
    for (var file in outputFiles) {
      if (await file.exists()) await file.delete();
    }
  }
}

Future<int> processFileAsync(File input, File output, int fileId) async {
  var processedCount = 0;
  var outputSink = output.openWrite();
  
  try {
    await input
        .openRead()
        .transform(utf8.decoder)
        .transform(LineSplitter())
        .asyncMap((line) async {
          // Simulate variable processing time
          await Future.delayed(Duration(microseconds: 5 + (fileId * 2)));
          
          // Transform the line
          var processed = line.toUpperCase().replaceAll('SAMPLE', 'PROCESSED');
          return 'FILE_$fileId: $processed';
        })
        .listen((processed) {
          outputSink.writeln(processed);
          processedCount++;
        })
        .asFuture();
    
  } finally {
    await outputSink.close();
  }
  
  return processedCount;
}

Future<void> demonstrateBatchProcessing() async {
  print('\n=== Batch File Processing ===');
  
  var dataFile = File('batch_data.json');
  var resultsFile = File('batch_results.json');
  
  try {
    // Create JSON data file
    var batchData = {
      'metadata': {
        'version': '1.0',
        'created': DateTime.now().toIso8601String(),
        'total_records': 5000
      },
      'records': List.generate(5000, (i) => {
        'id': i,
        'name': 'User $i',
        'email': 'user$i@example.com',
        'score': (i * 1.5).toInt() % 1000,
        'active': i % 3 == 0,
        'tags': ['tag${i % 10}', 'category${i % 5}']
      })
    };
    
    var jsonString = JsonEncoder.withIndent('  ').convert(batchData);
    await dataFile.writeAsString(jsonString);
    
    print('Created batch data file: ${(await dataFile.length())} bytes');
    
    // Process in batches
    var stopwatch = Stopwatch()..start();
    var batchSize = 500;
    var processedBatches = 0;
    var totalRecords = 0;
    var results = <String, dynamic>{
      'metadata': {
        'processed_at': DateTime.now().toIso8601String(),
        'batch_size': batchSize,
      },
      'summary': <String, dynamic>{},
      'processed_records': <Map<String, dynamic>>[]
    };
    
    // Read and parse JSON
    var jsonContent = await dataFile.readAsString();
    var data = jsonDecode(jsonContent) as Map<String, dynamic>;
    var records = data['records'] as List<dynamic>;
    
    // Process in batches
    for (int i = 0; i < records.length; i += batchSize) {
      var endIndex = (i + batchSize < records.length) ? i + batchSize : records.length;
      var batch = records.sublist(i, endIndex);
      
      // Process batch
      var batchResults = await processBatch(batch, processedBatches);
      
      // Aggregate results
      (results['processed_records'] as List).addAll(batchResults);
      totalRecords += batch.length;
      processedBatches++;
      
      print('Processed batch $processedBatches: ${batch.length} records');
      
      // Simulate batch processing delay
      await Future.delayed(Duration(milliseconds: 10));
    }
    
    stopwatch.stop();
    
    // Update summary
    results['summary'] = {
      'total_batches': processedBatches,
      'total_records': totalRecords,
      'processing_time_ms': stopwatch.elapsedMilliseconds,
      'records_per_second': (totalRecords / stopwatch.elapsedSeconds).toStringAsFixed(2),
      'average_batch_time_ms': (stopwatch.elapsedMilliseconds / processedBatches).toStringAsFixed(2)
    };
    
    // Write results
    var resultsJson = JsonEncoder.withIndent('  ').convert(results);
    await resultsFile.writeAsString(resultsJson);
    
    print('\nBatch Processing Complete:');
    print('  Batches: $processedBatches');
    print('  Records: $totalRecords');
    print('  Time: ${stopwatch.elapsedMilliseconds}ms');
    print('  Results file: ${await resultsFile.length()} bytes');
    
  } catch (e) {
    print('Batch processing error: $e');
  } finally {
    if (await dataFile.exists()) await dataFile.delete();
    if (await resultsFile.exists()) await resultsFile.delete();
  }
}

Future<List<Map<String, dynamic>>> processBatch(List<dynamic> batch, int batchId) async {
  var processed = <Map<String, dynamic>>[];
  
  for (var record in batch) {
    var rec = record as Map<String, dynamic>;
    
    // Example processing: calculate derived values
    var processedRecord = {
      'original_id': rec['id'],
      'batch_id': batchId,
      'processed_name': (rec['name'] as String).toUpperCase(),
      'email_domain': (rec['email'] as String).split('@')[1],
      'score_category': categorizeScore(rec['score'] as int),
      'is_vip': (rec['score'] as int) > 800,
      'tag_count': (rec['tags'] as List).length,
      'processed_at': DateTime.now().toIso8601String()
    };
    
    processed.add(processedRecord);
  }
  
  return processed;
}

String categorizeScore(int score) {
  if (score >= 800) return 'excellent';
  if (score >= 600) return 'good';
  if (score >= 400) return 'average';
  return 'needs_improvement';
}
```

Asynchronous file processing pipelines enable scalable data processing  
through stream composition and parallel execution. Proper batching and  
resource management ensure optimal performance for large-scale file  
operations.  