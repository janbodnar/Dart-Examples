# Dart Command-Line Applications

Command-line interface (CLI) applications are essential tools for automation,  
system administration, and developer workflows. Dart provides comprehensive  
support for building robust CLI applications through the `dart:io` library  
and ecosystem packages. These tools enable reading user input, processing  
arguments, manipulating files, and interacting with the operating system.  

Dart CLI applications can range from simple scripts to complex tools with  
sophisticated argument parsing, interactive prompts, and configuration  
management. The language's strong typing, async support, and rich standard  
library make it an excellent choice for building reliable command-line tools  
that work consistently across Windows, macOS, and Linux.  

Building CLI applications in Dart involves understanding input/output streams,  
argument parsing patterns, file system operations, and proper error handling.  
These examples demonstrate practical patterns for creating professional  
command-line tools that follow best practices and provide excellent user  
experiences.  

## Basic Hello CLI

The simplest CLI application prints output to the console. This foundational  
example demonstrates the entry point and basic output operations.  

```dart
void main() {
  print('Hello there!');
  print('Welcome to Dart CLI development');
  
  // Multiple lines
  print('Line 1');
  print('Line 2');
  print('Line 3');
  
  // Using string interpolation
  var name = 'Dart';
  print('Programming with $name');
}
```

The main function serves as the entry point for Dart applications. The print  
function writes text to stdout (standard output), which appears in the  
terminal. Each print call adds a newline automatically.  


## Reading Command-Line Arguments

Command-line arguments allow users to pass data to your application when  
launching it. These arguments provide configuration and control options.  

```dart
void main(List<String> arguments) {
  print('Number of arguments: ${arguments.length}');
  
  if (arguments.isEmpty) {
    print('No arguments provided');
    print('Usage: dart run cli.dart arg1 arg2 arg3');
    return;
  }
  
  print('Arguments received:');
  for (var i = 0; i < arguments.length; i++) {
    print('  [$i]: ${arguments[i]}');
  }
  
  // Access specific arguments
  var firstArg = arguments.first;
  var lastArg = arguments.last;
  print('\nFirst: $firstArg, Last: $lastArg');
}
```

The main function receives command-line arguments as a List<String>. Arguments  
are separated by spaces when invoking the program. This example demonstrates  
argument counting, iteration, and accessing specific arguments.  


## Exit Codes

Exit codes communicate success or failure to the shell or calling process.  
Standard convention uses 0 for success and non-zero for errors.  

```dart
import 'dart:io';

void main(List<String> arguments) {
  if (arguments.isEmpty) {
    stderr.writeln('Error: No input file specified');
    stderr.writeln('Usage: dart run program.dart <filename>');
    exit(1); // Exit with error code
  }
  
  var filename = arguments[0];
  var file = File(filename);
  
  if (!file.existsSync()) {
    stderr.writeln('Error: File not found: $filename');
    exit(2); // Different error code
  }
  
  print('Processing file: $filename');
  // Process file...
  
  print('Success!');
  exit(0); // Explicit success (optional, default)
}
```

The exit function terminates the program with a specified code. Writing to  
stderr (standard error) separates error messages from regular output. Exit  
codes help scripts and automation tools detect failures.  


## Standard Output and Error Streams

Separating normal output from error messages enables proper stream redirection  
and helps users distinguish between results and diagnostics.  

```dart
import 'dart:io';

void main() {
  // Write to stdout (standard output)
  stdout.writeln('This is normal output');
  stdout.write('No newline here');
  stdout.writeln(' - continuing same line');
  
  // Write to stderr (standard error)
  stderr.writeln('This is an error message');
  stderr.writeln('Warning: Something might be wrong');
  
  // Mix both streams
  stdout.writeln('Processing...');
  stderr.writeln('[DEBUG] Internal state logged');
  stdout.writeln('Complete!');
  
  // Flush ensures immediate output
  stdout.flush();
  stderr.flush();
}
```

The stdout and stderr streams provide explicit control over output destination.  
The write method outputs text without adding a newline, while writeln adds  
one. The flush method forces immediate output without buffering.  


## Reading User Input

Interactive CLI applications read input from users to gather information,  
make decisions, or respond to commands during execution.  

```dart
import 'dart:io';

void main() {
  stdout.write('Enter your name: ');
  var name = stdin.readLineSync();
  
  stdout.write('Enter your age: ');
  var ageInput = stdin.readLineSync();
  var age = int.tryParse(ageInput ?? '') ?? 0;
  
  print('\nHello there, $name!');
  print('You are $age years old.');
  
  if (age >= 18) {
    print('You are an adult.');
  } else {
    print('You are a minor.');
  }
}
```

The stdin.readLineSync() method reads a line of text from standard input,  
blocking until the user presses Enter. The method returns null at end-of-file,  
so null-checking is important. Input validation ensures robust handling of  
unexpected data.  


## Simple Menu System

Menu systems provide structured interaction patterns where users select from  
predefined options to navigate through application features.  

```dart
import 'dart:io';

void main() {
  while (true) {
    print('\n=== Main Menu ===');
    print('1. Option One');
    print('2. Option Two');
    print('3. Option Three');
    print('4. Exit');
    stdout.write('\nSelect an option (1-4): ');
    
    var choice = stdin.readLineSync();
    
    switch (choice) {
      case '1':
        print('\nYou selected Option One');
        break;
      case '2':
        print('\nYou selected Option Two');
        break;
      case '3':
        print('\nYou selected Option Three');
        break;
      case '4':
        print('\nGoodbye!');
        return;
      default:
        print('\nInvalid choice. Please try again.');
    }
  }
}
```

Menu systems use loops to repeatedly display options until the user chooses  
to exit. Switch statements handle different menu choices cleanly. This pattern  
is common in interactive CLI tools requiring multiple user operations.  


## Yes/No Confirmation Prompt

Confirmation prompts verify user intent before performing critical operations  
like deleting files or making irreversible changes.  

```dart
import 'dart:io';

bool confirm(String message) {
  while (true) {
    stdout.write('$message (yes/no): ');
    var response = stdin.readLineSync()?.toLowerCase().trim();
    
    if (response == 'yes' || response == 'y') {
      return true;
    } else if (response == 'no' || response == 'n') {
      return false;
    } else {
      print('Please answer yes or no.');
    }
  }
}

void main() {
  print('About to delete all files!');
  
  if (confirm('Are you sure you want to continue?')) {
    print('Proceeding with deletion...');
    // Perform destructive operation
  } else {
    print('Operation cancelled.');
  }
  
  if (confirm('Save changes?')) {
    print('Changes saved.');
  }
}
```

The confirm function encapsulates yes/no logic with validation and retry  
behavior. Converting input to lowercase and trimming whitespace makes the  
prompt more user-friendly. This pattern improves safety for destructive  
operations.  


## Argument Parsing with Flags

Command-line flags provide optional settings that modify application behavior.  
Manual parsing handles simple flag patterns effectively.  

```dart
void main(List<String> arguments) {
  var verbose = false;
  var dryRun = false;
  var outputFile = 'output.txt';
  var inputFiles = <String>[];
  
  for (var i = 0; i < arguments.length; i++) {
    var arg = arguments[i];
    
    if (arg == '--verbose' || arg == '-v') {
      verbose = true;
    } else if (arg == '--dry-run') {
      dryRun = true;
    } else if (arg == '--output' || arg == '-o') {
      if (i + 1 < arguments.length) {
        outputFile = arguments[++i];
      }
    } else if (!arg.startsWith('-')) {
      inputFiles.add(arg);
    }
  }
  
  print('Configuration:');
  print('  Verbose: $verbose');
  print('  Dry run: $dryRun');
  print('  Output: $outputFile');
  print('  Input files: $inputFiles');
}
```

This example shows manual argument parsing with boolean flags and value  
arguments. Flags beginning with dashes are interpreted as options, while  
other arguments are treated as positional parameters. Both long (--flag)  
and short (-f) forms are supported.  


## Using the args Package

The args package provides robust argument parsing with automatic help  
generation and type-safe option handling for complex CLI applications.  

```dart
import 'package:args/args.dart';

void main(List<String> arguments) {
  var parser = ArgParser()
    ..addFlag('verbose', 
        abbr: 'v', 
        help: 'Enable verbose output',
        negatable: false)
    ..addFlag('help',
        abbr: 'h',
        help: 'Show usage information',
        negatable: false)
    ..addOption('output',
        abbr: 'o',
        help: 'Output file path',
        defaultsTo: 'output.txt')
    ..addOption('count',
        abbr: 'c',
        help: 'Number of iterations',
        defaultsTo: '1');
  
  try {
    var results = parser.parse(arguments);
    
    if (results['help']) {
      print('Usage: dart run program.dart [options] [files...]');
      print('\nOptions:');
      print(parser.usage);
      return;
    }
    
    var verbose = results['verbose'] as bool;
    var output = results['output'] as String;
    var count = int.parse(results['count'] as String);
    var files = results.rest;
    
    if (verbose) {
      print('Verbose mode enabled');
    }
    print('Output: $output');
    print('Count: $count');
    print('Files: $files');
    
  } catch (e) {
    print('Error: $e');
    print('\nUsage: dart run program.dart [options]');
    print(parser.usage);
  }
}
```

The args package handles complex parsing scenarios with minimal code. It  
supports flags, options with values, abbreviations, default values, and  
automatic usage generation. The rest property contains non-option arguments.  


## Subcommands

Subcommands enable CLI applications to group related functionality under  
distinct commands, similar to git's commit, push, and pull commands.  

```dart
import 'package:args/command_runner.dart';

class InitCommand extends Command {
  @override
  final name = 'init';
  @override
  final description = 'Initialize a new project';
  
  InitCommand() {
    argParser.addOption('name', help: 'Project name');
  }
  
  @override
  void run() {
    var projectName = argResults?['name'] ?? 'my_project';
    print('Initializing project: $projectName');
  }
}

class BuildCommand extends Command {
  @override
  final name = 'build';
  @override
  final description = 'Build the project';
  
  BuildCommand() {
    argParser.addFlag('release', help: 'Release build');
  }
  
  @override
  void run() {
    var release = argResults?['release'] ?? false;
    print('Building project (release: $release)');
  }
}

void main(List<String> arguments) async {
  var runner = CommandRunner('mytool', 'A sample CLI tool')
    ..addCommand(InitCommand())
    ..addCommand(BuildCommand());
  
  try {
    await runner.run(arguments);
  } catch (e) {
    print('Error: $e');
  }
}
```

CommandRunner from the args package manages subcommands elegantly. Each  
Command subclass defines its name, description, and argument parsing. This  
pattern scales well for complex tools with multiple operations.  


## Reading Files Line by Line

Processing text files line by line is memory-efficient for large files and  
enables stream-based text processing workflows.  

```dart
import 'dart:io';

void main() async {
  var file = File('input.txt');
  
  // Create sample file
  await file.writeAsString('Line 1\nLine 2\nLine 3\nLine 4\nLine 5\n');
  
  print('Reading file line by line:');
  var lineNumber = 1;
  
  await for (var line in file.openRead()
      .transform(systemEncoding.decoder)
      .transform(LineSplitter())) {
    print('$lineNumber: $line');
    lineNumber++;
  }
  
  print('\nTotal lines: ${lineNumber - 1}');
}
```

Stream-based file reading with openRead() processes files without loading  
entire content into memory. The systemEncoding.decoder converts bytes to  
text, and LineSplitter divides text into lines. This approach handles  
gigabyte-sized files efficiently.  


## Writing Files from CLI

CLI applications often generate output files for reports, logs, or processed  
data. File writing requires proper error handling and resource management.  

```dart
import 'dart:io';

void main() async {
  var outputFile = File('report.txt');
  
  try {
    var sink = outputFile.openWrite();
    
    sink.writeln('=== Report Generated ===');
    sink.writeln('Date: ${DateTime.now()}');
    sink.writeln('');
    
    for (var i = 1; i <= 10; i++) {
      sink.writeln('Entry $i: Data value ${i * 10}');
    }
    
    await sink.flush();
    await sink.close();
    
    print('Report written to ${outputFile.path}');
    print('File size: ${await outputFile.length()} bytes');
    
  } catch (e) {
    stderr.writeln('Error writing file: $e');
    exit(1);
  }
}
```

The openWrite() method returns a stream sink for efficient writing. Writing  
line by line with writeln avoids building large strings in memory. Always  
flush and close the sink to ensure data is written and resources are released.  


## Directory Listing

Listing directory contents is essential for file management tools, search  
utilities, and backup applications that need to enumerate files.  

```dart
import 'dart:io';

void main() async {
  var directory = Directory.current;
  
  print('Contents of ${directory.path}:\n');
  
  await for (var entity in directory.list()) {
    if (entity is File) {
      var stat = await entity.stat();
      var size = stat.size;
      print('[FILE] ${entity.path} ($size bytes)');
    } else if (entity is Directory) {
      print('[DIR]  ${entity.path}');
    } else if (entity is Link) {
      print('[LINK] ${entity.path}');
    }
  }
}
```

The list() method returns a stream of file system entities. Checking entity  
types with 'is' enables different handling for files, directories, and  
symbolic links. The stat() method provides metadata like file size.  


## Recursive Directory Traversal

Recursively walking directory trees enables deep file searches, batch  
processing, and comprehensive file system analysis.  

```dart
import 'dart:io';

void main() async {
  var directory = Directory.current;
  
  print('Recursively listing ${directory.path}:\n');
  
  await for (var entity in directory.list(recursive: true)) {
    if (entity is File) {
      var relativePath = entity.path.replaceFirst(directory.path, '.');
      print('[FILE] $relativePath');
    } else if (entity is Directory) {
      var relativePath = entity.path.replaceFirst(directory.path, '.');
      print('[DIR]  $relativePath');
    }
  }
}
```

Setting recursive: true in list() traverses all subdirectories automatically.  
This simplifies deep directory processing without manual recursion. Relative  
paths improve readability by removing the base directory prefix.  


## Creating Directories

CLI tools often need to create directory structures for organizing output  
files, caching data, or setting up project scaffolding.  

```dart
import 'dart:io';

void main() async {
  // Create single directory
  var dir1 = Directory('output');
  if (!await dir1.exists()) {
    await dir1.create();
    print('Created directory: ${dir1.path}');
  }
  
  // Create nested directories
  var dir2 = Directory('data/cache/temp');
  if (!await dir2.exists()) {
    await dir2.create(recursive: true);
    print('Created nested directories: ${dir2.path}');
  }
  
  // Create with error handling
  try {
    var dir3 = Directory('logs');
    await dir3.create();
    print('Created logs directory');
  } catch (e) {
    print('Directory may already exist: $e');
  }
}
```

The create() method builds directories with optional recursive mode for nested  
structures. Checking exists() prevents errors when directories already exist.  
The recursive flag creates all parent directories automatically.  


## Environment Variables

Environment variables provide configuration without hardcoding values. CLI  
applications use them for paths, credentials, and behavioral settings.  

```dart
import 'dart:io';

void main() {
  // Read environment variables
  var path = Platform.environment['PATH'];
  var home = Platform.environment['HOME'] ?? 
             Platform.environment['USERPROFILE'];
  var user = Platform.environment['USER'] ?? 
             Platform.environment['USERNAME'];
  
  print('User: $user');
  print('Home: $home');
  print('PATH has ${path?.split(Platform.isWindows ? ';' : ':').length ?? 0} entries');
  
  // Check for custom variables
  var apiKey = Platform.environment['API_KEY'];
  if (apiKey != null) {
    print('API Key configured: ${apiKey.substring(0, 4)}...');
  } else {
    print('Warning: API_KEY not set');
  }
  
  // Get all environment variables
  print('\nTotal environment variables: ${Platform.environment.length}');
}
```

Platform.environment provides read-only access to all environment variables  
as a Map<String, String>. Different platforms use different variable names  
(HOME vs USERPROFILE), so applications should handle both.  


## Configuration File Loading

Configuration files enable users to customize CLI tool behavior without  
modifying code. Common formats include JSON, YAML, and INI.  

```dart
import 'dart:io';
import 'dart:convert';

class Config {
  final String apiUrl;
  final int timeout;
  final bool verbose;
  
  Config({
    required this.apiUrl,
    required this.timeout,
    required this.verbose,
  });
  
  factory Config.fromJson(Map<String, dynamic> json) {
    return Config(
      apiUrl: json['api_url'] ?? 'https://api.example.com',
      timeout: json['timeout'] ?? 30,
      verbose: json['verbose'] ?? false,
    );
  }
}

void main() async {
  var configFile = File('config.json');
  
  // Create default config if it doesn't exist
  if (!await configFile.exists()) {
    var defaultConfig = {
      'api_url': 'https://api.example.com',
      'timeout': 30,
      'verbose': false,
    };
    await configFile.writeAsString(
      JsonEncoder.withIndent('  ').convert(defaultConfig)
    );
    print('Created default config file');
  }
  
  // Load configuration
  try {
    var contents = await configFile.readAsString();
    var json = jsonDecode(contents);
    var config = Config.fromJson(json);
    
    print('Configuration loaded:');
    print('  API URL: ${config.apiUrl}');
    print('  Timeout: ${config.timeout}s');
    print('  Verbose: ${config.verbose}');
    
  } catch (e) {
    stderr.writeln('Error loading config: $e');
    exit(1);
  }
}
```

Configuration files separate settings from code. JSON provides a simple,  
human-readable format. The pattern creates default configuration if missing,  
loads and parses the file, and maps JSON to typed objects for safe access.  


## Progress Indicators

Long-running operations benefit from progress indicators that inform users  
about ongoing work and prevent perceived application freezing.  

```dart
import 'dart:io';

void showProgress(int current, int total) {
  var percentage = (current * 100 / total).toInt();
  var barLength = 40;
  var filledLength = (barLength * current / total).toInt();
  var bar = '=' * filledLength + ' ' * (barLength - filledLength);
  
  stdout.write('\rProgress: [$bar] $percentage% ($current/$total)');
  if (current == total) {
    stdout.writeln();
  }
}

void main() async {
  var total = 50;
  
  print('Processing $total items:');
  
  for (var i = 1; i <= total; i++) {
    // Simulate work
    await Future.delayed(Duration(milliseconds: 50));
    
    showProgress(i, total);
  }
  
  print('Complete!');
}
```

Progress bars use carriage return (\r) to overwrite the current line. The  
bar visualization provides quick visual feedback. Percentage and count give  
precise progress information. This pattern works well for batch processing  
and file operations.  


## Spinner Animation

Spinners indicate ongoing activity when progress cannot be measured precisely.  
They reassure users that the application is working, not frozen.  

```dart
import 'dart:io';

Future<void> withSpinner(String message, Future<void> Function() operation) async {
  var spinner = ['⠋', '⠙', '⠹', '⠸', '⠼', '⠴', '⠦', '⠧', '⠇', '⠏'];
  var index = 0;
  var running = true;
  
  // Spinner animation in separate async task
  Future.delayed(Duration.zero, () async {
    while (running) {
      stdout.write('\r${spinner[index]} $message');
      index = (index + 1) % spinner.length;
      await Future.delayed(Duration(milliseconds: 100));
    }
  });
  
  // Perform actual operation
  await operation();
  
  // Stop spinner
  running = false;
  await Future.delayed(Duration(milliseconds: 150));
  stdout.write('\r✓ $message\n');
}

void main() async {
  await withSpinner('Loading data...', () async {
    await Future.delayed(Duration(seconds: 2));
  });
  
  await withSpinner('Processing...', () async {
    await Future.delayed(Duration(seconds: 1));
  });
  
  print('All operations complete!');
}
```

The spinner runs concurrently with the actual operation using async execution.  
Unicode characters create smooth animation. The pattern wraps any async  
operation with visual feedback, improving user experience for network calls  
and long computations.  


## Colored Output

Terminal color support enhances CLI output readability by highlighting  
important information, warnings, and errors with visual distinction.  

```dart
import 'dart:io';

class Colors {
  static const reset = '\x1B[0m';
  static const red = '\x1B[31m';
  static const green = '\x1B[32m';
  static const yellow = '\x1B[33m';
  static const blue = '\x1B[34m';
  static const magenta = '\x1B[35m';
  static const cyan = '\x1B[36m';
  
  static const bold = '\x1B[1m';
  
  static String colorize(String text, String color) {
    return '$color$text$reset';
  }
}

void main() {
  print(Colors.colorize('Success!', Colors.green));
  print(Colors.colorize('Warning!', Colors.yellow));
  print(Colors.colorize('Error!', Colors.red));
  print(Colors.colorize('Info', Colors.blue));
  
  print('\n${Colors.bold}${Colors.cyan}=== System Status ===${Colors.reset}');
  print('${Colors.green}✓${Colors.reset} Service running');
  print('${Colors.yellow}!${Colors.reset} Low disk space');
  print('${Colors.red}✗${Colors.reset} Database connection failed');
  
  // Check if terminal supports colors
  if (stdout.supportsAnsiEscapes) {
    print('\n${Colors.colorize('Terminal supports colors!', Colors.magenta)}');
  }
}
```

ANSI escape codes control terminal colors and formatting. The reset code  
restores default styling. Check stdout.supportsAnsiEscapes before using  
colors to ensure compatibility. Colors significantly improve error visibility  
and output organization.  


## Error Handling Patterns

Robust error handling ensures CLI applications fail gracefully with helpful  
messages rather than cryptic stack traces or silent failures.  

```dart
import 'dart:io';

class CliError {
  final String message;
  final int exitCode;
  
  CliError(this.message, {this.exitCode = 1});
}

void processFile(String path) {
  var file = File(path);
  
  if (!file.existsSync()) {
    throw CliError('File not found: $path', exitCode: 2);
  }
  
  try {
    var contents = file.readAsStringSync();
    if (contents.isEmpty) {
      throw CliError('File is empty: $path', exitCode: 3);
    }
    print('Processing ${contents.length} bytes');
  } on FileSystemException catch (e) {
    throw CliError('Cannot read file: ${e.message}', exitCode: 4);
  }
}

void main(List<String> arguments) {
  try {
    if (arguments.isEmpty) {
      throw CliError('No file specified\nUsage: dart run program.dart <file>');
    }
    
    processFile(arguments[0]);
    print('Success!');
    
  } on CliError catch (e) {
    stderr.writeln('Error: ${e.message}');
    exit(e.exitCode);
  } catch (e, stackTrace) {
    stderr.writeln('Unexpected error: $e');
    stderr.writeln(stackTrace);
    exit(99);
  }
}
```

Custom error classes provide structured error information with descriptive  
messages and appropriate exit codes. Separating expected errors (CliError)  
from unexpected ones improves debugging and user experience. Different exit  
codes help scripts identify specific failure modes.  


## Logging Levels

Structured logging with severity levels enables detailed diagnostics during  
development while keeping production output clean and focused.  

```dart
import 'dart:io';

enum LogLevel {
  debug,
  info,
  warning,
  error,
}

class Logger {
  LogLevel level;
  
  Logger(this.level);
  
  void debug(String message) {
    if (level.index <= LogLevel.debug.index) {
      print('[DEBUG] $message');
    }
  }
  
  void info(String message) {
    if (level.index <= LogLevel.info.index) {
      print('[INFO] $message');
    }
  }
  
  void warning(String message) {
    if (level.index <= LogLevel.warning.index) {
      stderr.writeln('[WARNING] $message');
    }
  }
  
  void error(String message) {
    if (level.index <= LogLevel.error.index) {
      stderr.writeln('[ERROR] $message');
    }
  }
}

void main(List<String> arguments) {
  var verbose = arguments.contains('--verbose');
  var logger = Logger(verbose ? LogLevel.debug : LogLevel.info);
  
  logger.debug('Starting application');
  logger.info('Processing data');
  logger.debug('Loading configuration file');
  logger.warning('Configuration file not found, using defaults');
  logger.info('Application ready');
  logger.error('Failed to connect to database');
}
```

Log levels filter messages based on importance. Debug and info go to stdout,  
while warnings and errors use stderr. The verbose flag controls detail level.  
This pattern scales from simple scripts to complex applications requiring  
comprehensive logging.  


## File Pattern Matching

CLI tools often need to process multiple files matching specific patterns,  
such as all .txt files or files with specific naming conventions.  

```dart
import 'dart:io';

void main() async {
  var directory = Directory.current;
  
  // Find all Dart files
  print('Dart files:');
  await for (var entity in directory.list(recursive: true)) {
    if (entity is File && entity.path.endsWith('.dart')) {
      print('  ${entity.path}');
    }
  }
  
  // Find files matching multiple patterns
  print('\nMarkdown and text files:');
  var patterns = ['.md', '.txt'];
  await for (var entity in directory.list(recursive: true)) {
    if (entity is File) {
      if (patterns.any((pattern) => entity.path.endsWith(pattern))) {
        print('  ${entity.path}');
      }
    }
  }
  
  // Find by name pattern
  print('\nTest files:');
  await for (var entity in directory.list(recursive: true)) {
    if (entity is File && entity.path.contains('test')) {
      print('  ${entity.path}');
    }
  }
}
```

Pattern matching filters files during traversal. Simple patterns use endsWith  
or contains, while complex patterns might use regular expressions. Processing  
multiple files enables batch operations like conversion, analysis, or cleanup.  


## Temporary Files and Cleanup

Temporary files provide scratch space for intermediate data. Proper cleanup  
prevents disk space leaks and maintains system hygiene.  

```dart
import 'dart:io';

void main() async {
  // Get system temp directory
  var tempDir = Directory.systemTemp;
  print('System temp: ${tempDir.path}');
  
  // Create temporary file
  var tempFile = await File('${tempDir.path}/myapp_${DateTime.now().millisecondsSinceEpoch}.tmp')
      .create();
  print('Created temp file: ${tempFile.path}');
  
  try {
    // Use temporary file
    await tempFile.writeAsString('Temporary data');
    var contents = await tempFile.readAsString();
    print('Temp file contents: $contents');
    
  } finally {
    // Always clean up
    if (await tempFile.exists()) {
      await tempFile.delete();
      print('Temp file deleted');
    }
  }
  
  // Create temporary directory
  var tempWorkDir = await Directory('${tempDir.path}/myapp_work_${DateTime.now().millisecondsSinceEpoch}')
      .create();
  print('Created temp directory: ${tempWorkDir.path}');
  
  try {
    // Use temp directory
    var file1 = File('${tempWorkDir.path}/data.txt');
    await file1.writeAsString('Some data');
    
  } finally {
    // Clean up directory and contents
    if (await tempWorkDir.exists()) {
      await tempWorkDir.delete(recursive: true);
      print('Temp directory deleted');
    }
  }
}
```

Directory.systemTemp provides the platform's temporary directory. Timestamps  
in filenames prevent collisions. Try-finally ensures cleanup even if  
operations fail. The recursive flag deletes directories with their contents.  


## File Copying and Moving

File management operations like copying and moving enable backup tools,  
file organizers, and data migration utilities.  

```dart
import 'dart:io';

void main() async {
  // Create source file
  var source = File('source.txt');
  await source.writeAsString('This is the source file content.');
  
  // Copy file
  var destination = File('destination.txt');
  await source.copy(destination.path);
  print('Copied ${source.path} to ${destination.path}');
  
  // Verify copy
  var destContent = await destination.readAsString();
  print('Destination contents: $destContent');
  
  // Move/rename file
  var newPath = 'renamed.txt';
  await destination.rename(newPath);
  print('Renamed ${destination.path} to $newPath');
  
  // Check original is gone
  print('Destination exists: ${await destination.exists()}');
  print('Renamed exists: ${await File(newPath).exists()}');
  
  // Cleanup
  await source.delete();
  await File(newPath).delete();
  print('Cleanup complete');
}
```

The copy method duplicates file contents to a new location. The rename method  
moves or renames files atomically on most systems. These operations are  
building blocks for file management utilities and backup systems.  


## File Permissions Check

Checking file permissions before operations prevents runtime errors and  
enables proper error messages when access is denied.  

```dart
import 'dart:io';

void main() async {
  var file = File('test.txt');
  
  // Create test file
  await file.writeAsString('Test content');
  
  // Check existence
  var exists = await file.exists();
  print('File exists: $exists');
  
  if (exists) {
    // Get file stats
    var stat = await file.stat();
    
    print('File type: ${stat.type}');
    print('Modified: ${stat.modified}');
    print('Accessed: ${stat.accessed}');
    print('Changed: ${stat.changed}');
    print('Size: ${stat.size} bytes');
    print('Mode: ${stat.mode.toRadixString(8)}');
    
    // Check if we can read/write
    try {
      await file.readAsString();
      print('File is readable');
    } catch (e) {
      print('File is not readable: $e');
    }
    
    try {
      await file.writeAsString('test', mode: FileMode.append);
      print('File is writable');
    } catch (e) {
      print('File is not writable: $e');
    }
  }
  
  // Cleanup
  await file.delete();
}
```

The stat() method returns file metadata including size, timestamps, and mode.  
Testing operations in try-catch blocks verifies actual access permissions.  
This information helps tools provide accurate error messages and avoid  
failed operations.  


## Command Execution

CLI applications often need to execute external commands for integration  
with system tools, version control, or other utilities.  

```dart
import 'dart:io';

void main() async {
  print('=== Running External Commands ===\n');
  
  // Simple command execution
  var result = await Process.run('echo', ['Hello there!']);
  print('Exit code: ${result.exitCode}');
  print('Output: ${result.stdout}');
  
  // List directory
  var lsResult = await Process.run(
    Platform.isWindows ? 'cmd' : 'ls',
    Platform.isWindows ? ['/c', 'dir'] : ['-la']
  );
  print('Directory listing:');
  print(lsResult.stdout);
  
  // Handle errors
  var failResult = await Process.run('nonexistent_command', []);
  if (failResult.exitCode != 0) {
    print('Command failed:');
    print('Exit code: ${failResult.exitCode}');
    print('Error: ${failResult.stderr}');
  }
  
  // Capture both stdout and stderr
  var gitResult = await Process.run('git', ['status']);
  if (gitResult.exitCode == 0) {
    print('Git status:');
    print(gitResult.stdout);
  } else {
    print('Git error: ${gitResult.stderr}');
  }
}
```

Process.run executes commands and waits for completion. The result contains  
exit code, stdout, and stderr. Platform.isWindows enables cross-platform  
command execution. This pattern integrates CLI tools with the broader  
system ecosystem.  


## Interactive Process Communication

Interactive processes enable real-time communication with long-running  
commands, providing input and receiving output as the process executes.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  print('Starting interactive process...\n');
  
  // Start process
  var process = await Process.start('cat', []);
  
  // Listen to output
  process.stdout
      .transform(utf8.decoder)
      .listen((data) {
    print('Received: $data');
  });
  
  process.stderr
      .transform(utf8.decoder)
      .listen((data) {
    stderr.write('Error: $data');
  });
  
  // Send input
  process.stdin.writeln('Hello there!');
  await Future.delayed(Duration(milliseconds: 100));
  
  process.stdin.writeln('This is a test.');
  await Future.delayed(Duration(milliseconds: 100));
  
  process.stdin.writeln('Goodbye!');
  await Future.delayed(Duration(milliseconds: 100));
  
  // Close input and wait for process
  await process.stdin.close();
  var exitCode = await process.exitCode;
  print('\nProcess exited with code: $exitCode');
}
```

Process.start returns a running process with stdin, stdout, and stderr  
streams. This enables bidirectional communication for interactive scenarios.  
Listening to output streams processes data as it arrives. Closing stdin  
signals end-of-input to the process.  


## Signal Handling

Signal handling enables graceful shutdown when users press Ctrl+C or when  
the system terminates the application.  

```dart
import 'dart:io';

void main() async {
  var running = true;
  
  // Handle Ctrl+C (SIGINT)
  ProcessSignal.sigint.watch().listen((signal) {
    print('\nReceived signal: $signal');
    print('Shutting down gracefully...');
    running = false;
  });
  
  // Handle SIGTERM
  if (!Platform.isWindows) {
    ProcessSignal.sigterm.watch().listen((signal) {
      print('\nReceived SIGTERM');
      print('Cleaning up...');
      running = false;
    });
  }
  
  print('Application running. Press Ctrl+C to stop.');
  print('Processing...');
  
  var count = 0;
  while (running) {
    await Future.delayed(Duration(seconds: 1));
    count++;
    print('Working... ($count seconds)');
    
    if (count >= 30) {
      print('Timeout reached');
      break;
    }
  }
  
  print('Cleanup complete. Exiting.');
}
```

ProcessSignal.watch() provides a stream of signal events. Handling SIGINT  
(Ctrl+C) and SIGTERM enables cleanup before exit. This pattern is essential  
for applications managing resources like files, network connections, or  
background tasks.  


## Performance Timing

Measuring execution time helps identify bottlenecks and provides users with  
feedback about operation duration.  

```dart
import 'dart:io';

void main() async {
  print('=== Performance Timing ===\n');
  
  // Time single operation
  var start = DateTime.now();
  
  await Future.delayed(Duration(seconds: 1));
  
  var end = DateTime.now();
  var duration = end.difference(start);
  print('Operation took: ${duration.inMilliseconds}ms');
  
  // Time multiple operations
  var operations = ['Load data', 'Process', 'Save results'];
  var timings = <String, Duration>{};
  
  for (var op in operations) {
    var opStart = DateTime.now();
    
    // Simulate work
    await Future.delayed(Duration(milliseconds: 500));
    
    var opEnd = DateTime.now();
    timings[op] = opEnd.difference(opStart);
    print('$op: ${timings[op]!.inMilliseconds}ms');
  }
  
  // Total time
  var total = timings.values.reduce((a, b) => a + b);
  print('\nTotal time: ${total.inMilliseconds}ms');
  
  // Using Stopwatch for high-precision timing
  var stopwatch = Stopwatch()..start();
  
  for (var i = 0; i < 1000000; i++) {
    // Some computation
    var x = i * i;
  }
  
  stopwatch.stop();
  print('\nComputation time: ${stopwatch.elapsedMilliseconds}ms');
  print('Computation time: ${stopwatch.elapsedMicroseconds}μs');
}
```

DateTime.now() provides timestamps for measuring durations. Stopwatch offers  
higher precision for micro-benchmarking. Timing information helps optimize  
performance and sets user expectations for long operations.  


## HTTP Requests from CLI

CLI tools often need to interact with web APIs for data retrieval,  
authentication, or integration with cloud services.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  var client = HttpClient();
  
  try {
    print('Making HTTP request...\n');
    
    // GET request
    var request = await client.getUrl(Uri.parse('https://api.github.com/users/dart-lang'));
    var response = await request.close();
    
    print('Status code: ${response.statusCode}');
    print('Headers:');
    response.headers.forEach((name, values) {
      print('  $name: ${values.join(', ')}');
    });
    
    if (response.statusCode == 200) {
      var responseBody = await response.transform(utf8.decoder).join();
      var data = jsonDecode(responseBody);
      
      print('\nUser data:');
      print('  Name: ${data['name']}');
      print('  Bio: ${data['bio']}');
      print('  Public repos: ${data['public_repos']}');
      print('  Followers: ${data['followers']}');
    } else {
      print('Error: ${response.reasonPhrase}');
    }
    
  } catch (e) {
    stderr.writeln('Request failed: $e');
  } finally {
    client.close();
  }
}
```

HttpClient provides HTTP functionality without external dependencies. The  
pattern opens a connection, sends a request, receives the response, and  
processes the body. Always close the client to release resources. This  
enables CLI tools to interact with REST APIs and web services.  


## JSON Data Processing

CLI tools frequently process JSON data from files, APIs, or configuration.  
Parsing and generating JSON is a common requirement.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  // Generate JSON data
  var data = {
    'name': 'Sample Project',
    'version': '1.0.0',
    'dependencies': ['package1', 'package2', 'package3'],
    'config': {
      'debug': true,
      'timeout': 30,
    }
  };
  
  // Write JSON to file
  var file = File('data.json');
  var encoder = JsonEncoder.withIndent('  ');
  await file.writeAsString(encoder.convert(data));
  print('JSON written to file');
  
  // Read and parse JSON
  var contents = await file.readAsString();
  var parsed = jsonDecode(contents) as Map<String, dynamic>;
  
  print('\nParsed data:');
  print('Name: ${parsed['name']}');
  print('Version: ${parsed['version']}');
  print('Dependencies: ${parsed['dependencies']}');
  print('Debug mode: ${parsed['config']['debug']}');
  
  // Modify and save
  parsed['version'] = '1.0.1';
  (parsed['dependencies'] as List).add('package4');
  
  await file.writeAsString(encoder.convert(parsed));
  print('\nUpdated JSON saved');
  
  // Cleanup
  await file.delete();
}
```

JsonEncoder.withIndent creates readable, formatted JSON. The jsonDecode  
function parses JSON strings into Dart objects. Type casting ensures safe  
access to nested data. This pattern supports configuration management and  
data interchange.  


## Building Executable CLI Applications

Compiling Dart CLI applications to native executables creates standalone  
tools that run without requiring the Dart SDK installation.  

```dart
// save as bin/mytool.dart
import 'dart:io';

void main(List<String> arguments) {
  print('MyTool v1.0.0');
  
  if (arguments.isEmpty) {
    print('Usage: mytool <command>');
    print('Commands:');
    print('  version  - Show version information');
    print('  help     - Show this help message');
    exit(0);
  }
  
  switch (arguments[0]) {
    case 'version':
      print('Version: 1.0.0');
      print('Dart SDK: ${Platform.version}');
      break;
    case 'help':
      print('MyTool - A sample CLI application');
      print('For more info: https://example.com/mytool');
      break;
    default:
      stderr.writeln('Unknown command: ${arguments[0]}');
      exit(1);
  }
}

// To compile:
// dart compile exe bin/mytool.dart -o mytool
// 
// This creates a native executable that can be distributed
// and run without Dart SDK installed.
//
// The executable includes the Dart runtime, making it
// a fully standalone application.
```

The dart compile exe command produces native executables for the current  
platform. Place the main file in bin/ directory for convention. Compiled  
executables are portable within the same platform and include the Dart  
runtime, making distribution straightforward.  


## Package Structure for CLI Tools

Organizing CLI applications as packages enables code reuse, testing, and  
distribution through pub.dev for easy installation.  

```dart
// Project structure:
// my_cli_tool/
//   bin/
//     mytool.dart          - Executable entry point
//   lib/
//     src/
//       commands.dart      - Command implementations
//       utils.dart         - Utility functions
//     mytool.dart          - Public API
//   test/
//     commands_test.dart   - Tests
//   pubspec.yaml          - Package configuration
//   README.md             - Documentation

// bin/mytool.dart
import 'package:my_cli_tool/mytool.dart';

void main(List<String> arguments) {
  runCli(arguments);
}

// lib/mytool.dart
import 'dart:io';
import 'src/commands.dart';
import 'src/utils.dart';

void runCli(List<String> arguments) {
  if (arguments.isEmpty) {
    printUsage();
    exit(0);
  }
  
  var command = arguments[0];
  var args = arguments.sublist(1);
  
  switch (command) {
    case 'init':
      initCommand(args);
      break;
    case 'build':
      buildCommand(args);
      break;
    default:
      stderr.writeln('Unknown command: $command');
      exit(1);
  }
}

// lib/src/commands.dart
void initCommand(List<String> args) {
  print('Initializing project...');
  // Implementation
}

void buildCommand(List<String> args) {
  print('Building project...');
  // Implementation
}

// lib/src/utils.dart
void printUsage() {
  print('Usage: mytool <command> [options]');
  print('Commands:');
  print('  init   - Initialize new project');
  print('  build  - Build the project');
}

// pubspec.yaml configuration:
// name: my_cli_tool
// description: A sample CLI tool
// version: 1.0.0
// executables:
//   mytool: mytool
//
// Users can install with: dart pub global activate my_cli_tool
// Then run with: mytool <command>
```

Proper package structure separates entry points (bin/), implementation (lib/),  
and tests (test/). The executables section in pubspec.yaml defines command  
names. This structure enables publishing to pub.dev and global installation  
with dart pub global activate.  


## Testing CLI Applications

Testing ensures CLI tools work correctly across different inputs and  
scenarios. Dart's test package provides comprehensive testing capabilities.  

```dart
// lib/src/calculator.dart
int add(int a, int b) => a + b;
int subtract(int a, int b) => a - b;

// test/calculator_test.dart
import 'package:test/test.dart';
import 'package:my_cli_tool/src/calculator.dart';

void main() {
  group('Calculator', () {
    test('addition works correctly', () {
      expect(add(2, 3), equals(5));
      expect(add(-1, 1), equals(0));
      expect(add(0, 0), equals(0));
    });
    
    test('subtraction works correctly', () {
      expect(subtract(5, 3), equals(2));
      expect(subtract(1, 1), equals(0));
      expect(subtract(0, 5), equals(-5));
    });
  });
  
  group('Edge cases', () {
    test('handles large numbers', () {
      expect(add(1000000, 2000000), equals(3000000));
    });
    
    test('handles negative numbers', () {
      expect(add(-10, -5), equals(-15));
      expect(subtract(-10, -5), equals(-5));
    });
  });
}

// Run tests with: dart test
// 
// For integration testing CLI output:
import 'dart:io';

void main() {
  test('CLI produces correct output', () async {
    var result = await Process.run('dart', ['run', 'bin/mytool.dart', 'version']);
    expect(result.exitCode, equals(0));
    expect(result.stdout, contains('Version'));
  });
  
  test('CLI handles invalid command', () async {
    var result = await Process.run('dart', ['run', 'bin/mytool.dart', 'invalid']);
    expect(result.exitCode, isNot(0));
    expect(result.stderr, contains('Unknown command'));
  });
}
```

Unit tests verify function behavior in isolation. Integration tests execute  
the CLI application and verify output and exit codes. The test package  
provides expect assertions and test grouping. Process.run enables testing  
CLI behavior end-to-end. Testing ensures reliability as applications grow.  


## CSV File Processing

CSV files are common for data import/export in CLI tools. Parsing and  
generating CSV enables data transformation and reporting workflows.  

```dart
import 'dart:io';

void main() async {
  // Write CSV file
  var csvFile = File('data.csv');
  var sink = csvFile.openWrite();
  
  // Write header
  sink.writeln('Name,Age,City');
  
  // Write data rows
  sink.writeln('Alice,30,New York');
  sink.writeln('Bob,25,London');
  sink.writeln('Charlie,35,Tokyo');
  sink.writeln('Diana,28,Paris');
  
  await sink.close();
  print('CSV file created');
  
  // Read and parse CSV
  var lines = await csvFile.readAsLines();
  
  print('\nParsing CSV:');
  for (var i = 0; i < lines.length; i++) {
    var parts = lines[i].split(',');
    
    if (i == 0) {
      print('Headers: ${parts.join(' | ')}');
      print('-' * 40);
    } else {
      print('${parts[0]}: ${parts[1]} years old, lives in ${parts[2]}');
    }
  }
  
  // Filter data
  print('\nPeople over 28:');
  for (var i = 1; i < lines.length; i++) {
    var parts = lines[i].split(',');
    var age = int.parse(parts[1]);
    
    if (age > 28) {
      print('  ${parts[0]} (${parts[1]})');
    }
  }
  
  // Cleanup
  await csvFile.delete();
}
```

CSV processing splits lines by commas to extract fields. The first line  
typically contains headers. Filtering and transformation operations process  
rows based on field values. This pattern supports data analysis and reporting  
in CLI tools.  


## Table Formatting

Displaying data in formatted tables improves readability for CLI output,  
especially when showing multiple columns of information.  

```dart
void printTable(List<String> headers, List<List<String>> rows) {
  // Calculate column widths
  var widths = List.generate(headers.length, (i) => headers[i].length);
  
  for (var row in rows) {
    for (var i = 0; i < row.length && i < widths.length; i++) {
      if (row[i].length > widths[i]) {
        widths[i] = row[i].length;
      }
    }
  }
  
  // Print header
  var headerLine = '';
  for (var i = 0; i < headers.length; i++) {
    headerLine += headers[i].padRight(widths[i] + 2);
  }
  print(headerLine);
  
  // Print separator
  var separator = '';
  for (var width in widths) {
    separator += '-' * (width + 2);
  }
  print(separator);
  
  // Print rows
  for (var row in rows) {
    var line = '';
    for (var i = 0; i < row.length; i++) {
      line += row[i].padRight(widths[i] + 2);
    }
    print(line);
  }
}

void main() {
  var headers = ['Name', 'Age', 'City', 'Status'];
  var rows = [
    ['Alice', '30', 'New York', 'Active'],
    ['Bob', '25', 'London', 'Active'],
    ['Charlie', '35', 'Tokyo', 'Inactive'],
    ['Diana', '28', 'Paris', 'Active'],
  ];
  
  print('User Information:');
  printTable(headers, rows);
  
  print('\nProduct Inventory:');
  printTable(
    ['Product', 'SKU', 'Stock', 'Price'],
    [
      ['Widget A', 'WDG-001', '150', '\$19.99'],
      ['Widget B', 'WDG-002', '75', '\$29.99'],
      ['Gadget X', 'GDG-100', '200', '\$49.99'],
    ],
  );
}
```

Table formatting calculates column widths based on content length. The  
padRight method aligns columns consistently. Separators visually distinguish  
headers from data. This pattern presents structured data clearly in terminal  
output.  


## Configuration with Command-Line Override

Combining configuration files with command-line overrides provides flexible  
configuration management for CLI applications.  

```dart
import 'dart:io';
import 'dart:convert';

class AppConfig {
  String host;
  int port;
  bool debug;
  int timeout;
  
  AppConfig({
    this.host = 'localhost',
    this.port = 8080,
    this.debug = false,
    this.timeout = 30,
  });
  
  factory AppConfig.fromJson(Map<String, dynamic> json) {
    return AppConfig(
      host: json['host'] ?? 'localhost',
      port: json['port'] ?? 8080,
      debug: json['debug'] ?? false,
      timeout: json['timeout'] ?? 30,
    );
  }
  
  void applyOverrides(List<String> arguments) {
    for (var i = 0; i < arguments.length; i++) {
      var arg = arguments[i];
      
      if (arg == '--host' && i + 1 < arguments.length) {
        host = arguments[++i];
      } else if (arg == '--port' && i + 1 < arguments.length) {
        port = int.tryParse(arguments[++i]) ?? port;
      } else if (arg == '--debug') {
        debug = true;
      } else if (arg == '--timeout' && i + 1 < arguments.length) {
        timeout = int.tryParse(arguments[++i]) ?? timeout;
      }
    }
  }
  
  @override
  String toString() {
    return 'Config(host: $host, port: $port, debug: $debug, timeout: ${timeout}s)';
  }
}

void main(List<String> arguments) async {
  var config = AppConfig();
  
  // Try to load from config file
  var configFile = File('app-config.json');
  if (await configFile.exists()) {
    try {
      var contents = await configFile.readAsString();
      var json = jsonDecode(contents);
      config = AppConfig.fromJson(json);
      print('Loaded configuration from file');
    } catch (e) {
      print('Warning: Could not load config file: $e');
    }
  }
  
  // Apply command-line overrides
  config.applyOverrides(arguments);
  
  print('\nFinal configuration:');
  print(config);
  
  print('\nStarting application with above configuration...');
}
```

Configuration loading starts with defaults, loads from files if available,  
and applies command-line overrides last. This layered approach provides  
flexibility while maintaining simplicity. Command-line arguments take  
precedence for temporary overrides.  


## Piping and Stream Processing

CLI tools often work with pipes, reading from stdin and writing to stdout  
to integrate seamlessly with Unix-style command pipelines.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  // Check if input is from a pipe
  if (stdin.hasTerminal) {
    print('Usage: cat file.txt | dart run process.dart');
    print('       echo "text" | dart run process.dart');
    exit(1);
  }
  
  print('Reading from stdin...', stderr);
  
  var lineCount = 0;
  var wordCount = 0;
  var charCount = 0;
  
  // Process input line by line
  await for (var line in stdin
      .transform(utf8.decoder)
      .transform(LineSplitter())) {
    lineCount++;
    wordCount += line.split(RegExp(r'\s+')).where((w) => w.isNotEmpty).length;
    charCount += line.length;
    
    // Transform and output (uppercase in this example)
    print(line.toUpperCase());
  }
  
  // Write statistics to stderr (doesn't pollute pipeline output)
  stderr.writeln('');
  stderr.writeln('Statistics:');
  stderr.writeln('  Lines: $lineCount');
  stderr.writeln('  Words: $wordCount');
  stderr.writeln('  Characters: $charCount');
}
```

Stream processing transforms data flowing through pipes. The stdin.hasTerminal  
check detects if input comes from a pipe or terminal. Writing transformed  
data to stdout enables chaining with other commands. Statistics go to stderr  
to separate diagnostic output from data.  


## Async Batch Processing

Processing multiple items concurrently with async operations improves  
performance for I/O-bound batch operations in CLI tools.  

```dart
import 'dart:io';

Future<void> processFile(String filename) async {
  print('Processing $filename...');
  
  // Simulate processing time
  await Future.delayed(Duration(milliseconds: 500));
  
  var file = File(filename);
  if (await file.exists()) {
    var size = await file.length();
    print('  $filename: $size bytes');
  } else {
    print('  $filename: not found');
  }
}

void main() async {
  // Create sample files
  for (var i = 1; i <= 5; i++) {
    var file = File('file$i.txt');
    await file.writeAsString('Content for file $i\n' * 10);
  }
  
  var files = [
    'file1.txt',
    'file2.txt',
    'file3.txt',
    'file4.txt',
    'file5.txt',
  ];
  
  print('=== Sequential Processing ===');
  var start = DateTime.now();
  
  for (var file in files) {
    await processFile(file);
  }
  
  var sequential = DateTime.now().difference(start);
  print('Sequential time: ${sequential.inMilliseconds}ms\n');
  
  print('=== Concurrent Processing ===');
  start = DateTime.now();
  
  await Future.wait(files.map((file) => processFile(file)));
  
  var concurrent = DateTime.now().difference(start);
  print('Concurrent time: ${concurrent.inMilliseconds}ms');
  
  print('\nSpeedup: ${(sequential.inMilliseconds / concurrent.inMilliseconds).toStringAsFixed(2)}x');
  
  // Cleanup
  for (var i = 1; i <= 5; i++) {
    await File('file$i.txt').delete();
  }
}
```

Future.wait processes multiple operations concurrently, reducing total time  
for I/O-bound tasks. Sequential processing waits for each operation to  
complete before starting the next. Concurrent processing starts all operations  
simultaneously, achieving better throughput for independent tasks.  
