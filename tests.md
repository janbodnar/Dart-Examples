# Dart Testing

Testing is a fundamental aspect of software development that ensures code  
reliability, maintainability, and correctness. In the context of modern  
software engineering, comprehensive testing strategies help prevent bugs,  
facilitate refactoring, and provide documentation for expected behavior.  

Testing serves multiple critical purposes in the development lifecycle:  
it validates that code behaves as intended, catches regressions during  
modifications, enables confident refactoring, and serves as living  
documentation of system requirements. Well-tested code is more maintainable,  
has fewer production issues, and allows development teams to move faster  
with greater confidence.  

Dart provides a robust testing framework built into the language ecosystem,  
offering comprehensive tools for unit testing, integration testing, and  
widget testing. The Dart test framework follows established testing patterns  
while leveraging Dart's language features like strong typing, null safety,  
and asynchronous programming to create expressive and reliable test suites.  

## Testing Philosophy and Best Practices

Effective testing in Dart follows the testing pyramid concept: a large base  
of fast unit tests, a smaller number of integration tests, and minimal  
end-to-end tests. Unit tests focus on individual functions or classes in  
isolation, integration tests verify component interactions, and system tests  
validate complete workflows.  

The Arrange-Act-Assert (AAA) pattern forms the backbone of well-structured  
tests. The Arrange phase sets up test data and dependencies, the Act phase  
executes the code under test, and the Assert phase verifies the expected  
outcomes. This pattern creates readable, maintainable tests that clearly  
express their intent.  

Test-Driven Development (TDD) is particularly effective in Dart due to the  
language's excellent tooling and fast compilation. The TDD cycle of writing  
failing tests first, implementing minimal code to pass, and then refactoring  
creates robust, well-designed code with comprehensive test coverage.  

## Dart Testing Ecosystem

Dart's testing ecosystem centers around the `test` package, which provides  
a comprehensive framework for writing and running tests. The framework  
supports synchronous and asynchronous tests, test grouping, setup and  
teardown hooks, and powerful assertion libraries.  

The `mockito` package enables sophisticated mocking capabilities, allowing  
tests to isolate units of code by replacing dependencies with controlled  
test doubles. This isolation is crucial for creating fast, reliable unit  
tests that don't depend on external systems or complex object graphs.  

For Flutter applications, the `flutter_test` package extends Dart's testing  
capabilities with widget testing tools. These tools enable testing of UI  
components in isolation, simulating user interactions, and validating  
rendered output without requiring a full application runtime.  

## Test Organization and Structure

Dart tests typically reside in a `test` directory that mirrors the structure  
of the `lib` directory. This convention makes it easy to locate tests for  
specific code modules and maintains clear separation between production and  
test code. Test files conventionally end with `_test.dart` to distinguish  
them from regular Dart files.  

Within test files, the `group` function organizes related tests into logical  
collections, creating hierarchical test structures that improve readability  
and enable targeted test execution. Groups can be nested to create detailed  
categorization of test scenarios.  

The `setUp` and `tearDown` functions provide hooks for initializing and  
cleaning up test environments. These functions run before and after each  
test in their scope, ensuring tests start with clean state and don't leave  
behind artifacts that could affect other tests.  

## Basic Test Setup and Execution

Dart tests begin with importing the test framework and the code under test.  
The basic structure includes test functions that define individual test  
cases, using descriptive names that clearly communicate what behavior is  
being verified.  

```dart
import 'package:test/test.dart';

void main() {
  test('should demonstrate basic test structure', () {
    // Arrange
    var value = 42;
    
    // Act
    var result = value * 2;
    
    // Assert
    expect(result, equals(84));
  });
}
```

This fundamental example demonstrates the core testing pattern: importing  
necessary dependencies, defining test cases with descriptive names, and  
using the expect function to verify outcomes against expected values.  

## Basic Unit Testing

Unit tests verify individual functions, methods, or classes in isolation  
from their dependencies. These tests should be fast, independent, and  
focused on a single unit of functionality.  

```dart
import 'package:test/test.dart';

// Simple calculator class to test
class Calculator {
  int add(int a, int b) => a + b;
  int subtract(int a, int b) => a - b;
  int multiply(int a, int b) => a * b;
  double divide(int a, int b) {
    if (b == 0) throw ArgumentError('Cannot divide by zero');
    return a / b;
  }
}

void main() {
  group('Calculator', () {
    late Calculator calculator;
    
    setUp(() {
      calculator = Calculator();
    });
    
    test('should add two numbers correctly', () {
      var result = calculator.add(5, 3);
      expect(result, equals(8));
    });
    
    test('should subtract two numbers correctly', () {
      var result = calculator.subtract(10, 4);
      expect(result, equals(6));
    });
    
    test('should multiply two numbers correctly', () {
      var result = calculator.multiply(6, 7);
      expect(result, equals(42));
    });
    
    test('should divide two numbers correctly', () {
      var result = calculator.divide(15, 3);
      expect(result, equals(5.0));
    });
    
    test('should throw error when dividing by zero', () {
      expect(() => calculator.divide(10, 0), throwsArgumentError);
    });
  });
}
```

Unit testing focuses on individual components with clear inputs and outputs.  
The setUp function ensures each test starts with a fresh calculator instance,  
preventing tests from affecting each other. Exception testing verifies error  
handling behavior using throwsA and specific exception matchers.  

## Testing with Different Assertion Types

Dart's test framework provides numerous assertion types for comprehensive  
verification of different data types and conditions.  

```dart
import 'package:test/test.dart';

void main() {
  group('Different Assertion Types', () {
    test('should test various equality assertions', () {
      // Basic equality
      expect(42, equals(42));
      expect('hello', equals('hello'));
      expect(true, isTrue);
      expect(false, isFalse);
      expect(null, isNull);
      expect('test', isNotNull);
    });
    
    test('should test numeric assertions', () {
      expect(10, greaterThan(5));
      expect(3, lessThan(10));
      expect(7, greaterThanOrEqualTo(7));
      expect(5, lessThanOrEqualTo(8));
      expect(3.14159, closeTo(3.14, 0.01));
    });
    
    test('should test string assertions', () {
      expect('Hello there!', contains('Hello'));
      expect('example@email.com', matches(r'.*@.*\.com'));
      expect('Hello there!', startsWith('Hello'));
      expect('Hello there!', endsWith('there!'));
      expect('test string', hasLength(11));
    });
    
    test('should test collection assertions', () {
      var list = [1, 2, 3, 4, 5];
      expect(list, contains(3));
      expect(list, hasLength(5));
      expect(list, isEmpty.not);
      expect(list, containsAll([2, 4]));
      expect([1, 2, 3], orderedEquals([1, 2, 3]));
      expect([3, 1, 2], unorderedEquals([1, 2, 3]));
    });
    
    test('should test type assertions', () {
      var value = 'test string';
      expect(value, isA<String>());
      expect(42, isA<int>());
      expect([1, 2, 3], isA<List<int>>());
    });
  });
}
```

Comprehensive assertion types enable precise verification of expected  
behavior. String matchers support pattern matching and substring verification,  
numeric matchers handle precision and comparison operations, and collection  
matchers verify container contents and structure.  

## Test Groups and Organization

Organizing tests into logical groups creates maintainable test suites that  
clearly document system behavior and enable targeted test execution.  

```dart
import 'package:test/test.dart';

class BankAccount {
  double _balance = 0;
  final String accountNumber;
  
  BankAccount(this.accountNumber);
  
  double get balance => _balance;
  
  void deposit(double amount) {
    if (amount <= 0) throw ArgumentError('Amount must be positive');
    _balance += amount;
  }
  
  void withdraw(double amount) {
    if (amount <= 0) throw ArgumentError('Amount must be positive');
    if (amount > _balance) throw StateError('Insufficient funds');
    _balance -= amount;
  }
  
  void transfer(BankAccount destination, double amount) {
    withdraw(amount);
    destination.deposit(amount);
  }
}

void main() {
  group('BankAccount', () {
    late BankAccount account;
    
    setUp(() {
      account = BankAccount('12345');
    });
    
    group('Deposit Operations', () {
      test('should accept valid deposits', () {
        account.deposit(100.0);
        expect(account.balance, equals(100.0));
      });
      
      test('should accumulate multiple deposits', () {
        account.deposit(50.0);
        account.deposit(75.0);
        expect(account.balance, equals(125.0));
      });
      
      test('should reject negative deposits', () {
        expect(() => account.deposit(-50.0), throwsArgumentError);
      });
      
      test('should reject zero deposits', () {
        expect(() => account.deposit(0.0), throwsArgumentError);
      });
    });
    
    group('Withdrawal Operations', () {
      setUp(() {
        account.deposit(200.0);  // Setup specific to withdrawal tests
      });
      
      test('should allow valid withdrawals', () {
        account.withdraw(50.0);
        expect(account.balance, equals(150.0));
      });
      
      test('should prevent overdrafts', () {
        expect(() => account.withdraw(300.0), throwsStateError);
      });
      
      test('should reject negative withdrawals', () {
        expect(() => account.withdraw(-25.0), throwsArgumentError);
      });
    });
    
    group('Transfer Operations', () {
      late BankAccount destinationAccount;
      
      setUp(() {
        account.deposit(300.0);
        destinationAccount = BankAccount('67890');
      });
      
      test('should transfer funds between accounts', () {
        account.transfer(destinationAccount, 100.0);
        expect(account.balance, equals(200.0));
        expect(destinationAccount.balance, equals(100.0));
      });
      
      test('should fail transfer when insufficient funds', () {
        expect(() => account.transfer(destinationAccount, 400.0), 
               throwsStateError);
        expect(account.balance, equals(300.0));
        expect(destinationAccount.balance, equals(0.0));
      });
    });
  });
}
```

Nested groups create hierarchical test organization that mirrors the  
structure of the code being tested. Each group can have its own setUp  
and tearDown functions, allowing for granular test environment control  
and shared test data management.  

## Asynchronous Testing

Modern Dart applications rely heavily on asynchronous operations, and  
testing async code requires special consideration for timing and state  
management.  

```dart
import 'dart:async';
import 'package:test/test.dart';

class DataService {
  Future<String> fetchUserData(String userId) async {
    await Future.delayed(Duration(milliseconds: 100));
    if (userId == 'invalid') throw Exception('User not found');
    return 'User data for $userId';
  }
  
  Stream<int> generateNumbers(int count) async* {
    for (int i = 1; i <= count; i++) {
      await Future.delayed(Duration(milliseconds: 50));
      yield i;
    }
  }
  
  Future<List<String>> fetchMultipleUsers(List<String> userIds) async {
    var futures = userIds.map((id) => fetchUserData(id));
    return await Future.wait(futures);
  }
}

void main() {
  group('Asynchronous Testing', () {
    late DataService dataService;
    
    setUp(() {
      dataService = DataService();
    });
    
    test('should test async functions with await', () async {
      var result = await dataService.fetchUserData('user123');
      expect(result, equals('User data for user123'));
    });
    
    test('should test async exceptions', () async {
      expect(() => dataService.fetchUserData('invalid'), throwsException);
    });
    
    test('should test streams with expectLater', () async {
      var stream = dataService.generateNumbers(3);
      
      expect(stream, emitsInOrder([1, 2, 3]));
      await expectLater(stream, emitsInOrder([1, 2, 3]));
    });
    
    test('should test stream completion', () async {
      var stream = dataService.generateNumbers(2);
      
      await expectLater(stream, emitsInOrder([1, 2, emitsDone]));
    });
    
    test('should test concurrent futures', () async {
      var stopwatch = Stopwatch()..start();
      
      var results = await dataService.fetchMultipleUsers(['user1', 'user2', 'user3']);
      
      stopwatch.stop();
      
      expect(results, hasLength(3));
      expect(results[0], contains('user1'));
      expect(results[1], contains('user2'));
      expect(results[2], contains('user3'));
      
      // Should complete faster than sequential execution
      expect(stopwatch.elapsedMilliseconds, lessThan(250));
    });
    
    test('should test timeout behavior', () async {
      // Testing with custom timeout
      expect(() async {
        await dataService.fetchUserData('user123').timeout(Duration(milliseconds: 50));
      }, throwsA(isA<TimeoutException>()));
    });
  });
}
```

Async testing requires the test function to be marked as async and use  
await for Future operations. Stream testing uses expectLater for proper  
async verification, and emit matchers validate stream behavior including  
completion and error states.  

## Mocking and Test Doubles

Mocking allows tests to control dependencies and verify interactions  
without relying on real implementations, creating fast and reliable  
unit tests.  

```dart
import 'package:test/test.dart';
import 'package:mockito/mockito.dart';
import 'package:mockito/annotations.dart';

// Service interfaces
abstract class EmailService {
  Future<bool> sendEmail(String to, String subject, String body);
}

abstract class UserRepository {
  Future<User?> findById(String id);
  Future<void> save(User user);
}

class User {
  final String id;
  final String email;
  final String name;
  
  User({required this.id, required this.email, required this.name});
}

// Generate mock classes with mockito
@GenerateMocks([EmailService, UserRepository])
import 'test.mocks.dart';

class UserService {
  final UserRepository _userRepository;
  final EmailService _emailService;
  
  UserService(this._userRepository, this._emailService);
  
  Future<void> sendWelcomeEmail(String userId) async {
    var user = await _userRepository.findById(userId);
    if (user == null) throw ArgumentError('User not found');
    
    await _emailService.sendEmail(
      user.email,
      'Welcome!',
      'Hello there, ${user.name}! Welcome to our service.'
    );
  }
  
  Future<User> updateUserEmail(String userId, String newEmail) async {
    var user = await _userRepository.findById(userId);
    if (user == null) throw ArgumentError('User not found');
    
    var updatedUser = User(
      id: user.id,
      email: newEmail,
      name: user.name
    );
    
    await _userRepository.save(updatedUser);
    return updatedUser;
  }
}

void main() {
  group('UserService with Mocks', () {
    late MockUserRepository mockRepository;
    late MockEmailService mockEmailService;
    late UserService userService;
    
    setUp(() {
      mockRepository = MockUserRepository();
      mockEmailService = MockEmailService();
      userService = UserService(mockRepository, mockEmailService);
    });
    
    test('should send welcome email to existing user', () async {
      // Arrange
      var testUser = User(id: '123', email: 'test@example.com', name: 'Alice');
      when(mockRepository.findById('123')).thenAnswer((_) async => testUser);
      when(mockEmailService.sendEmail(any, any, any)).thenAnswer((_) async => true);
      
      // Act
      await userService.sendWelcomeEmail('123');
      
      // Assert
      verify(mockRepository.findById('123')).called(1);
      verify(mockEmailService.sendEmail(
        'test@example.com',
        'Welcome!',
        'Hello there, Alice! Welcome to our service.'
      )).called(1);
    });
    
    test('should throw error when user not found', () async {
      // Arrange
      when(mockRepository.findById('nonexistent')).thenAnswer((_) async => null);
      
      // Act & Assert
      expect(() => userService.sendWelcomeEmail('nonexistent'), 
             throwsArgumentError);
      
      verify(mockRepository.findById('nonexistent')).called(1);
      verifyNever(mockEmailService.sendEmail(any, any, any));
    });
    
    test('should update user email successfully', () async {
      // Arrange
      var originalUser = User(id: '123', email: 'old@example.com', name: 'Bob');
      when(mockRepository.findById('123')).thenAnswer((_) async => originalUser);
      when(mockRepository.save(any)).thenAnswer((_) async {});
      
      // Act
      var result = await userService.updateUserEmail('123', 'new@example.com');
      
      // Assert
      expect(result.email, equals('new@example.com'));
      expect(result.name, equals('Bob'));
      expect(result.id, equals('123'));
      
      verify(mockRepository.findById('123')).called(1);
      verify(mockRepository.save(any)).called(1);
    });
  });
}
```

Mocking enables isolation testing by controlling dependencies and verifying  
interactions. The when/thenAnswer pattern stubs method behavior, while  
verify confirms expected method calls occurred with correct parameters.  

## Parameterized Testing

Parameterized tests enable testing multiple scenarios with different input  
data sets, reducing code duplication and improving test coverage.  

```dart
import 'package:test/test.dart';

class PasswordValidator {
  static bool isValid(String password) {
    if (password.length < 8) return false;
    if (!password.contains(RegExp(r'[A-Z]'))) return false;
    if (!password.contains(RegExp(r'[a-z]'))) return false;
    if (!password.contains(RegExp(r'[0-9]'))) return false;
    if (!password.contains(RegExp(r'[!@#$%^&*(),.?":{}|<>]'))) return false;
    return true;
  }
  
  static String getStrengthDescription(String password) {
    var score = 0;
    if (password.length >= 8) score++;
    if (password.contains(RegExp(r'[A-Z]'))) score++;
    if (password.contains(RegExp(r'[a-z]'))) score++;
    if (password.contains(RegExp(r'[0-9]'))) score++;
    if (password.contains(RegExp(r'[!@#$%^&*(),.?":{}|<>]'))) score++;
    
    switch (score) {
      case 0:
      case 1:
        return 'Very Weak';
      case 2:
        return 'Weak';
      case 3:
        return 'Fair';
      case 4:
        return 'Good';
      case 5:
        return 'Strong';
      default:
        return 'Unknown';
    }
  }
}

void main() {
  group('Parameterized Testing', () {
    group('Password Validation', () {
      var validPasswords = [
        'StrongP@ss1',
        'MySecure123!',
        'C0mpl3x#Pass',
        'Tr1cky&Password'
      ];
      
      for (var password in validPasswords) {
        test('should accept valid password: $password', () {
          expect(PasswordValidator.isValid(password), isTrue);
        });
      }
      
      var invalidPasswords = [
        ('short', 'Too short'),
        ('nouppercase123!', 'No uppercase'),
        ('NOLOWERCASE123!', 'No lowercase'),
        ('NoDigits!', 'No digits'),
        ('NoSpecialChars123', 'No special characters'),
        ('', 'Empty password'),
      ];
      
      for (var testCase in invalidPasswords) {
        test('should reject invalid password: ${testCase.$1} (${testCase.$2})', () {
          expect(PasswordValidator.isValid(testCase.$1), isFalse);
        });
      }
    });
    
    group('Password Strength Assessment', () {
      var strengthTests = [
        ('weak', 'Very Weak'),
        ('Weak1', 'Weak'),
        ('Weak1!', 'Fair'),
        ('StrongP1!', 'Good'),
        ('StrongPass1!', 'Strong'),
      ];
      
      for (var testCase in strengthTests) {
        test('should rate "${testCase.$1}" as ${testCase.$2}', () {
          var result = PasswordValidator.getStrengthDescription(testCase.$1);
          expect(result, equals(testCase.$2));
        });
      }
    });
    
    group('Mathematical Functions', () {
      int factorial(int n) {
        if (n <= 1) return 1;
        return n * factorial(n - 1);
      }
      
      var factorialTests = [
        (0, 1),
        (1, 1),
        (2, 2),
        (3, 6),
        (4, 24),
        (5, 120),
      ];
      
      for (var testCase in factorialTests) {
        test('factorial of ${testCase.$1} should be ${testCase.$2}', () {
          expect(factorial(testCase.$1), equals(testCase.$2));
        });
      }
    });
  });
}
```

Parameterized testing uses data-driven approaches to verify behavior  
across multiple input scenarios. Using tuples or custom test case classes  
enables descriptive test names and organized test data management.  

## Testing Exception Handling

Proper exception handling is crucial for robust applications, and testing  
error conditions ensures graceful failure and appropriate error messaging.  

```dart
import 'package:test/test.dart';

class FileProcessor {
  String processFile(String filename, String content) {
    if (filename.isEmpty) {
      throw ArgumentError('Filename cannot be empty');
    }
    
    if (content.isEmpty) {
      throw ArgumentError('Content cannot be empty');
    }
    
    if (!filename.endsWith('.txt')) {
      throw FormatException('Only .txt files are supported');
    }
    
    if (content.length > 1000) {
      throw RangeError('Content too large (max 1000 characters)');
    }
    
    return content.toUpperCase();
  }
  
  Future<String> processFileAsync(String filename) async {
    await Future.delayed(Duration(milliseconds: 10));
    
    if (filename == 'network_error.txt') {
      throw StateError('Network connection failed');
    }
    
    if (filename == 'timeout.txt') {
      throw TimeoutException('Operation timed out', Duration(seconds: 30));
    }
    
    return 'Processed: $filename';
  }
}

void main() {
  group('Exception Handling Tests', () {
    late FileProcessor processor;
    
    setUp(() {
      processor = FileProcessor();
    });
    
    group('Synchronous Exception Testing', () {
      test('should throw ArgumentError for empty filename', () {
        expect(() => processor.processFile('', 'content'),
               throwsArgumentError);
      });
      
      test('should throw ArgumentError for empty content', () {
        expect(() => processor.processFile('test.txt', ''),
               throwsArgumentError);
      });
      
      test('should throw FormatException for wrong file type', () {
        expect(() => processor.processFile('test.doc', 'content'),
               throwsFormatException);
      });
      
      test('should throw RangeError for content too large', () {
        var largeContent = 'x' * 1001;
        expect(() => processor.processFile('test.txt', largeContent),
               throwsRangeError);
      });
      
      test('should test specific exception messages', () {
        expect(
          () => processor.processFile('', 'content'),
          throwsA(
            isA<ArgumentError>()
                .having((e) => e.message, 'message', 'Filename cannot be empty')
          )
        );
      });
      
      test('should process valid file successfully', () {
        var result = processor.processFile('test.txt', 'hello there');
        expect(result, equals('HELLO THERE'));
      });
    });
    
    group('Asynchronous Exception Testing', () {
      test('should handle async network errors', () async {
        expect(() => processor.processFileAsync('network_error.txt'),
               throwsStateError);
      });
      
      test('should handle timeout exceptions', () async {
        expect(() => processor.processFileAsync('timeout.txt'),
               throwsA(isA<TimeoutException>()));
      });
      
      test('should test specific async exception details', () async {
        expect(
          () => processor.processFileAsync('timeout.txt'),
          throwsA(
            isA<TimeoutException>()
                .having((e) => e.message, 'message', 'Operation timed out')
                .having((e) => e.duration, 'duration', Duration(seconds: 30))
          )
        );
      });
      
      test('should process valid async file', () async {
        var result = await processor.processFileAsync('valid.txt');
        expect(result, equals('Processed: valid.txt'));
      });
    });
    
    group('Exception Recovery Testing', () {
      String safeProcessFile(String filename, String content) {
        try {
          return processor.processFile(filename, content);
        } catch (e) {
          return 'Error: ${e.toString()}';
        }
      }
      
      test('should handle exceptions gracefully', () {
        var result = safeProcessFile('', 'content');
        expect(result, contains('Error:'));
        expect(result, contains('Filename cannot be empty'));
      });
    });
  });
}
```

Exception testing verifies error handling logic using throwsA matchers  
and exception type validation. Testing both the occurrence and details  
of exceptions ensures proper error reporting and graceful failure modes.  

## Testing with Setup and Teardown

Setup and teardown functions manage test environments, ensuring consistent  
initial state and proper cleanup after test execution.  

```dart
import 'dart:io';
import 'package:test/test.dart';

class TempFileManager {
  final Directory _tempDir;
  
  TempFileManager(this._tempDir);
  
  Future<File> createFile(String name, String content) async {
    var file = File('${_tempDir.path}/$name');
    await file.writeAsString(content);
    return file;
  }
  
  Future<List<String>> listFiles() async {
    var entities = await _tempDir.list().toList();
    return entities.whereType<File>().map((f) => f.path.split('/').last).toList();
  }
  
  Future<String> readFile(String name) async {
    var file = File('${_tempDir.path}/$name');
    return await file.readAsString();
  }
  
  Future<void> deleteFile(String name) async {
    var file = File('${_tempDir.path}/$name');
    if (await file.exists()) {
      await file.delete();
    }
  }
}

void main() {
  group('Setup and Teardown Example', () {
    late Directory tempDir;
    late TempFileManager fileManager;
    
    setUpAll(() async {
      // Runs once before all tests in this group
      tempDir = await Directory.systemTemp.createTemp('test_files_');
      print('Created temp directory: ${tempDir.path}');
    });
    
    setUp(() async {
      // Runs before each individual test
      fileManager = TempFileManager(tempDir);
      
      // Create some default test files
      await fileManager.createFile('default.txt', 'Default content');
      await fileManager.createFile('sample.txt', 'Sample data');
    });
    
    tearDown(() async {
      // Runs after each individual test
      var files = await tempDir.list().toList();
      for (var file in files) {
        if (file is File) {
          await file.delete();
        }
      }
    });
    
    tearDownAll(() async {
      // Runs once after all tests in this group
      if (await tempDir.exists()) {
        await tempDir.delete(recursive: true);
        print('Cleaned up temp directory');
      }
    });
    
    test('should create new files', () async {
      await fileManager.createFile('test.txt', 'Test content');
      var files = await fileManager.listFiles();
      
      expect(files, hasLength(3)); // 2 default + 1 new
      expect(files, contains('test.txt'));
    });
    
    test('should read file contents', () async {
      var content = await fileManager.readFile('default.txt');
      expect(content, equals('Default content'));
    });
    
    test('should delete files', () async {
      await fileManager.deleteFile('sample.txt');
      var files = await fileManager.listFiles();
      
      expect(files, hasLength(1));
      expect(files, isNot(contains('sample.txt')));
    });
    
    test('should start with clean state each time', () async {
      // This test verifies that tearDown properly cleans up
      var files = await fileManager.listFiles();
      expect(files, hasLength(2)); // Only default files
      expect(files, contains('default.txt'));
      expect(files, contains('sample.txt'));
    });
  });
}
```

SetUp functions ensure consistent test environments, while tearDown  
functions prevent test pollution. SetUpAll and tearDownAll handle  
expensive operations that can be shared across multiple tests.  

## State Management Testing

Testing stateful objects requires careful management of object state  
and verification of state transitions across method calls.  

```dart
import 'package:test/test.dart';

enum OrderStatus { pending, confirmed, shipped, delivered, cancelled }

class Order {
  final String id;
  final List<String> items;
  OrderStatus _status;
  double _total;
  
  Order(this.id, this.items, this._total) : _status = OrderStatus.pending;
  
  OrderStatus get status => _status;
  double get total => _total;
  
  void confirm() {
    if (_status != OrderStatus.pending) {
      throw StateError('Can only confirm pending orders');
    }
    _status = OrderStatus.confirmed;
  }
  
  void ship() {
    if (_status != OrderStatus.confirmed) {
      throw StateError('Can only ship confirmed orders');
    }
    _status = OrderStatus.shipped;
  }
  
  void deliver() {
    if (_status != OrderStatus.shipped) {
      throw StateError('Can only deliver shipped orders');
    }
    _status = OrderStatus.delivered;
  }
  
  void cancel() {
    if (_status == OrderStatus.delivered) {
      throw StateError('Cannot cancel delivered orders');
    }
    _status = OrderStatus.cancelled;
  }
  
  void addItem(String item, double price) {
    if (_status != OrderStatus.pending) {
      throw StateError('Cannot modify non-pending orders');
    }
    items.add(item);
    _total += price;
  }
  
  void removeItem(String item, double price) {
    if (_status != OrderStatus.pending) {
      throw StateError('Cannot modify non-pending orders');
    }
    if (!items.contains(item)) {
      throw ArgumentError('Item not found in order');
    }
    items.remove(item);
    _total -= price;
  }
}

void main() {
  group('State Management Testing', () {
    late Order order;
    
    setUp(() {
      order = Order('ORD123', ['Widget A', 'Widget B'], 150.0);
    });
    
    test('should start in pending state', () {
      expect(order.status, equals(OrderStatus.pending));
      expect(order.total, equals(150.0));
      expect(order.items, hasLength(2));
    });
    
    test('should transition through valid state sequence', () {
      // Pending -> Confirmed
      order.confirm();
      expect(order.status, equals(OrderStatus.confirmed));
      
      // Confirmed -> Shipped  
      order.ship();
      expect(order.status, equals(OrderStatus.shipped));
      
      // Shipped -> Delivered
      order.deliver();
      expect(order.status, equals(OrderStatus.delivered));
    });
    
    test('should allow cancellation before delivery', () {
      order.confirm();
      order.cancel();
      expect(order.status, equals(OrderStatus.cancelled));
    });
    
    test('should prevent invalid state transitions', () {
      // Can't ship pending order
      expect(() => order.ship(), throwsStateError);
      
      // Can't deliver unshipped order
      order.confirm();
      expect(() => order.deliver(), throwsStateError);
      
      // Can't cancel delivered order
      order.ship();
      order.deliver();
      expect(() => order.cancel(), throwsStateError);
    });
    
    test('should allow item modification only when pending', () {
      // Should work when pending
      order.addItem('Widget C', 50.0);
      expect(order.items, hasLength(3));
      expect(order.total, equals(200.0));
      
      // Should fail after confirmation
      order.confirm();
      expect(() => order.addItem('Widget D', 25.0), throwsStateError);
      expect(() => order.removeItem('Widget A', 75.0), throwsStateError);
    });
    
    test('should handle item removal correctly', () {
      order.removeItem('Widget B', 75.0);
      expect(order.items, hasLength(1));
      expect(order.total, equals(75.0));
      expect(order.items, contains('Widget A'));
      expect(order.items, isNot(contains('Widget B')));
    });
    
    test('should reject removal of non-existent items', () {
      expect(() => order.removeItem('Widget Z', 10.0), throwsArgumentError);
    });
  });
}
```

State management testing verifies object state transitions and business  
rule enforcement. Testing both valid state progressions and invalid  
transitions ensures robust state machine implementation.  

## Integration Testing

Integration tests verify that multiple components work together correctly,  
testing the interactions between different parts of the system.  

```dart
import 'package:test/test.dart';

// Domain models
class Product {
  final String id;
  final String name;
  final double price;
  
  Product({required this.id, required this.name, required this.price});
}

class CartItem {
  final Product product;
  final int quantity;
  
  CartItem({required this.product, required this.quantity});
  
  double get total => product.price * quantity;
}

class ShoppingCart {
  final List<CartItem> _items = [];
  
  List<CartItem> get items => List.unmodifiable(_items);
  
  double get total => _items.fold(0, (sum, item) => sum + item.total);
  
  void addItem(Product product, int quantity) {
    var existingIndex = _items.indexWhere((item) => item.product.id == product.id);
    
    if (existingIndex >= 0) {
      var existing = _items[existingIndex];
      _items[existingIndex] = CartItem(
        product: existing.product,
        quantity: existing.quantity + quantity
      );
    } else {
      _items.add(CartItem(product: product, quantity: quantity));
    }
  }
  
  void removeItem(String productId) {
    _items.removeWhere((item) => item.product.id == productId);
  }
  
  void updateQuantity(String productId, int newQuantity) {
    if (newQuantity <= 0) {
      removeItem(productId);
      return;
    }
    
    var index = _items.indexWhere((item) => item.product.id == productId);
    if (index >= 0) {
      _items[index] = CartItem(
        product: _items[index].product,
        quantity: newQuantity
      );
    }
  }
  
  void clear() {
    _items.clear();
  }
}

class DiscountService {
  double calculateDiscount(ShoppingCart cart) {
    if (cart.total >= 100) {
      return cart.total * 0.1; // 10% discount for orders over $100
    }
    return 0.0;
  }
  
  String getDiscountDescription(double discount) {
    if (discount > 0) {
      return '10% discount applied';
    }
    return 'No discount';
  }
}

class OrderService {
  final DiscountService _discountService;
  
  OrderService(this._discountService);
  
  OrderSummary calculateOrder(ShoppingCart cart) {
    var subtotal = cart.total;
    var discount = _discountService.calculateDiscount(cart);
    var discountDescription = _discountService.getDiscountDescription(discount);
    var finalTotal = subtotal - discount;
    
    return OrderSummary(
      items: cart.items,
      subtotal: subtotal,
      discount: discount,
      discountDescription: discountDescription,
      total: finalTotal
    );
  }
}

class OrderSummary {
  final List<CartItem> items;
  final double subtotal;
  final double discount;
  final String discountDescription;
  final double total;
  
  OrderSummary({
    required this.items,
    required this.subtotal,
    required this.discount,
    required this.discountDescription,
    required this.total
  });
}

void main() {
  group('Integration Testing - Shopping System', () {
    late ShoppingCart cart;
    late DiscountService discountService;
    late OrderService orderService;
    late List<Product> testProducts;
    
    setUp(() {
      cart = ShoppingCart();
      discountService = DiscountService();
      orderService = OrderService(discountService);
      
      testProducts = [
        Product(id: '1', name: 'Widget A', price: 25.99),
        Product(id: '2', name: 'Widget B', price: 45.50),
        Product(id: '3', name: 'Widget C', price: 12.75),
        Product(id: '4', name: 'Premium Widget', price: 89.99),
      ];
    });
    
    test('should handle complete shopping workflow', () {
      // Add items to cart
      cart.addItem(testProducts[0], 2); // 2 x $25.99 = $51.98
      cart.addItem(testProducts[1], 1); // 1 x $45.50 = $45.50
      
      expect(cart.items, hasLength(2));
      expect(cart.total, closeTo(97.48, 0.01));
      
      // Calculate order (no discount - under $100)
      var order = orderService.calculateOrder(cart);
      
      expect(order.subtotal, closeTo(97.48, 0.01));
      expect(order.discount, equals(0.0));
      expect(order.total, closeTo(97.48, 0.01));
      expect(order.discountDescription, equals('No discount'));
    });
    
    test('should apply discount for qualifying orders', () {
      // Add expensive item to qualify for discount
      cart.addItem(testProducts[3], 1); // $89.99
      cart.addItem(testProducts[0], 1); // $25.99
      
      var order = orderService.calculateOrder(cart);
      
      expect(order.subtotal, closeTo(115.98, 0.01));
      expect(order.discount, closeTo(11.60, 0.01)); // 10% of subtotal
      expect(order.total, closeTo(104.38, 0.01));
      expect(order.discountDescription, equals('10% discount applied'));
    });
    
    test('should handle cart modifications correctly', () {
      cart.addItem(testProducts[0], 3);
      cart.addItem(testProducts[1], 2);
      
      expect(cart.total, closeTo(168.97, 0.01));
      
      // Update quantity
      cart.updateQuantity('1', 1); // Reduce Widget A from 3 to 1
      expect(cart.total, closeTo(117.49, 0.01));
      
      // Remove item
      cart.removeItem('2'); // Remove Widget B
      expect(cart.total, closeTo(25.99, 0.01));
      
      var order = orderService.calculateOrder(cart);
      expect(order.discount, equals(0.0)); // Below discount threshold
    });
    
    test('should handle duplicate item additions', () {
      cart.addItem(testProducts[0], 1);
      cart.addItem(testProducts[0], 2); // Should combine with existing
      
      expect(cart.items, hasLength(1));
      expect(cart.items.first.quantity, equals(3));
      expect(cart.total, closeTo(77.97, 0.01));
    });
    
    test('should handle zero quantity updates', () {
      cart.addItem(testProducts[0], 2);
      cart.addItem(testProducts[1], 1);
      
      expect(cart.items, hasLength(2));
      
      cart.updateQuantity('1', 0); // Should remove item
      
      expect(cart.items, hasLength(1));
      expect(cart.items.first.product.id, equals('2'));
    });
    
    test('should integrate with complex scenarios', () {
      // Complex scenario: multiple items, modifications, discount eligibility
      cart.addItem(testProducts[0], 2); // $51.98
      cart.addItem(testProducts[1], 1); // $45.50
      cart.addItem(testProducts[2], 3); // $38.25
      
      expect(cart.total, closeTo(135.73, 0.01));
      
      var order1 = orderService.calculateOrder(cart);
      expect(order1.discount, greaterThan(0)); // Should have discount
      
      // Remove expensive item
      cart.removeItem('2');
      expect(cart.total, closeTo(90.23, 0.01));
      
      var order2 = orderService.calculateOrder(cart);
      expect(order2.discount, equals(0.0)); // Should lose discount
      
      // Clear cart
      cart.clear();
      expect(cart.items, isEmpty);
      expect(cart.total, equals(0.0));
      
      var order3 = orderService.calculateOrder(cart);
      expect(order3.total, equals(0.0));
    });
  });
}
```

Integration testing validates component interactions and end-to-end  
workflows. These tests verify that business logic integrates correctly  
across multiple classes and that complex scenarios work as expected.  

## Custom Matchers and Assertions

Creating custom matchers enables domain-specific assertions that make  
tests more readable and expressive while encapsulating complex validation  
logic.  

```dart
import 'package:test/test.dart';
import 'dart:math';

// Custom matcher for validating email addresses
class EmailMatcher extends Matcher {
  @override
  bool matches(dynamic item, Map matchState) {
    if (item is! String) return false;
    
    var emailRegex = RegExp(r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$');
    return emailRegex.hasMatch(item);
  }
  
  @override
  Description describe(Description description) {
    return description.add('a valid email address');
  }
  
  @override
  Description describeMismatch(dynamic item, Description mismatchDescription,
      Map matchState, bool verbose) {
    return mismatchDescription.add('was "$item" which is not a valid email');
  }
}

// Custom matcher for number ranges
class InRangeMatcher extends Matcher {
  final num min;
  final num max;
  final bool inclusive;
  
  InRangeMatcher(this.min, this.max, {this.inclusive = true});
  
  @override
  bool matches(dynamic item, Map matchState) {
    if (item is! num) return false;
    
    if (inclusive) {
      return item >= min && item <= max;
    } else {
      return item > min && item < max;
    }
  }
  
  @override
  Description describe(Description description) {
    var rangeType = inclusive ? 'inclusive' : 'exclusive';
    return description.add('a number in range [$min, $max] ($rangeType)');
  }
}

// Custom matcher for geometric points
class Point {
  final double x;
  final double y;
  
  Point(this.x, this.y);
  
  double distanceTo(Point other) {
    return sqrt(pow(x - other.x, 2) + pow(y - other.y, 2));
  }
  
  @override
  String toString() => 'Point($x, $y)';
  
  @override
  bool operator ==(Object other) {
    return other is Point && x == other.x && y == other.y;
  }
  
  @override
  int get hashCode => Object.hash(x, y);
}

class NearPointMatcher extends Matcher {
  final Point expectedPoint;
  final double tolerance;
  
  NearPointMatcher(this.expectedPoint, this.tolerance);
  
  @override
  bool matches(dynamic item, Map matchState) {
    if (item is! Point) return false;
    return item.distanceTo(expectedPoint) <= tolerance;
  }
  
  @override
  Description describe(Description description) {
    return description.add('a point within $tolerance units of $expectedPoint');
  }
}

// Convenience functions for custom matchers
Matcher isValidEmail() => EmailMatcher();
Matcher inRange(num min, num max, {bool inclusive = true}) =>
    InRangeMatcher(min, max, inclusive: inclusive);
Matcher nearPoint(Point point, double tolerance) =>
    NearPointMatcher(point, tolerance);

void main() {
  group('Custom Matchers and Assertions', () {
    test('should validate email addresses with custom matcher', () {
      expect('user@example.com', isValidEmail());
      expect('test.email+tag@domain.co.uk', isValidEmail());
      expect('hello.there@test-domain.org', isValidEmail());
      
      expect('invalid-email', isNot(isValidEmail()));
      expect('@invalid.com', isNot(isValidEmail()));
      expect('user@', isNot(isValidEmail()));
      expect('', isNot(isValidEmail()));
    });
    
    test('should validate number ranges', () {
      expect(5, inRange(1, 10));
      expect(1, inRange(1, 10)); // Inclusive by default
      expect(10, inRange(1, 10));
      expect(0, isNot(inRange(1, 10)));
      expect(11, isNot(inRange(1, 10)));
      
      // Exclusive range
      expect(5, inRange(1, 10, inclusive: false));
      expect(1, isNot(inRange(1, 10, inclusive: false)));
      expect(10, isNot(inRange(1, 10, inclusive: false)));
    });
    
    test('should validate geometric proximity', () {
      var origin = Point(0, 0);
      var nearby = Point(0.5, 0.5);
      var distant = Point(10, 10);
      
      expect(nearby, nearPoint(origin, 1.0));
      expect(distant, isNot(nearPoint(origin, 1.0)));
      
      // Test with calculated distance
      var point1 = Point(3, 4);
      var point2 = Point(6, 8); // Distance = 5
      expect(point2, nearPoint(point1, 5.1));
      expect(point2, isNot(nearPoint(point1, 4.9)));
    });
    
    test('should combine custom matchers with built-in ones', () {
      var testData = [
        'valid@email.com',
        'another@test.org',
        'invalid-email'
      ];
      
      var validEmails = testData.where((email) => isValidEmail().matches(email, {}));
      
      expect(validEmails, hasLength(2));
      expect(validEmails, everyElement(isValidEmail()));
    });
    
    test('should demonstrate complex custom validation', () {
      // Test coordinate validation for a game system
      bool isValidGameCoordinate(Point point) {
        return inRange(0, 100).matches(point.x, {}) &&
               inRange(0, 100).matches(point.y, {});
      }
      
      var validPoint = Point(50, 75);
      var invalidPoint = Point(-5, 120);
      
      expect(isValidGameCoordinate(validPoint), isTrue);
      expect(isValidGameCoordinate(invalidPoint), isFalse);
    });
  });
}
```

Custom matchers encapsulate domain-specific validation logic and provide  
expressive test assertions. They improve test readability by expressing  
business rules as reusable validation components.  

## Performance and Benchmark Testing

Performance testing ensures code meets timing requirements and helps  
identify performance regressions during development.  

```dart
import 'package:test/test.dart';
import 'dart:math';

class DataProcessor {
  List<int> bubbleSort(List<int> data) {
    var result = List<int>.from(data);
    var n = result.length;
    
    for (int i = 0; i < n - 1; i++) {
      for (int j = 0; j < n - i - 1; j++) {
        if (result[j] > result[j + 1]) {
          var temp = result[j];
          result[j] = result[j + 1];
          result[j + 1] = temp;
        }
      }
    }
    return result;
  }
  
  List<int> quickSort(List<int> data) {
    if (data.length <= 1) return data;
    
    var pivot = data[data.length ~/ 2];
    var less = data.where((x) => x < pivot).toList();
    var equal = data.where((x) => x == pivot).toList();
    var greater = data.where((x) => x > pivot).toList();
    
    return [...quickSort(less), ...equal, ...quickSort(greater)];
  }
  
  List<int> generateRandomData(int size) {
    var random = Random();
    return List.generate(size, (_) => random.nextInt(1000));
  }
  
  Map<String, int> countFrequencies(List<String> items) {
    var frequencies = <String, int>{};
    for (var item in items) {
      frequencies[item] = (frequencies[item] ?? 0) + 1;
    }
    return frequencies;
  }
  
  List<String> processLargeText(String text) {
    return text
        .toLowerCase()
        .replaceAll(RegExp(r'[^\w\s]'), '')
        .split(' ')
        .where((word) => word.isNotEmpty)
        .toList();
  }
}

void main() {
  group('Performance and Benchmark Testing', () {
    late DataProcessor processor;
    
    setUp(() {
      processor = DataProcessor();
    });
    
    test('should sort small datasets efficiently', () {
      var testData = processor.generateRandomData(100);
      var stopwatch = Stopwatch()..start();
      
      var result = processor.quickSort(testData);
      
      stopwatch.stop();
      
      // Verify correctness
      expect(result, hasLength(100));
      expect(result, orderedEquals([...result]..sort()));
      
      // Performance assertion
      expect(stopwatch.elapsedMicroseconds, lessThan(5000)); // 5ms
      
      print('QuickSort (100 items): ${stopwatch.elapsedMicroseconds}μs');
    });
    
    test('should compare sorting algorithm performance', () {
      var testData = processor.generateRandomData(1000);
      
      // Measure bubble sort
      var bubbleStopwatch = Stopwatch()..start();
      var bubbleResult = processor.bubbleSort(testData);
      bubbleStopwatch.stop();
      
      // Measure quick sort
      var quickStopwatch = Stopwatch()..start();
      var quickResult = processor.quickSort(testData);
      quickStopwatch.stop();
      
      // Verify both produce correct results
      expect(bubbleResult, orderedEquals([...bubbleResult]..sort()));
      expect(quickResult, orderedEquals([...quickResult]..sort()));
      
      // QuickSort should be significantly faster
      expect(quickStopwatch.elapsedMicroseconds, 
             lessThan(bubbleStopwatch.elapsedMicroseconds ~/ 10));
      
      print('BubbleSort (1000 items): ${bubbleStopwatch.elapsedMilliseconds}ms');
      print('QuickSort (1000 items): ${quickStopwatch.elapsedMicroseconds}μs');
    });
    
    test('should benchmark text processing performance', () {
      var largeText = 'Hello there! ' * 10000;
      var stopwatch = Stopwatch();
      
      // Warm up JIT compiler
      for (int i = 0; i < 10; i++) {
        processor.processLargeText(largeText.substring(0, 100));
      }
      
      stopwatch.start();
      var result = processor.processLargeText(largeText);
      stopwatch.stop();
      
      expect(result, isNotEmpty);
      expect(result.first, equals('hello'));
      expect(result[1], equals('there'));
      expect(stopwatch.elapsedMilliseconds, lessThan(100));
      
      print('Text processing: ${stopwatch.elapsedMilliseconds}ms for ${largeText.length} chars');
    });
    
    test('should measure memory efficiency', () {
      var initialMemory = ProcessInfo.currentRss;
      var testData = <List<int>>[];
      
      // Allocate test data
      for (int i = 0; i < 100; i++) {
        testData.add(processor.generateRandomData(1000));
      }
      
      var peakMemory = ProcessInfo.currentRss;
      var memoryIncrease = peakMemory - initialMemory;
      
      // Clean up
      testData.clear();
      
      expect(memoryIncrease, lessThan(50 * 1024 * 1024)); // Less than 50MB
      print('Memory increase: ${memoryIncrease ~/ 1024}KB');
    }, skip: 'Memory testing requires platform-specific implementation');
    
    test('should benchmark concurrent operations', () async {
      var stopwatch = Stopwatch()..start();
      
      // Simulate concurrent data processing
      var futures = <Future>[];
      for (int i = 0; i < 10; i++) {
        futures.add(Future.microtask(() {
          var data = processor.generateRandomData(500);
          return processor.quickSort(data);
        }));
      }
      
      var results = await Future.wait(futures);
      stopwatch.stop();
      
      expect(results, hasLength(10));
      expect(results.every((result) => result.length == 500), isTrue);
      expect(stopwatch.elapsedMilliseconds, lessThan(1000));
      
      print('Concurrent sorting (10 tasks): ${stopwatch.elapsedMilliseconds}ms');
    });
    
    test('should profile frequency counting performance', () {
      var words = List.generate(10000, (i) => 'word${i % 100}');
      var stopwatch = Stopwatch()..start();
      
      var frequencies = processor.countFrequencies(words);
      
      stopwatch.stop();
      
      expect(frequencies, hasLength(100));
      expect(frequencies.values.reduce((a, b) => a + b), equals(10000));
      expect(stopwatch.elapsedMicroseconds, lessThan(50000)); // 50ms
      
      print('Frequency counting: ${stopwatch.elapsedMicroseconds}μs for 10k items');
    });
  });
}

// Mock ProcessInfo for demonstration
class ProcessInfo {
  static int get currentRss => 1024 * 1024; // Mock 1MB
}
```

Performance testing validates timing requirements and identifies bottlenecks.  
Benchmarking compares algorithm efficiency and tracks performance over time,  
ensuring code changes don't introduce performance regressions.  

## Widget Testing (Flutter Context)

Widget testing validates UI components in isolation, simulating user  
interactions and verifying rendered output without requiring device  
emulation.  

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';

// Example Flutter widgets to test
class CounterWidget extends StatefulWidget {
  final int initialValue;
  final String? title;
  
  const CounterWidget({
    Key? key,
    this.initialValue = 0,
    this.title,
  }) : super(key: key);
  
  @override
  State<CounterWidget> createState() => _CounterWidgetState();
}

class _CounterWidgetState extends State<CounterWidget> {
  late int _counter;
  
  @override
  void initState() {
    super.initState();
    _counter = widget.initialValue;
  }
  
  void _increment() {
    setState(() {
      _counter++;
    });
  }
  
  void _decrement() {
    setState(() {
      _counter--;
    });
  }
  
  void _reset() {
    setState(() {
      _counter = widget.initialValue;
    });
  }
  
  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        if (widget.title != null)
          Text(
            widget.title!,
            style: Theme.of(context).textTheme.headlineSmall,
          ),
        Text(
          '$_counter',
          key: const Key('counter_text'),
          style: Theme.of(context).textTheme.headlineLarge,
        ),
        const SizedBox(height: 20),
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceEvenly,
          children: [
            ElevatedButton(
              key: const Key('decrement_button'),
              onPressed: _decrement,
              child: const Text('-'),
            ),
            ElevatedButton(
              key: const Key('reset_button'),
              onPressed: _reset,
              child: const Text('Reset'),
            ),
            ElevatedButton(
              key: const Key('increment_button'),
              onPressed: _increment,
              child: const Text('+'),
            ),
          ],
        ),
      ],
    );
  }
}

class TodoItem {
  final String id;
  final String title;
  final bool completed;
  
  TodoItem({
    required this.id,
    required this.title,
    this.completed = false,
  });
  
  TodoItem copyWith({
    String? id,
    String? title,
    bool? completed,
  }) {
    return TodoItem(
      id: id ?? this.id,
      title: title ?? this.title,
      completed: completed ?? this.completed,
    );
  }
}

class TodoListWidget extends StatefulWidget {
  const TodoListWidget({Key? key}) : super(key: key);
  
  @override
  State<TodoListWidget> createState() => _TodoListWidgetState();
}

class _TodoListWidgetState extends State<TodoListWidget> {
  final List<TodoItem> _todos = [];
  final TextEditingController _textController = TextEditingController();
  
  void _addTodo(String title) {
    if (title.trim().isNotEmpty) {
      setState(() {
        _todos.add(TodoItem(
          id: DateTime.now().millisecondsSinceEpoch.toString(),
          title: title.trim(),
        ));
      });
      _textController.clear();
    }
  }
  
  void _toggleTodo(String id) {
    setState(() {
      var index = _todos.indexWhere((todo) => todo.id == id);
      if (index != -1) {
        _todos[index] = _todos[index].copyWith(completed: !_todos[index].completed);
      }
    });
  }
  
  void _removeTodo(String id) {
    setState(() {
      _todos.removeWhere((todo) => todo.id == id);
    });
  }
  
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Padding(
          padding: const EdgeInsets.all(16.0),
          child: Row(
            children: [
              Expanded(
                child: TextField(
                  key: const Key('todo_input'),
                  controller: _textController,
                  decoration: const InputDecoration(
                    hintText: 'Enter todo item',
                    border: OutlineInputBorder(),
                  ),
                ),
              ),
              const SizedBox(width: 8),
              ElevatedButton(
                key: const Key('add_todo_button'),
                onPressed: () => _addTodo(_textController.text),
                child: const Text('Add'),
              ),
            ],
          ),
        ),
        Expanded(
          child: ListView.builder(
            key: const Key('todo_list'),
            itemCount: _todos.length,
            itemBuilder: (context, index) {
              var todo = _todos[index];
              return ListTile(
                key: Key('todo_item_${todo.id}'),
                leading: Checkbox(
                  value: todo.completed,
                  onChanged: (_) => _toggleTodo(todo.id),
                ),
                title: Text(
                  todo.title,
                  style: TextStyle(
                    decoration: todo.completed 
                        ? TextDecoration.lineThrough 
                        : TextDecoration.none,
                  ),
                ),
                trailing: IconButton(
                  icon: const Icon(Icons.delete),
                  onPressed: () => _removeTodo(todo.id),
                ),
              );
            },
          ),
        ),
      ],
    );
  }
}

void main() {
  group('Widget Testing Examples', () {
    testWidgets('CounterWidget should display initial value', (tester) async {
      await tester.pumpWidget(
        const MaterialApp(
          home: Scaffold(
            body: CounterWidget(initialValue: 5),
          ),
        ),
      );
      
      expect(find.text('5'), findsOneWidget);
      expect(find.byKey(const Key('counter_text')), findsOneWidget);
    });
    
    testWidgets('CounterWidget should increment and decrement', (tester) async {
      await tester.pumpWidget(
        const MaterialApp(
          home: Scaffold(
            body: CounterWidget(initialValue: 0),
          ),
        ),
      );
      
      // Initial state
      expect(find.text('0'), findsOneWidget);
      
      // Test increment
      await tester.tap(find.byKey(const Key('increment_button')));
      await tester.pump();
      expect(find.text('1'), findsOneWidget);
      
      // Test multiple increments
      await tester.tap(find.byKey(const Key('increment_button')));
      await tester.pump();
      expect(find.text('2'), findsOneWidget);
      
      // Test decrement
      await tester.tap(find.byKey(const Key('decrement_button')));
      await tester.pump();
      expect(find.text('1'), findsOneWidget);
    });
    
    testWidgets('CounterWidget should reset to initial value', (tester) async {
      await tester.pumpWidget(
        const MaterialApp(
          home: Scaffold(
            body: CounterWidget(initialValue: 10),
          ),
        ),
      );
      
      // Change the counter value
      await tester.tap(find.byKey(const Key('increment_button')));
      await tester.pump();
      expect(find.text('11'), findsOneWidget);
      
      // Reset should return to initial value
      await tester.tap(find.byKey(const Key('reset_button')));
      await tester.pump();
      expect(find.text('10'), findsOneWidget);
    });
    
    testWidgets('CounterWidget should display custom title', (tester) async {
      await tester.pumpWidget(
        const MaterialApp(
          home: Scaffold(
            body: CounterWidget(
              initialValue: 0,
              title: 'Hello there Counter!',
            ),
          ),
        ),
      );
      
      expect(find.text('Hello there Counter!'), findsOneWidget);
      expect(find.text('0'), findsOneWidget);
    });
    
    testWidgets('TodoListWidget should add new todos', (tester) async {
      await tester.pumpWidget(
        const MaterialApp(
          home: Scaffold(
            body: TodoListWidget(),
          ),
        ),
      );
      
      // Enter text and add todo
      await tester.enterText(find.byKey(const Key('todo_input')), 'Test todo item');
      await tester.tap(find.byKey(const Key('add_todo_button')));
      await tester.pump();
      
      expect(find.text('Test todo item'), findsOneWidget);
      expect(find.byType(ListTile), findsOneWidget);
    });
    
    testWidgets('TodoListWidget should toggle todo completion', (tester) async {
      await tester.pumpWidget(
        const MaterialApp(
          home: Scaffold(
            body: TodoListWidget(),
          ),
        ),
      );
      
      // Add a todo
      await tester.enterText(find.byKey(const Key('todo_input')), 'Complete this task');
      await tester.tap(find.byKey(const Key('add_todo_button')));
      await tester.pump();
      
      // Toggle completion
      await tester.tap(find.byType(Checkbox));
      await tester.pump();
      
      // Verify checkbox is checked and text has strikethrough
      var checkbox = tester.widget<Checkbox>(find.byType(Checkbox));
      expect(checkbox.value, isTrue);
    });
    
    testWidgets('TodoListWidget should remove todos', (tester) async {
      await tester.pumpWidget(
        const MaterialApp(
          home: Scaffold(
            body: TodoListWidget(),
          ),
        ),
      );
      
      // Add a todo
      await tester.enterText(find.byKey(const Key('todo_input')), 'Delete this item');
      await tester.tap(find.byKey(const Key('add_todo_button')));
      await tester.pump();
      
      expect(find.text('Delete this item'), findsOneWidget);
      
      // Delete the todo
      await tester.tap(find.byIcon(Icons.delete));
      await tester.pump();
      
      expect(find.text('Delete this item'), findsNothing);
      expect(find.byType(ListTile), findsNothing);
    });
    
    testWidgets('TodoListWidget should handle multiple todos', (tester) async {
      await tester.pumpWidget(
        const MaterialApp(
          home: Scaffold(
            body: TodoListWidget(),
          ),
        ),
      );
      
      // Add multiple todos
      var todoItems = ['First todo', 'Second todo', 'Third todo'];
      
      for (var item in todoItems) {
        await tester.enterText(find.byKey(const Key('todo_input')), item);
        await tester.tap(find.byKey(const Key('add_todo_button')));
        await tester.pump();
      }
      
      // Verify all todos are displayed
      for (var item in todoItems) {
        expect(find.text(item), findsOneWidget);
      }
      
      expect(find.byType(ListTile), findsNWidgets(3));
    });
  });
}
```

Widget testing validates UI behavior through simulated user interactions.  
The tester.pump() method rebuilds widgets after state changes, and finder  
methods locate widgets by type, key, or text for interaction and assertion.  

## Test-Driven Development (TDD) Example

Test-Driven Development follows a Red-Green-Refactor cycle: write failing  
tests, implement minimal code to pass, then refactor for quality.  

```dart
import 'package:test/test.dart';

// Step 1: Write failing tests first (Red)
// Step 2: Implement minimal code to pass (Green)  
// Step 3: Refactor while keeping tests passing (Refactor)

class StringAnalyzer {
  Map<String, dynamic> analyze(String text) {
    if (text.isEmpty) {
      return {
        'length': 0,
        'wordCount': 0,
        'sentenceCount': 0,
        'averageWordLength': 0.0,
        'mostCommonWord': null,
        'containsDigits': false,
        'isAllUppercase': false,
        'isAllLowercase': false,
      };
    }
    
    var words = text.toLowerCase()
        .replaceAll(RegExp(r'[^\w\s]'), '')
        .split(RegExp(r'\s+'))
        .where((w) => w.isNotEmpty)
        .toList();
    
    var sentences = text.split(RegExp(r'[.!?]+'))
        .where((s) => s.trim().isNotEmpty)
        .length;
    
    var wordFrequency = <String, int>{};
    for (var word in words) {
      wordFrequency[word] = (wordFrequency[word] ?? 0) + 1;
    }
    
    var mostCommon = wordFrequency.isNotEmpty
        ? wordFrequency.entries.reduce((a, b) => a.value > b.value ? a : b).key
        : null;
    
    var avgWordLength = words.isEmpty 
        ? 0.0 
        : words.fold(0, (sum, word) => sum + word.length) / words.length;
    
    return {
      'length': text.length,
      'wordCount': words.length,
      'sentenceCount': sentences,
      'averageWordLength': avgWordLength,
      'mostCommonWord': mostCommon,
      'containsDigits': text.contains(RegExp(r'\d')),
      'isAllUppercase': text == text.toUpperCase() && text.contains(RegExp(r'[a-zA-Z]')),
      'isAllLowercase': text == text.toLowerCase() && text.contains(RegExp(r'[a-zA-Z]')),
    };
  }
}

void main() {
  group('TDD Example - String Analyzer', () {
    late StringAnalyzer analyzer;
    
    setUp(() {
      analyzer = StringAnalyzer();
    });
    
    // RED: Write failing test first
    test('should handle empty string', () {
      var result = analyzer.analyze('');
      
      expect(result['length'], equals(0));
      expect(result['wordCount'], equals(0));
      expect(result['sentenceCount'], equals(0));
      expect(result['averageWordLength'], equals(0.0));
      expect(result['mostCommonWord'], isNull);
      expect(result['containsDigits'], isFalse);
      expect(result['isAllUppercase'], isFalse);
      expect(result['isAllLowercase'], isFalse);
    });
    
    // GREEN: Implement minimal code to pass
    test('should count basic text properties', () {
      var result = analyzer.analyze('Hello there world!');
      
      expect(result['length'], equals(18));
      expect(result['wordCount'], equals(3));
      expect(result['sentenceCount'], equals(1));
    });
    
    // Continue with more tests
    test('should calculate average word length', () {
      var result = analyzer.analyze('Hello world test');
      // Words: "hello"(5), "world"(5), "test"(4) = avg 4.67
      
      expect(result['averageWordLength'], closeTo(4.67, 0.01));
    });
    
    test('should identify most common word', () {
      var result = analyzer.analyze('the cat and the dog and the bird');
      
      expect(result['mostCommonWord'], equals('the')); // appears 3 times
    });
    
    test('should count sentences correctly', () {
      var result = analyzer.analyze('First sentence. Second sentence! Third?');
      
      expect(result['sentenceCount'], equals(3));
    });
    
    test('should detect digits', () {
      var withDigits = analyzer.analyze('Hello 123 world!');
      var withoutDigits = analyzer.analyze('Hello world!');
      
      expect(withDigits['containsDigits'], isTrue);
      expect(withoutDigits['containsDigits'], isFalse);
    });
    
    test('should detect case patterns', () {
      var uppercase = analyzer.analyze('HELLO WORLD');
      var lowercase = analyzer.analyze('hello world');
      var mixed = analyzer.analyze('Hello World');
      
      expect(uppercase['isAllUppercase'], isTrue);
      expect(uppercase['isAllLowercase'], isFalse);
      
      expect(lowercase['isAllUppercase'], isFalse);
      expect(lowercase['isAllLowercase'], isTrue);
      
      expect(mixed['isAllUppercase'], isFalse);
      expect(mixed['isAllLowercase'], isFalse);
    });
    
    // REFACTOR: Test edge cases to drive refactoring
    test('should handle punctuation correctly', () {
      var result = analyzer.analyze('Hello, world! How are you today?');
      
      expect(result['wordCount'], equals(6));
      expect(result['sentenceCount'], equals(2));
    });
    
    test('should handle multiple spaces', () {
      var result = analyzer.analyze('Hello    world     test');
      
      expect(result['wordCount'], equals(3));
    });
  });
}
```

TDD ensures comprehensive test coverage by writing tests before  
implementation. This approach leads to better design decisions and  
prevents over-engineering while maintaining high code quality.  

## Advanced Test Organization

Complex test suites require sophisticated organization strategies to  
maintain clarity and enable efficient test execution.  

```dart
import 'package:test/test.dart';

// Shared test fixtures and utilities
class TestFixtures {
  static const validUser = {
    'id': '123',
    'name': 'Alice Johnson',
    'email': 'alice@example.com',
    'role': 'admin',
    'active': true,
  };
  
  static const inactiveUser = {
    'id': '456',
    'name': 'Bob Smith',
    'email': 'bob@example.com',
    'role': 'user',
    'active': false,
  };
  
  static List<Map<String, dynamic>> get sampleUsers => [
    validUser,
    inactiveUser,
    {
      'id': '789',
      'name': 'Charlie Brown',
      'email': 'charlie@example.com',
      'role': 'moderator',
      'active': true,
    },
  ];
}

// Test utilities
class TestUtils {
  static Map<String, dynamic> createUser({
    String? id,
    String? name,
    String? email,
    String? role,
    bool? active,
  }) {
    return {
      'id': id ?? 'test_${DateTime.now().millisecondsSinceEpoch}',
      'name': name ?? 'Test User',
      'email': email ?? 'test@example.com',
      'role': role ?? 'user',
      'active': active ?? true,
    };
  }
  
  static bool isValidEmail(String email) {
    return RegExp(r'^[^@]+@[^@]+\.[^@]+').hasMatch(email);
  }
}

// Service class to test
class UserService {
  final List<Map<String, dynamic>> _users = [];
  
  void addUser(Map<String, dynamic> user) {
    if (!TestUtils.isValidEmail(user['email'])) {
      throw ArgumentError('Invalid email format');
    }
    if (user['name'].toString().isEmpty) {
      throw ArgumentError('Name cannot be empty');
    }
    _users.add(Map.from(user));
  }
  
  List<Map<String, dynamic>> getActiveUsers() {
    return _users.where((user) => user['active'] == true).toList();
  }
  
  List<Map<String, dynamic>> getUsersByRole(String role) {
    return _users.where((user) => user['role'] == role).toList();
  }
  
  Map<String, dynamic>? findUserById(String id) {
    try {
      return _users.firstWhere((user) => user['id'] == id);
    } catch (e) {
      return null;
    }
  }
  
  void deactivateUser(String id) {
    var user = findUserById(id);
    if (user != null) {
      user['active'] = false;
    }
  }
  
  int get userCount => _users.length;
}

void main() {
  // Shared setup for all test groups
  late UserService userService;
  
  setUpAll(() {
    print('Starting UserService test suite');
  });
  
  tearDownAll(() {
    print('UserService test suite completed');
  });
  
  group('UserService', () {
    setUp(() {
      userService = UserService();
    });
    
    group('User Addition', () {
      test('should add valid user', () {
        userService.addUser(TestFixtures.validUser);
        expect(userService.userCount, equals(1));
      });
      
      test('should reject invalid email', () {
        var invalidUser = TestUtils.createUser(email: 'invalid-email');
        expect(() => userService.addUser(invalidUser), throwsArgumentError);
      });
      
      test('should reject empty name', () {
        var invalidUser = TestUtils.createUser(name: '');
        expect(() => userService.addUser(invalidUser), throwsArgumentError);
      });
      
      group('Bulk Operations', () {
        test('should add multiple users', () {
          for (var user in TestFixtures.sampleUsers) {
            userService.addUser(user);
          }
          expect(userService.userCount, equals(3));
        });
      });
    });
    
    group('User Retrieval', () {
      setUp(() {
        // Add sample data for retrieval tests
        for (var user in TestFixtures.sampleUsers) {
          userService.addUser(user);
        }
      });
      
      test('should find user by ID', () {
        var user = userService.findUserById('123');
        expect(user, isNotNull);
        expect(user!['name'], equals('Alice Johnson'));
      });
      
      test('should return null for non-existent user', () {
        var user = userService.findUserById('nonexistent');
        expect(user, isNull);
      });
      
      group('Filtering', () {
        test('should get active users only', () {
          var activeUsers = userService.getActiveUsers();
          expect(activeUsers, hasLength(2));
          expect(activeUsers.every((user) => user['active'] == true), isTrue);
        });
        
        test('should filter users by role', () {
          var adminUsers = userService.getUsersByRole('admin');
          var regularUsers = userService.getUsersByRole('user');
          
          expect(adminUsers, hasLength(1));
          expect(regularUsers, hasLength(1));
          expect(adminUsers.first['name'], equals('Alice Johnson'));
        });
      });
    });
    
    group('User Management', () {
      setUp(() {
        userService.addUser(TestFixtures.validUser);
      });
      
      test('should deactivate user', () {
        userService.deactivateUser('123');
        var user = userService.findUserById('123');
        expect(user!['active'], isFalse);
      });
      
      test('should handle deactivation of non-existent user', () {
        // Should not throw error
        userService.deactivateUser('nonexistent');
        expect(userService.userCount, equals(1));
      });
    });
    
    group('Edge Cases', () {
      test('should handle empty service', () {
        expect(userService.userCount, equals(0));
        expect(userService.getActiveUsers(), isEmpty);
        expect(userService.getUsersByRole('admin'), isEmpty);
      });
      
      test('should handle special characters in data', () {
        var specialUser = TestUtils.createUser(
          name: 'José María O\'Brien',
          email: 'jose.maria@test-domain.co.uk',
        );
        
        userService.addUser(specialUser);
        expect(userService.userCount, equals(1));
      });
    });
  });
  
  // Separate group for integration scenarios
  group('UserService Integration Scenarios', () {
    late UserService service1;
    late UserService service2;
    
    setUp(() {
      service1 = UserService();
      service2 = UserService();
    });
    
    test('should demonstrate service isolation', () {
      service1.addUser(TestFixtures.validUser);
      
      expect(service1.userCount, equals(1));
      expect(service2.userCount, equals(0));
    });
    
    test('should handle concurrent modifications', () {
      // Simulate concurrent operations
      var user1 = TestUtils.createUser(name: 'User 1');
      var user2 = TestUtils.createUser(name: 'User 2');
      
      service1.addUser(user1);
      service1.addUser(user2);
      
      expect(service1.userCount, equals(2));
      
      // Modify one user
      service1.deactivateUser(user1['id']);
      
      var activeUsers = service1.getActiveUsers();
      expect(activeUsers, hasLength(1));
      expect(activeUsers.first['name'], equals('User 2'));
    });
  });
}
```

Advanced test organization uses nested groups, shared fixtures, and  
utility classes to create maintainable test suites. This structure  
enables focused testing while maintaining clear test separation.  

## Testing with External Dependencies

Testing code that interacts with external services requires mocking  
and stubbing techniques to create reliable, fast-running tests.  

```dart
import 'package:test/test.dart';
import 'dart:convert';
import 'dart:async';

// External service interfaces
abstract class HttpClient {
  Future<HttpResponse> get(String url, {Map<String, String>? headers});
  Future<HttpResponse> post(String url, {String? body, Map<String, String>? headers});
}

class HttpResponse {
  final int statusCode;
  final String body;
  final Map<String, String> headers;
  
  HttpResponse({
    required this.statusCode,
    required this.body,
    this.headers = const {},
  });
}

abstract class FileSystem {
  Future<String> readFile(String path);
  Future<void> writeFile(String path, String content);
  Future<bool> fileExists(String path);
}

// Service that depends on external systems
class ConfigurationService {
  final HttpClient _httpClient;
  final FileSystem _fileSystem;
  
  ConfigurationService(this._httpClient, this._fileSystem);
  
  Future<Map<String, dynamic>> loadConfiguration() async {
    try {
      // Try to load from local file first
      if (await _fileSystem.fileExists('config.json')) {
        var content = await _fileSystem.readFile('config.json');
        return json.decode(content);
      }
      
      // Fallback to remote configuration
      var response = await _httpClient.get('https://api.example.com/config');
      if (response.statusCode == 200) {
        var config = json.decode(response.body);
        
        // Cache the configuration locally
        await _fileSystem.writeFile('config.json', response.body);
        
        return config;
      } else {
        throw Exception('Failed to load configuration: ${response.statusCode}');
      }
    } catch (e) {
      // Return default configuration on error
      return {
        'appName': 'Default App',
        'version': '1.0.0',
        'debug': false,
        'apiUrl': 'https://api.example.com',
      };
    }
  }
  
  Future<void> updateConfiguration(Map<String, dynamic> config) async {
    var jsonContent = json.encode(config);
    
    // Update local file
    await _fileSystem.writeFile('config.json', jsonContent);
    
    // Sync with remote service
    var response = await _httpClient.post(
      'https://api.example.com/config',
      body: jsonContent,
      headers: {'Content-Type': 'application/json'},
    );
    
    if (response.statusCode != 200) {
      throw Exception('Failed to sync configuration: ${response.statusCode}');
    }
  }
}

// Mock implementations for testing
class MockHttpClient implements HttpClient {
  final Map<String, HttpResponse> _getResponses = {};
  final Map<String, HttpResponse> _postResponses = {};
  final List<String> _getCalls = [];
  final List<String> _postCalls = [];
  
  void stubGet(String url, HttpResponse response) {
    _getResponses[url] = response;
  }
  
  void stubPost(String url, HttpResponse response) {
    _postResponses[url] = response;
  }
  
  List<String> get getCalls => List.unmodifiable(_getCalls);
  List<String> get postCalls => List.unmodifiable(_postCalls);
  
  @override
  Future<HttpResponse> get(String url, {Map<String, String>? headers}) async {
    _getCalls.add(url);
    await Future.delayed(Duration(milliseconds: 10)); // Simulate network delay
    
    if (_getResponses.containsKey(url)) {
      return _getResponses[url]!;
    }
    
    return HttpResponse(statusCode: 404, body: 'Not Found');
  }
  
  @override
  Future<HttpResponse> post(String url, {String? body, Map<String, String>? headers}) async {
    _postCalls.add(url);
    await Future.delayed(Duration(milliseconds: 10)); // Simulate network delay
    
    if (_postResponses.containsKey(url)) {
      return _postResponses[url]!;
    }
    
    return HttpResponse(statusCode: 404, body: 'Not Found');
  }
}

class MockFileSystem implements FileSystem {
  final Map<String, String> _files = {};
  final List<String> _readCalls = [];
  final List<String> _writeCalls = [];
  
  List<String> get readCalls => List.unmodifiable(_readCalls);
  List<String> get writeCalls => List.unmodifiable(_writeCalls);
  
  @override
  Future<String> readFile(String path) async {
    _readCalls.add(path);
    await Future.delayed(Duration(milliseconds: 5)); // Simulate file I/O
    
    if (_files.containsKey(path)) {
      return _files[path]!;
    }
    
    throw Exception('File not found: $path');
  }
  
  @override
  Future<void> writeFile(String path, String content) async {
    _writeCalls.add(path);
    await Future.delayed(Duration(milliseconds: 5)); // Simulate file I/O
    _files[path] = content;
  }
  
  @override
  Future<bool> fileExists(String path) async {
    return _files.containsKey(path);
  }
  
  void addFile(String path, String content) {
    _files[path] = content;
  }
}

void main() {
  group('Testing with External Dependencies', () {
    late ConfigurationService configService;
    late MockHttpClient mockHttp;
    late MockFileSystem mockFileSystem;
    
    setUp(() {
      mockHttp = MockHttpClient();
      mockFileSystem = MockFileSystem();
      configService = ConfigurationService(mockHttp, mockFileSystem);
    });
    
    test('should load configuration from local file when available', () async {
      // Arrange
      var localConfig = {
        'appName': 'Test App',
        'version': '2.0.0',
        'debug': true,
      };
      mockFileSystem.addFile('config.json', json.encode(localConfig));
      
      // Act
      var result = await configService.loadConfiguration();
      
      // Assert
      expect(result, equals(localConfig));
      expect(mockFileSystem.readCalls, contains('config.json'));
      expect(mockHttp.getCalls, isEmpty); // Should not make HTTP call
    });
    
    test('should load from remote when local file not available', () async {
      // Arrange
      var remoteConfig = {
        'appName': 'Remote App',
        'version': '3.0.0',
        'apiUrl': 'https://api.remote.com',
      };
      
      mockHttp.stubGet(
        'https://api.example.com/config',
        HttpResponse(statusCode: 200, body: json.encode(remoteConfig)),
      );
      
      // Act
      var result = await configService.loadConfiguration();
      
      // Assert
      expect(result, equals(remoteConfig));
      expect(mockHttp.getCalls, contains('https://api.example.com/config'));
      expect(mockFileSystem.writeCalls, contains('config.json')); // Should cache locally
    });
    
    test('should return default config when both sources fail', () async {
      // Arrange
      mockHttp.stubGet(
        'https://api.example.com/config',
        HttpResponse(statusCode: 500, body: 'Server Error'),
      );
      
      // Act
      var result = await configService.loadConfiguration();
      
      // Assert
      expect(result['appName'], equals('Default App'));
      expect(result['version'], equals('1.0.0'));
      expect(result['debug'], isFalse);
    });
    
    test('should update configuration locally and remotely', () async {
      // Arrange
      var newConfig = {
        'appName': 'Updated App',
        'version': '4.0.0',
        'debug': true,
      };
      
      mockHttp.stubPost(
        'https://api.example.com/config',
        HttpResponse(statusCode: 200, body: 'OK'),
      );
      
      // Act
      await configService.updateConfiguration(newConfig);
      
      // Assert
      expect(mockFileSystem.writeCalls, contains('config.json'));
      expect(mockHttp.postCalls, contains('https://api.example.com/config'));
    });
    
    test('should handle remote update failure', () async {
      // Arrange
      var config = {'test': 'value'};
      
      mockHttp.stubPost(
        'https://api.example.com/config',
        HttpResponse(statusCode: 403, body: 'Forbidden'),
      );
      
      // Act & Assert
      expect(() => configService.updateConfiguration(config), throwsException);
      expect(mockFileSystem.writeCalls, contains('config.json')); // Should still write locally
    });
    
    test('should handle network timeouts gracefully', () async {
      // Arrange
      mockHttp.stubGet(
        'https://api.example.com/config',
        HttpResponse(statusCode: 408, body: 'Timeout'),
      );
      
      // Act
      var result = await configService.loadConfiguration();
      
      // Assert - Should fall back to default configuration
      expect(result['appName'], equals('Default App'));
      expect(mockHttp.getCalls, contains('https://api.example.com/config'));
    });
    
    test('should verify interaction order and timing', () async {
      // This test verifies the order of operations
      var remoteConfig = {'test': 'remote'};
      mockHttp.stubGet(
        'https://api.example.com/config',
        HttpResponse(statusCode: 200, body: json.encode(remoteConfig)),
      );
      
      var stopwatch = Stopwatch()..start();
      await configService.loadConfiguration();
      stopwatch.stop();
      
      // Verify correct sequence: check file exists -> HTTP call -> write file
      expect(mockHttp.getCalls, hasLength(1));
      expect(mockFileSystem.writeCalls, hasLength(1));
      
      // Verify timing (should include simulated delays)
      expect(stopwatch.elapsedMilliseconds, greaterThan(10));
    });
  });
}
```

Testing with external dependencies requires mock implementations that  
simulate real service behavior. Mock objects enable controlled testing  
scenarios and verification of interaction patterns.  

## Testing Utilities and Helpers

Test utilities and helper functions reduce code duplication and provide  
reusable testing infrastructure for complex scenarios.  

```dart
import 'package:test/test.dart';
import 'dart:math';

// Test data builders
class UserBuilder {
  String _id = 'user_123';
  String _name = 'Test User';
  String _email = 'test@example.com';
  int _age = 25;
  List<String> _roles = ['user'];
  bool _active = true;
  
  UserBuilder withId(String id) {
    _id = id;
    return this;
  }
  
  UserBuilder withName(String name) {
    _name = name;
    return this;
  }
  
  UserBuilder withEmail(String email) {
    _email = email;
    return this;
  }
  
  UserBuilder withAge(int age) {
    _age = age;
    return this;
  }
  
  UserBuilder withRoles(List<String> roles) {
    _roles = List.from(roles);
    return this;
  }
  
  UserBuilder inactive() {
    _active = false;
    return this;
  }
  
  UserBuilder admin() {
    _roles.add('admin');
    return this;
  }
  
  Map<String, dynamic> build() {
    return {
      'id': _id,
      'name': _name,
      'email': _email,
      'age': _age,
      'roles': List.from(_roles),
      'active': _active,
    };
  }
}

// Test data generators
class DataGenerator {
  static final Random _random = Random();
  
  static String randomString(int length) {
    const chars = 'abcdefghijklmnopqrstuvwxyz0123456789';
    return String.fromCharCodes(Iterable.generate(
        length, (_) => chars.codeUnitAt(_random.nextInt(chars.length))));
  }
  
  static String randomEmail() {
    var username = randomString(8);
    var domain = randomString(6);
    var tld = ['com', 'org', 'net', 'edu'][_random.nextInt(4)];
    return '$username@$domain.$tld';
  }
  
  static List<Map<String, dynamic>> randomUsers(int count) {
    return List.generate(count, (i) {
      return UserBuilder()
          .withId('user_$i')
          .withName('User $i')
          .withEmail(randomEmail())
          .withAge(18 + _random.nextInt(50))
          .build();
    });
  }
  
  static List<int> randomIntegers(int count, {int min = 0, int max = 100}) {
    return List.generate(count, (_) => min + _random.nextInt(max - min + 1));
  }
}

// Custom assertions
class CustomAssertions {
  static void assertUserValid(Map<String, dynamic> user) {
    expect(user['id'], isNotNull);
    expect(user['name'], isNotEmpty);
    expect(user['email'], matches(RegExp(r'^[^@]+@[^@]+\.[^@]+$')));
    expect(user['age'], greaterThan(0));
    expect(user['roles'], isA<List>());
    expect(user['active'], isA<bool>());
  }
  
  static void assertListSorted<T extends Comparable>(List<T> list) {
    for (int i = 1; i < list.length; i++) {
      expect(list[i - 1].compareTo(list[i]), lessThanOrEqualTo(0),
          reason: 'List not sorted at index $i: ${list[i - 1]} > ${list[i]}');
    }
  }
  
  static void assertWithinTimeRange(DateTime actual, DateTime expected, 
      Duration tolerance) {
    var difference = actual.difference(expected).abs();
    expect(difference, lessThanOrEqualTo(tolerance),
        reason: 'Time difference ${difference.inMilliseconds}ms exceeds tolerance ${tolerance.inMilliseconds}ms');
  }
}

// Test fixtures with lifecycle management
class DatabaseFixture {
  final List<Map<String, dynamic>> _users = [];
  final List<Map<String, dynamic>> _products = [];
  
  void addUser(Map<String, dynamic> user) {
    _users.add(Map.from(user));
  }
  
  void addUsers(List<Map<String, dynamic>> users) {
    _users.addAll(users.map((u) => Map.from(u)));
  }
  
  void addProduct(Map<String, dynamic> product) {
    _products.add(Map.from(product));
  }
  
  List<Map<String, dynamic>> getUsersByRole(String role) {
    return _users.where((user) => 
        (user['roles'] as List).contains(role)).toList();
  }
  
  Map<String, dynamic>? getUserById(String id) {
    try {
      return _users.firstWhere((user) => user['id'] == id);
    } catch (e) {
      return null;
    }
  }
  
  void clear() {
    _users.clear();
    _products.clear();
  }
  
  int get userCount => _users.length;
  int get productCount => _products.length;
}

// Performance testing utilities
class PerformanceHelper {
  static Future<Duration> measureAsync(Future Function() operation) async {
    var stopwatch = Stopwatch()..start();
    await operation();
    stopwatch.stop();
    return Duration(microseconds: stopwatch.elapsedMicroseconds);
  }
  
  static Duration measureSync(void Function() operation) {
    var stopwatch = Stopwatch()..start();
    operation();
    stopwatch.stop();
    return Duration(microseconds: stopwatch.elapsedMicroseconds);
  }
  
  static Future<List<Duration>> benchmark(Future Function() operation, 
      int iterations) async {
    var durations = <Duration>[];
    
    for (int i = 0; i < iterations; i++) {
      durations.add(await measureAsync(operation));
      
      // Small delay between iterations
      await Future.delayed(Duration(milliseconds: 1));
    }
    
    return durations;
  }
  
  static Map<String, double> analyzePerformance(List<Duration> durations) {
    if (durations.isEmpty) return {};
    
    var microseconds = durations.map((d) => d.inMicroseconds).toList();
    microseconds.sort();
    
    var sum = microseconds.fold(0, (a, b) => a + b);
    var average = sum / microseconds.length;
    
    return {
      'min': microseconds.first.toDouble(),
      'max': microseconds.last.toDouble(),
      'average': average,
      'median': microseconds[microseconds.length ~/ 2].toDouble(),
      'p95': microseconds[(microseconds.length * 0.95).floor()].toDouble(),
    };
  }
}

void main() {
  group('Testing Utilities and Helpers', () {
    late DatabaseFixture dbFixture;
    
    setUp(() {
      dbFixture = DatabaseFixture();
    });
    
    tearDown(() {
      dbFixture.clear();
    });
    
    test('should use builder pattern for test data', () {
      var admin = UserBuilder()
          .withName('Admin User')
          .withEmail('admin@test.com')
          .admin()
          .build();
      
      var inactiveUser = UserBuilder()
          .withName('Inactive User')
          .inactive()
          .build();
      
      CustomAssertions.assertUserValid(admin);
      CustomAssertions.assertUserValid(inactiveUser);
      
      expect(admin['roles'], contains('admin'));
      expect(inactiveUser['active'], isFalse);
    });
    
    test('should generate random test data', () {
      var users = DataGenerator.randomUsers(10);
      
      expect(users, hasLength(10));
      
      for (var user in users) {
        CustomAssertions.assertUserValid(user);
        expect(user['age'], inRange(18, 68));
      }
      
      // Verify uniqueness of generated data
      var ids = users.map((u) => u['id']).toSet();
      expect(ids, hasLength(10));
    });
    
    test('should use database fixture for complex scenarios', () {
      // Setup test data
      dbFixture.addUsers([
        UserBuilder().withName('Alice').admin().build(),
        UserBuilder().withName('Bob').withRoles(['user', 'editor']).build(),
        UserBuilder().withName('Charlie').build(),
      ]);
      
      expect(dbFixture.userCount, equals(3));
      
      var admins = dbFixture.getUsersByRole('admin');
      var editors = dbFixture.getUsersByRole('editor');
      
      expect(admins, hasLength(1));
      expect(admins.first['name'], equals('Alice'));
      expect(editors, hasLength(1));
      expect(editors.first['name'], equals('Bob'));
    });
    
    test('should validate sorted data with custom assertions', () {
      var numbers = DataGenerator.randomIntegers(20);
      numbers.sort();
      
      CustomAssertions.assertListSorted(numbers);
      
      // Test assertion failure scenario
      var unsorted = [3, 1, 4, 1, 5];
      expect(() => CustomAssertions.assertListSorted(unsorted), 
             throwsTestFailure);
    });
    
    test('should measure performance with utilities', () async {
      // Test synchronous operation
      var syncDuration = PerformanceHelper.measureSync(() {
        var list = List.generate(1000, (i) => i);
        list.sort();
      });
      
      expect(syncDuration.inMicroseconds, greaterThan(0));
      
      // Test asynchronous operation
      var asyncDuration = await PerformanceHelper.measureAsync(() async {
        await Future.delayed(Duration(milliseconds: 10));
        return 'completed';
      });
      
      expect(asyncDuration.inMilliseconds, greaterThanOrEqualTo(10));
    });
    
    test('should benchmark operations', () async {
      var durations = await PerformanceHelper.benchmark(() async {
        var data = List.generate(100, (i) => i);
        data.shuffle();
        data.sort();
      }, 5);
      
      expect(durations, hasLength(5));
      
      var stats = PerformanceHelper.analyzePerformance(durations);
      expect(stats['average'], greaterThan(0));
      expect(stats['min'], lessThanOrEqualTo(stats['average']!));
      expect(stats['max'], greaterThanOrEqualTo(stats['average']!));
    });
    
    test('should validate time-based operations', () {
      var start = DateTime.now();
      
      // Simulate some operation
      var end = start.add(Duration(milliseconds: 100));
      
      CustomAssertions.assertWithinTimeRange(
        end, 
        start.add(Duration(milliseconds: 95)), 
        Duration(milliseconds: 10)
      );
    });
    
    test('should combine utilities for complex testing', () {
      // Generate test data
      var users = DataGenerator.randomUsers(5);
      
      // Setup database fixture
      dbFixture.addUsers(users);
      
      // Validate all users
      for (var user in users) {
        CustomAssertions.assertUserValid(user);
        
        // Verify they can be found by ID
        var found = dbFixture.getUserById(user['id']);
        expect(found, equals(user));
      }
      
      // Measure retrieval performance
      var duration = PerformanceHelper.measureSync(() {
        for (var user in users) {
          dbFixture.getUserById(user['id']);
        }
      });
      
      expect(duration.inMicroseconds, lessThan(1000)); // Should be fast
    });
  });
}
```

Test utilities provide reusable infrastructure including data builders,  
generators, custom assertions, and performance measurement tools. These  
utilities reduce test code duplication and enable sophisticated testing  
scenarios.  

## Testing Anti-patterns and Best Practices

Understanding testing anti-patterns helps avoid common mistakes that  
lead to brittle, unreliable, or unmaintainable test suites.  

```dart
import 'package:test/test.dart';

// Example of code to demonstrate testing anti-patterns
class NotificationService {
  final List<String> _notifications = [];
  DateTime? _lastSent;
  
  void addNotification(String message) {
    if (message.trim().isNotEmpty) {
      _notifications.add(message);
    }
  }
  
  void sendNotifications() {
    if (_notifications.isNotEmpty) {
      _lastSent = DateTime.now();
      print('Sending ${_notifications.length} notifications...');
      _notifications.clear();
    }
  }
  
  bool get hasNotifications => _notifications.isNotEmpty;
  int get notificationCount => _notifications.length;
  DateTime? get lastSentTime => _lastSent;
}

class OrderProcessor {
  final NotificationService _notificationService;
  
  OrderProcessor(this._notificationService);
  
  void processOrder(Map<String, dynamic> order) {
    // Validate order
    if (order['total'] == null || order['total'] <= 0) {
      throw ArgumentError('Invalid order total');
    }
    
    // Process payment (simulated)
    var success = order['paymentMethod'] != 'failed_card';
    
    if (success) {
      _notificationService.addNotification(
          'Order ${order['id']} processed successfully');
    } else {
      _notificationService.addNotification(
          'Order ${order['id']} payment failed');
    }
  }
}

void main() {
  group('Testing Anti-patterns and Best Practices', () {
    
    // ANTI-PATTERN: Testing implementation details
    group('❌ Bad: Testing Implementation Details', () {
      test('should not test private fields directly', () {
        var service = NotificationService();
        service.addNotification('Test message');
        
        // BAD: Testing internal state instead of behavior
        // This test breaks if internal implementation changes
        expect(service.notificationCount, equals(1));
        
        // BETTER: Test observable behavior
        expect(service.hasNotifications, isTrue);
      });
    });
    
    // GOOD PRACTICE: Testing behavior, not implementation
    group('✅ Good: Testing Behavior', () {
      test('should verify notification service behavior', () {
        var service = NotificationService();
        
        // Test initial state
        expect(service.hasNotifications, isFalse);
        
        // Test adding notifications
        service.addNotification('Important message');
        expect(service.hasNotifications, isTrue);
        
        // Test sending clears notifications
        service.sendNotifications();
        expect(service.hasNotifications, isFalse);
      });
    });
    
    // ANTI-PATTERN: Dependent tests
    group('❌ Bad: Test Dependencies', () {
      var sharedService = NotificationService();
      
      test('first test modifies shared state', () {
        sharedService.addNotification('Message 1');
        expect(sharedService.hasNotifications, isTrue);
      });
      
      test('second test depends on first test state', () {
        // BAD: This test depends on the previous test running first
        sharedService.addNotification('Message 2');
        expect(sharedService.notificationCount, equals(2)); // Fragile!
      });
    });
    
    // GOOD PRACTICE: Independent tests
    group('✅ Good: Independent Tests', () {
      late NotificationService service;
      
      setUp(() {
        service = NotificationService(); // Fresh instance for each test
      });
      
      test('should handle single notification', () {
        service.addNotification('Test message');
        expect(service.hasNotifications, isTrue);
      });
      
      test('should handle multiple notifications', () {
        service.addNotification('Message 1');
        service.addNotification('Message 2');
        expect(service.notificationCount, equals(2));
      });
    });
    
    // ANTI-PATTERN: Testing multiple concerns in one test
    group('❌ Bad: Testing Multiple Concerns', () {
      test('should do everything at once', () {
        var service = NotificationService();
        var processor = OrderProcessor(service);
        
        // BAD: Testing too many things in one test
        service.addNotification('Direct message');
        expect(service.hasNotifications, isTrue);
        
        processor.processOrder({'id': '123', 'total': 100.0, 'paymentMethod': 'card'});
        expect(service.notificationCount, equals(2));
        
        service.sendNotifications();
        expect(service.hasNotifications, isFalse);
        expect(service.lastSentTime, isNotNull);
        
        processor.processOrder({'id': '456', 'total': 0, 'paymentMethod': 'card'});
        // This test is hard to understand and debug
      });
    });
    
    // GOOD PRACTICE: Single responsibility per test
    group('✅ Good: Single Responsibility', () {
      late NotificationService service;
      late OrderProcessor processor;
      
      setUp(() {
        service = NotificationService();
        processor = OrderProcessor(service);
      });
      
      test('should add notifications directly', () {
        service.addNotification('Direct message');
        expect(service.hasNotifications, isTrue);
      });
      
      test('should process successful orders', () {
        processor.processOrder({'id': '123', 'total': 100.0, 'paymentMethod': 'card'});
        expect(service.hasNotifications, isTrue);
      });
      
      test('should handle order validation errors', () {
        expect(() => processor.processOrder({'id': '456', 'total': 0}), 
               throwsArgumentError);
      });
      
      test('should clear notifications when sent', () {
        service.addNotification('Test');
        service.sendNotifications();
        expect(service.hasNotifications, isFalse);
      });
    });
    
    // ANTI-PATTERN: Hard-coded test data
    group('❌ Bad: Hard-coded Test Data', () {
      test('should use magic numbers and strings', () {
        var service = NotificationService();
        
        // BAD: Magic numbers and unclear test data
        service.addNotification('Test message 1');
        service.addNotification('Test message 2');
        service.addNotification('Test message 3');
        
        expect(service.notificationCount, equals(3)); // What does 3 represent?
      });
    });
    
    // GOOD PRACTICE: Meaningful test data
    group('✅ Good: Meaningful Test Data', () {
      test('should handle multiple notification types', () {
        var service = NotificationService();
        
        var welcomeMessage = 'Welcome to our service!';
        var orderConfirmation = 'Your order has been confirmed';
        var reminderMessage = 'Don\'t forget to complete your profile';
        
        service.addNotification(welcomeMessage);
        service.addNotification(orderConfirmation);
        service.addNotification(reminderMessage);
        
        var expectedNotificationCount = 3;
        expect(service.notificationCount, equals(expectedNotificationCount));
      });
    });
    
    // ANTI-PATTERN: Ignoring test failures
    group('❌ Bad: Ignoring Failures', () {
      test('should not skip failing tests without reason', () {
        var service = NotificationService();
        
        service.addNotification('Test');
        
        // BAD: Skipping test without clear reason
        // expect(service.someNewFeature(), equals('expected'));
      }, skip: 'This test is failing, will fix later'); // Vague reason
    });
    
    // GOOD PRACTICE: Proper test management
    group('✅ Good: Test Management', () {
      test('should handle current functionality correctly', () {
        var service = NotificationService();
        service.addNotification('Test message');
        expect(service.hasNotifications, isTrue);
      });
      
      test('should validate empty message handling', () {
        var service = NotificationService();
        
        service.addNotification('   '); // Whitespace only
        service.addNotification('');    // Empty string
        
        expect(service.hasNotifications, isFalse);
      });
      
      // If skipping, provide clear reason and timeline
      test('should integrate with email service', () {
        // TODO: Implement after email service API is finalized
        // Expected completion: Sprint 15
        // This test validates integration with external email service
      }, skip: 'Waiting for email service API specification');
    });
    
    // ANTI-PATTERN: Over-mocking
    group('❌ Bad: Over-mocking', () {
      test('should not mock everything unnecessarily', () {
        // BAD: Mocking simple value objects and basic operations
        // This makes tests complex and fragile
        
        var service = NotificationService();
        service.addNotification('Hello there!');
        
        // Good: Test simple operations directly
        expect(service.hasNotifications, isTrue);
      });
    });
    
    // GOOD PRACTICE: Selective mocking
    group('✅ Good: Strategic Mocking', () {
      test('should mock only external dependencies', () {
        // Mock only when necessary (external services, complex dependencies)
        var service = NotificationService();
        var processor = OrderProcessor(service);
        
        // Test real collaboration between simple objects
        processor.processOrder({
          'id': 'order_123',
          'total': 299.99,
          'paymentMethod': 'credit_card'
        });
        
        expect(service.hasNotifications, isTrue);
      });
    });
    
    // Best practices summary test
    test('should demonstrate testing best practices', () {
      // GOOD PRACTICES DEMONSTRATED:
      // 1. Clear test name describing behavior
      // 2. Arrange-Act-Assert pattern
      // 3. Single responsibility
      // 4. Meaningful test data
      // 5. Testing behavior, not implementation
      
      // Arrange
      var notificationService = NotificationService();
      var orderProcessor = OrderProcessor(notificationService);
      
      var validOrder = {
        'id': 'ORD-2024-001',
        'total': 149.99,
        'paymentMethod': 'credit_card'
      };
      
      // Act
      orderProcessor.processOrder(validOrder);
      
      // Assert
      expect(notificationService.hasNotifications, isTrue,
          reason: 'Successful order should generate notification');
    });
  });
}
```

Testing anti-patterns create fragile, hard-to-maintain test suites.  
Following best practices like independence, single responsibility, and  
behavior-focused testing creates robust, maintainable test coverage  
that serves as reliable documentation and regression protection.  
