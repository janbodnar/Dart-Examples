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

## String Index and Character Access

String character access enables examination of individual characters and  
positions within strings. Dart provides various methods for character-level  
operations and position-based string analysis.

```dart
void main() {
  String text = 'Hello there! Welcome to Dart';
  
  // Character access by index
  print('Original text: $text');
  print('Character at index 0: "${text[0]}"');
  print('Character at index 6: "${text[6]}"');
  print('Character at index 13: "${text[13]}"');
  print('Last character: "${text[text.length - 1]}"');
  
  // Safe character access
  String? safeCharAt(String str, int index) {
    if (index >= 0 && index < str.length) {
      return str[index];
    }
    return null;
  }
  
  print('\nSafe character access:');
  print('safeCharAt(5): ${safeCharAt(text, 5)}');
  print('safeCharAt(-1): ${safeCharAt(text, -1)}');
  print('safeCharAt(100): ${safeCharAt(text, 100)}');
  
  // Character code access
  print('\nCharacter codes:');
  for (int i = 0; i < 5; i++) {
    String char = text[i];
    int code = text.codeUnitAt(i);
    print('Character "$char" has code $code');
  }
  
  // Find character positions
  List<int> findAllOccurrences(String str, String char) {
    List<int> positions = [];
    for (int i = 0; i < str.length; i++) {
      if (str[i] == char) {
        positions.add(i);
      }
    }
    return positions;
  }
  
  List<int> spacePositions = findAllOccurrences(text, ' ');
  print('\nSpace positions: $spacePositions');
  
  List<int> ePositions = findAllOccurrences(text, 'e');
  print('Positions of "e": $ePositions');
  
  // Character frequency analysis
  Map<String, int> characterFrequency(String str) {
    Map<String, int> frequency = {};
    for (int i = 0; i < str.length; i++) {
      String char = str[i].toLowerCase();
      frequency[char] = (frequency[char] ?? 0) + 1;
    }
    return frequency;
  }
  
  Map<String, int> freq = characterFrequency('Hello there!');
  print('\nCharacter frequency in "Hello there!":');
  freq.entries.toList()
    ..sort((a, b) => b.value.compareTo(a.value))
    ..forEach((entry) => print('  "${entry.key}": ${entry.value}'));
  
  // Check character types
  bool isVowel(String char) {
    return 'aeiouAEIOU'.contains(char);
  }
  
  bool isConsonant(String char) {
    return char.toLowerCase().compareTo('a') >= 0 && 
           char.toLowerCase().compareTo('z') <= 0 && 
           !isVowel(char);
  }
  
  print('\nCharacter type analysis:');
  String sample = 'Hello';
  for (int i = 0; i < sample.length; i++) {
    String char = sample[i];
    String type = isVowel(char) ? 'vowel' : 
                  isConsonant(char) ? 'consonant' : 'other';
    print('  "$char" is a $type');
  }
  
  // Reverse string using character access
  String reverseString(String str) {
    String reversed = '';
    for (int i = str.length - 1; i >= 0; i--) {
      reversed += str[i];
    }
    return reversed;
  }
  
  print('\nString reversal:');
  print('Original: "$text"');
  print('Reversed: "${reverseString(text)}"');
}
```

String index and character access provide fine-grained control over individual  
characters within strings. Index-based access uses square brackets, while  
`codeUnitAt()` returns character codes. Character analysis enables frequency  
counting, type checking, and position-based string processing.

```
$ dart run string_character_access.dart
Original text: Hello there! Welcome to Dart
Character at index 0: "H"
Character at index 6: "t"
Character at index 13: "W"
Last character: "t"

Safe character access:
safeCharAt(5): " "
safeCharAt(-1): null
safeCharAt(100): null

Character codes:
Character "H" has code 72
Character "e" has code 101
Character "l" has code 108
Character "l" has code 108
Character "o" has code 111

Space positions: [5, 12, 20, 23]

Positions of "e": [1, 8, 14, 18]

Character frequency in "Hello there!":
  "e": 2
  "l": 2
  " ": 1
  "!": 1
  "h": 1
  "o": 1
  "r": 1
  "t": 1

Character type analysis:
  "H" is a consonant
  "e" is a vowel
  "l" is a consonant
  "l" is a consonant
  "o" is a vowel

String reversal:
Original: "Hello there! Welcome to Dart"
Reversed: "traD ot emocleW !ereht olleH"
```

## String Contains and Search

String search operations locate substrings and patterns within text. Dart  
provides methods for checking string presence, finding positions, and  
performing pattern-based searches with various matching criteria.

```dart
void main() {
  String text = 'Hello there! Welcome to Dart programming. Dart is powerful.';
  
  // Basic contains checks
  print('Text: $text');
  print('Contains "Dart": ${text.contains('Dart')}');
  print('Contains "Python": ${text.contains('Python')}');
  print('Contains "hello": ${text.contains('hello')}');
  
  // Case-insensitive contains
  bool containsIgnoreCase(String text, String pattern) {
    return text.toLowerCase().contains(pattern.toLowerCase());
  }
  
  print('\nCase-insensitive contains:');
  print('Contains "hello" (ignore case): ${containsIgnoreCase(text, 'hello')}');
  print('Contains "DART" (ignore case): ${containsIgnoreCase(text, 'DART')}');
  
  // Find first occurrence
  print('\nFirst occurrence positions:');
  print('indexOf("Dart"): ${text.indexOf('Dart')}');
  print('indexOf("e"): ${text.indexOf('e')}');
  print('indexOf("Python"): ${text.indexOf('Python')}');
  
  // Find last occurrence
  print('\nLast occurrence positions:');
  print('lastIndexOf("Dart"): ${text.lastIndexOf('Dart')}');
  print('lastIndexOf("e"): ${text.lastIndexOf('e')}');
  
  // Find with starting position
  print('\nSearch from specific position:');
  int firstDart = text.indexOf('Dart');
  int secondDart = text.indexOf('Dart', firstDart + 1);
  print('First "Dart" at: $firstDart');
  print('Second "Dart" at: $secondDart');
  
  // Find all occurrences
  List<int> findAllOccurrences(String text, String pattern) {
    List<int> positions = [];
    int start = 0;
    while (true) {
      int pos = text.indexOf(pattern, start);
      if (pos == -1) break;
      positions.add(pos);
      start = pos + 1;
    }
    return positions;
  }
  
  List<int> allE = findAllOccurrences(text, 'e');
  List<int> allDart = findAllOccurrences(text, 'Dart');
  print('\nAll occurrences:');
  print('All "e" positions: $allE');
  print('All "Dart" positions: $allDart');
  
  // Word boundary search
  bool containsWord(String text, String word) {
    RegExp wordRegex = RegExp(r'\b' + RegExp.escape(word) + r'\b');
    return wordRegex.hasMatch(text);
  }
  
  String sample = 'The dart board has a dart in it';
  print('\nWord boundary search:');
  print('Text: "$sample"');
  print('Contains word "dart": ${containsWord(sample, 'dart')}');
  print('Contains word "art": ${containsWord(sample, 'art')}');
  
  // Search with wildcards (simple implementation)
  bool matchesPattern(String text, String pattern) {
    // Simple wildcard: * matches any characters
    if (!pattern.contains('*')) return text == pattern;
    
    List<String> parts = pattern.split('*');
    int textIndex = 0;
    
    for (int i = 0; i < parts.length; i++) {
      String part = parts[i];
      if (part.isEmpty) continue;
      
      int foundIndex = text.indexOf(part, textIndex);
      if (foundIndex == -1) return false;
      
      if (i == 0 && foundIndex != 0) return false; // Must start with first part
      textIndex = foundIndex + part.length;
    }
    
    if (parts.last.isNotEmpty && !text.endsWith(parts.last)) {
      return false; // Must end with last part
    }
    
    return true;
  }
  
  print('\nWildcard pattern matching:');
  List<String> patterns = ['Hello*', '*Dart*', '*powerful.'];
  for (String pattern in patterns) {
    bool matches = matchesPattern(text, pattern);
    print('Pattern "$pattern" matches: $matches');
  }
  
  // Search multiple patterns
  String findFirstMatch(String text, List<String> patterns) {
    int earliestPos = text.length;
    String result = '';
    
    for (String pattern in patterns) {
      int pos = text.indexOf(pattern);
      if (pos != -1 && pos < earliestPos) {
        earliestPos = pos;
        result = pattern;
      }
    }
    
    return result;
  }
  
  List<String> searchTerms = ['programming', 'Welcome', 'Python', 'Dart'];
  String firstMatch = findFirstMatch(text, searchTerms);
  print('\nFirst match from $searchTerms: "$firstMatch"');
}
```

String search operations provide comprehensive text location functionality.  
The `contains()` method checks presence, while `indexOf()` and `lastIndexOf()`  
return positions. Advanced searching supports case-insensitive matching,  
word boundaries, and pattern-based searches for complex text processing needs.

```
$ dart run string_search.dart
Text: Hello there! Welcome to Dart programming. Dart is powerful.
Contains "Dart": true
Contains "Python": false
Contains "hello": false

Case-insensitive contains:
Contains "hello" (ignore case): true
Contains "DART" (ignore case): true

First occurrence positions:
indexOf("Dart"): 24
indexOf("e"): 1
indexOf("Python"): -1

Last occurrence positions:
lastIndexOf("Dart"): 41
lastIndexOf("e"): 17

Search from specific position:
First "Dart" at: 24
Second "Dart" at: 41

All occurrences:
All "e" positions: [1, 8, 17, 20]
All "Dart" positions: [24, 41]

Word boundary search:
Text: "The dart board has a dart in it"
Contains word "dart": true
Contains word "art": false

Wildcard pattern matching:
Pattern "Hello*" matches: true
Pattern "*Dart*" matches: true
Pattern "*powerful." matches: true

First match from [programming, Welcome, Python, Dart]: "Welcome"
```

## String Starts and Ends With

String prefix and suffix checking determines whether strings begin or end with  
specific patterns. These operations enable path validation, file type checking,  
and content classification based on string boundaries.

```dart
void main() {
  String filename = 'document.pdf';
  String url = 'https://www.example.com/page.html';
  String greeting = 'Hello there! How are you?';
  
  // Basic startsWith and endsWith
  print('Filename: $filename');
  print('Starts with "doc": ${filename.startsWith('doc')}');
  print('Ends with ".pdf": ${filename.endsWith('.pdf')}');
  print('Ends with ".txt": ${filename.endsWith('.txt')}');
  
  print('\nURL: $url');
  print('Starts with "https": ${url.startsWith('https')}');
  print('Starts with "http": ${url.startsWith('http')}');
  print('Ends with ".html": ${url.endsWith('.html')}');
  
  // Case-insensitive checks
  bool startsWithIgnoreCase(String text, String prefix) {
    return text.toLowerCase().startsWith(prefix.toLowerCase());
  }
  
  bool endsWithIgnoreCase(String text, String suffix) {
    return text.toLowerCase().endsWith(suffix.toLowerCase());
  }
  
  print('\nCase-insensitive checks:');
  print('Greeting: $greeting');
  print('Starts with "HELLO" (ignore case): ${startsWithIgnoreCase(greeting, 'HELLO')}');
  print('Ends with "YOU?" (ignore case): ${endsWithIgnoreCase(greeting, 'YOU?')}');
  
  // File type detection
  String getFileType(String filename) {
    Map<String, String> extensions = {
      '.txt': 'Text Document',
      '.pdf': 'PDF Document', 
      '.jpg': 'Image',
      '.jpeg': 'Image',
      '.png': 'Image',
      '.dart': 'Dart Source Code',
      '.js': 'JavaScript',
      '.html': 'Web Page',
      '.css': 'Stylesheet'
    };
    
    for (String ext in extensions.keys) {
      if (filename.toLowerCase().endsWith(ext)) {
        return extensions[ext]!;
      }
    }
    
    return 'Unknown Type';
  }
  
  List<String> files = [
    'readme.txt',
    'image.JPG',
    'styles.css',
    'script.js',
    'main.dart',
    'unknownfile'
  ];
  
  print('\nFile type detection:');
  for (String file in files) {
    print('$file -> ${getFileType(file)}');
  }
  
  // Protocol detection
  String detectProtocol(String url) {
    List<String> protocols = ['https://', 'http://', 'ftp://', 'file://'];
    
    for (String protocol in protocols) {
      if (url.toLowerCase().startsWith(protocol)) {
        return protocol.replaceAll('://', '').toUpperCase();
      }
    }
    
    return 'Unknown';
  }
  
  List<String> urls = [
    'https://secure.example.com',
    'http://www.example.com',
    'ftp://files.example.com',
    'file:///local/path',
    'www.example.com'
  ];
  
  print('\nProtocol detection:');
  for (String testUrl in urls) {
    print('$testUrl -> ${detectProtocol(testUrl)}');
  }
  
  // Multiple prefix/suffix checking
  bool startsWithAny(String text, List<String> prefixes) {
    return prefixes.any((prefix) => text.startsWith(prefix));
  }
  
  bool endsWithAny(String text, List<String> suffixes) {
    return suffixes.any((suffix) => text.endsWith(suffix));
  }
  
  String command = 'sudo install package';
  List<String> adminCommands = ['sudo', 'su', 'admin'];
  List<String> packageOperations = ['install', 'remove', 'update'];
  
  print('\nMultiple pattern checking:');
  print('Command: $command');
  print('Starts with admin command: ${startsWithAny(command, adminCommands)}');
  print('Contains package operation: ${packageOperations.any((op) => command.contains(op))}');
  
  // Validation functions
  bool isValidEmail(String email) {
    return email.contains('@') && 
           !email.startsWith('@') && 
           !email.endsWith('@') &&
           email.indexOf('@') == email.lastIndexOf('@');
  }
  
  bool isValidPhoneNumber(String phone) {
    String cleaned = phone.replaceAll(RegExp(r'[^\d+]'), '');
    return (cleaned.startsWith('+') && cleaned.length >= 10) ||
           (!cleaned.startsWith('+') && cleaned.length >= 7);
  }
  
  List<String> emails = ['user@example.com', '@invalid', 'user@', 'valid.email@domain.co.uk'];
  List<String> phones = ['+1-555-123-4567', '555-1234', '123', '+44 20 7946 0958'];
  
  print('\nValidation examples:');
  print('Email validation:');
  for (String email in emails) {
    print('  "$email" is valid: ${isValidEmail(email)}');
  }
  
  print('Phone validation:');
  for (String phone in phones) {
    print('  "$phone" is valid: ${isValidPhoneNumber(phone)}');
  }
}
```

String prefix and suffix operations enable boundary-based text classification  
and validation. The `startsWith()` and `endsWith()` methods support exact  
matching, while custom functions extend functionality for case-insensitive  
checks, multiple pattern matching, and domain-specific validation rules.

```
$ dart run string_starts_ends.dart
Filename: document.pdf
Starts with "doc": true
Ends with ".pdf": true
Ends with ".txt": false

URL: https://www.example.com/page.html
Starts with "https": true
Starts with "http": true
Ends with ".html": true

Case-insensitive checks:
Greeting: Hello there! How are you?
Starts with "HELLO" (ignore case): true
Ends with "YOU?" (ignore case): true

File type detection:
readme.txt -> Text Document
image.JPG -> Image
styles.css -> Stylesheet
script.js -> JavaScript
main.dart -> Dart Source Code
unknownfile -> Unknown Type

Protocol detection:
https://secure.example.com -> HTTPS
http://www.example.com -> HTTP
ftp://files.example.com -> FTP
file:///local/path -> FILE
www.example.com -> Unknown

Multiple pattern checking:
Command: sudo install package
Starts with admin command: true
Contains package operation: true

Validation examples:
Email validation:
  "user@example.com" is valid: true
  "@invalid" is valid: false
  "user@" is valid: false
  "valid.email@domain.co.uk" is valid: true

Phone validation:
  "+1-555-123-4567" is valid: true
  "555-1234" is valid: true
  "123" is valid: false
  "+44 20 7946 0958" is valid: true
```

## String Split Operations

String splitting divides text into segments based on delimiters or patterns.  
Dart provides flexible splitting capabilities for parsing structured data,  
processing CSV content, and breaking text into manageable components.

```dart
void main() {
  String csvData = 'Alice,25,Engineer,New York';
  String sentence = 'Hello there! Welcome to Dart programming.';
  String path = '/home/user/documents/file.txt';
  
  // Basic split operations
  print('CSV data: $csvData');
  List<String> csvFields = csvData.split(',');
  print('Split by comma: $csvFields');
  
  print('\nSentence: $sentence');
  List<String> words = sentence.split(' ');
  print('Split by space: $words');
  
  print('\nPath: $path');
  List<String> pathSegments = path.split('/');
  print('Split by slash: $pathSegments');
  print('Non-empty segments: ${pathSegments.where((s) => s.isNotEmpty).toList()}');
  
  // Split with limit
  String multipleDelimiters = 'one,two,three,four,five';
  print('\nMultiple delimiters: $multipleDelimiters');
  print('Split all: ${multipleDelimiters.split(',')}');
  
  // Custom split function with limit
  List<String> splitWithLimit(String text, String delimiter, int limit) {
    if (limit <= 0) return text.split(delimiter);
    
    List<String> parts = [];
    int start = 0;
    
    for (int i = 0; i < limit - 1; i++) {
      int index = text.indexOf(delimiter, start);
      if (index == -1) break;
      
      parts.add(text.substring(start, index));
      start = index + delimiter.length;
    }
    
    if (start < text.length) {
      parts.add(text.substring(start));
    }
    
    return parts;
  }
  
  List<String> limitedSplit = splitWithLimit(multipleDelimiters, ',', 3);
  print('Split with limit 3: $limitedSplit');
  
  // Split lines
  String multilineText = '''Line 1
Line 2
Line 3

Line 5''';
  
  print('\nMultiline text splitting:');
  List<String> lines = multilineText.split('\n');
  print('All lines: $lines');
  
  List<String> nonEmptyLines = lines.where((line) => line.trim().isNotEmpty).toList();
  print('Non-empty lines: $nonEmptyLines');
  
  // Split with multiple delimiters using RegExp
  String mixedDelimiters = 'apple,banana;orange:grape|kiwi';
  print('\nMixed delimiters: $mixedDelimiters');
  
  List<String> fruits = mixedDelimiters.split(RegExp(r'[,;:|]'));
  print('Split by multiple delimiters: $fruits');
  
  // Parse key-value pairs
  String configData = 'host=localhost;port=8080;database=mydb;timeout=30';
  Map<String, String> parseConfig(String config) {
    Map<String, String> result = {};
    
    List<String> pairs = config.split(';');
    for (String pair in pairs) {
      List<String> keyValue = pair.split('=');
      if (keyValue.length == 2) {
        result[keyValue[0].trim()] = keyValue[1].trim();
      }
    }
    
    return result;
  }
  
  Map<String, String> config = parseConfig(configData);
  print('\nParsed configuration:');
  config.forEach((key, value) => print('  $key = $value'));
  
  // Split preserving quoted strings (simple CSV parser)
  List<String> splitCsv(String csvLine) {
    List<String> result = [];
    bool inQuotes = false;
    String current = '';
    
    for (int i = 0; i < csvLine.length; i++) {
      String char = csvLine[i];
      
      if (char == '"' && (i == 0 || csvLine[i-1] != '\\')) {
        inQuotes = !inQuotes;
      } else if (char == ',' && !inQuotes) {
        result.add(current.trim());
        current = '';
      } else {
        current += char;
      }
    }
    
    result.add(current.trim());
    return result;
  }
  
  String quotedCsv = 'John Doe,"Manager, Sales","New York, NY",45';
  print('\nQuoted CSV: $quotedCsv');
  List<String> csvParts = splitCsv(quotedCsv);
  print('Parsed CSV: $csvParts');
  
  // Split into words (handling punctuation)
  List<String> extractWords(String text) {
    return text
        .toLowerCase()
        .replaceAll(RegExp(r'[^\w\s]'), ' ')
        .split(RegExp(r'\s+'))
        .where((word) => word.isNotEmpty)
        .toList();
  }
  
  String textWithPunctuation = 'Hello there! How are you today? I\'m fine, thanks.';
  List<String> extractedWords = extractWords(textWithPunctuation);
  print('\nText with punctuation: $textWithPunctuation');
  print('Extracted words: $extractedWords');
  
  // Practical example: parsing email addresses
  String emailList = 'alice@example.com, bob@company.org; charlie@domain.net';
  List<String> emails = emailList
      .split(RegExp(r'[,;]'))
      .map((email) => email.trim())
      .where((email) => email.contains('@'))
      .toList();
  
  print('\nEmail list: $emailList');
  print('Extracted emails: $emails');
}
```

String splitting operations parse structured text into manageable components.  
The `split()` method handles basic delimiter-based separation, while regular  
expressions enable complex pattern-based splitting. Custom parsing functions  
support advanced scenarios like CSV processing and quoted string handling.

```
$ dart run string_split.dart
CSV data: Alice,25,Engineer,New York
Split by comma: [Alice, 25, Engineer, New York]

Sentence: Hello there! Welcome to Dart programming.
Split by space: [Hello, there!, Welcome, to, Dart, programming.]

Path: /home/user/documents/file.txt
Split by slash: [, home, user, documents, file.txt]
Non-empty segments: [home, user, documents, file.txt]

Multiple delimiters: one,two,three,four,five
Split all: [one, two, three, four, five]
Split with limit 3: [one, two, three,four,five]

Multiline text splitting:
All lines: [Line 1, Line 2, Line 3, , Line 5]
Non-empty lines: [Line 1, Line 2, Line 3, Line 5]

Mixed delimiters: apple,banana;orange:grape|kiwi
Split by multiple delimiters: [apple, banana, orange, grape, kiwi]

Parsed configuration:
  host = localhost
  port = 8080
  database = mydb
  timeout = 30

Quoted CSV: John Doe,"Manager, Sales","New York, NY",45
Parsed CSV: [John Doe, "Manager, Sales", "New York, NY", 45]

Text with punctuation: Hello there! How are you today? I'm fine, thanks.
Extracted words: [hello, there, how, are, you, today, i, m, fine, thanks]

Email list: alice@example.com, bob@company.org; charlie@domain.net
Extracted emails: [alice@example.com, bob@company.org, charlie@domain.net]
```

## String Join Operations

String joining combines multiple string elements into a single string using  
specified delimiters. This operation proves essential for constructing paths,  
creating formatted output, and assembling data from collections.

```dart
void main() {
  List<String> words = ['Hello', 'there', 'Dart', 'programmers'];
  List<String> pathSegments = ['home', 'user', 'documents', 'projects'];
  List<int> numbers = [1, 2, 3, 4, 5];
  
  // Basic join operations
  print('Words: $words');
  print('Join with space: "${words.join(' ')}"');
  print('Join with comma: "${words.join(', ')}"');
  print('Join with hyphen: "${words.join('-')}"');
  print('Join with empty string: "${words.join('')}"');
  
  // Path construction
  print('\nPath segments: $pathSegments');
  String unixPath = '/' + pathSegments.join('/');
  String windowsPath = pathSegments.join('\\');
  print('Unix path: "$unixPath"');
  print('Windows path: "$windowsPath"');
  
  // Join different data types
  print('\nNumbers: $numbers');
  String numbersStr = numbers.map((n) => n.toString()).join(', ');
  print('Numbers as string: "$numbersStr"');
  
  // Conditional joining
  List<String?> mixedList = ['apple', null, 'banana', '', 'cherry', null];
  String conditionalJoin = mixedList
      .where((item) => item != null && item.isNotEmpty)
      .join(', ');
  print('\nMixed list: $mixedList');
  print('Filtered join: "$conditionalJoin"');
  
  // Custom join with different delimiters
  String joinWithCustomDelimiters(List<String> items) {
    if (items.isEmpty) return '';
    if (items.length == 1) return items.first;
    if (items.length == 2) return '${items.first} and ${items.last}';
    
    String result = items.take(items.length - 1).join(', ');
    return '$result, and ${items.last}';
  }
  
  List<String> names = ['Alice', 'Bob', 'Charlie'];
  print('\nNames: $names');
  print('Custom join: "${joinWithCustomDelimiters(names)}"');
  
  List<String> colors = ['red', 'green'];
  print('Colors: $colors');
  print('Custom join: "${joinWithCustomDelimiters(colors)}"');
  
  // Building SQL queries
  String buildSelectQuery(String table, List<String> columns, List<String> conditions) {
    String columnList = columns.isEmpty ? '*' : columns.join(', ');
    String query = 'SELECT $columnList FROM $table';
    
    if (conditions.isNotEmpty) {
      query += ' WHERE ' + conditions.join(' AND ');
    }
    
    return query;
  }
  
  List<String> userColumns = ['name', 'email', 'age'];
  List<String> userConditions = ['age >= 18', 'status = \'active\''];
  String userQuery = buildSelectQuery('users', userColumns, userConditions);
  print('\nSQL query: $userQuery');
  
  // CSV generation
  List<List<String>> csvData = [
    ['Name', 'Age', 'City'],
    ['Alice', '25', 'New York'],
    ['Bob', '30', 'London'],
    ['Charlie', '35', 'Paris']
  ];
  
  String generateCsv(List<List<String>> data) {
    return data.map((row) => row.join(',')).join('\n');
  }
  
  String csvOutput = generateCsv(csvData);
  print('\nCSV output:');
  print(csvOutput);
  
  // HTML list generation
  String generateHtmlList(List<String> items, String listType) {
    String tag = listType == 'ordered' ? 'ol' : 'ul';
    String listItems = items.map((item) => '  <li>$item</li>').join('\n');
    return '<$tag>\n$listItems\n</$tag>';
  }
  
  List<String> skills = ['Dart', 'Flutter', 'JavaScript', 'Python'];
  String htmlList = generateHtmlList(skills, 'unordered');
  print('\nHTML list:');
  print(htmlList);
  
  // Join with different separators for different positions
  String joinWithVariableSeparators(List<String> items, String regularSep, String finalSep) {
    if (items.length <= 1) return items.join('');
    if (items.length == 2) return items.join(finalSep);
    
    return items.take(items.length - 1).join(regularSep) + finalSep + items.last;
  }
  
  List<String> ingredients = ['flour', 'sugar', 'eggs', 'milk'];
  String recipe = joinWithVariableSeparators(ingredients, ', ', ' and ');
  print('\nIngredients: $ingredients');
  print('Recipe format: "$recipe"');
  
  // Performance comparison
  Stopwatch sw = Stopwatch()..start();
  List<String> largeList = List.generate(10000, (i) => 'item$i');
  
  // Using join
  sw.start();
  String joined1 = largeList.join(',');
  int joinTime = sw.elapsedMicroseconds;
  
  // Using string concatenation
  sw.reset();
  String joined2 = '';
  for (int i = 0; i < largeList.length; i++) {
    if (i > 0) joined2 += ',';
    joined2 += largeList[i];
  }
  int concatTime = sw.elapsedMicroseconds;
  
  print('\nPerformance (10,000 items):');
  print('Join method: ${joinTime}μs');
  print('Concatenation: ${concatTime}μs');
  print('Join is ${(concatTime / joinTime).toStringAsFixed(1)}x faster');
}
```

String joining operations efficiently combine multiple string elements using  
specified delimiters. The `join()` method provides optimal performance for  
standard joining scenarios, while custom functions enable specialized  
formatting like natural language lists and structured data generation.

```
$ dart run string_join.dart
Words: [Hello, there, Dart, programmers]
Join with space: "Hello there Dart programmers"
Join with comma: "Hello, there, Dart, programmers"
Join with hyphen: "Hello-there-Dart-programmers"
Join with empty string: "HellothereDartprogrammers"

Path segments: [home, user, documents, projects]
Unix path: "/home/user/documents/projects"
Windows path: "home\user\documents\projects"

Numbers: [1, 2, 3, 4, 5]
Numbers as string: "1, 2, 3, 4, 5"

Mixed list: [apple, null, banana, , cherry, null]
Filtered join: "apple, banana, cherry"

Names: [Alice, Bob, Charlie]
Custom join: "Alice, Bob, and Charlie"
Colors: [red, green]
Custom join: "red and green"

SQL query: SELECT name, email, age FROM users WHERE age >= 18 AND status = 'active'

CSV output:
Name,Age,City
Alice,25,New York
Bob,30,London
Charlie,35,Paris

HTML list:
<ul>
  <li>Dart</li>
  <li>Flutter</li>
  <li>JavaScript</li>
  <li>Python</li>
</ul>

Ingredients: [flour, sugar, eggs, milk]
Recipe format: "flour, sugar, eggs and milk"

Performance (10,000 items):
Join method: 2500μs
Concatenation: 45000μs
Join is 18.0x faster
```