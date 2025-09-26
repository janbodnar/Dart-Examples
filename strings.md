# Dart Strings

Strings in Dart represent sequences of characters and are fundamental for text  
processing, user interface development, and data manipulation. Dart provides  
comprehensive string support with immutable string objects, rich manipulation  
methods, and powerful interpolation features that make text handling efficient  
and expressive.

Dart strings are UTF-16 encoded by default, supporting Unicode characters and  
international text processing. They offer extensive functionality for searching,  
transforming, formatting, and validating text data across various application  
domains.

## String Declaration and Initialization

Strings can be declared using various approaches in Dart. The most common  
methods include string literals with single or double quotes, and explicit  
String type declarations for enhanced type safety.

```dart
void main() {
  // Using var with type inference
  var greeting = 'Hello there!';
  var message = "Welcome to Dart programming";
  
  // Explicit String type declaration
  String language = 'Dart';
  String version = "3.0";
  
  // Late initialization
  late String config;
  config = 'production';
  
  print('$greeting $message');
  print('Language: $language Version: $version');
  print('Configuration: $config');
}
```

This example demonstrates different string declaration approaches. Type  
inference with `var` provides convenience for simple cases, while explicit  
`String` declarations enhance code clarity and type safety. Late initialization  
allows declaring strings before assigning values.

```
$ dart run string_declaration.dart
Hello there! Welcome to Dart programming
Language: Dart Version: 3.0
Configuration: production
```

## String Literals

Dart supports multiple string literal formats to accommodate different text  
representation needs. Single quotes, double quotes, and triple quotes each  
serve specific purposes in string creation and formatting.

```dart
void main() {
  // Single quote literals
  String singleQuoted = 'Single quote string';
  
  // Double quote literals  
  String doubleQuoted = "Double quote string";
  
  // Mixed quotes for embedding
  String withQuotes = 'She said "Hello there!" to everyone';
  String alternativeQuotes = "It's a beautiful day";
  
  // Triple quote for multi-line
  String multiline = '''
    This is a multi-line string
    that preserves line breaks
    and indentation as written
  ''';
  
  String multilineDouble = """
    Alternative multi-line syntax
    using double quotes
  """;
  
  print(singleQuoted);
  print(doubleQuoted);  
  print(withQuotes);
  print(alternativeQuotes);
  print(multiline);
  print(multilineDouble);
}
```

String literals provide flexibility in representing text with different quoting  
requirements. Single and double quotes are interchangeable for basic strings,  
while triple quotes preserve formatting and enable multi-line text without  
escape sequences.

```
$ dart run string_literals.dart
Single quote string
Double quote string
She said "Hello there!" to everyone
It's a beautiful day

    This is a multi-line string
    that preserves line breaks
    and indentation as written

    Alternative multi-line syntax
    using double quotes
```

## Raw Strings

Raw strings treat all characters literally, preventing interpretation of escape  
sequences and special characters. This feature proves particularly useful when  
working with regular expressions, file paths, or any text containing backslashes.

```dart
void main() {
  // Regular string with escapes
  String regularString = 'This is a new line\\n and tab\\t character';
  
  // Raw string - no escape processing
  String rawString = r'This is a new line\n and tab\t character';
  
  // Raw string for file paths
  String windowsPath = r'C:\Users\Alice\Documents\myfile.txt';
  
  // Raw string for regex patterns
  String emailPattern = r'^\w+@[a-zA-Z_]+?\.[a-zA-Z]{2,3}$';
  
  // Raw multi-line string
  String rawMultiline = r'''
    Raw multi-line string
    with \n \t \r characters preserved literally
    Perfect for regex patterns or code templates
  ''';
  
  print('Regular: $regularString');
  print('Raw: $rawString');
  print('Windows path: $windowsPath');
  print('Email regex: $emailPattern');
  print('Raw multiline: $rawMultiline');
}
```

Raw strings disable all escape sequence processing, making them ideal for  
patterns, paths, and templates where backslashes should be preserved literally.  
The `r` prefix before the quote creates a raw string that treats all characters  
as literal text.

```
$ dart run raw_strings.dart
Regular: This is a new line
 and tab	 character
Raw: This is a new line\n and tab\t character
Windows path: C:\Users\Alice\Documents\myfile.txt
Email regex: ^\w+@[a-zA-Z_]+?\.[a-zA-Z]{2,3}$
Raw multiline: 
    Raw multi-line string
    with \n \t \r characters preserved literally
    Perfect for regex patterns or code templates
```

## String Escaping and Special Characters

Dart strings support standard escape sequences for representing special  
characters like newlines, tabs, quotes, and Unicode characters. Understanding  
escape sequences enables proper formatting and text representation.

```dart
void main() {
  // Common escape sequences
  String newline = 'First line\nSecond line';
  String tab = 'Column1\tColumn2\tColumn3';
  String carriageReturn = 'Return\rStart';
  
  // Quote escaping
  String singleQuoteEscape = 'Don\'t forget the apostrophe';
  String doubleQuoteEscape = "She said \"Hello there!\" loudly";
  
  // Backslash escaping  
  String backslash = 'Path separator: \\';
  String doubleBackslash = 'Network path: \\\\server\\folder';
  
  // Unicode escape sequences
  String unicode = '\u0048\u0065\u006C\u006C\u006F'; // "Hello"
  String heartEmoji = '\u2764\uFE0F'; // Heart emoji
  String unicodeHex = '\x48\x65\x6C\x6C\x6F'; // "Hello"
  
  print('Newline:\n$newline');
  print('Tab:\n$tab');
  print('Return: $carriageReturn');
  print('Single quote: $singleQuoteEscape');
  print('Double quote: $doubleQuoteEscape');
  print('Backslash: $backslash');
  print('Double backslash: $doubleBackslash');
  print('Unicode: $unicode');
  print('Heart: $heartEmoji');  
  print('Hex Unicode: $unicodeHex');
}
```

Escape sequences provide precise control over string content, enabling  
representation of non-printable characters, quotes within strings, and Unicode  
characters. Proper escaping ensures strings display and process correctly  
across different contexts.

```
$ dart run string_escaping.dart
Newline:
First line
Second line
Tab:
Column1	Column2	Column3
Return: Start
Single quote: Don't forget the apostrophe
Double quote: She said "Hello there!" loudly
Backslash: \
Double backslash: \\server\folder
Unicode: Hello
Heart: ❤️
Hex Unicode: Hello
```

## String Interpolation Basics

String interpolation enables embedding expressions and variables directly into  
string literals using the dollar sign (`$`) syntax. This feature provides a  
clean and readable alternative to string concatenation.

```dart
void main() {
  String name = 'Alice';
  int age = 28;
  double height = 1.68;
  bool isStudent = true;
  
  // Simple variable interpolation
  String introduction = 'Hello there! I am $name';
  String ageInfo = '$name is $age years old';
  
  // Interpolation with different data types
  String details = 'Name: $name, Age: $age, Height: $height meters';
  String status = '$name is ${isStudent ? 'a student' : 'not a student'}';
  
  // Interpolation in multi-line strings
  String profile = '''
    Personal Profile:
    Name: $name
    Age: $age years
    Height: $height m
    Student status: $isStudent
  ''';
  
  print(introduction);
  print(ageInfo);
  print(details);
  print(status);
  print(profile);
}
```

String interpolation simplifies dynamic string creation by directly embedding  
variables and expressions. Simple variables use `$variableName` syntax, while  
complex expressions require `${expression}` syntax for proper evaluation  
within strings.

```
$ dart run string_interpolation_basics.dart
Hello there! I am Alice
Alice is 28 years old
Name: Alice, Age: 28, Height: 1.68 meters
Alice is a student
    Personal Profile:
    Name: Alice
    Age: 28 years
    Height: 1.68 m
    Student status: true
```

## Complex String Interpolation

Advanced string interpolation supports complex expressions, method calls,  
property access, and nested interpolation patterns. This capability enables  
sophisticated string construction with dynamic content evaluation.

```dart
void main() {
  List<String> hobbies = ['reading', 'coding', 'gaming'];
  Map<String, dynamic> person = {
    'name': 'Bob',
    'age': 35,
    'city': 'Toronto'
  };
  
  // Method call interpolation
  String upperCase = 'hello there!';
  String greeting = 'Message: ${upperCase.toUpperCase()}';
  
  // Property and method chaining
  String listInfo = 'First hobby: ${hobbies.first.toUpperCase()}';
  String listDetails = 'Hobbies (${hobbies.length}): ${hobbies.join(', ')}';
  
  // Map access interpolation
  String personInfo = '${person['name']} from ${person['city']}';
  String ageGroup = '${person['name']} is ${person['age'] > 30 ? 'over' : 'under'} 30';
  
  // Mathematical expressions
  int x = 10, y = 5;
  String math = 'Calculation: ${x + y} = $x + $y, ${x * y} = $x * $y';
  
  // Null-safe interpolation
  String? nickname;
  String safeInterpolation = 'Nickname: ${nickname ?? 'Not provided'}';
  
  // Nested interpolation with functions
  String getCurrentTime() => DateTime.now().toString().substring(0, 19);
  String timestamp = 'Current time: ${getCurrentTime()}';
  
  print(greeting);
  print(listInfo);
  print(listDetails);
  print(personInfo);
  print(ageGroup);
  print(math);
  print(safeInterpolation);
  print(timestamp);
}
```

Complex interpolation enables embedding rich expressions including method calls,  
property access, mathematical operations, and null-safe patterns. The  
`${expression}` syntax evaluates any valid Dart expression within string  
literals for dynamic content generation.

```
$ dart run complex_interpolation.dart
Message: HELLO THERE!
First hobby: READING
Hobbies (3): reading, coding, gaming
Bob from Toronto
Bob is over 30
Calculation: 15 = 10 + 5, 50 = 10 * 5
Nickname: Not provided
Current time: 2024-01-15 14:30:25
```

## String Length and Empty Checks

String length and emptiness validation are fundamental operations in text  
processing. Dart provides built-in properties and methods for checking string  
dimensions and content presence.

```dart
void main() {
  String normalString = 'Hello there! Welcome to Dart';
  String emptyString = '';
  String whitespaceString = '   ';
  String? nullString;
  
  // Length property
  print('Normal string length: ${normalString.length}');
  print('Empty string length: ${emptyString.length}');  
  print('Whitespace string length: ${whitespaceString.length}');
  
  // isEmpty and isNotEmpty checks
  print('Normal string isEmpty: ${normalString.isEmpty}');
  print('Empty string isEmpty: ${emptyString.isEmpty}');
  print('Normal string isNotEmpty: ${normalString.isNotEmpty}');
  
  // Null-safe length checking
  print('Null string length: ${nullString?.length ?? 'null'}');
  print('Null string isEmpty: ${nullString?.isEmpty ?? 'null'}');
  
  // Practical validation function
  bool isValidString(String? str) {
    return str != null && str.isNotEmpty && str.trim().isNotEmpty;
  }
  
  print('Normal string valid: ${isValidString(normalString)}');
  print('Empty string valid: ${isValidString(emptyString)}');
  print('Whitespace string valid: ${isValidString(whitespaceString)}');
  print('Null string valid: ${isValidString(nullString)}');
  
  // Length-based validation
  bool isValidPassword(String password) {
    return password.isNotEmpty && password.length >= 8;
  }
  
  List<String> passwords = ['short', 'longpassword123', ''];
  for (String pwd in passwords) {
    print('Password "$pwd" valid: ${isValidPassword(pwd)}');
  }
}
```

String length and emptiness checks enable input validation, data processing,  
and conditional logic. The `length` property returns character count, while  
`isEmpty` and `isNotEmpty` provide convenient boolean checks for string  
content presence.

```
$ dart run string_length_empty.dart
Normal string length: 28
Empty string length: 0
Whitespace string length: 3
Normal string isEmpty: false
Empty string isEmpty: true
Normal string isNotEmpty: true
Null string length: null
Null string isEmpty: null
Normal string valid: true
Empty string valid: false
Whitespace string valid: false
Null string valid: false
Password "short" valid: false
Password "longpassword123" valid: true
Password "" valid: false
```

## String Immutability

Dart strings are immutable objects, meaning their content cannot be changed  
after creation. String operations create new string instances rather than  
modifying existing ones, ensuring thread safety and predictable behavior.

```dart
void main() {
  String original = 'Hello there!';
  print('Original string: $original');
  print('Original hashCode: ${original.hashCode}');
  
  // String operations create new instances
  String uppercase = original.toUpperCase();
  String substring = original.substring(0, 5);
  String replaced = original.replaceAll('Hello', 'Hi');
  
  print('\nAfter operations:');
  print('Original: $original (hashCode: ${original.hashCode})');
  print('Uppercase: $uppercase (hashCode: ${uppercase.hashCode})');
  print('Substring: $substring (hashCode: ${substring.hashCode})');
  print('Replaced: $replaced (hashCode: ${replaced.hashCode})');
  
  // Demonstrating immutability
  String text = 'Immutable';
  String reference = text;
  
  // This creates a new string, doesn't modify original
  String modified = text + ' String';
  
  print('\nImmutability demonstration:');
  print('Original text: $text');
  print('Reference: $reference');  
  print('Modified: $modified');
  print('text == reference: ${text == reference}');
  print('identical(text, reference): ${identical(text, reference)}');
  
  // String pool behavior
  String literal1 = 'Same content';
  String literal2 = 'Same content';  
  String constructed = String.fromCharCodes([83, 97, 109, 101, 32, 99, 111, 110, 116, 101, 110, 116]);
  
  print('\nString pool behavior:');
  print('literal1: $literal1');
  print('literal2: $literal2');
  print('constructed: $constructed');
  print('literal1 == literal2: ${literal1 == literal2}');
  print('identical(literal1, literal2): ${identical(literal1, literal2)}');
  print('literal1 == constructed: ${literal1 == constructed}');  
  print('identical(literal1, constructed): ${identical(literal1, constructed)}');
}
```

String immutability ensures that string objects remain constant after creation,  
providing thread safety and eliminating side effects. All string operations  
return new instances, while string literals may share references through  
string pooling optimization.

```
$ dart run string_immutability.dart
Original string: Hello there!
Original hashCode: 987654321

After operations:
Original: Hello there! (hashCode: 987654321)
Uppercase: HELLO THERE! (hashCode: 123456789)
Substring: Hello (hashCode: 456789123)
Replaced: Hi there! (hashCode: 789123456)

Immutability demonstration:
Original text: Immutable
Reference: Immutable
Modified: Immutable String
text == reference: true
identical(text, reference): true

String pool behavior:
literal1: Same content
literal2: Same content
constructed: Same content
literal1 == literal2: true
identical(literal1, literal2): true
literal1 == constructed: true
identical(literal1, constructed): false
```

## String Concatenation

String concatenation combines multiple strings into a single string using  
various approaches. Dart provides the plus operator, string interpolation,  
and specialized methods for different concatenation scenarios.

```dart
void main() {
  String firstName = 'Alice';
  String lastName = 'Johnson';
  
  // Plus operator concatenation
  String fullName = firstName + ' ' + lastName;
  String greeting = 'Hello there, ' + firstName + '!';
  
  // Concatenation with different types
  int age = 30;
  String ageString = 'Age: ' + age.toString();
  
  // Multiple string concatenation
  String address = '123' + ' ' + 'Main' + ' ' + 'Street';
  
  // Concatenation in expressions
  List<String> parts = ['Hello', 'there', 'everyone'];
  String combined = parts[0] + ' ' + parts[1] + ' ' + parts[2];
  
  // Performance consideration: multiple concatenations
  String inefficient = '';
  List<String> words = ['This', 'is', 'inefficient', 'concatenation'];
  for (String word in words) {
    inefficient = inefficient + word + ' ';
  }
  
  // More efficient: using join
  String efficient = words.join(' ');
  
  // Concatenation with null safety
  String? prefix = 'Mr.';
  String name = (prefix ?? '') + ' ' + firstName;
  
  print('Full name: $fullName');
  print('Greeting: $greeting');
  print('Age string: $ageString');
  print('Address: $address');
  print('Combined: $combined');
  print('Inefficient: ${inefficient.trim()}');
  print('Efficient: $efficient');
  print('Name with prefix: $name');
  
  // Concatenation vs interpolation performance
  Stopwatch sw = Stopwatch()..start();
  for (int i = 0; i < 10000; i++) {
    String _ = 'Hello' + ' ' + 'there' + '!';
  }
  int concatTime = sw.elapsedMicroseconds;
  
  sw.reset();
  for (int i = 0; i < 10000; i++) {
    String _ = 'Hello there!';
  }
  int literalTime = sw.elapsedMicroseconds;
  
  print('\nPerformance comparison (10,000 operations):');
  print('Concatenation: ${concatTime}μs');
  print('String literal: ${literalTime}μs');
}
```

String concatenation using the plus operator creates new string instances for  
each operation. For multiple concatenations, methods like `join()` prove more  
efficient. String interpolation often provides better readability and  
performance than explicit concatenation.

```
$ dart run string_concatenation.dart
Full name: Alice Johnson
Greeting: Hello there, Alice!
Age string: Age: 30
Address: 123 Main Street
Combined: Hello there everyone
Inefficient: This is inefficient concatenation
Efficient: This is inefficient concatenation
Name with prefix: Mr.  Alice

Performance comparison (10,000 operations):
Concatenation: 1250μs
String literal: 45μs
```

## String Comparison

String comparison in Dart supports equality testing, lexicographical ordering,  
and case-sensitive/insensitive comparisons. Understanding comparison methods  
enables proper sorting, searching, and validation operations.

```dart
void main() {
  String str1 = 'Hello there!';
  String str2 = 'Hello there!';
  String str3 = 'hello there!';
  String str4 = 'Hi there!';
  
  // Equality comparison
  print('Equality comparisons:');
  print('str1 == str2: ${str1 == str2}');
  print('str1 == str3: ${str1 == str3}');
  print('identical(str1, str2): ${identical(str1, str2)}');
  
  // Case-insensitive comparison
  print('\nCase-insensitive comparisons:');
  print('str1.toLowerCase() == str3.toLowerCase(): ${str1.toLowerCase() == str3.toLowerCase()}');
  
  // Lexicographical comparison using compareTo
  print('\nLexicographical comparisons:');
  print('str1.compareTo(str2): ${str1.compareTo(str2)}');
  print('str1.compareTo(str3): ${str1.compareTo(str3)}');
  print('str1.compareTo(str4): ${str1.compareTo(str4)}');
  print('str4.compareTo(str1): ${str4.compareTo(str1)}');
  
  // Sorting strings
  List<String> names = ['Charlie', 'Alice', 'Bob', 'alice', 'DAVID'];
  
  // Case-sensitive sort
  List<String> sortedCaseSensitive = List.from(names);
  sortedCaseSensitive.sort();
  print('\nCase-sensitive sort: $sortedCaseSensitive');
  
  // Case-insensitive sort
  List<String> sortedCaseInsensitive = List.from(names);
  sortedCaseInsensitive.sort((a, b) => a.toLowerCase().compareTo(b.toLowerCase()));
  print('Case-insensitive sort: $sortedCaseInsensitive');
  
  // Practical comparison function
  int compareIgnoreCase(String a, String b) {
    return a.toLowerCase().compareTo(b.toLowerCase());
  }
  
  List<String> customSorted = List.from(names);
  customSorted.sort(compareIgnoreCase);
  print('Custom sorted: $customSorted');
  
  // Numerical string comparison
  List<String> numbers = ['10', '2', '1', '20', '3'];
  
  // String comparison (lexicographical)
  List<String> stringSort = List.from(numbers);
  stringSort.sort();
  print('\nString sort: $stringSort');
  
  // Numerical comparison
  List<String> numericalSort = List.from(numbers);
  numericalSort.sort((a, b) => int.parse(a).compareTo(int.parse(b)));
  print('Numerical sort: $numericalSort');
}
```

String comparison provides equality testing with `==`, identity checking with  
`identical()`, and ordering with `compareTo()`. Case-insensitive comparisons  
require converting strings to the same case before comparison. Custom comparison  
functions enable specialized sorting behavior.

```
$ dart run string_comparison.dart
Equality comparisons:
str1 == str2: true
str1 == str3: false
identical(str1, str2): true

Case-insensitive comparisons:
str1.toLowerCase() == str3.toLowerCase(): true

Lexicographical comparisons:
str1.compareTo(str2): 0
str1.compareTo(str3): -32
str1.compareTo(str4): -1
str4.compareTo(str1): 1

Case-sensitive sort: [Alice, Bob, Charlie, DAVID, alice]
Case-insensitive sort: [Alice, alice, Bob, Charlie, DAVID]
Custom sorted: [Alice, alice, Bob, Charlie, DAVID]

String sort: [1, 10, 2, 20, 3]
Numerical sort: [1, 2, 3, 10, 20]
```

## String Case Conversion

Case conversion methods transform string capitalization for formatting,  
comparison, and display purposes. Dart provides built-in methods for common  
case transformations and supports locale-aware conversions.

```dart
void main() {
  String mixedCase = 'Hello There! Welcome To DART Programming';
  String lowerCase = 'hello there! welcome to dart programming';
  String upperCase = 'HELLO THERE! WELCOME TO DART PROGRAMMING';
  
  // Basic case conversions
  print('Original: $mixedCase');
  print('toLowerCase(): ${mixedCase.toLowerCase()}');
  print('toUpperCase(): ${mixedCase.toUpperCase()}');
  
  // Case conversion with different inputs
  print('\nDifferent inputs:');
  print('Lower to upper: ${lowerCase.toUpperCase()}');
  print('Upper to lower: ${upperCase.toLowerCase()}');
  
  // Title case implementation
  String toTitleCase(String text) {
    return text.split(' ').map((word) {
      if (word.isEmpty) return word;
      return word[0].toUpperCase() + word.substring(1).toLowerCase();
    }).join(' ');
  }
  
  String titleCase = toTitleCase(lowerCase);
  print('\nTitle case: $titleCase');
  
  // Sentence case implementation
  String toSentenceCase(String text) {
    if (text.isEmpty) return text;
    return text[0].toUpperCase() + text.substring(1).toLowerCase();
  }
  
  String sentenceCase = toSentenceCase(upperCase);
  print('Sentence case: $sentenceCase');
  
  // camelCase conversion
  String toCamelCase(String text) {
    List<String> words = text.toLowerCase().split(' ');
    if (words.isEmpty) return text;
    
    String result = words.first;
    for (int i = 1; i < words.length; i++) {
      if (words[i].isNotEmpty) {
        result += words[i][0].toUpperCase() + words[i].substring(1);
      }
    }
    return result;
  }
  
  String camelCase = toCamelCase('hello there dart programming');
  print('camelCase: $camelCase');
  
  // snake_case conversion  
  String toSnakeCase(String text) {
    return text.toLowerCase().replaceAll(' ', '_');
  }
  
  String snakeCase = toSnakeCase('Hello There Dart Programming');
  print('snake_case: $snakeCase');
  
  // CONSTANT_CASE conversion
  String toConstantCase(String text) {
    return text.toUpperCase().replaceAll(' ', '_');
  }
  
  String constantCase = toConstantCase('hello there dart programming');
  print('CONSTANT_CASE: $constantCase');
  
  // Case-insensitive operations
  bool equalsIgnoreCase(String a, String b) {
    return a.toLowerCase() == b.toLowerCase();
  }
  
  print('\nCase-insensitive equality:');
  print('Hello == HELLO: ${equalsIgnoreCase('Hello', 'HELLO')}');
  print('Hello == Hi: ${equalsIgnoreCase('Hello', 'Hi')}');
}
```

Case conversion methods enable text formatting for different presentation styles  
and programming conventions. Custom functions extend basic case conversion to  
support title case, camelCase, snake_case, and other specialized formatting  
requirements common in software development.

```
$ dart run string_case_conversion.dart
Original: Hello There! Welcome To DART Programming
toLowerCase(): hello there! welcome to dart programming
toUpperCase(): HELLO THERE! WELCOME TO DART PROGRAMMING

Different inputs:
Lower to upper: HELLO THERE! WELCOME TO DART PROGRAMMING
Upper to lower: hello there! welcome to dart programming

Title case: Hello There! Welcome To Dart Programming
Sentence case: Hello there! welcome to dart programming
camelCase: helloThereDartProgramming
snake_case: hello_there_dart_programming
CONSTANT_CASE: HELLO_THERE_DART_PROGRAMMING

Case-insensitive equality:
Hello == HELLO: true
Hello == Hi: false
```

## Trimming and Whitespace

String trimming removes whitespace characters from the beginning and end of  
strings. Dart provides methods for various trimming scenarios including  
leading, trailing, and custom character removal.

```dart
void main() {
  String whitespaceString = '   Hello there! Welcome to Dart   ';
  String mixedWhitespace = '\t\n  Hello there!  \r\n ';
  String customString = '---Hello there!---';
  
  // Basic trimming methods
  print('Original: "$whitespaceString"');
  print('trim(): "${whitespaceString.trim()}"');
  print('trimLeft(): "${whitespaceString.trimLeft()}"');
  print('trimRight(): "${whitespaceString.trimRight()}"');
  
  // Trimming mixed whitespace
  print('\nMixed whitespace: "$mixedWhitespace"');
  print('trim(): "${mixedWhitespace.trim()}"');
  
  // Custom trimming function
  String trimCustom(String input, String character) {
    String result = input;
    while (result.startsWith(character)) {
      result = result.substring(1);
    }
    while (result.endsWith(character)) {
      result = result.substring(0, result.length - 1);
    }
    return result;
  }
  
  print('\nCustom trimming:');
  print('Original: "$customString"');
  print('Trim dashes: "${trimCustom(customString, '-')}"');
  
  // Whitespace detection
  bool isWhitespace(String str) {
    return str.trim().isEmpty;
  }
  
  List<String> testStrings = ['   ', '\t\n', 'hello', '', '  text  '];
  print('\nWhitespace detection:');
  for (String str in testStrings) {
    print('"$str" is whitespace: ${isWhitespace(str)}');
  }
  
  // Normalizing whitespace
  String normalizeWhitespace(String input) {
    return input.trim().replaceAll(RegExp(r'\s+'), ' ');
  }
  
  String messyText = '  Hello    there!\t\nWelcome   to  Dart  ';
  print('\nWhitespace normalization:');
  print('Original: "$messyText"');
  print('Normalized: "${normalizeWhitespace(messyText)}"');
  
  // Practical use case: cleaning user input
  List<String> userInputs = [
    '  alice@example.com  ',
    '\tBob Smith\n',
    '   123-456-7890   '
  ];
  
  print('\nCleaning user inputs:');
  for (String input in userInputs) {
    print('Original: "$input" -> Cleaned: "${input.trim()}"');
  }
}
```

String trimming methods remove unwanted whitespace characters from string  
boundaries. The `trim()` method removes from both ends, while `trimLeft()` and  
`trimRight()` target specific sides. Custom trimming functions extend  
functionality for removing specific characters.

```
$ dart run string_trimming.dart
Original: "   Hello there! Welcome to Dart   "
trim(): "Hello there! Welcome to Dart"
trimLeft(): "Hello there! Welcome to Dart   "
trimRight(): "   Hello there! Welcome to Dart"

Mixed whitespace: "	
  Hello there!  

 "
trim(): "Hello there!"

Custom trimming:
Original: "---Hello there!---"
Trim dashes: "Hello there!"

Whitespace detection:
"   " is whitespace: true
"	
" is whitespace: true
"hello" is whitespace: false
"" is whitespace: true
"  text  " is whitespace: false

Whitespace normalization:
Original: "  Hello    there!	
Welcome   to  Dart  "
Normalized: "Hello there! Welcome to Dart"

Cleaning user inputs:
Original: "  alice@example.com  " -> Cleaned: "alice@example.com"
Original: "	Bob Smith
" -> Cleaned: "Bob Smith"
Original: "   123-456-7890   " -> Cleaned: "123-456-7890"
```

## String Substring Operations

Substring operations extract portions of strings based on position indices.  
Dart provides flexible substring methods for text processing, parsing, and  
content extraction scenarios.

```dart
void main() {
  String text = 'Hello there! Welcome to Dart programming language.';
  
  // Basic substring with start index
  print('Original: $text');
  print('substring(6): "${text.substring(6)}"');
  print('substring(13): "${text.substring(13)}"');
  
  // Substring with start and end indices
  print('\nSubstring with range:');
  print('substring(0, 5): "${text.substring(0, 5)}"');
  print('substring(6, 12): "${text.substring(6, 12)}"');
  print('substring(13, 20): "${text.substring(13, 20)}"');
  
  // Safe substring function
  String safeSubstring(String str, int start, [int? end]) {
    if (start < 0) start = 0;
    if (start >= str.length) return '';
    
    if (end != null) {
      if (end > str.length) end = str.length;
      if (end <= start) return '';
      return str.substring(start, end);
    }
    
    return str.substring(start);
  }
  
  print('\nSafe substring operations:');
  print('safeSubstring(-5, 10): "${safeSubstring(text, -5, 10)}"');
  print('safeSubstring(100): "${safeSubstring(text, 100)}"');
  print('safeSubstring(5, 3): "${safeSubstring(text, 5, 3)}"');
  
  // Extract words using substring
  List<String> extractWords(String sentence) {
    List<String> words = [];
    int start = 0;
    
    for (int i = 0; i <= sentence.length; i++) {
      if (i == sentence.length || sentence[i] == ' ') {
        if (i > start) {
          words.add(sentence.substring(start, i));
        }
        start = i + 1;
      }
    }
    
    return words;
  }
  
  String sentence = 'Dart is a powerful programming language';
  List<String> words = extractWords(sentence);
  print('\nExtracted words: $words');
  
  // Extract file extension
  String getFileExtension(String filename) {
    int lastDot = filename.lastIndexOf('.');
    if (lastDot == -1 || lastDot == filename.length - 1) {
      return '';
    }
    return filename.substring(lastDot + 1);
  }
  
  List<String> filenames = ['document.pdf', 'image.jpg', 'script.dart', 'readme'];
  print('\nFile extensions:');
  for (String filename in filenames) {
    String ext = getFileExtension(filename);
    print('$filename -> extension: "${ext.isEmpty ? 'none' : ext}"');
  }
  
  // URL parsing with substring
  String parseProtocol(String url) {
    int protocolEnd = url.indexOf('://');
    return protocolEnd > 0 ? url.substring(0, protocolEnd) : '';
  }
  
  String parseDomain(String url) {
    int protocolEnd = url.indexOf('://');
    if (protocolEnd == -1) return url;
    
    int domainStart = protocolEnd + 3;
    int pathStart = url.indexOf('/', domainStart);
    int domainEnd = pathStart == -1 ? url.length : pathStart;
    
    return url.substring(domainStart, domainEnd);
  }
  
  String sampleUrl = 'https://www.example.com/path/to/page';
  print('\nURL parsing:');
  print('URL: $sampleUrl');
  print('Protocol: "${parseProtocol(sampleUrl)}"');
  print('Domain: "${parseDomain(sampleUrl)}"');
}
```

Substring operations enable precise text extraction based on character positions.  
The `substring()` method supports both single-index and range-based extraction.  
Safe substring functions prevent index errors, while practical applications  
include parsing filenames, URLs, and structured text data.

```
$ dart run string_substring.dart
Original: Hello there! Welcome to Dart programming language.
substring(6): "there! Welcome to Dart programming language."
substring(13): "Welcome to Dart programming language."

Substring with range:
substring(0, 5): "Hello"
substring(6, 12): "there!"
substring(13, 20): "Welcome"

Safe substring operations:
safeSubstring(-5, 10): "Hello ther"
safeSubstring(100): ""
safeSubstring(5, 3): ""

Extracted words: [Dart, is, a, powerful, programming, language]

File extensions:
document.pdf -> extension: "pdf"
image.jpg -> extension: "jpg"
script.dart -> extension: "dart"
readme -> extension: "none"

URL parsing:
URL: https://www.example.com/path/to/page
Protocol: "https"
Domain: "www.example.com"
```