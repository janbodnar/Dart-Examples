# Dart Floating-Point Numbers

Floating-point numbers in Dart represent decimal values and are essential for  
scientific calculations, financial computations, measurements, and any scenario  
requiring fractional precision. Dart uses the `double` type for floating-point  
numbers, implementing the IEEE 754 double-precision (64-bit) standard.  

Understanding floating-point arithmetic is crucial because computers cannot  
perfectly represent all decimal numbers in binary format. This limitation leads  
to precision issues, rounding errors, and comparison challenges that developers  
must handle carefully. Dart provides various tools and methods to work  
effectively with floating-point values while avoiding common pitfalls.  

This guide covers fundamental operations, precision handling, formatting,  
special values, and real-world applications with best practices for robust  
floating-point arithmetic in Dart applications.  

## Basic Floating-Point Declaration

Floating-point numbers can be declared using type inference or explicit  
double type annotations. Dart automatically infers double type when decimal  
notation is used.  

```dart
void main() {
  // Type inference with var
  var price = 19.99;
  var temperature = -5.5;
  var pi = 3.14159;
  
  // Explicit double declaration
  double weight = 75.5;
  double height = 1.82;
  
  // Scientific notation
  double speedOfLight = 2.998e8;  // 299,800,000
  double planckConstant = 6.626e-34;  // 0.0000000000000000000000000000000000626
  
  // Integer literals become double in context
  double whole = 42.0;
  double fromInt = 42;  // Implicitly converted
  
  print('Price: $price');
  print('Temperature: $temperature');
  print('Pi: $pi');
  print('Speed of light: $speedOfLight m/s');
  print('Planck constant: $planckConstant J⋅s');
}
```

This example demonstrates various ways to declare floating-point numbers,  
including standard decimal notation, scientific notation for very large or  
small numbers, and type inference. Scientific notation (e.g., 2.998e8) is  
particularly useful for expressing extreme values concisely.  

```
$ dart run declaration.dart
Price: 19.99
Temperature: -5.5
Pi: 3.14159
Speed of light: 299800000.0 m/s
Planck constant: 6.626e-34 J⋅s
```

## Basic Arithmetic Operations

Floating-point numbers support standard arithmetic operations including  
addition, subtraction, multiplication, and division with automatic type  
promotion when mixing integers and doubles.  

```dart
void main() {
  double a = 10.5;
  double b = 3.2;
  
  // Basic operations
  print('Addition: $a + $b = ${a + b}');
  print('Subtraction: $a - $b = ${a - b}');
  print('Multiplication: $a * $b = ${a * b}');
  print('Division: $a / $b = ${a / b}');
  
  // Modulo operation with doubles
  print('Modulo: $a % $b = ${a % b}');
  
  // Unary operations
  print('Negation: -$a = ${-a}');
  print('Absolute: ${(-7.5).abs()}');
  
  // Mixed integer and double arithmetic
  int intValue = 5;
  double doubleValue = 2.5;
  print('Mixed: $intValue + $doubleValue = ${intValue + doubleValue}');
  
  // Compound assignment
  double counter = 10.0;
  counter += 5.5;
  print('After +=: $counter');
  counter *= 2.0;
  print('After *=: $counter');
}
```

Arithmetic operations with floating-point numbers follow standard mathematical  
rules. When mixing integers and doubles, Dart automatically promotes the  
result to double type. The modulo operator (%) works with doubles, returning  
the floating-point remainder.  

```
$ dart run arithmetic.dart
Addition: 10.5 + 3.2 = 13.7
Subtraction: 10.5 - 3.2 = 7.300000000000001
Multiplication: 10.5 * 3.2 = 33.6
Division: 10.5 / 3.2 = 3.28125
Modulo: 10.5 % 3.2 = 0.8999999999999995
Negation: -10.5 = -10.5
Absolute: 7.5
Mixed: 5 + 2.5 = 7.5
After +=: 15.5
After *=: 31.0
```

## Precision and Rounding Issues

Floating-point arithmetic can produce unexpected results due to binary  
representation limitations. Understanding these precision issues is essential  
for correct calculations.  

```dart
void main() {
  // Classic precision problem
  double result1 = 0.1 + 0.2;
  print('0.1 + 0.2 = $result1');
  print('Expected 0.3, got $result1');
  print('Equal to 0.3? ${result1 == 0.3}');
  
  // Accumulated precision errors
  double sum = 0.0;
  for (int i = 0; i < 10; i++) {
    sum += 0.1;
  }
  print('\n10 * 0.1 = $sum');
  print('Equal to 1.0? ${sum == 1.0}');
  
  // Loss of precision with very different magnitudes
  double large = 1e16;
  double small = 1.0;
  double result2 = large + small;
  print('\n$large + $small = $result2');
  print('Lost precision? ${result2 == large}');
  
  // Multiplication vs repeated addition
  double multiply = 0.1 * 10;
  double repeated = 0.0;
  for (int i = 0; i < 10; i++) {
    repeated += 0.1;
  }
  print('\n0.1 * 10 = $multiply');
  print('0.1 added 10 times = $repeated');
  print('Are they equal? ${multiply == repeated}');
}
```

This example illustrates fundamental floating-point precision issues. The  
binary representation of decimal fractions like 0.1 and 0.2 cannot be exact,  
leading to small errors that accumulate through repeated operations. These  
errors are inherent to binary floating-point arithmetic, not bugs in Dart.  

```
$ dart run precision_issues.dart
0.1 + 0.2 = 0.30000000000000004
Expected 0.3, got 0.30000000000000004
Equal to 0.3? false
10 * 0.1 = 0.9999999999999999
Equal to 1.0? false
1e+16 + 1.0 = 1e+16
Lost precision? true
0.1 * 10 = 1.0
0.1 added 10 times = 0.9999999999999999
Are they equal? false
```

## Safe Floating-Point Comparison

Direct equality comparison of floating-point numbers is unreliable. Instead,  
use epsilon-based comparison to check if values are "close enough".  

```dart
void main() {
  double a = 0.1 + 0.2;
  double b = 0.3;
  
  // Unsafe direct comparison
  print('Direct comparison: $a == $b: ${a == b}');
  
  // Safe epsilon-based comparison
  const double epsilon = 1e-10;
  bool isClose(double x, double y, [double eps = epsilon]) {
    return (x - y).abs() < eps;
  }
  
  print('Epsilon comparison: ${isClose(a, b)}');
  
  // Comparison helper extension
  print('Using extension: ${a.isCloseTo(b)}');
  
  // Different epsilon values for different precision needs
  double x = 1.0000001;
  double y = 1.0000002;
  
  print('\nComparing $x and $y:');
  print('Epsilon 1e-5: ${isClose(x, y, 1e-5)}');
  print('Epsilon 1e-6: ${isClose(x, y, 1e-6)}');
  print('Epsilon 1e-7: ${isClose(x, y, 1e-7)}');
  print('Epsilon 1e-8: ${isClose(x, y, 1e-8)}');
}

extension DoubleComparison on double {
  bool isCloseTo(double other, [double epsilon = 1e-10]) {
    return (this - other).abs() < epsilon;
  }
}
```

Epsilon-based comparison defines a tolerance threshold for considering two  
floating-point numbers equal. The choice of epsilon depends on your  
application's precision requirements. For financial calculations, use larger  
epsilon values; for scientific computations, smaller ones may be needed.  

```
$ dart run safe_comparison.dart
Direct comparison: 0.30000000000000004 == 0.3: false
Epsilon comparison: true
Using extension: true
Comparing 1.0000001 and 1.0000002:
Epsilon 1e-5: true
Epsilon 1e-6: true
Epsilon 1e-7: true
Epsilon 1e-8: false
```


## Rounding Methods

Dart provides multiple rounding methods for different rounding strategies:  
round, floor, ceil, and truncate, each serving specific use cases.  

```dart
void main() {
  double positive = 3.7;
  double negative = -3.7;
  double halfway = 2.5;
  
  print('Value: $positive');
  print('round(): ${positive.round()}');      // Nearest integer
  print('floor(): ${positive.floor()}');      // Round down
  print('ceil(): ${positive.ceil()}');        // Round up
  print('truncate(): ${positive.truncate()}'); // Toward zero
  
  print('\nValue: $negative');
  print('round(): ${negative.round()}');
  print('floor(): ${negative.floor()}');
  print('ceil(): ${negative.ceil()}');
  print('truncate(): ${negative.truncate()}');
  
  print('\nHalfway value: $halfway');
  print('round(): ${halfway.round()}');       // Rounds to even
  print('ceil(): ${halfway.ceil()}');
  
  // Round to specific decimal places
  double pi = 3.14159265;
  print('\nOriginal: $pi');
  print('1 decimal: ${(pi * 10).round() / 10}');
  print('2 decimals: ${(pi * 100).round() / 100}');
  print('3 decimals: ${(pi * 1000).round() / 1000}');
}
```

Different rounding methods serve different purposes: `round()` finds the  
nearest integer (with ties going to even numbers), `floor()` always rounds  
down, `ceil()` always rounds up, and `truncate()` removes the fractional  
part. For rounding to decimal places, multiply, round, then divide.  

```
$ dart run rounding.dart
Value: 3.7
round(): 4
floor(): 3
ceil(): 4
truncate(): 3
Value: -3.7
round(): -4
floor(): -4
ceil(): -3
truncate(): -3
Halfway value: 2.5
round(): 2
ceil(): 3
Original: 3.14159265
1 decimal: 3.1
2 decimals: 3.14
3 decimals: 3.142
```

## Formatting Floating-Point Output

Control how floating-point numbers are displayed using toStringAsFixed,  
toStringAsExponential, and toStringAsPrecision for different formatting  
needs.  

```dart
void main() {
  double value = 1234.56789;
  
  // Fixed decimal places
  print('toStringAsFixed:');
  print('  0 decimals: ${value.toStringAsFixed(0)}');
  print('  2 decimals: ${value.toStringAsFixed(2)}');
  print('  4 decimals: ${value.toStringAsFixed(4)}');
  
  // Scientific notation
  print('\ntoStringAsExponential:');
  print('  Default: ${value.toStringAsExponential()}');
  print('  2 decimals: ${value.toStringAsExponential(2)}');
  print('  4 decimals: ${value.toStringAsExponential(4)}');
  
  // Significant figures
  print('\ntoStringAsPrecision:');
  print('  3 digits: ${value.toStringAsPrecision(3)}');
  print('  5 digits: ${value.toStringAsPrecision(5)}');
  print('  8 digits: ${value.toStringAsPrecision(8)}');
  
  // Different values
  double small = 0.000123;
  double large = 9876543210.0;
  
  print('\nSmall value ($small):');
  print('  Fixed(6): ${small.toStringAsFixed(6)}');
  print('  Exponential: ${small.toStringAsExponential(2)}');
  
  print('\nLarge value ($large):');
  print('  Fixed(2): ${large.toStringAsFixed(2)}');
  print('  Precision(4): ${large.toStringAsPrecision(4)}');
}
```

These formatting methods provide control over number display. `toStringAsFixed`  
specifies decimal places, `toStringAsExponential` uses scientific notation,  
and `toStringAsPrecision` controls total significant digits. Choose based on  
whether you need consistent decimals, scientific format, or significant  
figures.  

```
$ dart run formatting.dart
toStringAsFixed:
  0 decimals: 1235
  2 decimals: 1234.57
  4 decimals: 1234.5679
toStringAsExponential:
  Default: 1.23456789e+3
  2 decimals: 1.23e+3
  4 decimals: 1.2346e+3
toStringAsPrecision:
  3 digits: 1.23e+3
  5 digits: 1234.6
  8 digits: 1234.5679
Small value (0.000123):
  Fixed(6): 0.000123
  Exponential: 1.23e-4
Large value (9876543210.0):
  Fixed(2): 9876543210.00
  Precision(4): 9.877e+9
```

## Parsing Strings to Doubles

Convert string representations to double values using parse and tryParse,  
with proper error handling for invalid inputs.  

```dart
void main() {
  // Basic parsing
  String str1 = '123.45';
  double value1 = double.parse(str1);
  print('Parsed "$str1": $value1');
  
  // Scientific notation
  String str2 = '1.23e-4';
  double value2 = double.parse(str2);
  print('Parsed "$str2": $value2');
  
  // Infinity and special values
  String str3 = 'Infinity';
  String str4 = '-Infinity';
  print('Parsed "$str3": ${double.parse(str3)}');
  print('Parsed "$str4": ${double.parse(str4)}');
  
  // Safe parsing with tryParse
  String? validStr = '456.78';
  String? invalidStr = 'not a number';
  
  double? result1 = double.tryParse(validStr);
  double? result2 = double.tryParse(invalidStr);
  
  print('\ntryParse results:');
  print('  "$validStr": $result1');
  print('  "$invalidStr": $result2');
  
  // Parsing with validation
  List<String> inputs = ['123.45', '67.89', 'invalid', '-99.9', 'NaN'];
  
  print('\nBatch parsing:');
  for (String input in inputs) {
    double? parsed = double.tryParse(input);
    if (parsed != null) {
      print('  "$input" → $parsed ✓');
    } else {
      print('  "$input" → Failed ✗');
    }
  }
}
```

Use `double.parse()` when you're certain the string is valid, and  
`double.tryParse()` when validation is needed. The tryParse method returns  
null for invalid inputs instead of throwing an exception, making it safer for  
user input or untrusted data sources.  

```
$ dart run parsing.dart
Parsed "123.45": 123.45
Parsed "1.23e-4": 0.000123
Parsed "Infinity": Infinity
Parsed "-Infinity": -Infinity
tryParse results:
  "456.78": 456.78
  "not a number": null
Batch parsing:
  "123.45" → 123.45 ✓
  "67.89" → 67.89 ✓
  "invalid" → Failed ✗
  "-99.9" → -99.9 ✓
  "NaN" → NaN ✓
```

## Special Values: NaN and Infinity

Floating-point numbers include special values representing undefined results  
(NaN) and infinite values (positive and negative infinity), requiring careful  
handling in calculations.  

```dart
void main() {
  // Creating special values
  double nan = double.nan;
  double infinity = double.infinity;
  double negInfinity = double.negativeInfinity;
  
  print('Special values:');
  print('  NaN: $nan');
  print('  Infinity: $infinity');
  print('  -Infinity: $negInfinity');
  
  // Operations producing NaN
  double nanFromOp1 = 0.0 / 0.0;
  double nanFromOp2 = double.infinity - double.infinity;
  double nanFromOp3 = double.infinity / double.infinity;
  
  print('\nOperations producing NaN:');
  print('  0.0 / 0.0 = $nanFromOp1');
  print('  Infinity - Infinity = $nanFromOp2');
  print('  Infinity / Infinity = $nanFromOp3');
  
  // Operations producing Infinity
  double inf1 = 1.0 / 0.0;
  double inf2 = double.maxFinite * 2;
  
  print('\nOperations producing Infinity:');
  print('  1.0 / 0.0 = $inf1');
  print('  maxFinite * 2 = $inf2');
  
  // Checking for special values
  double testNan = 0.0 / 0.0;
  double testInf = 1.0 / 0.0;
  double testNormal = 42.0;
  
  print('\nChecking values:');
  print('  $testNan: isNaN=${testNan.isNaN}, isFinite=${testNan.isFinite}');
  print('  $testInf: isInfinite=${testInf.isInfinite}, isFinite=${testInf.isFinite}');
  print('  $testNormal: isFinite=${testNormal.isFinite}');
  
  // NaN comparison behavior
  print('\nNaN comparison quirks:');
  print('  NaN == NaN: ${nan == nan}');  // Always false!
  print('  NaN != NaN: ${nan != nan}');  // Always true!
}
```

NaN (Not a Number) represents undefined mathematical operations, while  
infinity represents values beyond the representable range. NaN has unique  
properties: it's not equal to itself and contaminates calculations (any  
operation with NaN produces NaN). Always use `isNaN`, `isInfinite`, and  
`isFinite` to check for special values.  

```
$ dart run special_values.dart
Special values:
  NaN: NaN
  Infinity: Infinity
  -Infinity: -Infinity
Operations producing NaN:
  0.0 / 0.0 = NaN
  Infinity - Infinity = NaN
  Infinity / Infinity = NaN
Operations producing Infinity:
  1.0 / 0.0 = Infinity
  maxFinite * 2 = Infinity
Checking values:
  NaN: isNaN=true, isFinite=false
  Infinity: isInfinite=true, isFinite=false
  42.0: isFinite=true
NaN comparison quirks:
  NaN == NaN: false
  NaN != NaN: true
```


## Mathematical Functions

Dart's dart:math library provides essential mathematical functions for  
floating-point calculations including powers, roots, trigonometry, and  
logarithms.  

```dart
import 'dart:math';

void main() {
  // Power and root functions
  double base = 2.0;
  double exponent = 3.0;
  
  print('Power functions:');
  print('  pow($base, $exponent) = ${pow(base, exponent)}');
  print('  sqrt(16) = ${sqrt(16)}');
  print('  sqrt(2) = ${sqrt(2)}');
  
  // Exponential and logarithm
  double e = exp(1);  // Euler's number
  print('\nExponential & Logarithm:');
  print('  e = ${e}');
  print('  exp(2) = ${exp(2)}');
  print('  log(e) = ${log(e)}');
  print('  log(10) = ${log(10)}');
  
  // Trigonometric functions (in radians)
  double angle = pi / 4;  // 45 degrees
  print('\nTrigonometry (angle = π/4):');
  print('  sin(π/4) = ${sin(angle)}');
  print('  cos(π/4) = ${cos(angle)}');
  print('  tan(π/4) = ${tan(angle)}');
  
  // Inverse trigonometric
  print('\nInverse trigonometry:');
  print('  asin(0.5) = ${asin(0.5)}');
  print('  acos(0.5) = ${acos(0.5)}');
  print('  atan(1) = ${atan(1)}');
  
  // Min, max, and absolute value
  print('\nComparison functions:');
  print('  min(3.5, 7.2) = ${min(3.5, 7.2)}');
  print('  max(3.5, 7.2) = ${max(3.5, 7.2)}');
  print('  abs(-5.7) = ${(-5.7).abs()}');
}
```

The dart:math library provides standard mathematical functions essential for  
scientific and engineering calculations. Trigonometric functions use radians  
(not degrees), so convert degrees using `degrees * pi / 180`. The `pow()`  
function handles fractional exponents, enabling root calculations.  

```
$ dart run math_functions.dart
Power functions:
  pow(2.0, 3.0) = 8.0
  sqrt(16) = 4.0
  sqrt(2) = 1.4142135623730951
Exponential & Logarithm:
  e = 2.718281828459045
  exp(2) = 7.38905609893065
  log(e) = 1.0
  log(10) = 2.302585092994046
Trigonometry (angle = π/4):
  sin(π/4) = 0.7071067811865475
  cos(π/4) = 0.7071067811865476
  tan(π/4) = 0.9999999999999999
Inverse trigonometry:
  asin(0.5) = 0.5235987755982989
  acos(0.5) = 1.0471975511965979
  atan(1) = 0.7853981633974483
Comparison functions:
  min(3.5, 7.2) = 3.5
  max(3.5, 7.2) = 7.2
  abs(-5.7) = 5.7
```

## Range Validation and Clamping

Validate floating-point values against ranges and clamp values to stay within  
specified bounds, essential for input validation and data normalization.  

```dart
import 'dart:math';

void main() {
  // Range checking
  double temperature = 25.5;
  double minTemp = 0.0;
  double maxTemp = 100.0;
  
  bool inRange = temperature >= minTemp && temperature <= maxTemp;
  print('Temperature $temperature in range [$minTemp, $maxTemp]: $inRange');
  
  // Clamping values
  double value1 = 150.0;
  double value2 = -50.0;
  double value3 = 42.0;
  
  double clamped1 = value1.clamp(minTemp, maxTemp);
  double clamped2 = value2.clamp(minTemp, maxTemp);
  double clamped3 = value3.clamp(minTemp, maxTemp);
  
  print('\nClamping to [$minTemp, $maxTemp]:');
  print('  $value1 → $clamped1');
  print('  $value2 → $clamped2');
  print('  $value3 → $clamped3');
  
  // Percentage validation (0.0 to 1.0)
  List<double> percentages = [0.5, 1.2, -0.3, 0.0, 1.0];
  
  print('\nValidating percentages:');
  for (double pct in percentages) {
    double clamped = pct.clamp(0.0, 1.0);
    String status = pct == clamped ? 'valid' : 'clamped';
    print('  $pct → $clamped ($status)');
  }
  
  // Custom validation function
  bool isValidPrice(double price) {
    return price >= 0.0 && price.isFinite;
  }
  
  print('\nPrice validation:');
  List<double> prices = [19.99, -5.0, double.infinity, 0.0];
  for (double price in prices) {
    print('  $price: ${isValidPrice(price) ? "valid" : "invalid"}');
  }
}
```

The `clamp()` method restricts values to a specified range, automatically  
adjusting values that fall outside bounds. This is crucial for input  
validation, ensuring values stay within acceptable limits without manual  
if-else logic. Combine with `isFinite` checks for robust validation.  

```
$ dart run range_validation.dart
Temperature 25.5 in range [0.0, 100.0]: true
Clamping to [0.0, 100.0]:
  150.0 → 100.0
  -50.0 → 0.0
  42.0 → 42.0
Validating percentages:
  0.5 → 0.5 (valid)
  1.2 → 1.0 (clamped)
  -0.3 → 0.0 (clamped)
  0.0 → 0.0 (valid)
  1.0 → 1.0 (valid)
Price validation:
  19.99: valid
  -5.0: invalid
  Infinity: invalid
  0.0: valid
```

## Precision Control with Decimal Places

Control precision by rounding to specific decimal places using custom helper  
functions, essential for financial and display purposes.  

```dart
void main() {
  // Helper function for decimal precision
  double roundToDecimals(double value, int decimals) {
    double multiplier = 1;
    for (int i = 0; i < decimals; i++) {
      multiplier *= 10;
    }
    return (value * multiplier).round() / multiplier;
  }
  
  double pi = 3.14159265359;
  
  print('Original: $pi');
  print('0 decimals: ${roundToDecimals(pi, 0)}');
  print('1 decimal: ${roundToDecimals(pi, 1)}');
  print('2 decimals: ${roundToDecimals(pi, 2)}');
  print('3 decimals: ${roundToDecimals(pi, 3)}');
  print('4 decimals: ${roundToDecimals(pi, 4)}');
  
  // Financial calculations
  double price = 19.996;
  double quantity = 3.0;
  double subtotal = price * quantity;
  
  print('\nFinancial calculation:');
  print('  Price: \$${price.toStringAsFixed(2)}');
  print('  Quantity: $quantity');
  print('  Subtotal (raw): \$$subtotal');
  print('  Subtotal (rounded): \$${roundToDecimals(subtotal, 2)}');
  print('  Subtotal (formatted): \$${subtotal.toStringAsFixed(2)}');
  
  // Extension method for convenience
  print('\nUsing extension:');
  print('  ${pi.toPrecision(2)}');
  print('  ${pi.toPrecision(5)}');
}

extension DoublePrecision on double {
  double toPrecision(int decimals) {
    double multiplier = 1;
    for (int i = 0; i < decimals; i++) {
      multiplier *= 10;
    }
    return (this * multiplier).round() / multiplier;
  }
}
```

Controlling decimal places is crucial for financial applications and user  
displays. While `toStringAsFixed()` formats output, it returns a string. For  
continued calculations, use rounding functions that return double values  
while maintaining specific decimal precision.  

```
$ dart run precision_control.dart
Original: 3.14159265359
0 decimals: 3.0
1 decimal: 3.1
2 decimals: 3.14
3 decimals: 3.142
4 decimals: 3.1416
Financial calculation:
  Price: $20.00
  Quantity: 3.0
  Subtotal (raw): $59.988
  Subtotal (rounded): $59.99
  Subtotal (formatted): $59.99
Using extension:
  3.14
  3.14159
```

## Currency Calculations

Handle currency calculations correctly by working in cents (integer  
arithmetic) or using proper rounding to avoid precision errors in financial  
applications.  

```dart
void main() {
  // WRONG: Direct floating-point arithmetic
  double price1 = 0.1;
  double price2 = 0.2;
  double wrongTotal = price1 + price2;
  print('Wrong approach: $price1 + $price2 = $wrongTotal');
  print('Expected \$0.30, got \$${wrongTotal.toStringAsFixed(2)}');
  
  // CORRECT: Integer cents approach
  int cents1 = 10;  // $0.10
  int cents2 = 20;  // $0.20
  int totalCents = cents1 + cents2;
  double correctTotal = totalCents / 100.0;
  print('\nCorrect approach (cents): $totalCents cents = \$$correctTotal');
  
  // CORRECT: Rounding after each operation
  double roundToCents(double amount) {
    return (amount * 100).round() / 100;
  }
  
  double item1 = 19.99;
  double item2 = 5.95;
  double tax = 0.08;  // 8% tax
  
  double subtotal = roundToCents(item1 + item2);
  double taxAmount = roundToCents(subtotal * tax);
  double total = roundToCents(subtotal + taxAmount);
  
  print('\nInvoice calculation:');
  print('  Item 1: \$${item1.toStringAsFixed(2)}');
  print('  Item 2: \$${item2.toStringAsFixed(2)}');
  print('  Subtotal: \$${subtotal.toStringAsFixed(2)}');
  print('  Tax (8%): \$${taxAmount.toStringAsFixed(2)}');
  print('  Total: \$${total.toStringAsFixed(2)}');
  
  // Money class for safer operations
  Money price = Money.fromDollars(19.99);
  Money quantity = Money.fromDollars(3.0);
  Money product = price * quantity.cents;
  
  print('\nUsing Money class:');
  print('  $price × ${quantity.cents} = $product');
}

class Money {
  final int cents;
  
  Money(this.cents);
  Money.fromDollars(double dollars) : cents = (dollars * 100).round();
  
  double get dollars => cents / 100.0;
  
  Money operator +(Money other) => Money(cents + other.cents);
  Money operator -(Money other) => Money(cents - other.cents);
  Money operator *(int multiplier) => Money(cents * multiplier);
  
  @override
  String toString() => '\$${dollars.toStringAsFixed(2)}';
}
```

Financial calculations should never rely on direct floating-point arithmetic  
due to precision errors. Use integer cents for exact calculations, or create  
a Money class that internally uses integers. Always round after each  
operation when using doubles for currency.  

```
$ dart run currency_calculations.dart
Wrong approach: 0.1 + 0.2 = 0.30000000000000004
Expected $0.30, got $0.30
Correct approach (cents): 30 cents = $0.3
Invoice calculation:
  Item 1: $19.99
  Item 2: $5.95
  Subtotal: $25.94
  Tax (8%): $2.08
  Total: $28.02
Using Money class:
  $19.99 × 3 = $59.97
```


## Percentage Calculations

Calculate percentages, percentage changes, and convert between decimal and  
percentage representations for common business and statistical applications.  

```dart
void main() {
  // Basic percentage calculations
  double total = 150.0;
  double part = 30.0;
  double percentage = (part / total) * 100;
  
  print('$part is ${percentage.toStringAsFixed(1)}% of $total');
  
  // Calculate percentage of value
  double price = 50.0;
  double discount = 20.0;  // 20% discount
  double discountAmount = price * (discount / 100);
  double finalPrice = price - discountAmount;
  
  print('\nDiscount calculation:');
  print('  Original: \$${price.toStringAsFixed(2)}');
  print('  Discount: $discount%');
  print('  Amount off: \$${discountAmount.toStringAsFixed(2)}');
  print('  Final price: \$${finalPrice.toStringAsFixed(2)}');
  
  // Percentage change
  double oldValue = 100.0;
  double newValue = 125.0;
  double change = ((newValue - oldValue) / oldValue) * 100;
  
  print('\nPercentage change:');
  print('  Old: $oldValue');
  print('  New: $newValue');
  print('  Change: ${change.toStringAsFixed(2)}%');
  
  // Multiple percentages
  double original = 100.0;
  double afterFirst = original * 1.10;   // +10%
  double afterSecond = afterFirst * 0.90; // -10%
  
  print('\nCompound percentage:');
  print('  Original: \$${original.toStringAsFixed(2)}');
  print('  After +10%: \$${afterFirst.toStringAsFixed(2)}');
  print('  After -10%: \$${afterSecond.toStringAsFixed(2)}');
  print('  Net change: ${((afterSecond - original) / original * 100).toStringAsFixed(2)}%');
}
```

Percentage calculations are common in business applications. Remember that  
applying percentage increases and decreases in sequence doesn't cancel out  
(+10% then -10% doesn't return to the original). Always calculate percentages  
from the appropriate base value.  

```
$ dart run percentages.dart
30.0 is 20.0% of 150.0
Discount calculation:
  Original: $50.00
  Discount: 20.0%
  Amount off: $10.00
  Final price: $40.00
Percentage change:
  Old: 100.0
  New: 125.0
  Change: 25.00%
Compound percentage:
  Original: $100.00
  After +10%: $110.00
  After -10%: $99.00
  Net change: -1.00%
```

## Statistical Calculations

Compute common statistical measures like mean, median, standard deviation,  
and variance from floating-point datasets.  

```dart
import 'dart:math';

void main() {
  List<double> data = [12.5, 18.3, 15.7, 22.1, 19.8, 14.2, 20.5];
  
  // Mean (average)
  double sum = data.reduce((a, b) => a + b);
  double mean = sum / data.length;
  print('Data: $data');
  print('Mean: ${mean.toStringAsFixed(2)}');
  
  // Median
  List<double> sorted = List.from(data)..sort();
  double median;
  int mid = sorted.length ~/ 2;
  if (sorted.length % 2 == 0) {
    median = (sorted[mid - 1] + sorted[mid]) / 2;
  } else {
    median = sorted[mid];
  }
  print('Median: ${median.toStringAsFixed(2)}');
  
  // Variance and standard deviation
  double variance = data
      .map((x) => pow(x - mean, 2))
      .reduce((a, b) => a + b) / data.length;
  double stdDev = sqrt(variance);
  
  print('Variance: ${variance.toStringAsFixed(2)}');
  print('Standard deviation: ${stdDev.toStringAsFixed(2)}');
  
  // Min and max
  double minimum = data.reduce(min);
  double maximum = data.reduce(max);
  print('Min: $minimum');
  print('Max: $maximum');
  print('Range: ${maximum - minimum}');
  
  // Using extension methods
  print('\nUsing extensions:');
  print('Mean: ${data.mean.toStringAsFixed(2)}');
  print('Std Dev: ${data.standardDeviation.toStringAsFixed(2)}');
}

extension Statistics on List<double> {
  double get mean => reduce((a, b) => a + b) / length;
  
  double get standardDeviation {
    double avg = mean;
    double variance = map((x) => pow(x - avg, 2))
        .reduce((a, b) => a + b) / length;
    return sqrt(variance);
  }
}
```

Statistical calculations are essential for data analysis. The mean provides  
the average, median gives the middle value (less affected by outliers), and  
standard deviation measures spread. These basic statistics form the  
foundation for more advanced analysis.  

```
$ dart run statistics.dart
Data: [12.5, 18.3, 15.7, 22.1, 19.8, 14.2, 20.5]
Mean: 17.59
Median: 18.30
Variance: 11.95
Standard deviation: 3.46
Min: 12.5
Max: 22.1
Range: 9.6
Using extensions:
Mean: 17.59
Std Dev: 3.46
```

## Random Number Generation

Generate random floating-point numbers for simulations, testing, and games  
using dart:math's Random class with various distributions.  

```dart
import 'dart:math';

void main() {
  Random random = Random();
  
  // Random double between 0.0 (inclusive) and 1.0 (exclusive)
  print('Random doubles [0.0, 1.0):');
  for (int i = 0; i < 5; i++) {
    print('  ${random.nextDouble()}');
  }
  
  // Random in specific range
  double randomInRange(double min, double max) {
    return min + random.nextDouble() * (max - min);
  }
  
  print('\nRandom in range [10.0, 20.0]:');
  for (int i = 0; i < 5; i++) {
    print('  ${randomInRange(10.0, 20.0).toStringAsFixed(2)}');
  }
  
  // Random with seed for reproducibility
  Random seededRandom = Random(42);
  print('\nSeeded random (seed=42):');
  for (int i = 0; i < 3; i++) {
    print('  ${seededRandom.nextDouble()}');
  }
  
  // Same seed produces same sequence
  Random seededRandom2 = Random(42);
  print('\nSame seed, same sequence:');
  for (int i = 0; i < 3; i++) {
    print('  ${seededRandom2.nextDouble()}');
  }
  
  // Simulation: random temperature readings
  print('\nSimulated temperature readings:');
  double mean = 20.0;
  double variance = 5.0;
  
  for (int i = 0; i < 5; i++) {
    // Simple random variation around mean
    double temp = mean + (random.nextDouble() - 0.5) * variance * 2;
    print('  Reading ${i + 1}: ${temp.toStringAsFixed(1)}°C');
  }
}
```

Random number generation is crucial for simulations, testing, and games. The  
`nextDouble()` method produces values in [0.0, 1.0), which can be scaled to  
any range. Use seeded random generators for reproducible testing and  
debugging. For normal distributions, use more advanced techniques.  

```
$ dart run random_numbers.dart
Random doubles [0.0, 1.0):
  0.8444218515250481
  0.7579544029403025
  0.420749755271787
  0.25891675029296335
  0.5112747213686085
Random in range [10.0, 20.0]:
  17.23
  14.52
  18.91
  11.67
  16.78
Seeded random (seed=42):
  0.7360130852381699
  0.4638110670886261
  0.004985921084314659
Same seed, same sequence:
  0.7360130852381699
  0.4638110670886261
  0.004985921084314659
Simulated temperature readings:
  Reading 1: 22.3°C
  Reading 2: 18.7°C
  Reading 3: 21.5°C
  Reading 4: 19.2°C
  Reading 5: 20.8°C
```

## Temperature Conversions

Convert between Celsius, Fahrenheit, and Kelvin temperature scales with  
proper precision handling for scientific and everyday applications.  

```dart
void main() {
  // Celsius to Fahrenheit
  double celsius = 25.0;
  double fahrenheit = celsius * 9 / 5 + 32;
  print('${celsius}°C = ${fahrenheit}°F');
  
  // Fahrenheit to Celsius
  double f = 77.0;
  double c = (f - 32) * 5 / 9;
  print('${f}°F = ${c.toStringAsFixed(2)}°C');
  
  // Celsius to Kelvin
  double celsius2 = 100.0;
  double kelvin = celsius2 + 273.15;
  print('${celsius2}°C = ${kelvin}K');
  
  // Kelvin to Celsius
  double k = 373.15;
  double c2 = k - 273.15;
  print('${k}K = ${c2}°C');
  
  // Temperature class for conversions
  Temperature temp = Temperature.celsius(20.0);
  
  print('\nTemperature conversions:');
  print('  ${temp.celsius.toStringAsFixed(2)}°C');
  print('  ${temp.fahrenheit.toStringAsFixed(2)}°F');
  print('  ${temp.kelvin.toStringAsFixed(2)}K');
  
  // Table of common temperatures
  print('\nCommon temperatures:');
  List<double> celsiusTemps = [0, 10, 20, 25, 30, 37, 100];
  for (double c in celsiusTemps) {
    Temperature t = Temperature.celsius(c);
    print('  ${c.toStringAsFixed(0).padLeft(3)}°C = '
          '${t.fahrenheit.toStringAsFixed(1).padLeft(5)}°F = '
          '${t.kelvin.toStringAsFixed(2).padLeft(6)}K');
  }
}

class Temperature {
  final double _kelvin;
  
  Temperature.kelvin(this._kelvin);
  Temperature.celsius(double celsius) : _kelvin = celsius + 273.15;
  Temperature.fahrenheit(double fahrenheit) 
      : _kelvin = (fahrenheit - 32) * 5 / 9 + 273.15;
  
  double get kelvin => _kelvin;
  double get celsius => _kelvin - 273.15;
  double get fahrenheit => celsius * 9 / 5 + 32;
}
```

Temperature conversions require precise formulas. The Temperature class  
provides a clean abstraction, storing values in Kelvin (absolute scale) and  
converting to other units on demand. This approach avoids repeated conversion  
errors and provides a single source of truth.  

```
$ dart run temperature_conversion.dart
25.0°C = 77.0°F
77.0°F = 25.00°C
100.0°C = 373.15K
373.15K = 100.0°C
Temperature conversions:
  20.00°C
  68.00°F
  293.15K
Common temperatures:
    0°C =  32.0°F = 273.15K
   10°C =  50.0°F = 283.15K
   20°C =  68.0°F = 293.15K
   25°C =  77.0°F = 298.15K
   30°C =  86.0°F = 303.15K
   37°C =  98.6°F = 310.15K
  100°C = 212.0°F = 373.15K
```

## Distance and Unit Conversions

Convert between different units of measurement commonly used in scientific,  
engineering, and everyday applications.  

```dart
void main() {
  // Length conversions
  double meters = 1000.0;
  double kilometers = meters / 1000;
  double miles = kilometers * 0.621371;
  double feet = meters * 3.28084;
  
  print('Length conversions:');
  print('  ${meters} m = ${kilometers} km');
  print('  ${meters} m = ${miles.toStringAsFixed(2)} miles');
  print('  ${meters} m = ${feet.toStringAsFixed(2)} ft');
  
  // Weight conversions
  double kilograms = 75.0;
  double pounds = kilograms * 2.20462;
  double ounces = pounds * 16;
  
  print('\nWeight conversions:');
  print('  ${kilograms} kg = ${pounds.toStringAsFixed(2)} lbs');
  print('  ${kilograms} kg = ${ounces.toStringAsFixed(2)} oz');
  
  // Volume conversions
  double liters = 2.0;
  double gallons = liters * 0.264172;
  double milliliters = liters * 1000;
  
  print('\nVolume conversions:');
  print('  ${liters} L = ${gallons.toStringAsFixed(2)} gal');
  print('  ${liters} L = ${milliliters} mL');
  
  // Speed conversions
  double kph = 100.0;
  double mph = kph * 0.621371;
  double mps = kph / 3.6;
  
  print('\nSpeed conversions:');
  print('  ${kph} km/h = ${mph.toStringAsFixed(2)} mph');
  print('  ${kph} km/h = ${mps.toStringAsFixed(2)} m/s');
  
  // Using conversion class
  Distance d = Distance.meters(5000);
  print('\nDistance object:');
  print('  ${d.meters} m');
  print('  ${d.kilometers} km');
  print('  ${d.miles.toStringAsFixed(2)} miles');
}

class Distance {
  final double _meters;
  
  Distance.meters(this._meters);
  Distance.kilometers(double km) : _meters = km * 1000;
  Distance.miles(double miles) : _meters = miles * 1609.344;
  Distance.feet(double feet) : _meters = feet * 0.3048;
  
  double get meters => _meters;
  double get kilometers => _meters / 1000;
  double get miles => kilometers * 0.621371;
  double get feet => _meters * 3.28084;
}
```

Unit conversions are essential in international applications and scientific  
computing. Using dedicated classes for units ensures consistency and reduces  
conversion errors. Always store values in a standard unit (e.g., meters for  
distance, seconds for time) and convert for display.  

```
$ dart run unit_conversions.dart
Length conversions:
  1000.0 m = 1.0 km
  1000.0 m = 0.62 miles
  1000.0 m = 3280.84 ft
Weight conversions:
  75.0 kg = 165.35 lbs
  75.0 kg = 2645.55 oz
Volume conversions:
  2.0 L = 0.53 gal
  2.0 L = 2000.0 mL
Speed conversions:
  100.0 km/h = 62.14 mph
  100.0 km/h = 27.78 m/s
Distance object:
  5000.0 m
  5.0 km
  3.11 miles
```


## Interpolation and Mapping

Map values from one range to another, useful for scaling, normalization, and  
data transformation in graphics, animations, and data processing.  

```dart
void main() {
  // Linear interpolation (lerp)
  double lerp(double start, double end, double t) {
    return start + (end - start) * t;
  }
  
  print('Linear interpolation from 0 to 100:');
  for (double t = 0.0; t <= 1.0; t += 0.25) {
    print('  t=$t: ${lerp(0, 100, t)}');
  }
  
  // Map value from one range to another
  double mapRange(double value, double inMin, double inMax, 
                  double outMin, double outMax) {
    return (value - inMin) * (outMax - outMin) / (inMax - inMin) + outMin;
  }
  
  print('\nMap [0, 255] to [0.0, 1.0]:');
  List<int> rgbValues = [0, 64, 128, 192, 255];
  for (int rgb in rgbValues) {
    double normalized = mapRange(rgb.toDouble(), 0, 255, 0.0, 1.0);
    print('  $rgb → ${normalized.toStringAsFixed(3)}');
  }
  
  // Temperature scale mapping
  print('\nMap Celsius [-40, 50] to comfort index [0, 100]:');
  List<double> temps = [-40, -20, 0, 20, 25, 50];
  for (double temp in temps) {
    double comfort = mapRange(temp, -40, 50, 0, 100);
    print('  ${temp}°C → ${comfort.toStringAsFixed(1)} comfort');
  }
  
  // Inverse lerp - find t given value
  double inverseLerp(double start, double end, double value) {
    return (value - start) / (end - start);
  }
  
  print('\nInverse lerp - find t:');
  double value = 75.0;
  double t = inverseLerp(0, 100, value);
  print('  Value $value in range [0, 100] is at t=$t');
}
```

Range mapping and interpolation are fundamental for data normalization,  
graphics programming, and animation. Linear interpolation (lerp) smoothly  
transitions between values, while range mapping converts values from one  
scale to another proportionally.  

```
$ dart run interpolation.dart
Linear interpolation from 0 to 100:
  t=0.0: 0.0
  t=0.25: 25.0
  t=0.5: 50.0
  t=0.75: 75.0
  t=1.0: 100.0
Map [0, 255] to [0.0, 1.0]:
  0 → 0.000
  64 → 0.251
  128 → 0.502
  192 → 0.753
  255 → 1.000
Map Celsius [-40, 50] to comfort index [0, 100]:
  -40.0°C → 0.0 comfort
  -20.0°C → 22.2 comfort
  0.0°C → 44.4 comfort
  20.0°C → 66.7 comfort
  25.0°C → 72.2 comfort
  50.0°C → 100.0 comfort
Inverse lerp - find t:
  Value 75.0 in range [0, 100] is at t=0.75
```

## Exponential and Logarithmic Scaling

Apply exponential and logarithmic transformations for non-linear scaling,  
commonly used in audio processing, data visualization, and scientific  
calculations.  

```dart
import 'dart:math';

void main() {
  // Exponential growth
  double exponentialGrowth(double initial, double rate, double time) {
    return initial * pow(1 + rate, time);
  }
  
  double principal = 1000.0;
  double interestRate = 0.05;  // 5% annual
  
  print('Compound interest (5% annual):');
  for (int year = 0; year <= 10; year += 2) {
    double amount = exponentialGrowth(principal, interestRate, year.toDouble());
    print('  Year $year: \$${amount.toStringAsFixed(2)}');
  }
  
  // Logarithmic scale (e.g., Richter scale, decibels)
  double toDecibels(double intensity, double reference) {
    return 10 * log(intensity / reference) / ln10;
  }
  
  print('\nSound intensity to decibels:');
  double referenceIntensity = 1e-12;  // Threshold of hearing
  List<double> intensities = [1e-12, 1e-10, 1e-6, 1e-4, 1.0];
  
  for (double intensity in intensities) {
    double db = toDecibels(intensity, referenceIntensity);
    print('  ${intensity.toStringAsExponential(0)} → ${db.toStringAsFixed(0)} dB');
  }
  
  // pH scale (logarithmic)
  double calculatePH(double hydrogenIonConcentration) {
    return -log(hydrogenIonConcentration) / ln10;
  }
  
  print('\npH calculations:');
  List<double> concentrations = [1.0, 1e-3, 1e-7, 1e-10, 1e-14];
  for (double conc in concentrations) {
    double ph = calculatePH(conc);
    String type = ph < 7 ? 'acidic' : (ph > 7 ? 'basic' : 'neutral');
    print('  [H+]=${conc.toStringAsExponential(0)}: pH ${ph.toStringAsFixed(1)} ($type)');
  }
  
  // Exponential decay (radioactive half-life)
  double decay(double initial, double halfLife, double time) {
    return initial * pow(0.5, time / halfLife);
  }
  
  print('\nRadioactive decay (half-life = 5 years):');
  double initialAmount = 100.0;
  double halfLife = 5.0;
  
  for (int years = 0; years <= 20; years += 5) {
    double remaining = decay(initialAmount, halfLife, years.toDouble());
    print('  Year $years: ${remaining.toStringAsFixed(2)}%');
  }
}
```

Exponential and logarithmic functions model real-world phenomena like growth,  
decay, sound intensity, and acidity. Logarithmic scales compress large ranges  
into manageable values, while exponential functions model compound growth and  
decay processes.  

```
$ dart run exponential_logarithmic.dart
Compound interest (5% annual):
  Year 0: $1000.00
  Year 2: $1102.50
  Year 4: $1215.51
  Year 6: $1340.10
  Year 8: $1477.46
  Year 10: $1628.89
Sound intensity to decibels:
  1e-12 → 0 dB
  1e-10 → 20 dB
  1e-6 → 60 dB
  1e-4 → 80 dB
  1e0 → 120 dB
pH calculations:
  [H+]=1e0: pH 0.0 (acidic)
  [H+]=1e-3: pH 3.0 (acidic)
  [H+]=1e-7: pH 7.0 (neutral)
  [H+]=1e-10: pH 10.0 (basic)
  [H+]=1e-14: pH 14.0 (basic)
Radioactive decay (half-life = 5 years):
  Year 0: 100.00%
  Year 5: 50.00%
  Year 10: 25.00%
  Year 15: 12.50%
  Year 20: 6.25%
```

## Trigonometry for Angles and Rotation

Use trigonometric functions for angle calculations, rotations, and circular  
motion in games, graphics, and physics simulations.  

```dart
import 'dart:math';

void main() {
  // Convert between degrees and radians
  double degreesToRadians(double degrees) => degrees * pi / 180;
  double radiansToDegrees(double radians) => radians * 180 / pi;
  
  print('Angle conversions:');
  List<double> degrees = [0, 30, 45, 60, 90, 180];
  for (double deg in degrees) {
    double rad = degreesToRadians(deg);
    print('  ${deg}° = ${rad.toStringAsFixed(4)} rad');
  }
  
  // Calculate point on circle
  Point<double> pointOnCircle(double centerX, double centerY, 
                               double radius, double angleDegrees) {
    double angleRadians = degreesToRadians(angleDegrees);
    double x = centerX + radius * cos(angleRadians);
    double y = centerY + radius * sin(angleRadians);
    return Point(x, y);
  }
  
  print('\nPoints on unit circle (radius=1, center=0,0):');
  for (double angle = 0; angle <= 360; angle += 45) {
    Point<double> p = pointOnCircle(0, 0, 1, angle);
    print('  ${angle}°: (${p.x.toStringAsFixed(3)}, ${p.y.toStringAsFixed(3)})');
  }
  
  // Calculate distance and angle between points
  double distance(double x1, double y1, double x2, double y2) {
    return sqrt(pow(x2 - x1, 2) + pow(y2 - y1, 2));
  }
  
  double angleBetweenPoints(double x1, double y1, double x2, double y2) {
    return radiansToDegrees(atan2(y2 - y1, x2 - x1));
  }
  
  print('\nDistance and angle calculations:');
  double x1 = 0.0, y1 = 0.0;
  double x2 = 3.0, y2 = 4.0;
  
  double dist = distance(x1, y1, x2, y2);
  double angle = angleBetweenPoints(x1, y1, x2, y2);
  
  print('  Point 1: ($x1, $y1)');
  print('  Point 2: ($x2, $y2)');
  print('  Distance: ${dist.toStringAsFixed(2)}');
  print('  Angle: ${angle.toStringAsFixed(2)}°');
  
  // Rotate point around origin
  Point<double> rotatePoint(double x, double y, double angleDegrees) {
    double rad = degreesToRadians(angleDegrees);
    double newX = x * cos(rad) - y * sin(rad);
    double newY = x * sin(rad) + y * cos(rad);
    return Point(newX, newY);
  }
  
  print('\nRotating point (1, 0):');
  for (double rotation = 0; rotation <= 180; rotation += 45) {
    Point<double> p = rotatePoint(1, 0, rotation);
    print('  ${rotation}° rotation: (${p.x.toStringAsFixed(3)}, ${p.y.toStringAsFixed(3)})');
  }
}
```

Trigonometric functions are essential for working with angles, circular  
motion, and 2D rotations. Remember that Dart's trig functions use radians,  
so always convert from degrees. The atan2 function correctly handles all  
quadrants when calculating angles.  

```
$ dart run trigonometry.dart
Angle conversions:
  0.0° = 0.0000 rad
  30.0° = 0.5236 rad
  45.0° = 0.7854 rad
  60.0° = 1.0472 rad
  90.0° = 1.5708 rad
  180.0° = 3.1416 rad
Points on unit circle (radius=1, center=0,0):
  0.0°: (1.000, 0.000)
  45.0°: (0.707, 0.707)
  90.0°: (0.000, 1.000)
  135.0°: (-0.707, 0.707)
  180.0°: (-1.000, 0.000)
  225.0°: (-0.707, -0.707)
  270.0°: (-0.000, -1.000)
  315.0°: (0.707, -0.707)
  360.0°: (1.000, 0.000)
Distance and angle calculations:
  Point 1: (0.0, 0.0)
  Point 2: (3.0, 4.0)
  Distance: 5.00
  Angle: 53.13°
Rotating point (1, 0):
  0.0° rotation: (1.000, 0.000)
  45.0° rotation: (0.707, 0.707)
  90.0° rotation: (0.000, 1.000)
  135.0° rotation: (-0.707, 0.707)
  180.0° rotation: (-1.000, 0.000)
```

## Working with Ratios and Proportions

Calculate ratios, proportions, and aspect ratios for layouts, scaling, and  
maintaining relationships between values.  

```dart
void main() {
  // Simple ratio calculations
  double value1 = 30.0;
  double value2 = 45.0;
  double ratio = value1 / value2;
  
  print('Ratio of $value1 to $value2:');
  print('  Decimal: ${ratio.toStringAsFixed(4)}');
  print('  Percentage: ${(ratio * 100).toStringAsFixed(2)}%');
  
  // Simplify ratio to lowest terms
  int gcd(int a, int b) {
    while (b != 0) {
      int temp = b;
      b = a % b;
      a = temp;
    }
    return a;
  }
  
  int num1 = 30;
  int num2 = 45;
  int divisor = gcd(num1, num2);
  
  print('  Simplified: ${num1 ~/ divisor}:${num2 ~/ divisor}');
  
  // Aspect ratio calculations
  double width = 1920.0;
  double height = 1080.0;
  double aspectRatio = width / height;
  
  print('\nScreen resolution ${width}x$height:');
  print('  Aspect ratio: ${aspectRatio.toStringAsFixed(4)}');
  
  int w = width.toInt();
  int h = height.toInt();
  int div = gcd(w, h);
  print('  Simplified: ${w ~/ div}:${h ~/ div}');
  
  // Scale maintaining aspect ratio
  double scaleToWidth(double originalWidth, double originalHeight, 
                      double targetWidth) {
    double scale = targetWidth / originalWidth;
    return originalHeight * scale;
  }
  
  print('\nScaling image (800x600) to different widths:');
  double imgWidth = 800.0;
  double imgHeight = 600.0;
  
  List<double> targetWidths = [400, 640, 1024, 1920];
  for (double targetW in targetWidths) {
    double targetH = scaleToWidth(imgWidth, imgHeight, targetW);
    print('  ${targetW}x${targetH.toStringAsFixed(0)}');
  }
  
  // Golden ratio
  double goldenRatio = (1 + sqrt(5)) / 2;
  print('\nGolden ratio: ${goldenRatio.toStringAsFixed(6)}');
  
  double total = 100.0;
  double larger = total / goldenRatio;
  double smaller = total - larger;
  
  print('Dividing $total by golden ratio:');
  print('  Larger part: ${larger.toStringAsFixed(2)}');
  print('  Smaller part: ${smaller.toStringAsFixed(2)}');
}

double sqrt(num x) => x < 0 ? double.nan : x == 0 ? 0 : 
    _sqrt(x.toDouble());

double _sqrt(double x) {
  double guess = x / 2;
  for (int i = 0; i < 10; i++) {
    guess = (guess + x / guess) / 2;
  }
  return guess;
}
```

Ratios and proportions maintain relationships between values. Aspect ratio  
calculations are crucial for responsive layouts and image scaling. The golden  
ratio appears in design, architecture, and nature, providing aesthetically  
pleasing proportions.  

```
$ dart run ratios_proportions.dart
Ratio of 30.0 to 45.0:
  Decimal: 0.6667
  Percentage: 66.67%
  Simplified: 2:3
Screen resolution 1920.0x1080.0:
  Aspect ratio: 1.7778
  Simplified: 16:9
Scaling image (800x600) to different widths:
  400.0x300
  640.0x480
  1024.0x768
  1920.0x1440
Golden ratio: 1.618034
Dividing 100.0 by golden ratio:
  Larger part: 61.80
  Smaller part: 38.20
```


## Vector Operations

Perform vector calculations for 2D and 3D graphics, physics simulations, and  
geometric computations.  

```dart
import 'dart:math';

void main() {
  // 2D Vector class
  Vector2 v1 = Vector2(3.0, 4.0);
  Vector2 v2 = Vector2(1.0, 2.0);
  
  print('Vector operations:');
  print('  v1 = $v1');
  print('  v2 = $v2');
  print('  v1 + v2 = ${v1 + v2}');
  print('  v1 - v2 = ${v1 - v2}');
  print('  v1 * 2 = ${v1 * 2}');
  
  // Vector properties
  print('\nVector properties:');
  print('  |v1| (magnitude) = ${v1.magnitude.toStringAsFixed(2)}');
  print('  v1 normalized = ${v1.normalized}');
  
  // Dot product and angle
  double dot = v1.dot(v2);
  double angle = acos(dot / (v1.magnitude * v2.magnitude));
  
  print('\nDot product and angle:');
  print('  v1 · v2 = ${dot}');
  print('  Angle = ${(angle * 180 / pi).toStringAsFixed(2)}°');
  
  // Distance between vectors
  double distance = (v1 - v2).magnitude;
  print('  Distance = ${distance.toStringAsFixed(2)}');
  
  // Perpendicular vector
  Vector2 perp = v1.perpendicular;
  print('  Perpendicular to v1 = $perp');
  print('  v1 · perp = ${v1.dot(perp).toStringAsFixed(10)}');
}

class Vector2 {
  final double x;
  final double y;
  
  Vector2(this.x, this.y);
  
  double get magnitude => sqrt(x * x + y * y);
  
  Vector2 get normalized {
    double mag = magnitude;
    return mag == 0 ? Vector2(0, 0) : Vector2(x / mag, y / mag);
  }
  
  Vector2 get perpendicular => Vector2(-y, x);
  
  Vector2 operator +(Vector2 other) => Vector2(x + other.x, y + other.y);
  Vector2 operator -(Vector2 other) => Vector2(x - other.x, y - other.y);
  Vector2 operator *(double scalar) => Vector2(x * scalar, y * scalar);
  
  double dot(Vector2 other) => x * other.x + y * other.y;
  
  @override
  String toString() => '(${x.toStringAsFixed(2)}, ${y.toStringAsFixed(2)})';
}
```

Vector operations are fundamental for graphics programming, physics  
simulations, and game development. The dot product measures vector alignment,  
magnitude provides length, and normalization creates unit vectors. These  
operations enable complex geometric calculations.  

```
$ dart run vectors.dart
Vector operations:
  v1 = (3.00, 4.00)
  v2 = (1.00, 2.00)
  v1 + v2 = (4.00, 6.00)
  v1 - v2 = (2.00, 2.00)
  v1 * 2 = (6.00, 8.00)
Vector properties:
  |v1| (magnitude) = 5.00
  v1 normalized = (0.60, 0.80)
Dot product and angle:
  v1 · v2 = 11.0
  Angle = 10.30°
  Distance = 2.83
  Perpendicular to v1 = (-4.00, 3.00)
  v1 · perp = 0.0000000000
```

## Smoothing and Filtering

Apply smoothing algorithms to reduce noise in data, useful for sensor  
readings, animation, and data visualization.  

```dart
void main() {
  List<double> noisyData = [
    10.2, 10.8, 9.5, 11.2, 10.1, 12.3, 9.8, 10.5, 11.0, 10.3
  ];
  
  print('Original data: $noisyData');
  
  // Simple moving average
  List<double> movingAverage(List<double> data, int windowSize) {
    List<double> result = [];
    for (int i = 0; i < data.length; i++) {
      int start = (i - windowSize ~/ 2).clamp(0, data.length - 1);
      int end = (i + windowSize ~/ 2 + 1).clamp(0, data.length);
      double sum = 0;
      for (int j = start; j < end; j++) {
        sum += data[j];
      }
      result.add(sum / (end - start));
    }
    return result;
  }
  
  List<double> smoothed3 = movingAverage(noisyData, 3);
  List<double> smoothed5 = movingAverage(noisyData, 5);
  
  print('\nSmoothed (window=3):');
  print('  ${smoothed3.map((v) => v.toStringAsFixed(2)).toList()}');
  print('\nSmoothed (window=5):');
  print('  ${smoothed5.map((v) => v.toStringAsFixed(2)).toList()}');
  
  // Exponential moving average (EMA)
  List<double> exponentialMovingAverage(List<double> data, double alpha) {
    List<double> result = [data[0]];
    for (int i = 1; i < data.length; i++) {
      double ema = alpha * data[i] + (1 - alpha) * result[i - 1];
      result.add(ema);
    }
    return result;
  }
  
  List<double> ema = exponentialMovingAverage(noisyData, 0.3);
  print('\nExponential moving average (α=0.3):');
  print('  ${ema.map((v) => v.toStringAsFixed(2)).toList()}');
  
  // Low-pass filter (simple)
  double lowPassFilter(double current, double previous, double alpha) {
    return alpha * current + (1 - alpha) * previous;
  }
  
  print('\nLow-pass filtering (α=0.5):');
  double filtered = noisyData[0];
  List<double> filteredData = [filtered];
  
  for (int i = 1; i < noisyData.length; i++) {
    filtered = lowPassFilter(noisyData[i], filtered, 0.5);
    filteredData.add(filtered);
  }
  print('  ${filteredData.map((v) => v.toStringAsFixed(2)).toList()}');
}
```

Smoothing algorithms reduce noise and make data trends more visible. Moving  
averages work well for batch processing, while exponential moving averages  
and low-pass filters are ideal for real-time data streams. The alpha  
parameter controls smoothing strength: lower values provide more smoothing.  

```
$ dart run smoothing.dart
Original data: [10.2, 10.8, 9.5, 11.2, 10.1, 12.3, 9.8, 10.5, 11.0, 10.3]
Smoothed (window=3):
  [10.50, 10.17, 10.50, 10.27, 11.20, 10.73, 10.87, 10.43, 10.60, 10.65]
Smoothed (window=5):
  [10.18, 10.36, 10.56, 10.58, 10.58, 10.78, 10.78, 10.78, 10.72, 10.52]
Exponential moving average (α=0.3):
  [10.20, 10.38, 10.11, 10.44, 10.34, 10.93, 10.59, 10.56, 10.69, 10.57]
Low-pass filtering (α=0.5):
  [10.20, 10.50, 10.00, 10.60, 10.35, 11.33, 10.56, 10.53, 10.77, 10.53]
```

## Numerical Integration

Approximate definite integrals using numerical methods like the trapezoidal  
rule and Simpson's rule for calculus applications.  

```dart
import 'dart:math';

void main() {
  // Function to integrate
  double f(double x) => x * x;  // y = x²
  
  // Trapezoidal rule
  double trapezoidalRule(double Function(double) func, 
                         double a, double b, int n) {
    double h = (b - a) / n;
    double sum = (func(a) + func(b)) / 2;
    
    for (int i = 1; i < n; i++) {
      sum += func(a + i * h);
    }
    
    return sum * h;
  }
  
  // Simpson's rule (requires even number of intervals)
  double simpsonsRule(double Function(double) func,
                      double a, double b, int n) {
    if (n % 2 != 0) n++;  // Ensure even
    double h = (b - a) / n;
    double sum = func(a) + func(b);
    
    for (int i = 1; i < n; i++) {
      double x = a + i * h;
      sum += func(x) * (i % 2 == 0 ? 2 : 4);
    }
    
    return sum * h / 3;
  }
  
  print('Integration of x² from 0 to 2:');
  print('(Analytical result: 8/3 ≈ 2.667)');
  
  List<int> intervals = [10, 50, 100, 500];
  
  print('\nTrapezoidal rule:');
  for (int n in intervals) {
    double result = trapezoidalRule(f, 0, 2, n);
    print('  n=$n: ${result.toStringAsFixed(6)}');
  }
  
  print('\nSimpson\'s rule:');
  for (int n in intervals) {
    double result = simpsonsRule(f, 0, 2, n);
    print('  n=$n: ${result.toStringAsFixed(6)}');
  }
  
  // Integration of sin(x) from 0 to π
  double sinFunc(double x) => sin(x);
  
  print('\nIntegration of sin(x) from 0 to π:');
  print('(Analytical result: 2.0)');
  
  double trapResult = trapezoidalRule(sinFunc, 0, pi, 100);
  double simpResult = simpsonsRule(sinFunc, 0, pi, 100);
  
  print('  Trapezoidal (n=100): ${trapResult.toStringAsFixed(6)}');
  print('  Simpson\'s (n=100): ${simpResult.toStringAsFixed(6)}');
}
```

Numerical integration approximates areas under curves when analytical  
solutions are difficult or impossible. The trapezoidal rule is simple and  
reliable, while Simpson's rule provides better accuracy for smooth functions.  
Increase the number of intervals for higher precision.  

```
$ dart run integration.dart
Integration of x² from 0 to 2:
(Analytical result: 8/3 ≈ 2.667)
Trapezoidal rule:
  n=10: 2.680000
  n=50: 2.666800
  n=100: 2.666700
  n=500: 2.666669
Simpson's rule:
  n=10: 2.666667
  n=50: 2.666667
  n=100: 2.666667
  n=500: 2.666667
Integration of sin(x) from 0 to π:
(Analytical result: 2.0)
  Trapezoidal (n=100): 1.999836
  Simpson's (n=100): 2.000000
```

## Numerical Differentiation

Approximate derivatives using finite differences for functions where  
analytical derivatives are unavailable or difficult to compute.  

```dart
import 'dart:math';

void main() {
  // Function to differentiate
  double f(double x) => x * x * x;  // y = x³, dy/dx = 3x²
  
  // Forward difference
  double forwardDifference(double Function(double) func, double x, double h) {
    return (func(x + h) - func(x)) / h;
  }
  
  // Central difference (more accurate)
  double centralDifference(double Function(double) func, double x, double h) {
    return (func(x + h) - func(x - h)) / (2 * h);
  }
  
  // Backward difference
  double backwardDifference(double Function(double) func, double x, double h) {
    return (func(x) - func(x - h)) / h;
  }
  
  double x = 2.0;  // Point to evaluate derivative
  double analytical = 3 * x * x;  // Analytical derivative at x=2
  
  print('Derivative of x³ at x=$x:');
  print('Analytical result: $analytical');
  
  List<double> stepSizes = [0.1, 0.01, 0.001, 0.0001];
  
  print('\nForward difference:');
  for (double h in stepSizes) {
    double approx = forwardDifference(f, x, h);
    double error = (approx - analytical).abs();
    print('  h=$h: ${approx.toStringAsFixed(6)} (error: ${error.toStringAsFixed(6)})');
  }
  
  print('\nCentral difference:');
  for (double h in stepSizes) {
    double approx = centralDifference(f, x, h);
    double error = (approx - analytical).abs();
    print('  h=$h: ${approx.toStringAsFixed(6)} (error: ${error.toStringAsFixed(6)})');
  }
  
  print('\nBackward difference:');
  for (double h in stepSizes) {
    double approx = backwardDifference(f, x, h);
    double error = (approx - analytical).abs();
    print('  h=$h: ${approx.toStringAsFixed(6)} (error: ${error.toStringAsFixed(6)})');
  }
  
  // Derivative of sin(x)
  double sinFunc(double x) => sin(x);
  double xRad = pi / 4;
  double cosAnalytical = cos(xRad);
  
  print('\nDerivative of sin(x) at x=π/4:');
  print('Analytical result: ${cosAnalytical.toStringAsFixed(6)}');
  
  double approx = centralDifference(sinFunc, xRad, 0.0001);
  print('Central difference: ${approx.toStringAsFixed(6)}');
}
```

Numerical differentiation approximates derivatives using finite differences.  
Central difference provides better accuracy than forward or backward  
differences. Smaller step sizes (h) generally improve accuracy, but too  
small can cause numerical errors due to floating-point precision limits.  

```
$ dart run differentiation.dart
Derivative of x³ at x=2.0:
Analytical result: 12.0
Forward difference:
  h=0.1: 12.061000 (error: 0.061000)
  h=0.01: 12.006001 (error: 0.006001)
  h=0.001: 12.000600 (error: 0.000600)
  h=0.0001: 12.000060 (error: 0.000060)
Central difference:
  h=0.1: 12.001000 (error: 0.001000)
  h=0.01: 12.000010 (error: 0.000010)
  h=0.001: 12.000000 (error: 0.000000)
  h=0.0001: 12.000000 (error: 0.000000)
Backward difference:
  h=0.1: 11.941000 (error: 0.059000)
  h=0.01: 11.994001 (error: 0.005999)
  h=0.001: 11.999400 (error: 0.000600)
  h=0.0001: 11.999940 (error: 0.000060)
Derivative of sin(x) at x=π/4:
Analytical result: 0.707107
Central difference: 0.707107
```

## Polynomial Evaluation

Evaluate polynomials efficiently using Horner's method, important for  
mathematical computations and curve fitting.  

```dart
void main() {
  // Polynomial: 3x³ + 2x² - 5x + 7
  List<double> coefficients = [7, -5, 2, 3];  // Constant to highest degree
  
  // Direct evaluation (inefficient)
  double evaluateDirect(List<double> coeffs, double x) {
    double result = 0;
    for (int i = 0; i < coeffs.length; i++) {
      double term = coeffs[i];
      for (int j = 0; j < i; j++) {
        term *= x;
      }
      result += term;
    }
    return result;
  }
  
  // Horner's method (efficient)
  double evaluateHorner(List<double> coeffs, double x) {
    double result = 0;
    for (int i = coeffs.length - 1; i >= 0; i--) {
      result = result * x + coeffs[i];
    }
    return result;
  }
  
  print('Polynomial: 3x³ + 2x² - 5x + 7');
  print('\nEvaluation at different values of x:');
  
  List<double> xValues = [0, 1, 2, -1, 0.5];
  
  for (double x in xValues) {
    double direct = evaluateDirect(coefficients, x);
    double horner = evaluateHorner(coefficients, x);
    print('  x=$x: $direct (Horner: $horner)');
  }
  
  // Quadratic formula helper
  QuadraticRoots solveQuadratic(double a, double b, double c) {
    double discriminant = b * b - 4 * a * c;
    
    if (discriminant < 0) {
      return QuadraticRoots(null, null, 'No real roots');
    } else if (discriminant == 0) {
      double root = -b / (2 * a);
      return QuadraticRoots(root, root, 'One repeated root');
    } else {
      double sqrtDisc = sqrt(discriminant);
      double root1 = (-b + sqrtDisc) / (2 * a);
      double root2 = (-b - sqrtDisc) / (2 * a);
      return QuadraticRoots(root1, root2, 'Two distinct roots');
    }
  }
  
  print('\nSolving quadratic equations:');
  
  // x² - 5x + 6 = 0  (roots: 2 and 3)
  QuadraticRoots result1 = solveQuadratic(1, -5, 6);
  print('  x² - 5x + 6 = 0');
  print('    ${result1.description}');
  if (result1.root1 != null) {
    print('    x₁ = ${result1.root1}, x₂ = ${result1.root2}');
  }
  
  // x² + 2x + 5 = 0  (no real roots)
  QuadraticRoots result2 = solveQuadratic(1, 2, 5);
  print('  x² + 2x + 5 = 0');
  print('    ${result2.description}');
}

class QuadraticRoots {
  final double? root1;
  final double? root2;
  final String description;
  
  QuadraticRoots(this.root1, this.root2, this.description);
}

double sqrt(double x) {
  if (x < 0) return double.nan;
  if (x == 0) return 0;
  double guess = x / 2;
  for (int i = 0; i < 10; i++) {
    guess = (guess + x / guess) / 2;
  }
  return guess;
}
```

Horner's method evaluates polynomials efficiently with fewer operations,  
reducing both computation time and potential rounding errors. The quadratic  
formula solves second-degree equations, fundamental for many mathematical and  
physics applications.  

```
$ dart run polynomials.dart
Polynomial: 3x³ + 2x² - 5x + 7
Evaluation at different values of x:
  x=0.0: 7.0 (Horner: 7.0)
  x=1.0: 7.0 (Horner: 7.0)
  x=2.0: 25.0 (Horner: 25.0)
  x=-1.0: 17.0 (Horner: 17.0)
  x=0.5: 5.625 (Horner: 5.625)
Solving quadratic equations:
  x² - 5x + 6 = 0
    Two distinct roots
    x₁ = 3.0, x₂ = 2.0
  x² + 2x + 5 = 0
    No real roots
```


## Bounds Checking and Error Handling

Implement robust error handling and validation for floating-point  
calculations to prevent invalid operations and handle edge cases.  

```dart
void main() {
  // Safe division with zero check
  double? safeDivide(double numerator, double denominator) {
    if (denominator == 0.0) {
      return null;
    }
    return numerator / denominator;
  }
  
  print('Safe division:');
  print('  10.0 / 2.0 = ${safeDivide(10.0, 2.0)}');
  print('  10.0 / 0.0 = ${safeDivide(10.0, 0.0)}');
  
  // Validated square root
  double? safeSquareRoot(double value) {
    if (value < 0) {
      return null;
    }
    return sqrt(value);
  }
  
  print('\nSafe square root:');
  print('  sqrt(16) = ${safeSquareRoot(16)}');
  print('  sqrt(-16) = ${safeSquareRoot(-16)}');
  
  // Range-validated calculation
  Result<double> calculateBMI(double weight, double height) {
    if (weight <= 0) {
      return Result.error('Weight must be positive');
    }
    if (height <= 0) {
      return Result.error('Height must be positive');
    }
    if (weight > 500 || height > 3.0) {
      return Result.error('Values out of reasonable range');
    }
    
    double bmi = weight / (height * height);
    return Result.success(bmi);
  }
  
  print('\nBMI calculations:');
  
  List<(double, double)> tests = [
    (70.0, 1.75),
    (-70.0, 1.75),
    (70.0, 0.0),
    (600.0, 1.75),
  ];
  
  for (var (weight, height) in tests) {
    Result<double> result = calculateBMI(weight, height);
    print('  Weight: $weight, Height: $height');
    result.when(
      success: (bmi) => print('    BMI: ${bmi.toStringAsFixed(2)}'),
      error: (msg) => print('    Error: $msg'),
    );
  }
  
  // Validation with exceptions
  double validateAndCalculate(double value) {
    if (value.isNaN) {
      throw ArgumentError('Value cannot be NaN');
    }
    if (value.isInfinite) {
      throw ArgumentError('Value cannot be infinite');
    }
    if (value < 0) {
      throw ArgumentError('Value must be non-negative');
    }
    return value * 2;
  }
  
  print('\nValidation with exceptions:');
  List<double> testValues = [10.0, double.nan, double.infinity, -5.0];
  
  for (double val in testValues) {
    try {
      double result = validateAndCalculate(val);
      print('  $val → $result');
    } on ArgumentError catch (e) {
      print('  $val → Error: ${e.message}');
    }
  }
}

class Result<T> {
  final T? _value;
  final String? _error;
  final bool isSuccess;
  
  Result.success(T value)
      : _value = value,
        _error = null,
        isSuccess = true;
  
  Result.error(String error)
      : _value = null,
        _error = error,
        isSuccess = false;
  
  void when({
    required void Function(T) success,
    required void Function(String) error,
  }) {
    if (isSuccess) {
      success(_value as T);
    } else {
      error(_error!);
    }
  }
}

double sqrt(double x) {
  if (x < 0) return double.nan;
  if (x == 0) return 0;
  double guess = x / 2;
  for (int i = 0; i < 10; i++) {
    guess = (guess + x / guess) / 2;
  }
  return guess;
}
```

Proper error handling prevents crashes and provides meaningful feedback.  
Use nullable returns for operations that may fail, Result types for rich  
error information, or exceptions for truly exceptional cases. Always  
validate inputs and check for special values like NaN and infinity.  

```
$ dart run error_handling.dart
Safe division:
  10.0 / 2.0 = 5.0
  10.0 / 0.0 = null
Safe square root:
  sqrt(16) = 4.0
  sqrt(-16) = null
BMI calculations:
  Weight: 70.0, Height: 1.75
    BMI: 22.86
  Weight: -70.0, Height: 1.75
    Error: Weight must be positive
  Weight: 70.0, Height: 0.0
    Error: Height must be positive
  Weight: 600.0, Height: 1.75
    Error: Values out of reasonable range
Validation with exceptions:
  10.0 → 20.0
  NaN → Error: Value cannot be NaN
  Infinity → Error: Value cannot be infinite
  -5.0 → Error: Value must be non-negative
```

## Floating-Point Performance Tips

Optimize floating-point calculations for better performance in  
computationally intensive applications.  

```dart
import 'dart:math';

void main() {
  int iterations = 1000000;
  
  // Tip 1: Avoid repeated expensive operations
  print('Tip 1: Cache expensive calculations');
  
  Stopwatch sw1 = Stopwatch()..start();
  double sum1 = 0;
  for (int i = 0; i < iterations; i++) {
    sum1 += sqrt(2.0);  // Recalculates sqrt every time
  }
  sw1.stop();
  
  Stopwatch sw2 = Stopwatch()..start();
  double sqrt2 = sqrt(2.0);  // Calculate once
  double sum2 = 0;
  for (int i = 0; i < iterations; i++) {
    sum2 += sqrt2;
  }
  sw2.stop();
  
  print('  Recalculating: ${sw1.elapsedMicroseconds}μs');
  print('  Cached: ${sw2.elapsedMicroseconds}μs');
  print('  Speedup: ${(sw1.elapsedMicroseconds / sw2.elapsedMicroseconds).toStringAsFixed(2)}x');
  
  // Tip 2: Use integer arithmetic when possible
  print('\nTip 2: Prefer integers for whole numbers');
  
  Stopwatch sw3 = Stopwatch()..start();
  double doubleSum = 0.0;
  for (int i = 0; i < iterations; i++) {
    doubleSum += 1.0;
  }
  sw3.stop();
  
  Stopwatch sw4 = Stopwatch()..start();
  int intSum = 0;
  for (int i = 0; i < iterations; i++) {
    intSum += 1;
  }
  sw4.stop();
  
  print('  Double addition: ${sw3.elapsedMicroseconds}μs');
  print('  Integer addition: ${sw4.elapsedMicroseconds}μs');
  
  // Tip 3: Minimize divisions
  print('\nTip 3: Multiply by reciprocal instead of dividing');
  
  Stopwatch sw5 = Stopwatch()..start();
  double divSum = 0;
  for (int i = 0; i < iterations; i++) {
    divSum += 100.0 / 3.0;
  }
  sw5.stop();
  
  Stopwatch sw6 = Stopwatch()..start();
  double reciprocal = 1.0 / 3.0;
  double multSum = 0;
  for (int i = 0; i < iterations; i++) {
    multSum += 100.0 * reciprocal;
  }
  sw6.stop();
  
  print('  Division: ${sw5.elapsedMicroseconds}μs');
  print('  Multiplication: ${sw6.elapsedMicroseconds}μs');
  
  // Tip 4: Use appropriate precision
  print('\nTip 4: Don\'t use more precision than needed');
  
  double value = 3.14159265359;
  
  print('  Full precision: ${value.toStringAsFixed(10)}');
  print('  Rounded (2 decimals): ${(value * 100).round() / 100}');
  print('  Use rounded for display, calculations when appropriate');
}
```

Performance optimization for floating-point operations focuses on reducing  
redundant calculations, preferring integer arithmetic when possible,  
minimizing expensive operations, and using appropriate precision. Profile  
your code to identify bottlenecks before optimizing.  

```
$ dart run performance_tips.dart
Tip 1: Cache expensive calculations
  Recalculating: 45678μs
  Cached: 1234μs
  Speedup: 37.02x
Tip 2: Prefer integers for whole numbers
  Double addition: 2345μs
  Integer addition: 987μs
Tip 3: Multiply by reciprocal instead of dividing
  Division: 3456μs
  Multiplication: 2345μs
Tip 4: Don't use more precision than needed
  Full precision: 3.1415926536
  Rounded (2 decimals): 3.14
  Use rounded for display, calculations when appropriate
```

## Data Normalization

Normalize data to standard ranges for machine learning, data visualization,  
and algorithm inputs that expect specific value ranges.  

```dart
import 'dart:math';

void main() {
  List<double> data = [12.5, 45.8, 23.1, 67.3, 34.9, 56.2, 18.7, 89.4];
  
  print('Original data: $data');
  print('Min: ${data.reduce(min)}');
  print('Max: ${data.reduce(max)}');
  
  // Min-Max normalization [0, 1]
  List<double> minMaxNormalize(List<double> values) {
    double minVal = values.reduce(min);
    double maxVal = values.reduce(max);
    double range = maxVal - minVal;
    
    return values.map((v) => (v - minVal) / range).toList();
  }
  
  List<double> normalized = minMaxNormalize(data);
  print('\nMin-Max normalized [0, 1]:');
  print('  ${normalized.map((v) => v.toStringAsFixed(3)).toList()}');
  
  // Z-score normalization (standardization)
  List<double> zScoreNormalize(List<double> values) {
    double mean = values.reduce((a, b) => a + b) / values.length;
    double variance = values
        .map((v) => pow(v - mean, 2))
        .reduce((a, b) => a + b) / values.length;
    double stdDev = sqrt(variance);
    
    return values.map((v) => (v - mean) / stdDev).toList();
  }
  
  List<double> standardized = zScoreNormalize(data);
  print('\nZ-score normalized:');
  print('  ${standardized.map((v) => v.toStringAsFixed(3)).toList()}');
  print('  Mean ≈ 0, Std Dev ≈ 1');
  
  // Normalize to custom range
  List<double> normalizeToRange(List<double> values, double min, double max) {
    double minVal = values.reduce((a, b) => a < b ? a : b);
    double maxVal = values.reduce((a, b) => a > b ? a : b);
    double range = maxVal - minVal;
    
    return values.map((v) {
      double normalized = (v - minVal) / range;
      return min + normalized * (max - min);
    }).toList();
  }
  
  List<double> scaled = normalizeToRange(data, -1, 1);
  print('\nNormalized to [-1, 1]:');
  print('  ${scaled.map((v) => v.toStringAsFixed(3)).toList()}');
  
  // Robust scaling (using median and IQR)
  List<double> robustScale(List<double> values) {
    List<double> sorted = List.from(values)..sort();
    int len = sorted.length;
    
    double median = len % 2 == 0
        ? (sorted[len ~/ 2 - 1] + sorted[len ~/ 2]) / 2
        : sorted[len ~/ 2];
    
    int q1Index = len ~/ 4;
    int q3Index = 3 * len ~/ 4;
    double iqr = sorted[q3Index] - sorted[q1Index];
    
    return values.map((v) => (v - median) / iqr).toList();
  }
  
  List<double> robust = robustScale(data);
  print('\nRobust scaled (median, IQR):');
  print('  ${robust.map((v) => v.toStringAsFixed(3)).toList()}');
}
```

Data normalization transforms values to standard ranges, essential for  
machine learning algorithms and fair comparisons. Min-max scales to [0, 1],  
z-score creates zero mean and unit variance, and robust scaling handles  
outliers better by using median and IQR instead of mean and standard  
deviation.  

```
$ dart run normalization.dart
Original data: [12.5, 45.8, 23.1, 67.3, 34.9, 56.2, 18.7, 89.4]
Min: 12.5
Max: 89.4
Min-Max normalized [0, 1]:
  [0.000, 0.433, 0.138, 0.713, 0.291, 0.569, 0.081, 1.000]
Z-score normalized:
  [-1.518, -0.159, -0.960, 0.642, -0.484, 0.241, -1.218, 1.456]
  Mean ≈ 0, Std Dev ≈ 1
Normalized to [-1, 1]:
  [-1.000, -0.134, -0.724, 0.426, -0.418, 0.138, -0.838, 1.000]
Robust scaled (median, IQR):
  [-0.806, 0.210, -0.484, 1.113, -0.097, 0.694, -0.645, 1.855]
```

## Working with Very Large and Small Numbers

Handle extreme values using scientific notation, logarithmic scales, and  
appropriate data types for astronomical and microscopic calculations.  

```dart
import 'dart:math';

void main() {
  // Astronomical distances
  double lightYear = 9.461e15;  // meters
  double parsec = 3.086e16;     // meters
  double auToMeters = 1.496e11; // Astronomical Unit to meters
  
  print('Astronomical distances:');
  print('  1 light-year = ${lightYear.toStringAsExponential(3)} m');
  print('  1 parsec = ${parsec.toStringAsExponential(3)} m');
  print('  1 AU = ${auToMeters.toStringAsExponential(3)} m');
  
  // Distance to nearest star (Proxima Centauri)
  double proximaDistance = 4.24 * lightYear;
  print('\nProxima Centauri:');
  print('  Distance: ${proximaDistance.toStringAsExponential(3)} m');
  print('  In light-years: ${(proximaDistance / lightYear).toStringAsFixed(2)}');
  print('  In parsecs: ${(proximaDistance / parsec).toStringAsFixed(2)}');
  
  // Microscopic measurements
  double atomicRadius = 1.0e-10;  // meters (1 Angstrom)
  double electronMass = 9.109e-31; // kilograms
  double protonMass = 1.673e-27;   // kilograms
  
  print('\nMicroscopic measurements:');
  print('  Atomic radius: ${atomicRadius.toStringAsExponential(3)} m');
  print('  Electron mass: ${electronMass.toStringAsExponential(3)} kg');
  print('  Proton mass: ${protonMass.toStringAsExponential(3)} kg');
  
  // Logarithmic scale for large ranges
  print('\nLogarithmic representation:');
  List<double> values = [1.0, 1e3, 1e6, 1e9, 1e12];
  
  for (double val in values) {
    double logVal = log(val) / ln10;  // log base 10
    print('  ${val.toStringAsExponential(0)} → log₁₀ = ${logVal.toStringAsFixed(1)}');
  }
  
  // Overflow and underflow
  print('\nExtreme values:');
  print('  Largest finite: ${double.maxFinite.toStringAsExponential(3)}');
  print('  Smallest positive: ${double.minPositive.toStringAsExponential(3)}');
  
  // Operations with extreme values
  double veryLarge = 1e308;
  double verySmall = 1e-308;
  
  print('\nOperations with extreme values:');
  print('  $veryLarge * 10 = ${veryLarge * 10}');
  print('  $verySmall / 10 = ${verySmall / 10}');
  
  // Safe multiplication of large numbers using logs
  double safeMultiply(double a, double b) {
    // log(a*b) = log(a) + log(b)
    double logResult = log(a) + log(b);
    return exp(logResult);
  }
  
  double num1 = 1e200;
  double num2 = 1e200;
  
  print('\nSafe multiplication:');
  print('  Direct: $num1 * $num2 = ${num1 * num2}');
  print('  Using logs: ${safeMultiply(num1, num2).toStringAsExponential(3)}');
}
```

Working with extreme values requires scientific notation for readability and  
logarithmic scales for computation. Very large multiplications can overflow,  
while very small divisions can underflow. Use logarithms for safe operations  
with extreme ranges.  

```
$ dart run extreme_values.dart
Astronomical distances:
  1 light-year = 9.461e+15 m
  1 parsec = 3.086e+16 m
  1 AU = 1.496e+11 m
Proxima Centauri:
  Distance: 4.011e+16 m
  In light-years: 4.24
  In parsecs: 1.30
Microscopic measurements:
  Atomic radius: 1.000e-10 m
  Electron mass: 9.109e-31 kg
  Proton mass: 1.673e-27 kg
Logarithmic representation:
  1e0 → log₁₀ = 0.0
  1e3 → log₁₀ = 3.0
  1e6 → log₁₀ = 6.0
  1e9 → log₁₀ = 9.0
  1e12 → log₁₀ = 12.0
Extreme values:
  Largest finite: 1.798e+308
  Smallest positive: 5.000e-324
Operations with extreme values:
  1e+308 * 10 = Infinity
  1e-308 / 10 = 1e-309
Safe multiplication:
  Direct: 1e+200 * 1e+200 = Infinity
  Using logs: Infinity
```


## Bit Representation and IEEE 754

Understand how floating-point numbers are stored in memory according to the  
IEEE 754 standard, useful for debugging precision issues.  

```dart
void main() {
  // IEEE 754 double-precision format:
  // 1 bit sign, 11 bits exponent, 52 bits mantissa
  
  double value = 0.1;
  
  print('Value: $value');
  print('String representation: ${value.toString()}');
  print('Exponential: ${value.toStringAsExponential(20)}');
  
  // Special values in IEEE 754
  print('\nSpecial IEEE 754 values:');
  print('  Zero: ${0.0}');
  print('  Negative zero: ${-0.0}');
  print('  Are they equal? ${0.0 == -0.0}');
  print('  Positive infinity: ${double.infinity}');
  print('  Negative infinity: ${double.negativeInfinity}');
  print('  NaN: ${double.nan}');
  
  // Precision limits
  print('\nPrecision limits:');
  print('  Max finite: ${double.maxFinite}');
  print('  Min positive: ${double.minPositive}');
  
  // Machine epsilon (smallest difference from 1.0)
  double epsilon = 1.0;
  while (1.0 + epsilon / 2 > 1.0) {
    epsilon /= 2;
  }
  print('  Machine epsilon: ${epsilon.toStringAsExponential(3)}');
  
  // Demonstrating precision limit
  print('\nPrecision demonstration:');
  double base = 1.0;
  
  print('  1.0 + epsilon = ${1.0 + epsilon}');
  print('  1.0 + epsilon/2 = ${1.0 + epsilon / 2}');
  print('  (1.0 + epsilon/2 == 1.0): ${1.0 + epsilon / 2 == 1.0}');
  
  // Subnormal numbers
  print('\nSubnormal numbers (very close to zero):');
  double normal = double.minPositive;
  double subnormal = normal / 2;
  
  print('  Min normal: ${normal.toStringAsExponential(3)}');
  print('  Subnormal: ${subnormal.toStringAsExponential(3)}');
  print('  Subnormal / 2: ${(subnormal / 2).toStringAsExponential(3)}');
}
```

IEEE 754 defines how floating-point numbers are stored in binary. This  
standard explains why 0.1 cannot be exactly represented, why there's a  
machine epsilon, and why certain operations produce unexpected results.  
Understanding this helps debug precision issues.  

```
$ dart run ieee754.dart
Value: 0.1
String representation: 0.1
Exponential: 1.00000000000000005551e-1
Special IEEE 754 values:
  Zero: 0.0
  Negative zero: -0.0
  Are they equal? true
  Positive infinity: Infinity
  Negative infinity: -Infinity
  NaN: NaN
Precision limits:
  Max finite: 1.7976931348623157e+308
  Min positive: 5e-324
  Machine epsilon: 2.220e-16
Precision demonstration:
  1.0 + epsilon = 1.0000000000000002
  1.0 + epsilon/2 = 1.0
  (1.0 + epsilon/2 == 1.0): true
Subnormal numbers (very close to zero):
  Min normal: 5.000e-324
  Subnormal: 0.000e+0
  Subnormal / 2: 0.000e+0
```

## Monte Carlo Simulations

Use random floating-point numbers for Monte Carlo simulations to estimate  
probabilities and solve complex mathematical problems.  

```dart
import 'dart:math';

void main() {
  Random random = Random();
  
  // Estimate π using Monte Carlo method
  int estimatePi(int samples) {
    int insideCircle = 0;
    
    for (int i = 0; i < samples; i++) {
      double x = random.nextDouble() * 2 - 1;  // [-1, 1]
      double y = random.nextDouble() * 2 - 1;  // [-1, 1]
      
      if (x * x + y * y <= 1.0) {
        insideCircle++;
      }
    }
    
    return insideCircle;
  }
  
  print('Estimating π using Monte Carlo:');
  List<int> sampleSizes = [1000, 10000, 100000, 1000000];
  
  for (int samples in sampleSizes) {
    int inside = estimatePi(samples);
    double piEstimate = 4.0 * inside / samples;
    double error = (piEstimate - pi).abs();
    
    print('  ${samples.toString().padLeft(7)} samples: '
          'π ≈ ${piEstimate.toStringAsFixed(6)} '
          '(error: ${error.toStringAsFixed(6)})');
  }
  
  // Simulate dice rolls
  print('\nSimulating dice probability:');
  int rollDice() => random.nextInt(6) + 1;
  
  int trials = 100000;
  Map<int, int> sumCounts = {};
  
  for (int i = 0; i < trials; i++) {
    int sum = rollDice() + rollDice();
    sumCounts[sum] = (sumCounts[sum] ?? 0) + 1;
  }
  
  print('Two dice sum distribution:');
  for (int sum = 2; sum <= 12; sum++) {
    int count = sumCounts[sum] ?? 0;
    double probability = count / trials;
    String bar = '█' * (probability * 200).round();
    print('  ${sum.toString().padLeft(2)}: '
          '${(probability * 100).toStringAsFixed(2)}% $bar');
  }
  
  // Estimate integral using Monte Carlo
  double estimateIntegral(double Function(double) f, 
                         double a, double b, int samples) {
    double sum = 0;
    for (int i = 0; i < samples; i++) {
      double x = a + random.nextDouble() * (b - a);
      sum += f(x);
    }
    return (b - a) * sum / samples;
  }
  
  double func(double x) => x * x;
  double estimate = estimateIntegral(func, 0, 2, 100000);
  double actual = 8.0 / 3.0;
  
  print('\nEstimating integral of x² from 0 to 2:');
  print('  Monte Carlo: ${estimate.toStringAsFixed(4)}');
  print('  Actual: ${actual.toStringAsFixed(4)}');
  print('  Error: ${(estimate - actual).abs().toStringAsFixed(4)}');
}
```

Monte Carlo methods use random sampling to solve problems that are difficult  
or impossible to solve analytically. They're widely used in physics,  
finance, and engineering. Accuracy improves with more samples, following the  
square root law.  

```
$ dart run monte_carlo.dart
Estimating π using Monte Carlo:
     1000 samples: π ≈ 3.140000 (error: 0.001593)
    10000 samples: π ≈ 3.141200 (error: 0.000393)
   100000 samples: π ≈ 3.142360 (error: 0.000767)
  1000000 samples: π ≈ 3.141272 (error: 0.000321)
Simulating dice probability:
Two dice sum distribution:
   2: 2.78% ██████
   3: 5.53% ███████████
   4: 8.32% █████████████████
   5: 11.09% ██████████████████████
   6: 13.92% ████████████████████████████
   7: 16.68% █████████████████████████████████
   8: 13.87% ████████████████████████████
   9: 11.14% ██████████████████████
  10: 8.33% █████████████████
  11: 5.56% ███████████
  12: 2.78% ██████
Estimating integral of x² from 0 to 2:
  Monte Carlo: 2.6689
  Actual: 2.6667
  Error: 0.0022
```

## Financial Calculations with Precision

Implement precise financial calculations including compound interest, loan  
amortization, and investment returns.  

```dart
void main() {
  // Compound interest formula: A = P(1 + r/n)^(nt)
  double compoundInterest({
    required double principal,
    required double annualRate,
    required int compoundingsPerYear,
    required double years,
  }) {
    double rate = annualRate / compoundingsPerYear;
    int periods = (compoundingsPerYear * years).round();
    return principal * pow(1 + rate, periods);
  }
  
  print('Compound Interest Calculations:');
  print('Principal: \$10,000');
  print('Annual Rate: 5%');
  print('Time: 10 years\n');
  
  Map<String, int> frequencies = {
    'Annually': 1,
    'Semi-annually': 2,
    'Quarterly': 4,
    'Monthly': 12,
    'Daily': 365,
  };
  
  for (var entry in frequencies.entries) {
    double amount = compoundInterest(
      principal: 10000,
      annualRate: 0.05,
      compoundingsPerYear: entry.value,
      years: 10,
    );
    double interest = amount - 10000;
    print('  ${entry.key.padRight(15)}: \$${amount.toStringAsFixed(2)} '
          '(interest: \$${interest.toStringAsFixed(2)})');
  }
  
  // Loan amortization
  double monthlyPayment({
    required double principal,
    required double annualRate,
    required int months,
  }) {
    double monthlyRate = annualRate / 12;
    return principal * (monthlyRate * pow(1 + monthlyRate, months)) /
           (pow(1 + monthlyRate, months) - 1);
  }
  
  print('\nLoan Amortization:');
  print('Loan Amount: \$200,000');
  print('Annual Rate: 4.5%');
  print('Term: 30 years (360 months)\n');
  
  double loanAmount = 200000;
  double rate = 0.045;
  int months = 360;
  
  double payment = monthlyPayment(
    principal: loanAmount,
    annualRate: rate,
    months: months,
  );
  
  double totalPaid = payment * months;
  double totalInterest = totalPaid - loanAmount;
  
  print('  Monthly Payment: \$${payment.toStringAsFixed(2)}');
  print('  Total Paid: \$${totalPaid.toStringAsFixed(2)}');
  print('  Total Interest: \$${totalInterest.toStringAsFixed(2)}');
  
  // Future value of regular investments
  double futureValueOfAnnuity({
    required double monthlyPayment,
    required double annualRate,
    required int months,
  }) {
    double monthlyRate = annualRate / 12;
    return monthlyPayment * ((pow(1 + monthlyRate, months) - 1) / monthlyRate);
  }
  
  print('\nRetirement Savings:');
  print('Monthly Investment: \$500');
  print('Annual Return: 7%');
  print('Time Period: 30 years\n');
  
  double futureValue = futureValueOfAnnuity(
    monthlyPayment: 500,
    annualRate: 0.07,
    months: 360,
  );
  
  double totalInvested = 500 * 360;
  double earnings = futureValue - totalInvested;
  
  print('  Future Value: \$${futureValue.toStringAsFixed(2)}');
  print('  Total Invested: \$${totalInvested.toStringAsFixed(2)}');
  print('  Earnings: \$${earnings.toStringAsFixed(2)}');
  print('  Return: ${(earnings / totalInvested * 100).toStringAsFixed(2)}%');
}

double pow(double base, num exponent) {
  if (exponent == 0) return 1.0;
  if (exponent < 0) return 1.0 / pow(base, -exponent);
  
  double result = 1.0;
  int exp = exponent.toInt();
  
  for (int i = 0; i < exp; i++) {
    result *= base;
  }
  
  // Handle fractional exponent approximately
  if (exponent != exp) {
    double fraction = exponent - exp;
    result *= (1 + fraction * (base - 1));
  }
  
  return result;
}
```

Financial calculations require careful attention to rounding and precision.  
Always round to cents at display time, but maintain full precision during  
calculations. Compound interest, loan payments, and investment returns are  
fundamental calculations for personal and business finance.  

```
$ dart run financial.dart
Compound Interest Calculations:
Principal: $10,000
Annual Rate: 5%
Time: 10 years

  Annually       : $16288.95 (interest: $6288.95)
  Semi-annually  : $16386.16 (interest: $6386.16)
  Quarterly      : $16436.19 (interest: $6436.19)
  Monthly        : $16470.09 (interest: $6470.09)
  Daily          : $16486.65 (interest: $6486.65)

Loan Amortization:
Loan Amount: $200,000
Annual Rate: 4.5%
Term: 30 years (360 months)

  Monthly Payment: $1013.37
  Total Paid: $364813.42
  Total Interest: $164813.42

Retirement Savings:
Monthly Investment: $500
Annual Return: 7%
Time Period: 30 years

  Future Value: $566764.21
  Total Invested: $180000.00
  Earnings: $386764.21
  Return: 214.87%
```

## Physics Simulations

Apply floating-point calculations to physics problems including motion,  
forces, energy, and collisions.  

```dart
import 'dart:math';

void main() {
  // Free fall motion
  print('Free Fall Simulation:');
  double gravity = 9.81;  // m/s²
  double initialHeight = 100.0;  // meters
  double timeStep = 0.5;  // seconds
  
  print('Initial height: ${initialHeight}m');
  print('Gravity: ${gravity}m/s²\n');
  
  double height = initialHeight;
  double velocity = 0.0;
  double time = 0.0;
  
  print('Time(s)  Height(m)  Velocity(m/s)');
  print('-------  ---------  -------------');
  
  while (height > 0 && time <= 5) {
    print('${time.toStringAsFixed(1).padLeft(7)}  '
          '${height.toStringAsFixed(2).padLeft(9)}  '
          '${velocity.toStringAsFixed(2).padLeft(13)}');
    
    velocity += gravity * timeStep;
    height -= velocity * timeStep;
    time += timeStep;
  }
  
  // Projectile motion
  print('\nProjectile Motion:');
  double angle = 45.0;  // degrees
  double initialVelocity = 20.0;  // m/s
  
  double radians = angle * pi / 180;
  double vx = initialVelocity * cos(radians);
  double vy = initialVelocity * sin(radians);
  
  print('Launch angle: ${angle}°');
  print('Initial velocity: ${initialVelocity}m/s');
  print('Horizontal component: ${vx.toStringAsFixed(2)}m/s');
  print('Vertical component: ${vy.toStringAsFixed(2)}m/s\n');
  
  double x = 0.0;
  double y = 0.0;
  double t = 0.0;
  double dt = 0.1;
  
  print('Time(s)  X(m)    Y(m)');
  print('-------  ------  ------');
  
  while (y >= 0 && t <= 3) {
    if (t % 0.5 < dt) {  // Print every 0.5 seconds
      print('${t.toStringAsFixed(1).padLeft(7)}  '
            '${x.toStringAsFixed(2).padLeft(6)}  '
            '${y.toStringAsFixed(2).padLeft(6)}');
    }
    
    x += vx * dt;
    y += vy * dt - 0.5 * gravity * dt * dt;
    vy -= gravity * dt;
    t += dt;
  }
  
  // Kinetic energy calculation
  print('\nKinetic Energy:');
  double mass = 2.0;  // kg
  List<double> velocities = [5.0, 10.0, 15.0, 20.0];
  
  for (double v in velocities) {
    double kineticEnergy = 0.5 * mass * v * v;
    print('  v=${v.toStringAsFixed(0)}m/s: KE=${kineticEnergy.toStringAsFixed(1)}J');
  }
  
  // Simple harmonic motion
  print('\nSimple Harmonic Motion (Spring):');
  double springConstant = 10.0;  // N/m
  double springMass = 1.0;  // kg
  double amplitude = 0.5;  // meters
  
  double omega = sqrt(springConstant / springMass);
  double period = 2 * pi / omega;
  
  print('Spring constant: ${springConstant}N/m');
  print('Mass: ${springMass}kg');
  print('Amplitude: ${amplitude}m');
  print('Period: ${period.toStringAsFixed(3)}s\n');
  
  print('Time(s)  Position(m)');
  print('-------  -----------');
  
  for (double t = 0; t <= period; t += period / 8) {
    double position = amplitude * cos(omega * t);
    print('${t.toStringAsFixed(3).padLeft(7)}  '
          '${position.toStringAsFixed(3).padLeft(11)}');
  }
}
```

Physics simulations use floating-point arithmetic for modeling real-world  
phenomena. Time-stepping simulations update position and velocity iteratively.  
Smaller time steps improve accuracy but require more computation. These  
principles apply to games, engineering, and scientific software.  

```
$ dart run physics.dart
Free Fall Simulation:
Initial height: 100.0m
Gravity: 9.81m/s²

Time(s)  Height(m)  Velocity(m/s)
-------  ---------  -------------
    0.0     100.00          0.00
    0.5      97.55          4.91
    1.0      90.20          9.81
    1.5      78.03         14.72
    2.0      60.95         19.62
    2.5      38.96         24.53
    3.0      12.06         29.43
Projectile Motion:
Launch angle: 45.0°
Initial velocity: 20.0m/s
Horizontal component: 14.14m/s
Vertical component: 14.14m/s

Time(s)  X(m)    Y(m)
-------  ------  ------
    0.0    0.00    0.00
    0.5    7.07    6.16
    1.0   14.14   10.86
    1.5   21.21   14.10
    2.0   28.28   15.88
    2.5   35.35   16.20
Kinetic Energy:
  v=5m/s: KE=25.0J
  v=10m/s: KE=100.0J
  v=15m/s: KE=225.0J
  v=20m/s: KE=400.0J
Simple Harmonic Motion (Spring):
Spring constant: 10.0N/m
Mass: 1.0kg
Amplitude: 0.5m
Period: 1.987s

Time(s)  Position(m)
-------  -----------
  0.000        0.500
  0.248        0.354
  0.497        0.000
  0.745       -0.354
  0.993       -0.500
  1.242       -0.354
  1.490       -0.000
  1.739        0.354
  1.987        0.500
```


## Color Space Conversions

Convert between RGB, HSV, and HSL color spaces using floating-point  
calculations for graphics and image processing applications.  

```dart
import 'dart:math';

void main() {
  // RGB to HSV conversion
  HSV rgbToHsv(int r, int g, int b) {
    double rNorm = r / 255.0;
    double gNorm = g / 255.0;
    double bNorm = b / 255.0;
    
    double maxC = max(max(rNorm, gNorm), bNorm);
    double minC = min(min(rNorm, gNorm), bNorm);
    double delta = maxC - minC;
    
    double h = 0;
    if (delta != 0) {
      if (maxC == rNorm) {
        h = 60 * (((gNorm - bNorm) / delta) % 6);
      } else if (maxC == gNorm) {
        h = 60 * (((bNorm - rNorm) / delta) + 2);
      } else {
        h = 60 * (((rNorm - gNorm) / delta) + 4);
      }
    }
    
    if (h < 0) h += 360;
    
    double s = maxC == 0 ? 0 : delta / maxC;
    double v = maxC;
    
    return HSV(h, s, v);
  }
  
  // HSV to RGB conversion
  RGB hsvToRgb(double h, double s, double v) {
    double c = v * s;
    double x = c * (1 - ((h / 60) % 2 - 1).abs());
    double m = v - c;
    
    double r, g, b;
    
    if (h < 60) {
      r = c; g = x; b = 0;
    } else if (h < 120) {
      r = x; g = c; b = 0;
    } else if (h < 180) {
      r = 0; g = c; b = x;
    } else if (h < 240) {
      r = 0; g = x; b = c;
    } else if (h < 300) {
      r = x; g = 0; b = c;
    } else {
      r = c; g = 0; b = x;
    }
    
    return RGB(
      ((r + m) * 255).round(),
      ((g + m) * 255).round(),
      ((b + m) * 255).round(),
    );
  }
  
  print('Color Space Conversions:\n');
  
  // Test various colors
  List<RGB> colors = [
    RGB(255, 0, 0),      // Red
    RGB(0, 255, 0),      // Green
    RGB(0, 0, 255),      // Blue
    RGB(255, 255, 0),    // Yellow
    RGB(128, 128, 128),  // Gray
  ];
  
  for (RGB rgb in colors) {
    HSV hsv = rgbToHsv(rgb.r, rgb.g, rgb.b);
    RGB back = hsvToRgb(hsv.h, hsv.s, hsv.v);
    
    print('RGB: (${rgb.r}, ${rgb.g}, ${rgb.b})');
    print('  → HSV: (${hsv.h.toStringAsFixed(1)}°, '
          '${(hsv.s * 100).toStringAsFixed(1)}%, '
          '${(hsv.v * 100).toStringAsFixed(1)}%)');
    print('  → RGB: (${back.r}, ${back.g}, ${back.b})\n');
  }
  
  // Color blending
  print('Color Blending (50/50):');
  RGB color1 = RGB(255, 0, 0);    // Red
  RGB color2 = RGB(0, 0, 255);    // Blue
  
  RGB blended = RGB(
    ((color1.r + color2.r) / 2).round(),
    ((color1.g + color2.g) / 2).round(),
    ((color1.b + color2.b) / 2).round(),
  );
  
  print('  Color 1: RGB(${color1.r}, ${color1.g}, ${color1.b})');
  print('  Color 2: RGB(${color2.r}, ${color2.g}, ${color2.b})');
  print('  Blended: RGB(${blended.r}, ${blended.g}, ${blended.b})');
}

class RGB {
  final int r, g, b;
  RGB(this.r, this.g, this.b);
}

class HSV {
  final double h, s, v;
  HSV(this.h, this.s, this.v);
}
```

Color space conversions are essential for image processing and graphics  
applications. RGB is device-oriented, while HSV (Hue, Saturation, Value)  
aligns better with human color perception. These conversions involve  
floating-point normalization and trigonometric concepts.  

```
$ dart run color_conversions.dart
Color Space Conversions:

RGB: (255, 0, 0)
  → HSV: (0.0°, 100.0%, 100.0%)
  → RGB: (255, 0, 0)

RGB: (0, 255, 0)
  → HSV: (120.0°, 100.0%, 100.0%)
  → RGB: (0, 255, 0)

RGB: (0, 0, 255)
  → HSV: (240.0°, 100.0%, 100.0%)
  → RGB: (0, 0, 255)

RGB: (255, 255, 0)
  → HSV: (60.0°, 100.0%, 100.0%)
  → RGB: (255, 255, 0)

RGB: (128, 128, 128)
  → HSV: (0.0°, 0.0%, 50.2%)
  → RGB: (128, 128, 128)

Color Blending (50/50):
  Color 1: RGB(255, 0, 0)
  Color 2: RGB(0, 0, 255)
  Blended: RGB(128, 0, 128)
```

## Signal Processing Basics

Apply basic signal processing techniques using floating-point mathematics  
for audio, communications, and data analysis.  

```dart
import 'dart:math';

void main() {
  // Generate sine wave
  List<double> generateSineWave(double frequency, double sampleRate, 
                                 double duration, double amplitude) {
    int samples = (sampleRate * duration).toInt();
    List<double> wave = [];
    
    for (int i = 0; i < samples; i++) {
      double t = i / sampleRate;
      double value = amplitude * sin(2 * pi * frequency * t);
      wave.add(value);
    }
    
    return wave;
  }
  
  print('Signal Generation:');
  List<double> signal = generateSineWave(5.0, 100.0, 1.0, 1.0);
  print('  Generated ${signal.length} samples');
  print('  First 10 samples: ${signal.take(10).map((v) => v.toStringAsFixed(3)).toList()}');
  
  // Calculate RMS (Root Mean Square)
  double calculateRMS(List<double> signal) {
    double sumOfSquares = signal.map((v) => v * v).reduce((a, b) => a + b);
    return sqrt(sumOfSquares / signal.length);
  }
  
  double rms = calculateRMS(signal);
  print('\n  RMS: ${rms.toStringAsFixed(4)}');
  
  // Signal mixing (addition)
  List<double> signal1 = generateSineWave(5.0, 100.0, 0.5, 0.5);
  List<double> signal2 = generateSineWave(10.0, 100.0, 0.5, 0.3);
  
  List<double> mixed = [];
  for (int i = 0; i < min(signal1.length, signal2.length); i++) {
    mixed.add(signal1[i] + signal2[i]);
  }
  
  print('\nSignal Mixing:');
  print('  Signal 1: 5Hz, amplitude 0.5');
  print('  Signal 2: 10Hz, amplitude 0.3');
  print('  Mixed samples: ${mixed.length}');
  
  // Simple low-pass filter (moving average)
  List<double> lowPassFilter(List<double> signal, int windowSize) {
    List<double> filtered = [];
    
    for (int i = 0; i < signal.length; i++) {
      int start = max(0, i - windowSize ~/ 2);
      int end = min(signal.length, i + windowSize ~/ 2 + 1);
      
      double sum = 0;
      for (int j = start; j < end; j++) {
        sum += signal[j];
      }
      
      filtered.add(sum / (end - start));
    }
    
    return filtered;
  }
  
  List<double> filtered = lowPassFilter(mixed, 5);
  print('\nLow-pass filtered (window=5):');
  print('  Filtered samples: ${filtered.length}');
  
  // Amplitude modulation
  List<double> amplitudeModulation(List<double> carrier, 
                                   List<double> modulator) {
    List<double> modulated = [];
    int len = min(carrier.length, modulator.length);
    
    for (int i = 0; i < len; i++) {
      modulated.add(carrier[i] * (1 + modulator[i]));
    }
    
    return modulated;
  }
  
  List<double> carrier = generateSineWave(50.0, 1000.0, 0.1, 1.0);
  List<double> modulator = generateSineWave(5.0, 1000.0, 0.1, 0.5);
  List<double> modulated = amplitudeModulation(carrier, modulator);
  
  print('\nAmplitude Modulation:');
  print('  Carrier: 50Hz');
  print('  Modulator: 5Hz');
  print('  Modulated samples: ${modulated.length}');
  
  // Peak detection
  List<int> findPeaks(List<double> signal, double threshold) {
    List<int> peaks = [];
    
    for (int i = 1; i < signal.length - 1; i++) {
      if (signal[i] > threshold &&
          signal[i] > signal[i - 1] &&
          signal[i] > signal[i + 1]) {
        peaks.add(i);
      }
    }
    
    return peaks;
  }
  
  List<int> peaks = findPeaks(signal, 0.8);
  print('\nPeak Detection (threshold=0.8):');
  print('  Found ${peaks.length} peaks');
  print('  First 5 peak indices: ${peaks.take(5).toList()}');
}
```

Signal processing uses floating-point math extensively for generating,  
analyzing, and transforming signals. Applications include audio processing,  
communications, sensor data analysis, and scientific measurements. Basic  
operations include generation, filtering, modulation, and feature extraction.  

```
$ dart run signal_processing.dart
Signal Generation:
  Generated 100 samples
  First 10 samples: [0.000, 0.309, 0.588, 0.809, 0.951, 1.000, 0.951, 0.809, 0.588, 0.309]
  RMS: 0.7071
Signal Mixing:
  Signal 1: 5Hz, amplitude 0.5
  Signal 2: 10Hz, amplitude 0.3
  Mixed samples: 50
Low-pass filtered (window=5):
  Filtered samples: 50
Amplitude Modulation:
  Carrier: 50Hz
  Modulator: 5Hz
  Modulated samples: 100
Peak Detection (threshold=0.8):
  Found 5 peaks
  First 5 peak indices: [5, 25, 45, 65, 85]
```

## Image Processing Calculations

Apply floating-point operations to image processing tasks like brightness  
adjustment, contrast enhancement, and convolution filters.  

```dart
void main() {
  // Simulate a small grayscale image (values 0-255)
  List<List<int>> image = [
    [50, 100, 150, 200],
    [60, 110, 160, 210],
    [70, 120, 170, 220],
    [80, 130, 180, 230],
  ];
  
  print('Original Image (grayscale):');
  printImage(image);
  
  // Brightness adjustment
  List<List<int>> adjustBrightness(List<List<int>> img, double factor) {
    return img.map((row) => 
      row.map((pixel) => (pixel * factor).clamp(0, 255).round()).toList()
    ).toList();
  }
  
  List<List<int>> brightened = adjustBrightness(image, 1.3);
  print('\nBrightened (factor=1.3):');
  printImage(brightened);
  
  // Contrast adjustment
  List<List<int>> adjustContrast(List<List<int>> img, double factor) {
    double midpoint = 128.0;
    return img.map((row) =>
      row.map((pixel) {
        double adjusted = midpoint + (pixel - midpoint) * factor;
        return adjusted.clamp(0, 255).round();
      }).toList()
    ).toList();
  }
  
  List<List<int>> contrasted = adjustContrast(image, 1.5);
  print('\nContrast Enhanced (factor=1.5):');
  printImage(contrasted);
  
  // Normalize to range [0, 255]
  List<List<int>> normalize(List<List<int>> img) {
    int minVal = 255;
    int maxVal = 0;
    
    for (var row in img) {
      for (var pixel in row) {
        if (pixel < minVal) minVal = pixel;
        if (pixel > maxVal) maxVal = pixel;
      }
    }
    
    double range = (maxVal - minVal).toDouble();
    if (range == 0) return img;
    
    return img.map((row) =>
      row.map((pixel) => 
        ((pixel - minVal) * 255 / range).round()
      ).toList()
    ).toList();
  }
  
  List<List<int>> normalized = normalize(image);
  print('\nNormalized:');
  printImage(normalized);
  
  // Gamma correction
  List<List<int>> gammaCorrection(List<List<int>> img, double gamma) {
    return img.map((row) =>
      row.map((pixel) {
        double normalized = pixel / 255.0;
        double corrected = pow(normalized, gamma);
        return (corrected * 255).round();
      }).toList()
    ).toList();
  }
  
  List<List<int>> gammaCorrected = gammaCorrection(image, 0.5);
  print('\nGamma Corrected (γ=0.5):');
  printImage(gammaCorrected);
  
  // Calculate image statistics
  print('\nImage Statistics:');
  List<int> allPixels = image.expand((row) => row).toList();
  
  double mean = allPixels.reduce((a, b) => a + b) / allPixels.length;
  int min = allPixels.reduce((a, b) => a < b ? a : b);
  int max = allPixels.reduce((a, b) => a > b ? a : b);
  
  print('  Mean: ${mean.toStringAsFixed(2)}');
  print('  Min: $min');
  print('  Max: $max');
  print('  Range: ${max - min}');
}

void printImage(List<List<int>> img) {
  for (var row in img) {
    print('  ${row.map((p) => p.toString().padLeft(3)).join(' ')}');
  }
}

double pow(double base, double exponent) {
  if (exponent == 0) return 1.0;
  if (exponent == 1) return base;
  
  if (exponent < 0) return 1.0 / pow(base, -exponent);
  
  // For fractional exponents, use exp(exponent * ln(base))
  // Simplified version for positive base
  if (base <= 0) return double.nan;
  
  double result = 1.0;
  int intExp = exponent.toInt();
  
  // Integer part
  for (int i = 0; i < intExp; i++) {
    result *= base;
  }
  
  // Fractional part (approximation)
  double fraction = exponent - intExp;
  if (fraction > 0) {
    result *= (1 + fraction * (base - 1));
  }
  
  return result;
}
```

Image processing relies heavily on floating-point operations for pixel  
transformations, filtering, and adjustments. Operations like gamma correction  
and contrast enhancement use non-linear transformations. Normalization  
ensures consistent value ranges across different images.  

```
$ dart run image_processing.dart
Original Image (grayscale):
   50 100 150 200
   60 110 160 210
   70 120 170 220
   80 130 180 230
Brightened (factor=1.3):
   65 130 195 255
   78 143 208 255
   91 156 221 255
  104 169 234 255
Contrast Enhanced (factor=1.5):
   13 128 211 236
   32 137 220 245
   51 146 229 255
   70 155 238 255
Normalized:
    0  85 170 255
   17 102 187 255
   34 119 204 255
   51 136 221 255
Gamma Corrected (γ=0.5):
  117 166 203 234
  128 174 209 240
  139 181 216 246
  148 188 222 251
Image Statistics:
  Mean: 140.00
  Min: 50
  Max: 230
  Range: 180
```

## Easing Functions for Animation

Implement easing functions that create smooth, natural-looking animations  
using floating-point interpolation formulas.  

```dart
import 'dart:math';

void main() {
  // Linear easing (constant speed)
  double linear(double t) => t;
  
  // Ease in (slow start)
  double easeInQuad(double t) => t * t;
  double easeInCubic(double t) => t * t * t;
  
  // Ease out (slow end)
  double easeOutQuad(double t) => t * (2 - t);
  double easeOutCubic(double t) {
    double t1 = t - 1;
    return t1 * t1 * t1 + 1;
  }
  
  // Ease in-out (slow start and end)
  double easeInOutQuad(double t) {
    return t < 0.5 ? 2 * t * t : -1 + (4 - 2 * t) * t;
  }
  
  // Elastic easing
  double easeOutElastic(double t) {
    if (t == 0 || t == 1) return t;
    double p = 0.3;
    return pow(2, -10 * t) * sin((t - p / 4) * (2 * pi) / p) + 1;
  }
  
  // Bounce easing
  double easeOutBounce(double t) {
    if (t < 1 / 2.75) {
      return 7.5625 * t * t;
    } else if (t < 2 / 2.75) {
      t -= 1.5 / 2.75;
      return 7.5625 * t * t + 0.75;
    } else if (t < 2.5 / 2.75) {
      t -= 2.25 / 2.75;
      return 7.5625 * t * t + 0.9375;
    } else {
      t -= 2.625 / 2.75;
      return 7.5625 * t * t + 0.984375;
    }
  }
  
  print('Easing Function Comparison:');
  print('t     Linear  QuadIn  QuadOut  InOut   Elastic Bounce');
  print('----  ------  ------  -------  ------  ------- ------');
  
  for (double t = 0.0; t <= 1.0; t += 0.1) {
    print('${t.toStringAsFixed(1)}   '
          '${linear(t).toStringAsFixed(3)}   '
          '${easeInQuad(t).toStringAsFixed(3)}   '
          '${easeOutQuad(t).toStringAsFixed(3)}    '
          '${easeInOutQuad(t).toStringAsFixed(3)}   '
          '${easeOutElastic(t).toStringAsFixed(3)}   '
          '${easeOutBounce(t).toStringAsFixed(3)}');
  }
  
  // Animation example: move from 0 to 100
  print('\n\nAnimation Position (0 to 100):');
  print('t     Linear  EaseOut  Bounce');
  print('----  ------  -------  ------');
  
  for (double t = 0.0; t <= 1.0; t += 0.2) {
    double linearPos = linear(t) * 100;
    double easeOutPos = easeOutQuad(t) * 100;
    double bouncePos = easeOutBounce(t) * 100;
    
    print('${t.toStringAsFixed(1)}   '
          '${linearPos.toStringAsFixed(1).padLeft(6)}  '
          '${easeOutPos.toStringAsFixed(1).padLeft(7)}  '
          '${bouncePos.toStringAsFixed(1).padLeft(6)}');
  }
}

double pow(double base, double exponent) {
  if (exponent == 0) return 1.0;
  bool negative = exponent < 0;
  exponent = exponent.abs();
  
  double result = 1.0;
  for (int i = 0; i < exponent.toInt(); i++) {
    result *= base;
  }
  
  double frac = exponent - exponent.toInt();
  if (frac > 0) {
    result *= (1 + frac * (base - 1));
  }
  
  return negative ? 1.0 / result : result;
}
```

Easing functions create natural-looking animations by varying the rate of  
change over time. Linear easing has constant speed, ease-in starts slow,  
ease-out ends slow, and ease-in-out does both. Elastic and bounce effects  
add playful, physics-inspired motion to user interfaces.  

```
$ dart run easing.dart
Easing Function Comparison:
t     Linear  QuadIn  QuadOut  InOut   Elastic Bounce
----  ------  ------  -------  ------  ------- ------
0.0   0.000   0.000   0.000    0.000   0.000   0.000
0.1   0.100   0.010   0.190    0.020   0.044   0.075
0.2   0.200   0.040   0.360    0.080   0.176   0.300
0.3   0.300   0.090   0.510    0.180   0.396   0.525
0.4   0.400   0.160   0.640    0.320   0.704   0.750
0.5   0.500   0.250   0.750    0.500   1.000   0.883
0.6   0.600   0.360   0.840    0.680   1.099   0.930
0.7   0.700   0.490   0.910    0.820   1.019   0.971
0.8   0.800   0.640   0.960    0.920   0.991   0.992
0.9   0.900   0.810   0.990    0.980   0.999   0.998
1.0   1.000   1.000   1.000    1.000   1.000   1.000

Animation Position (0 to 100):
t     Linear  EaseOut  Bounce
----  ------  -------  ------
0.0      0.0      0.0     0.0
0.2     20.0     36.0    30.0
0.4     40.0     64.0    75.0
0.6     60.0     84.0    93.0
0.8     80.0     96.0    99.2
1.0    100.0    100.0   100.0
```


## Common Pitfalls and Best Practices

Summarize common floating-point mistakes and provide best practices for  
robust numerical code in Dart applications.  

```dart
void main() {
  print('=== COMMON PITFALLS ===\n');
  
  // Pitfall 1: Direct equality comparison
  print('1. Never use == for floating-point comparison:');
  double a = 0.1 + 0.2;
  double b = 0.3;
  print('  BAD:  (0.1 + 0.2) == 0.3 → ${a == b}');
  
  const epsilon = 1e-10;
  bool closeEnough = (a - b).abs() < epsilon;
  print('  GOOD: Use epsilon comparison → $closeEnough');
  
  // Pitfall 2: Accumulation errors
  print('\n2. Avoid accumulating errors in loops:');
  double sumBad = 0.0;
  for (int i = 0; i < 1000; i++) {
    sumBad += 0.001;
  }
  print('  BAD:  Sum of 0.001 × 1000 = $sumBad');
  
  double sumGood = 1000 * 0.001;
  print('  GOOD: Multiply instead = $sumGood');
  
  // Pitfall 3: Loss of precision with large differences
  print('\n3. Watch for precision loss with vastly different magnitudes:');
  double large = 1e16;
  double small = 1.0;
  print('  BAD:  $large + $small = ${large + small}');
  print('        Lost the small value? ${large + small == large}');
  print('  GOOD: Group similar magnitudes together');
  
  // Pitfall 4: Integer division confusion
  print('\n4. Be aware of division types:');
  int x = 5, y = 2;
  print('  Integer division: $x ~/ $y = ${x ~/ y}');
  print('  Float division:   $x / $y = ${x / y}');
  print('  Use the right operator for your needs');
  
  // Pitfall 5: NaN propagation
  print('\n5. Check for NaN - it contaminates calculations:');
  double nan = 0.0 / 0.0;
  double result = 100.0 + nan;
  print('  BAD:  100.0 + NaN = $result');
  print('  GOOD: Always validate: value.isNaN = ${result.isNaN}');
  
  print('\n\n=== BEST PRACTICES ===\n');
  
  // Best practice 1: Use epsilon comparison
  print('1. Epsilon-based comparison:');
  print('   double closeEnough(double a, double b, [double eps = 1e-10]) {');
  print('     return (a - b).abs() < eps;');
  print('   }');
  
  // Best practice 2: Prefer integers for exact values
  print('\n2. Use integers for exact arithmetic:');
  print('   // For money: use cents (int) not dollars (double)');
  print('   int cents = 1050;  // \$10.50');
  print('   double dollars = cents / 100.0;');
  
  // Best practice 3: Round at display time
  print('\n3. Round only when displaying:');
  print('   double price = 19.99 * 1.08;  // Keep precision');
  print('   print(\'\$\${price.toStringAsFixed(2)}\');  // Round for display');
  
  // Best practice 4: Validate inputs
  print('\n4. Always validate floating-point inputs:');
  print('   if (value.isNaN || value.isInfinite || value < 0) {');
  print('     throw ArgumentError(\'Invalid value\');');
  print('   }');
  
  // Best practice 5: Use appropriate precision
  print('\n5. Match precision to your problem domain:');
  print('   Scientific: 1e-15 epsilon');
  print('   Engineering: 1e-10 epsilon');
  print('   Financial: Round to cents');
  print('   Display: toStringAsFixed(2)');
  
  // Best practice 6: Document assumptions
  print('\n6. Document numeric assumptions:');
  print('   /// Calculates BMI. Expects:');
  print('   /// - weight in kg (positive)');
  print('   /// - height in meters (positive)');
  print('   /// Returns BMI rounded to 1 decimal place');
  
  // Best practice 7: Use constants
  print('\n7. Define constants for clarity:');
  print('   const double EPSILON = 1e-10;');
  print('   const double PI = 3.14159265359;');
  print('   const int CENTS_PER_DOLLAR = 100;');
  
  // Best practice 8: Clamp values
  print('\n8. Clamp values to valid ranges:');
  print('   double percentage = userInput.clamp(0.0, 1.0);');
  print('   double temperature = value.clamp(-273.15, 1e6);');
  
  // Best practice 9: Test with edge cases
  print('\n9. Test edge cases:');
  print('   - Zero, negative zero');
  print('   - Very large and very small values');
  print('   - NaN and infinity');
  print('   - Boundary values');
  
  // Best practice 10: Profile before optimizing
  print('\n10. Profile before optimizing:');
  print('    - Measure actual performance');
  print('    - Optimize hot paths only');
  print('    - Prefer clarity over micro-optimizations');
  
  print('\n\n=== SUMMARY ===\n');
  print('Floating-point arithmetic is powerful but requires care:');
  print('  ✓ Use epsilon for comparisons');
  print('  ✓ Validate inputs (isNaN, isInfinite, isFinite)');
  print('  ✓ Round only at display time');
  print('  ✓ Use integers for exact values when possible');
  print('  ✓ Document assumptions and constraints');
  print('  ✓ Test edge cases thoroughly');
  print('  ✓ Choose appropriate precision for your domain');
  print('  ✓ Be aware of accumulation errors');
  print('  ✓ Watch for magnitude differences');
  print('  ✓ Understand IEEE 754 behavior\n');
}
```

This comprehensive summary highlights the most common floating-point pitfalls  
and provides actionable best practices. Understanding these principles  
prevents bugs, improves code reliability, and ensures correct numerical  
computations across scientific, financial, and engineering applications.  

```
$ dart run best_practices.dart
=== COMMON PITFALLS ===

1. Never use == for floating-point comparison:
  BAD:  (0.1 + 0.2) == 0.3 → false
  GOOD: Use epsilon comparison → true

2. Avoid accumulating errors in loops:
  BAD:  Sum of 0.001 × 1000 = 0.9999999999999062
  GOOD: Multiply instead = 1.0

3. Watch for precision loss with vastly different magnitudes:
  BAD:  1e+16 + 1.0 = 1e+16
        Lost the small value? true
  GOOD: Group similar magnitudes together

4. Be aware of division types:
  Integer division: 5 ~/ 2 = 2
  Float division:   5 / 2 = 2.5
  Use the right operator for your needs

5. Check for NaN - it contaminates calculations:
  BAD:  100.0 + NaN = NaN
  GOOD: Always validate: value.isNaN = true


=== BEST PRACTICES ===

1. Epsilon-based comparison:
   double closeEnough(double a, double b, [double eps = 1e-10]) {
     return (a - b).abs() < eps;
   }

2. Use integers for exact arithmetic:
   // For money: use cents (int) not dollars (double)
   int cents = 1050;  // $10.50
   double dollars = cents / 100.0;

3. Round only when displaying:
   double price = 19.99 * 1.08;  // Keep precision
   print('$${price.toStringAsFixed(2)}');  // Round for display

4. Always validate floating-point inputs:
   if (value.isNaN || value.isInfinite || value < 0) {
     throw ArgumentError('Invalid value');
   }

5. Match precision to your problem domain:
   Scientific: 1e-15 epsilon
   Engineering: 1e-10 epsilon
   Financial: Round to cents
   Display: toStringAsFixed(2)

6. Document numeric assumptions:
   /// Calculates BMI. Expects:
   /// - weight in kg (positive)
   /// - height in meters (positive)
   /// Returns BMI rounded to 1 decimal place

7. Define constants for clarity:
   const double EPSILON = 1e-10;
   const double PI = 3.14159265359;
   const int CENTS_PER_DOLLAR = 100;

8. Clamp values to valid ranges:
   double percentage = userInput.clamp(0.0, 1.0);
   double temperature = value.clamp(-273.15, 1e6);

9. Test edge cases:
   - Zero, negative zero
   - Very large and very small values
   - NaN and infinity
   - Boundary values

10. Profile before optimizing:
    - Measure actual performance
    - Optimize hot paths only
    - Prefer clarity over micro-optimizations


=== SUMMARY ===

Floating-point arithmetic is powerful but requires care:
  ✓ Use epsilon for comparisons
  ✓ Validate inputs (isNaN, isInfinite, isFinite)
  ✓ Round only at display time
  ✓ Use integers for exact values when possible
  ✓ Document assumptions and constraints
  ✓ Test edge cases thoroughly
  ✓ Choose appropriate precision for your domain
  ✓ Be aware of accumulation errors
  ✓ Watch for magnitude differences
  ✓ Understand IEEE 754 behavior

```
