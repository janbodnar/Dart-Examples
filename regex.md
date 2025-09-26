
# Dart Regular Expressions

Regular expressions are powerful tools for pattern matching, text searching,  
and advanced text manipulation. In Dart, they enable efficient string  
processing for validation, extraction, and transformation tasks.  

Regular expressions are found in command-line tools like grep and sed, text  
editors such as vi and Emacs, and virtually all modern programming languages.  
They provide a concise way to describe complex text patterns.  

`RegExp` is Dart's class for defining regular expressions. Dart regular  
expressions follow the same syntax and semantics as JavaScript regular  
expressions, making them familiar to web developers.  

## Basic Pattern Matching

The `hasMatch` method checks whether a regular expression pattern has any  
match within a specified string. It returns a boolean value indicating  
whether the pattern was found.  

```dart
void main() {
  var words = <String>['Seven', 'even', 'Maven', 'Amen', 'eleven'];
  
  var rx = RegExp(r'.even');
  
  for (var word in words) {
    if (rx.hasMatch(word)) {
      print('$word matches the pattern');
    } else {
      print('$word does not match');
    }
  }
}
```

This example demonstrates basic pattern matching with five words. We check  
which words match the `.even` regular expression pattern.  

The `RegExp(r'.even')` pattern uses the dot (.) metacharacter, which matches  
any single character followed by 'even'. The 'r' prefix creates a raw string,  
preventing Dart from interpreting escape sequences.  

The `hasMatch` method efficiently determines pattern presence without  
extracting the actual match, making it ideal for validation scenarios.

## Alternation Patterns

The alternation operator `|` creates regular expressions with multiple  
choices, allowing a pattern to match any of several alternatives.  

```dart
void main() {
  var names = [
    'Jane',
    'Thomas',
    'Robert',
    'Lucy',
    'Beky',
    'John',
    'Peter',
    'Andy'
  ];
  
  var rx = RegExp('Jane|Beky|Robert');
  
  names.forEach((name) {
    if (rx.hasMatch(name)) {
      print('$name is in our target list');
    }
  });
}
```

This example searches through eight names to find specific matches. The  
alternation pattern `Jane|Beky|Robert` matches any string containing  
'Jane', 'Beky', or 'Robert'.  

Alternation is useful for creating flexible patterns that can match multiple  
variations without requiring separate regular expressions for each option.


## Subpatterns and Grouping

Subpatterns are patterns within patterns, created using parentheses `()`.  
They enable grouping of pattern elements and application of quantifiers  
to entire groups rather than single characters.  

```dart
void main() {
  var words = [
    'book',
    'bookshelf',
    'bookworm',
    'bookcase',
    'bookish',
    'bookkeeper',
    'booklet',
    'bookmark'
  ];
  
  var rx = RegExp(r'^book(worm|mark|keeper)?$');
  
  for (var word in words) {
    if (rx.hasMatch(word)) {
      print('$word matches our book pattern');
    } else {
      print('$word does not match');
    }
  }
}
```

This example finds words matching specific book-related patterns. The  
subpattern `(worm|mark|keeper)?` creates an optional group that matches  
'worm', 'mark', or 'keeper' after 'book'.  

The `^` and `$` anchors ensure the pattern matches the entire word, not just  
a portion. The `?` quantifier makes the subpattern optional, allowing 'book'  
alone to match as well as compound words.





## Finding All Matches

The `allMatches` method finds every occurrence of a pattern within text,  
returning an iterable of Match objects that contain position and content  
information for each match.  

```dart
void main() {
  var text = 'The Sun was shining; I went for a walk.';
  
  var rx = RegExp(r'\w+');
  
  var found = rx.allMatches(text);
  print('Found ${found.length} words');
  
  found.forEach((match) {
    print('${match.group(0)} at positions ${match.start}:${match.end}');
  });
}
```

This example extracts all words from a sentence. The pattern `\w+` matches  
sequences of word characters (letters, digits, and underscores).  

The `allMatches` method returns all non-overlapping matches. Each Match  
object provides the matched text via `group(0)` and position information  
through `start` and `end` properties.  

This approach is efficient for extracting multiple pieces of information  
from text in a single pass, making it ideal for parsing and data extraction  
tasks.


## Capturing Groups

Capturing groups use parentheses to extract specific parts of matched text.  
They enable precise data extraction by dividing complex patterns into  
meaningful components that can be accessed individually.  

```dart
void main() {
  var sites = ['webcode.me', 'zetcode.com', 'freebsd.org', 'netbsd.org'];
  
  var rx = RegExp(r'(\w+)\.(\w+)');
  
  for (var site in sites) {
    var matches = rx.allMatches(site);
    matches.forEach((match) {
      print('Full match: ${match.group(0)}');
      print('Domain: ${match.group(1)}');
      print('Extension: ${match.group(2)}');
      print('---');
    });
  }
}
```

This example demonstrates capturing groups by parsing domain names. The  
pattern `(\w+)\.(\w+)` creates two capturing groups separated by a literal  
dot character.  

Groups are accessed using the `group` method: `group(0)` returns the entire  
match, while `group(1)` and `group(2)` return the first and second captured  
groups respectively.  

Capturing groups are essential for structured data extraction, enabling  
complex parsing operations in a single regular expression.

## Character Classes and Ranges

Character classes define sets of characters that can match at a single  
position. They provide efficient ways to match specific character types  
or ranges.  

```dart
void main() {
  var text = 'Hello123 World! @#\$%';
  
  var patterns = {
    r'[a-z]+': 'Lowercase letters',
    r'[A-Z]+': 'Uppercase letters', 
    r'[0-9]+': 'Digits',
    r'[a-zA-Z]+': 'Any letters',
    r'[^a-zA-Z0-9\s]+': 'Special characters'
  };
  
  patterns.forEach((pattern, description) {
    var rx = RegExp(pattern);
    var matches = rx.allMatches(text);
    print('$description: ${matches.map((m) => m.group(0)).join(', ')}');
  });
}
```

Character classes use square brackets to define character sets. Ranges are  
specified with hyphens (e.g., `a-z`), and negation uses the caret symbol  
(`^`) at the beginning of the class.  

The pattern `[^a-zA-Z0-9\s]` demonstrates negated character classes,  
matching anything that is NOT a letter, digit, or whitespace character.

## Quantifiers and Repetition

Quantifiers specify how many times a pattern element should be repeated.  
They provide flexible matching for patterns of varying lengths.  

```dart
void main() {
  var text = 'a aa aaa aaaa hello there! test123 x y';
  
  var patterns = {
    r'a+': 'One or more a',
    r'a*': 'Zero or more a',
    r'a?': 'Zero or one a',
    r'a{3}': 'Exactly three a',
    r'a{2,4}': 'Two to four a',
    r'\w{5,}': 'Five or more word chars'
  };
  
  patterns.forEach((pattern, description) {
    var rx = RegExp(pattern);
    var matches = rx.allMatches(text);
    print('$description: ${matches.map((m) => m.group(0)).join(', ')}');
  });
}
```

Common quantifiers include `+` (one or more), `*` (zero or more), `?`  
(zero or one), `{n}` (exactly n), and `{n,m}` (between n and m times).  

Quantifiers are greedy by default, matching as many characters as possible.  
Adding `?` after a quantifier makes it non-greedy (lazy), matching the  
minimum possible characters.

## Anchors and Boundaries

Anchors and boundaries define positions within text rather than matching  
specific characters. They control where matches can occur relative to  
word boundaries, line boundaries, and string boundaries.  

```dart
void main() {
  var lines = [
    'start middle end',
    'prefix start',
    'end suffix', 
    'start',
    'end'
  ];
  
  var patterns = {
    r'^start': 'Start of line/string',
    r'end$': 'End of line/string',
    r'\bstart\b': 'Whole word "start"',
    r'\Bend': 'Not at word boundary before "end"',
  };
  
  patterns.forEach((pattern, description) {
    var rx = RegExp(pattern);
    print('$description:');
    lines.forEach((line) {
      if (rx.hasMatch(line)) {
        print('  Matches: "$line"');
      }
    });
    print('');
  });
}
```

The `^` anchor matches the start of a string or line, while `$` matches the  
end. Word boundaries `\b` match positions between word and non-word  
characters, while `\B` matches non-word boundaries.  

These anchors are crucial for precise pattern matching, especially when  
distinguishing between partial and complete word matches.

## Case Sensitivity Control

Regular expressions can be made case-insensitive using the `caseSensitive`  
parameter, enabling flexible text matching regardless of letter case.  

```dart
void main() {
  var text = 'Hello there! HELLO world. hello again.';
  
  var caseSensitive = RegExp('hello');
  var caseInsensitive = RegExp('hello', caseSensitive: false);
  
  print('Case sensitive matches:');
  caseSensitive.allMatches(text).forEach((match) {
    print('  "${match.group(0)}" at ${match.start}:${match.end}');
  });
  
  print('\nCase insensitive matches:');
  caseInsensitive.allMatches(text).forEach((match) {
    print('  "${match.group(0)}" at ${match.start}:${match.end}');
  });
  
  // Alternative using RegExp constructor
  var alternative = RegExp(r'WORLD', caseSensitive: false);
  print('\nAlternative case insensitive:');
  if (alternative.hasMatch(text)) {
    print('  Found "world" in any case');
  }
}
```

Case-insensitive matching is essential for user input validation and text  
processing where case variations are common. This approach is more efficient  
than converting strings to lowercase before matching.

## Email Validation Pattern

Email validation using regular expressions requires careful pattern design  
to balance accuracy with practical usability. This example shows a  
comprehensive email validation approach.  

```dart
void main() {
  var emails = [
    'user@example.com',
    'test.email@domain.co.uk',
    'invalid@',
    '@invalid.com',
    'no-at-sign.com',
    'spaces @invalid.com',
    'valid_123@test-domain.org'
  ];
  
  var emailPattern = RegExp(
    r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
  );
  
  print('Email validation results:');
  emails.forEach((email) {
    var isValid = emailPattern.hasMatch(email);
    print('  $email: ${isValid ? 'Valid' : 'Invalid'}');
  });
}
```

This email pattern breaks down as follows: `^[a-zA-Z0-9._%+-]+` matches  
the username portion, `@` requires the at symbol, `[a-zA-Z0-9.-]+` matches  
the domain name, and `\.[a-zA-Z]{2,}$` requires a domain extension.  

While no single regex can perfectly validate all email formats, this  
pattern covers most common cases while rejecting obviously invalid inputs.

## Phone Number Validation

Phone number validation requires handling multiple formats and optional  
components like country codes, area codes, and different separator  
characters.  

```dart
void main() {
  var phoneNumbers = [
    '(555) 123-4567',
    '555-123-4567',
    '555.123.4567',
    '5551234567',
    '+1 (555) 123-4567',
    '123-45-6789', // Too few digits
    '555-123-456'   // Too few digits
  ];
  
  var phonePattern = RegExp(
    r'^\+?1?\s*\(?[0-9]{3}\)?\s*[-.\s]?[0-9]{3}[-.\s]?[0-9]{4}$'
  );
  
  print('Phone number validation:');
  phoneNumbers.forEach((phone) {
    var isValid = phonePattern.hasMatch(phone);
    print('  $phone: ${isValid ? 'Valid' : 'Invalid'}');
  });
}
```

This pattern handles optional country code (`\+?1?`), optional parentheses  
around area code (`\(?[0-9]{3}\)?`), and flexible separators between  
number groups (`[-.\s]?`).  

Phone validation patterns must balance flexibility with accuracy, accounting  
for the many ways people format phone numbers while ensuring the correct  
number of digits.

## URL Pattern Matching

URL validation and extraction requires handling multiple components including  
protocol, domain, path, and query parameters. This example demonstrates  
comprehensive URL pattern matching.  

```dart
void main() {
  var urls = [
    'https://www.example.com',
    'http://subdomain.test.co.uk/path',
    'https://example.com:8080/path?query=value',
    'ftp://files.example.org/directory/',
    'invalid-url',
    'www.example.com', // Missing protocol
  ];
  
  var urlPattern = RegExp(
    r'^https?:\/\/(www\.)?[-a-zA-Z0-9@:%._\+~#=]{1,256}\.[a-zA-Z0-9()]{1,6}\b([-a-zA-Z0-9()@:%_\+.~#?&//=]*)$'
  );
  
  print('URL validation:');
  urls.forEach((url) {
    var isValid = urlPattern.hasMatch(url);
    print('  $url: ${isValid ? 'Valid HTTP/HTTPS URL' : 'Invalid'}');
  });
}
```

This URL pattern validates HTTP and HTTPS protocols, optional www prefix,  
domain names with various characters, and optional paths with query  
parameters.  

URL patterns are complex due to the variety of valid URL formats. This  
pattern covers common web URLs while being permissive enough for practical  
use cases.

## Non-Capturing Groups

Non-capturing groups use `(?:...)` syntax to group pattern elements without  
creating numbered capture groups. They improve performance and reduce  
memory usage when grouping is needed only for quantifiers or alternation.  

```dart
void main() {
  var text = 'color colour flavor flavour';
  
  // With capturing groups (creates groups 1, 2, 3, 4)
  var capturing = RegExp(r'(colou?r)|(flavou?r)');
  
  // With non-capturing groups (no numbered groups)
  var nonCapturing = RegExp(r'(?:colou?r)|(?:flavou?r)');
  
  print('Capturing groups:');
  capturing.allMatches(text).forEach((match) {
    print('  Match: ${match.group(0)}');
    print('  Group 1: ${match.group(1)}');
    print('  Group 2: ${match.group(2)}');
  });
  
  print('\nNon-capturing groups:');
  nonCapturing.allMatches(text).forEach((match) {
    print('  Match: ${match.group(0)}');
    // No numbered groups available
  });
}
```

Non-capturing groups are preferable when you need grouping for pattern  
structure but don't need to extract the grouped content. They make patterns  
more efficient and easier to maintain.

## Greedy vs Non-Greedy Quantifiers

Quantifiers can be greedy (default) or non-greedy (lazy). Understanding  
the difference is crucial for precise pattern matching, especially when  
dealing with nested structures or delimited content.  

```dart
void main() {
  var html = '<div>First</div><div>Second</div>';
  
  var greedy = RegExp(r'<div>.*</div>');
  var nonGreedy = RegExp(r'<div>.*?</div>');
  
  print('Original: $html\n');
  
  print('Greedy quantifier (.*):');
  greedy.allMatches(html).forEach((match) {
    print('  "${match.group(0)}"');
  });
  
  print('\nNon-greedy quantifier (.*?):');
  nonGreedy.allMatches(html).forEach((match) {
    print('  "${match.group(0)}"');
  });
}
```

Greedy quantifiers match as much as possible, while non-greedy quantifiers  
match as little as possible. The `?` after a quantifier makes it non-greedy.  

This distinction is crucial when parsing markup, extracting quoted strings,  
or matching content between delimiters where multiple instances exist.

## Lookahead and Lookbehind Assertions

Lookahead and lookbehind assertions check for patterns that must be present  
(or absent) before or after the main match, without including them in the  
match result.  

```dart
void main() {
  var text = 'password123 secret456 public789 password_admin';
  
  // Positive lookahead: match 'password' followed by digits
  var positiveLookahead = RegExp(r'password(?=\d+)');
  
  // Negative lookahead: match 'password' NOT followed by underscore
  var negativeLookahead = RegExp(r'password(?!_)');
  
  // Positive lookbehind: match digits preceded by 'secret'
  var positiveLookbehind = RegExp(r'(?<=secret)\d+');
  
  print('Positive lookahead (password followed by digits):');
  positiveLookahead.allMatches(text).forEach((match) {
    print('  "${match.group(0)}"');
  });
  
  print('\nNegative lookahead (password NOT followed by underscore):');
  negativeLookahead.allMatches(text).forEach((match) {
    print('  "${match.group(0)}"');
  });
  
  print('\nPositive lookbehind (digits after secret):');
  positiveLookbehind.allMatches(text).forEach((match) {
    print('  "${match.group(0)}"');
  });
}
```

Assertions are powerful for contextual matching where the surrounding text  
matters but shouldn't be part of the match. They enable sophisticated  
pattern matching without complex post-processing.

## String Splitting with Regex

The `split` method can use regular expressions to divide strings at complex  
boundaries, providing more flexibility than simple string splitting.  

```dart
void main() {
  var data = 'apple,banana;orange:grape|kiwi';
  var text = 'The quick   brown\tfox\njumps';
  var csv = 'name,age,city\nAlice,25,"New York"\nBob,30,London';
  
  // Split on multiple delimiters
  var multiDelimiter = RegExp(r'[,;:|]');
  print('Multi-delimiter split:');
  print('  ${data.split(multiDelimiter)}');
  
  // Split on whitespace (any amount)
  var whitespace = RegExp(r'\s+');
  print('\nWhitespace split:');
  print('  ${text.split(whitespace)}');
  
  // Split CSV but respect quoted fields
  var csvPattern = RegExp(r',(?=(?:[^"]*"[^"]*")*[^"]*$)');
  print('\nCSV split (respecting quotes):');
  csv.split('\n').forEach((line) {
    if (line.isNotEmpty) {
      print('  ${line.split(csvPattern)}');
    }
  });
}
```

Regular expression splitting handles complex scenarios where simple string  
delimiters are insufficient. The CSV example shows how to split on commas  
while preserving quoted fields containing commas.

## String Replacement with Patterns

The `replaceAll` and `replaceFirst` methods accept regular expressions,  
enabling sophisticated text transformations and data cleaning operations.  

```dart
void main() {
  var text = 'Hello there! How are you today? I hope you are well.';
  var html = '<p>This is <strong>bold</strong> text</p>';
  var phone = 'Call me at (555) 123-4567 or (555) 987-6543';
  
  // Replace multiple whitespace with single space
  var cleanText = text.replaceAll(RegExp(r'\s+'), ' ');
  print('Cleaned whitespace: $cleanText');
  
  // Remove HTML tags
  var plainText = html.replaceAll(RegExp(r'<[^>]*>'), '');
  print('Removed HTML: $plainText');
  
  // Format phone numbers
  var phonePattern = RegExp(r'\((\d{3})\)\s*(\d{3})-(\d{4})');
  var formattedPhone = phone.replaceAll(phonePattern, r'$1-$2-$3');
  print('Formatted phones: $formattedPhone');
  
  // Replace with callback function
  var callback = text.replaceAllMapped(RegExp(r'\b\w+\b'), (match) {
    var word = match.group(0)!;
    return word.length > 3 ? word.toUpperCase() : word;
  });
  print('Long words uppercase: $callback');
}
```

Pattern replacement supports backreferences (`$1`, `$2`) for captured groups  
and callback functions for complex transformations. This enables powerful  
text processing operations in concise code.

## Named Capturing Groups

Named groups provide descriptive names for captured content, improving  
code readability and maintainability compared to numbered groups.  

```dart
void main() {
  var logs = [
    '2023-12-15 14:30:25 ERROR: Connection failed',
    '2023-12-15 14:31:02 INFO: User login successful',
    '2023-12-15 14:31:45 WARN: High memory usage detected'
  ];
  
  var logPattern = RegExp(
    r'(?<date>\d{4}-\d{2}-\d{2}) (?<time>\d{2}:\d{2}:\d{2}) (?<level>\w+): (?<message>.*)'
  );
  
  print('Log parsing with named groups:');
  logs.forEach((log) {
    var match = logPattern.firstMatch(log);
    if (match != null) {
      print('  Date: ${match.namedGroup('date')}');
      print('  Time: ${match.namedGroup('time')}');
      print('  Level: ${match.namedGroup('level')}');
      print('  Message: ${match.namedGroup('message')}');
      print('  ---');
    }
  });
}
```

Named groups use `(?<name>pattern)` syntax and are accessed via  
`namedGroup('name')`. They make complex patterns self-documenting and  
reduce errors when working with many capture groups.

## Unicode Support and Character Properties

Dart's regex engine supports Unicode character properties, enabling  
matching based on character categories, scripts, and other Unicode  
attributes.  

```dart
void main() {
  var multilingualText = 'Hello مرحبا 你好 🌍 123 αβγ';
  
  var patterns = {
    r'\p{L}+': 'Letter characters',
    r'\p{N}+': 'Number characters',
    r'\p{So}': 'Other symbols (emoji)',
    r'\p{Script=Latin}+': 'Latin script',
    r'\p{Script=Arabic}+': 'Arabic script',
    r'\p{Script=Han}+': 'Chinese characters'
  };
  
  print('Unicode property matching:');
  patterns.forEach((pattern, description) {
    var rx = RegExp(pattern, unicode: true);
    var matches = rx.allMatches(multilingualText);
    if (matches.isNotEmpty) {
      print('$description: ${matches.map((m) => m.group(0)).join(', ')}');
    }
  });
}
```

Unicode properties provide precise character matching across different  
languages and scripts. The `unicode: true` flag enables full Unicode  
support in the regular expression engine.

## Multiline Mode and Dot-All

Multiline mode changes the behavior of `^` and `$` anchors to match  
line boundaries within strings, while dot-all mode makes `.` match  
newline characters.  

```dart
void main() {
  var multilineText = '''First line
Second line
Third line''';
  
  // Default: ^ and $ match string boundaries only
  var defaultMode = RegExp(r'^Second');
  
  // Multiline: ^ and $ match line boundaries
  var multilineMode = RegExp(r'^Second', multiLine: true);
  
  // Default: . doesn't match newlines
  var defaultDot = RegExp(r'First.*Third');
  
  // Dot-all: . matches newlines too
  var dotAllMode = RegExp(r'First.*Third', dotAll: true);
  
  print('Text: "${multilineText.replaceAll('\n', '\\n')}"');
  print('');
  
  print('Default ^ anchor: ${defaultMode.hasMatch(multilineText)}');
  print('Multiline ^ anchor: ${multilineMode.hasMatch(multilineText)}');
  print('');
  
  print('Default . matching: ${defaultDot.hasMatch(multilineText)}');
  print('Dot-all . matching: ${dotAllMode.hasMatch(multilineText)}');
}
```

Understanding these modes is crucial for processing multiline text where  
line boundaries and newline characters affect pattern matching behavior.

## Password Strength Validation

Password validation requires checking multiple criteria simultaneously.  
Regular expressions can validate complex password requirements efficiently.  

```dart
void main() {
  var passwords = [
    'password123',
    'Password123!',
    'P@ssw0rd',
    'StrongPass123!',
    'weak',
    'ALLUPPERCASE123!',
    'alllowercase123!'
  ];
  
  var requirements = {
    r'^.{8,}$': 'At least 8 characters',
    r'^(?=.*[a-z])': 'Contains lowercase letter',
    r'^(?=.*[A-Z])': 'Contains uppercase letter', 
    r'^(?=.*\d)': 'Contains digit',
    r'^(?=.*[!@#\$&*])': 'Contains special character',
  };
  
  print('Password strength validation:');
  passwords.forEach((password) {
    print('\nPassword: "$password"');
    var score = 0;
    requirements.forEach((pattern, description) {
      var matches = RegExp(pattern).hasMatch(password);
      print('  $description: ${matches ? '✓' : '✗'}');
      if (matches) score++;
    });
    print('  Strength: ${_getStrength(score)}/5');
  });
}

String _getStrength(int score) {
  switch (score) {
    case 5: return 'Very Strong';
    case 4: return 'Strong';
    case 3: return 'Medium';
    case 2: return 'Weak';
    default: return 'Very Weak';
  }
}
```

This approach uses positive lookahead assertions to check each requirement  
independently, providing detailed feedback about password strength and  
specific missing criteria.

## Credit Card Number Validation

Credit card validation involves checking number format, length, and  
applying the Luhn algorithm. Regular expressions handle the format  
validation efficiently.  

```dart
void main() {
  var cardNumbers = [
    '4532-1234-5678-9012', // Visa format
    '4532123456789012',     // Visa no dashes
    '5555-5555-5555-4444', // Mastercard
    '3782-822463-10005',   // American Express
    '1234-5678-9012',      // Too short
    'abcd-efgh-ijkl-mnop'  // Invalid characters
  ];
  
  var cardPatterns = {
    'Visa': RegExp(r'^4\d{3}[-\s]?\d{4}[-\s]?\d{4}[-\s]?\d{4}$'),
    'Mastercard': RegExp(r'^5[1-5]\d{2}[-\s]?\d{4}[-\s]?\d{4}[-\s]?\d{4}$'),
    'American Express': RegExp(r'^3[47]\d{2}[-\s]?\d{6}[-\s]?\d{5}$'),
  };
  
  print('Credit card validation:');
  cardNumbers.forEach((number) {
    print('\nCard: $number');
    var cardType = 'Unknown';
    var isValidFormat = false;
    
    cardPatterns.forEach((type, pattern) {
      if (pattern.hasMatch(number)) {
        cardType = type;
        isValidFormat = true;
      }
    });
    
    print('  Type: $cardType');
    print('  Format: ${isValidFormat ? 'Valid' : 'Invalid'}');
  });
}
```

Different card types have distinct number patterns and lengths. This  
validation approach identifies card types and verifies format correctness  
before applying additional validation like the Luhn algorithm.

## IPv4 Address Validation

IP address validation requires checking numeric ranges and dotted decimal  
format. Regular expressions provide efficient validation for network  
addresses.  

```dart
void main() {
  var ipAddresses = [
    '192.168.1.1',
    '10.0.0.255',
    '172.16.0.1',
    '256.1.1.1',    // Invalid: > 255
    '192.168.1',    // Missing octet
    '192.168.1.1.1', // Extra octet
    '192.168.01.1', // Leading zero (debatable)
  ];
  
  // Simple pattern (may allow invalid ranges)
  var simplePattern = RegExp(r'^\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}$');
  
  // Precise pattern (validates 0-255 range)
  var precisePattern = RegExp(
    r'^(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$'
  );
  
  print('IPv4 address validation:');
  ipAddresses.forEach((ip) {
    var simpleValid = simplePattern.hasMatch(ip);
    var preciseValid = precisePattern.hasMatch(ip);
    print('$ip: Simple=${simpleValid ? '✓' : '✗'}, Precise=${preciseValid ? '✓' : '✗'}');
  });
}
```

The precise pattern validates that each octet is in the 0-255 range by  
breaking down the validation into specific cases: 250-255, 200-249,  
100-199, 10-99, and 0-9.

## Date Format Validation

Date validation requires handling various formats while ensuring valid  
day, month, and year ranges. Regular expressions provide format checking  
before applying date logic validation.  

```dart
void main() {
  var dates = [
    '2023-12-15',     // ISO format
    '12/15/2023',     // US format
    '15/12/2023',     // European format
    '15-Dec-2023',    // Month name
    '2023/13/45',     // Invalid month/day
    'Dec 15, 2023',   // Long format
    '2023-2-5'        // No leading zeros
  ];
  
  var datePatterns = {
    'ISO (YYYY-MM-DD)': RegExp(r'^\d{4}-\d{2}-\d{2}$'),
    'US (MM/DD/YYYY)': RegExp(r'^\d{1,2}/\d{1,2}/\d{4}$'),
    'European (DD/MM/YYYY)': RegExp(r'^\d{1,2}/\d{1,2}/\d{4}$'),
    'Month name (DD-MMM-YYYY)': RegExp(r'^\d{1,2}-[A-Za-z]{3}-\d{4}$'),
    'Long format': RegExp(r'^[A-Za-z]{3}\s+\d{1,2},\s+\d{4}$'),
  };
  
  print('Date format validation:');
  dates.forEach((date) {
    print('\nDate: "$date"');
    datePatterns.forEach((format, pattern) {
      var matches = pattern.hasMatch(date);
      if (matches) {
        print('  Matches: $format');
      }
    });
  });
}
```

Date format validation is the first step in date processing. After format  
validation, additional logic should verify that dates are valid (correct  
month ranges, leap years, etc.).

## HTML Tag Extraction and Cleaning

Regular expressions can extract or remove HTML tags, though they have  
limitations for complex nested structures. This example shows practical  
HTML processing patterns.  

```dart
void main() {
  var html = '''
    <div class="content">
      <h1>Title</h1>
      <p>This is a <strong>bold</strong> paragraph with a 
      <a href="https://example.com">link</a>.</p>
      <img src="image.jpg" alt="Description">
      <!-- This is a comment -->
    </div>
  ''';
  
  // Extract all tags
  var tagPattern = RegExp(r'<[^>]+>');
  print('All HTML tags:');
  tagPattern.allMatches(html).forEach((match) {
    print('  ${match.group(0)}');
  });
  
  // Remove all tags (simple approach)
  var withoutTags = html.replaceAll(RegExp(r'<[^>]*>'), '');
  print('\nContent without tags:');
  print(withoutTags.trim());
  
  // Extract link URLs
  var linkPattern = RegExp(r'href="([^"]*)"');
  print('\nExtracted URLs:');
  linkPattern.allMatches(html).forEach((match) {
    print('  ${match.group(1)}');
  });
  
  // Remove comments
  var withoutComments = html.replaceAll(RegExp(r'<!--.*?-->'), '');
  print('\nHTML without comments:');
  print(withoutComments.trim());
}
```

While regular expressions work for simple HTML processing, complex nested  
structures require proper HTML parsers. Use regex for basic cleaning and  
extraction tasks only.

## Log File Analysis

Log files contain structured information that regular expressions can  
efficiently extract and analyze. This example demonstrates parsing  
common log formats.  

```dart
void main() {
  var logEntries = [
    '192.168.1.100 - - [15/Dec/2023:14:30:25 +0000] "GET /home HTTP/1.1" 200 1234',
    '10.0.0.50 - admin [15/Dec/2023:14:31:02 +0000] "POST /login HTTP/1.1" 302 0',
    '172.16.1.200 - - [15/Dec/2023:14:31:45 +0000] "GET /api/data HTTP/1.1" 404 512',
  ];
  
  // Apache Common Log Format pattern
  var logPattern = RegExp(
    r'^(\d+\.\d+\.\d+\.\d+) - (\S+) \[([^\]]+)\] "(\w+) (\S+) HTTP/[\d.]+" (\d+) (\d+)$'
  );
  
  print('Log file analysis:');
  logEntries.forEach((entry) {
    var match = logPattern.firstMatch(entry);
    if (match != null) {
      print('IP: ${match.group(1)}');
      print('User: ${match.group(2)}');
      print('Timestamp: ${match.group(3)}');
      print('Method: ${match.group(4)}');
      print('Resource: ${match.group(5)}');
      print('Status: ${match.group(6)}');
      print('Size: ${match.group(7)} bytes');
      print('---');
    }
  });
}
```

Log analysis with regex enables quick extraction of key information for  
monitoring, debugging, and analytics. Patterns can be adapted for different  
log formats and custom fields.

## Configuration File Parsing

Configuration files often use key-value pairs with various formats.  
Regular expressions can parse these efficiently while handling comments  
and different syntaxes.  

```dart
void main() {
  var configContent = '''
    # Database configuration
    db.host=localhost
    db.port=5432
    db.name="myapp_db"
    
    # Server settings
    server_port = 8080
    debug_mode=true
    
    # Comments and empty lines
    # max_connections = 100
    log_level = "INFO"
  ''';
  
  var patterns = {
    'Key-Value pairs': RegExp(r'^(\w+(?:\.\w+)*)\s*=\s*(.+)$', multiLine: true),
    'Comments': RegExp(r'^\s*#.*$', multiLine: true),
    'Quoted values': RegExp(r'"([^"]*)"'),
    'Boolean values': RegExp(r'\b(true|false)\b', caseSensitive: false),
  };
  
  print('Configuration parsing:');
  
  // Extract key-value pairs
  var kvPairs = patterns['Key-Value pairs']!.allMatches(configContent);
  print('\nConfiguration settings:');
  kvPairs.forEach((match) {
    var key = match.group(1);
    var value = match.group(2)?.trim();
    print('  $key = $value');
  });
  
  // Extract comments
  var comments = patterns['Comments']!.allMatches(configContent);
  print('\nComments found: ${comments.length}');
  
  // Extract quoted strings
  var quoted = patterns['Quoted values']!.allMatches(configContent);
  print('\nQuoted values:');
  quoted.forEach((match) {
    print('  "${match.group(1)}"');
  });
}
```

Configuration parsing with regex handles various syntaxes and formats  
commonly found in config files, including comments, quotes, and different  
assignment operators.

## Data Extraction from Structured Text

Regular expressions excel at extracting structured data from formatted  
text, such as addresses, financial information, or technical specifications.  

```dart
void main() {
  var document = '''
    Contact Information:
    John Doe
    Phone: (555) 123-4567
    Email: john.doe@example.com
    Address: 123 Main St, Anytown, ST 12345
    
    Financial Details:
    Account: 1234-5678-9012-3456
    Amount: \$1,234.56
    Date: 2023-12-15
    
    Technical Specs:
    CPU: Intel i7-12700K @ 3.60GHz
    RAM: 32GB DDR4-3200
    Storage: 1TB NVMe SSD
  ''';
  
  var extractionPatterns = {
    'Names': RegExp(r'^[A-Z][a-z]+ [A-Z][a-z]+$', multiLine: true),
    'Phone Numbers': RegExp(r'\(\d{3}\) \d{3}-\d{4}'),
    'Email Addresses': RegExp(r'\b[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}\b'),
    'Addresses': RegExp(r'\d+\s+[A-Z][a-z]+\s+St,\s+[A-Z][a-z]+,\s+[A-Z]{2}\s+\d{5}'),
    'Currency': RegExp(r'\$[\d,]+\.\d{2}'),
    'Dates': RegExp(r'\d{4}-\d{2}-\d{2}'),
    'Technical Specs': RegExp(r'(\w+):\s*(.+?)(?=\n\w+:|$)', multiLine: true, dotAll: true),
  };
  
  print('Data extraction from structured text:');
  extractionPatterns.forEach((type, pattern) {
    var matches = pattern.allMatches(document);
    if (matches.isNotEmpty) {
      print('\n$type:');
      matches.forEach((match) {
        if (type == 'Technical Specs') {
          print('  ${match.group(1)}: ${match.group(2)?.trim()}');
        } else {
          print('  ${match.group(0)}');
        }
      });
    }
  });
}
```

Structured text extraction enables automated data processing from documents,  
reports, and formatted text sources, reducing manual data entry and  
improving accuracy.

## Performance Optimization Techniques

Regular expression performance can vary significantly based on pattern  
complexity and input size. Understanding optimization techniques helps  
create efficient regex patterns.  

```dart
void main() {
  var largeText = 'word ' * 10000 + 'target' + ' word ' * 10000;
  
  // Inefficient: catastrophic backtracking
  var inefficient = RegExp(r'(w+)+t');
  
  // Efficient: specific pattern
  var efficient = RegExp(r'target');
  
  // Compile once, use multiple times
  var compiled = RegExp(r'\btarget\b');
  
  print('Performance comparison:');
  
  // Time the efficient pattern
  var stopwatch = Stopwatch()..start();
  var found = efficient.hasMatch(largeText);
  stopwatch.stop();
  print('Efficient pattern: ${stopwatch.elapsedMicroseconds}μs (found: $found)');
  
  // Pre-compiled usage
  stopwatch.reset();
  stopwatch.start();
  for (int i = 0; i < 1000; i++) {
    compiled.hasMatch('This is a target word');
  }
  stopwatch.stop();
  print('Pre-compiled 1000x: ${stopwatch.elapsedMicroseconds}μs');
  
  print('\nOptimization tips:');
  print('  - Use specific patterns over generic ones');
  print('  - Avoid nested quantifiers when possible');
  print('  - Compile patterns once and reuse');
  print('  - Use non-capturing groups when appropriate');
  print('  - Consider string methods for simple searches');
}
```

Performance optimization involves choosing appropriate patterns, avoiding  
catastrophic backtracking, and reusing compiled expressions for repeated  
operations.

## Best Practices and Common Pitfalls

Understanding regex best practices and common mistakes helps write  
maintainable and efficient patterns while avoiding subtle bugs and  
performance issues.  

```dart
void main() {
  print('Regular Expression Best Practices:\n');
  
  // 1. Use raw strings for regex patterns
  print('1. Use raw strings:');
  print('   Good: RegExp(r\'\\d+\')');
  print('   Bad:  RegExp(\'\\\\d+\')');
  
  // 2. Be specific with character classes
  print('\n2. Be specific with character classes:');
  var text = 'Price: \$123.45';
  var specific = RegExp(r'\d+\.\d{2}');
  var generic = RegExp(r'.+');
  print('   Text: "$text"');
  print('   Specific \\d+\\.\\d{2}: "${specific.firstMatch(text)?.group(0)}"');
  print('   Generic .+: "${generic.firstMatch(text)?.group(0)}"');
  
  // 3. Handle edge cases
  print('\n3. Handle edge cases:');
  var emails = ['user@domain.com', 'invalid@', '@invalid.com', ''];
  var emailRegex = RegExp(r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$');
  
  emails.forEach((email) {
    var isValid = email.isNotEmpty && emailRegex.hasMatch(email);
    print('   "$email": ${isValid ? 'Valid' : 'Invalid'}');
  });
  
  // 4. Document complex patterns
  print('\n4. Document complex patterns:');
  print('''   // Match IPv4 addresses (0-255 per octet)
   var ipv4 = RegExp(
     r'^(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?\\.)''' +
     '''{3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\$'
   );''');
   
  print('\n5. Test with edge cases and validate assumptions');
  print('6. Consider string methods for simple operations');
  print('7. Use online regex testers for complex patterns');
  print('8. Prefer readability over cleverness');
}
```

Following best practices ensures regex patterns are maintainable, efficient,  
and behave correctly across different inputs and edge cases.








