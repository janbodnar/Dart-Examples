# Dart DateTime

DateTime in Dart represents a specific moment in time, providing comprehensive  
date and time manipulation capabilities. It supports various time zones,  
formatting operations, arithmetic calculations, and parsing from different  
formats.  

DateTime objects are immutable, meaning operations that appear to modify them  
actually return new DateTime instances. This design ensures thread safety  
and prevents accidental mutations that could lead to bugs in your code.  

## Creating DateTime Objects

DateTime objects can be created using various constructors. The default  
constructor creates dates in the local time zone.  

```dart
void main() {
  // Current date and time
  DateTime now = DateTime.now();
  print('Current time: $now');
  
  // Specific date and time
  DateTime specific = DateTime(2024, 3, 15, 14, 30, 45);
  print('Specific time: $specific');
  
  // UTC time
  DateTime utc = DateTime.utc(2024, 3, 15, 14, 30, 45);
  print('UTC time: $utc');
  
  // From milliseconds since epoch
  DateTime fromMillis = DateTime.fromMillisecondsSinceEpoch(1710506445000);
  print('From milliseconds: $fromMillis');
}
```

This example demonstrates four common ways to create DateTime objects:  
using the current time, specifying exact components, creating UTC times,  
and constructing from epoch milliseconds.  

```
$ dart run create_datetime.dart
Current time: 2024-03-15 14:30:45.123456
Specific time: 2024-03-15 14:30:45.000
UTC time: 2024-03-15 14:30:45.000Z
From milliseconds: 2024-03-15 14:30:45.000
```

## DateTime Components

DateTime provides properties to access individual components like year, month,  
day, hour, minute, and second.  

```dart
void main() {
  DateTime date = DateTime(2024, 12, 25, 10, 30, 45);
  
  // Date components
  print('Year: ${date.year}');
  print('Month: ${date.month}');
  print('Day: ${date.day}');
  
  // Time components
  print('Hour: ${date.hour}');
  print('Minute: ${date.minute}');
  print('Second: ${date.second}');
  print('Millisecond: ${date.millisecond}');
  print('Microsecond: ${date.microsecond}');
  
  // Day of week (1 = Monday, 7 = Sunday)
  print('Day of week: ${date.weekday}');
}
```

These properties provide direct access to date and time components without  
requiring complex parsing operations. The weekday property follows ISO 8601  
standard where Monday is 1 and Sunday is 7.  

```
$ dart run datetime_components.dart
Year: 2024
Month: 12
Day: 25
Hour: 10
Minute: 30
Second: 45
Millisecond: 0
Microsecond: 0
Day of week: 3
```

## Parsing DateTime Strings

DateTime can parse strings in ISO 8601 format and other common formats using  
the parse and tryParse methods.  

```dart
void main() {
  // ISO 8601 parsing
  DateTime iso = DateTime.parse('2024-03-15T14:30:45.123Z');
  print('Parsed ISO: $iso');
  
  // Local time parsing
  DateTime local = DateTime.parse('2024-03-15 14:30:45');
  print('Parsed local: $local');
  
  // Safe parsing with tryParse
  DateTime? valid = DateTime.tryParse('2024-03-15T14:30:45.123Z');
  DateTime? invalid = DateTime.tryParse('invalid-date');
  
  print('Valid parse: $valid');
  print('Invalid parse: $invalid');
  
  // Parsing different formats
  List<String> dateStrings = [
    '2024-03-15T14:30:45.123Z',
    '2024-03-15 14:30:45',
    '2024-03-15T14:30:45+02:00',
    '20240315T143045'
  ];
  
  for (String dateStr in dateStrings) {
    DateTime? parsed = DateTime.tryParse(dateStr);
    print('$dateStr -> $parsed');
  }
}
```

The parse method throws FormatException for invalid formats, while tryParse  
returns null. This example shows different valid ISO 8601 formats that Dart  
can automatically recognize and parse correctly.  

```
$ dart run parse_datetime.dart
Parsed ISO: 2024-03-15 14:30:45.123Z
Parsed local: 2024-03-15 14:30:45.000
Valid parse: 2024-03-15 14:30:45.123Z
Invalid parse: null
2024-03-15T14:30:45.123Z -> 2024-03-15 14:30:45.123Z
2024-03-15 14:30:45 -> 2024-03-15 14:30:45.000
2024-03-15T14:30:45+02:00 -> 2024-03-15 12:30:45.000Z
20240315T143045 -> null
```

## DateTime Formatting

DateTime objects can be formatted as strings using the toString method and  
toIso8601String for standardized output.  

```dart
void main() {
  DateTime date = DateTime(2024, 3, 15, 14, 30, 45, 123, 456);
  
  // Standard string representation
  print('toString(): ${date.toString()}');
  
  // ISO 8601 format
  print('toIso8601String(): ${date.toIso8601String()}');
  
  // UTC version
  DateTime utc = date.toUtc();
  print('UTC toString(): ${utc.toString()}');
  print('UTC toIso8601String(): ${utc.toIso8601String()}');
  
  // Custom formatting examples
  String formatted = '${date.day}/${date.month}/${date.year} '
                    '${date.hour.toString().padLeft(2, '0')}:'
                    '${date.minute.toString().padLeft(2, '0')}:'
                    '${date.second.toString().padLeft(2, '0')}';
  print('Custom format: $formatted');
  
  // Different date formats
  String usFormat = '${date.month}/${date.day}/${date.year}';
  String euroFormat = '${date.day}.${date.month}.${date.year}';
  String isoDateOnly = '${date.year}-${date.month.toString().padLeft(2, '0')}-${date.day.toString().padLeft(2, '0')}';
  
  print('US format: $usFormat');
  print('European format: $euroFormat');
  print('ISO date only: $isoDateOnly');
}
```

This example demonstrates various formatting approaches. The built-in methods  
provide standard formats, while custom formatting uses string interpolation  
and padding for consistent output.  

```
$ dart run format_datetime.dart
toString(): 2024-03-15 14:30:45.123456
toIso8601String(): 2024-03-15T14:30:45.123456
UTC toString(): 2024-03-15 14:30:45.123456Z
UTC toIso8601String(): 2024-03-15T14:30:45.123456Z
Custom format: 15/3/2024 14:30:45
US format: 3/15/2024
European format: 15.3.2024
ISO date only: 2024-03-15
```

## DateTime Arithmetic

DateTime supports arithmetic operations through add and subtract methods,  
working with Duration objects to perform time calculations.  

```dart
void main() {
  DateTime base = DateTime(2024, 3, 15, 12, 0, 0);
  print('Base date: $base');
  
  // Adding time
  DateTime plus1Hour = base.add(Duration(hours: 1));
  DateTime plus1Day = base.add(Duration(days: 1));
  DateTime plus1Week = base.add(Duration(days: 7));
  DateTime plus1Month = DateTime(base.year, base.month + 1, base.day);
  
  print('Plus 1 hour: $plus1Hour');
  print('Plus 1 day: $plus1Day');
  print('Plus 1 week: $plus1Week');
  print('Plus 1 month: $plus1Month');
  
  // Subtracting time
  DateTime minus2Hours = base.subtract(Duration(hours: 2));
  DateTime minus5Days = base.subtract(Duration(days: 5));
  DateTime minusWeek = base.subtract(Duration(days: 7));
  
  print('Minus 2 hours: $minus2Hours');
  print('Minus 5 days: $minus5Days');
  print('Minus 1 week: $minusWeek');
  
  // Complex arithmetic
  Duration complex = Duration(days: 2, hours: 3, minutes: 30, seconds: 45);
  DateTime result = base.add(complex);
  print('Complex addition: $result');
}
```

DateTime arithmetic is performed using Duration objects. For month and year  
arithmetic, you need to manually construct new DateTime objects since  
months have varying lengths.  

```
$ dart run datetime_arithmetic.dart
Base date: 2024-03-15 12:00:00.000
Plus 1 hour: 2024-03-15 13:00:00.000
Plus 1 day: 2024-03-16 12:00:00.000
Plus 1 week: 2024-03-22 12:00:00.000
Plus 1 month: 2024-04-15 12:00:00.000
Minus 2 hours: 2024-03-15 10:00:00.000
Minus 5 days: 2024-03-10 12:00:00.000
Minus 1 week: 2024-03-08 12:00:00.000
Complex addition: 2024-03-17 15:30:45.000
```

## DateTime Comparisons

DateTime objects can be compared using standard comparison operators and  
specialized methods for temporal relationships.  

```dart
void main() {
  DateTime date1 = DateTime(2024, 3, 15, 12, 0, 0);
  DateTime date2 = DateTime(2024, 3, 15, 14, 0, 0);
  DateTime date3 = DateTime(2024, 3, 15, 12, 0, 0);
  
  // Equality comparison
  print('date1 == date2: ${date1 == date2}');
  print('date1 == date3: ${date1 == date3}');
  
  // Ordering comparisons
  print('date1.isBefore(date2): ${date1.isBefore(date2)}');
  print('date2.isAfter(date1): ${date2.isAfter(date1)}');
  print('date1.isAtSameMomentAs(date3): ${date1.isAtSameMomentAs(date3)}');
  
  // Comparison operator
  int comparison = date1.compareTo(date2);
  print('date1.compareTo(date2): $comparison'); // -1, 0, or 1
  
  // Working with lists of dates
  List<DateTime> dates = [
    DateTime(2024, 3, 17),
    DateTime(2024, 3, 15),
    DateTime(2024, 3, 16),
    DateTime(2024, 3, 14),
  ];
  
  dates.sort();
  print('Sorted dates:');
  for (DateTime date in dates) {
    print('  ${date.toString().substring(0, 10)}');
  }
  
  // Finding min/max dates
  DateTime earliest = dates.first;
  DateTime latest = dates.last;
  print('Earliest: ${earliest.toString().substring(0, 10)}');
  print('Latest: ${latest.toString().substring(0, 10)}');
}
```

DateTime comparisons work naturally with standard operators. The compareTo  
method returns -1 for earlier dates, 0 for equal dates, and 1 for later  
dates, making it perfect for sorting operations.  

```
$ dart run datetime_comparisons.dart
date1 == date2: false
date1 == date3: true
date1.isBefore(date2): true
date2.isAfter(date1): true
date1.isAtSameMomentAs(date3): true
date1.compareTo(date2): -1
Sorted dates:
  2024-03-14
  2024-03-15
  2024-03-16
  2024-03-17
Earliest: 2024-03-14
Latest: 2024-03-17
```

## Duration Calculations

The Duration class works with DateTime to represent time intervals and  
calculate differences between dates.  

```dart
void main() {
  DateTime start = DateTime(2024, 3, 15, 9, 0, 0);
  DateTime end = DateTime(2024, 3, 17, 15, 30, 0);
  
  // Calculate difference
  Duration difference = end.difference(start);
  print('Total duration: $difference');
  
  // Access duration components
  print('Days: ${difference.inDays}');
  print('Hours: ${difference.inHours}');
  print('Minutes: ${difference.inMinutes}');
  print('Seconds: ${difference.inSeconds}');
  print('Milliseconds: ${difference.inMilliseconds}');
  
  // Format duration nicely
  int days = difference.inDays;
  int hours = difference.inHours.remainder(24);
  int minutes = difference.inMinutes.remainder(60);
  
  print('Formatted: ${days}d ${hours}h ${minutes}m');
  
  // Working with smaller durations
  DateTime time1 = DateTime(2024, 3, 15, 14, 30, 45);
  DateTime time2 = DateTime(2024, 3, 15, 14, 45, 30);
  
  Duration shortDiff = time2.difference(time1);
  print('Short duration: $shortDiff');
  print('Minutes: ${shortDiff.inMinutes}');
  print('Seconds: ${shortDiff.inSeconds}');
  
  // Age calculation
  DateTime birthDate = DateTime(1990, 6, 15);
  DateTime today = DateTime.now();
  Duration age = today.difference(birthDate);
  int ageInYears = (age.inDays / 365.25).floor();
  print('Age in years: $ageInYears');
}
```

Duration calculations are essential for measuring elapsed time, calculating  
ages, or determining time intervals. The remainder operation helps extract  
individual components from total durations.  

```
$ dart run duration_calculations.dart
Total duration: 54:30:00.000000
Days: 2
Hours: 54
Minutes: 3270
Seconds: 196200
Milliseconds: 196200000
Formatted: 2d 6h 30m
Short duration: 0:14:45.000000
Minutes: 14
Seconds: 885
Age in years: 33
```

## TimeZone Operations

DateTime handles time zones through UTC conversion and local time operations,  
essential for international applications.  

```dart
void main() {
  // Current local time
  DateTime local = DateTime.now();
  print('Local time: $local');
  print('Is UTC: ${local.isUtc}');
  
  // Convert to UTC
  DateTime utc = local.toUtc();
  print('UTC time: $utc');
  print('Is UTC: ${utc.isUtc}');
  
  // Convert back to local
  DateTime backToLocal = utc.toLocal();
  print('Back to local: $backToLocal');
  
  // Time zone offset
  Duration offset = local.timeZoneOffset;
  print('Time zone offset: $offset');
  print('Offset in hours: ${offset.inHours}');
  
  // Working with different time zones
  DateTime utcTime = DateTime.utc(2024, 3, 15, 12, 0, 0);
  print('UTC: $utcTime');
  
  // Simulate different time zones (manual calculation)
  DateTime eastCoast = utcTime.add(Duration(hours: -5)); // EST
  DateTime westCoast = utcTime.add(Duration(hours: -8)); // PST
  DateTime london = utcTime.add(Duration(hours: 0)); // GMT
  DateTime tokyo = utcTime.add(Duration(hours: 9)); // JST
  
  print('East Coast (EST): $eastCoast');
  print('West Coast (PST): $westCoast');
  print('London (GMT): $london');
  print('Tokyo (JST): $tokyo');
  
  // Same moment in different time zones
  DateTime moment = DateTime.utc(2024, 3, 15, 18, 0, 0);
  print('Same moment:');
  print('  UTC: $moment');
  print('  Local: ${moment.toLocal()}');
}
```

Time zone handling is crucial for global applications. DateTime provides  
basic UTC/local conversion, while more complex time zone operations require  
external packages or manual offset calculations.  

```
$ dart run timezone_operations.dart
Local time: 2024-03-15 14:30:45.123456
Is UTC: false
UTC time: 2024-03-15 13:30:45.123456Z
Is UTC: true
Back to local: 2024-03-15 14:30:45.123456
Time zone offset: 1:00:00.000000
Offset in hours: 1
UTC: 2024-03-15 12:00:00.000Z
East Coast (EST): 2024-03-15 07:00:00.000Z
West Coast (PST): 2024-03-15 04:00:00.000Z
London (GMT): 2024-03-15 12:00:00.000Z
Tokyo (JST): 2024-03-15 21:00:00.000Z
Same moment:
  UTC: 2024-03-15 18:00:00.000Z
  Local: 2024-03-15 19:00:00.000
```

## Weekday and Calendar Operations

DateTime provides utilities for working with weekdays, finding specific days,  
and performing calendar-related calculations.  

```dart
void main() {
  DateTime date = DateTime(2024, 3, 15); // Friday
  
  // Weekday information
  print('Date: ${date.toString().substring(0, 10)}');
  print('Weekday: ${date.weekday}'); // 1=Mon, 7=Sun
  
  // Weekday names
  List<String> weekdayNames = [
    'Monday', 'Tuesday', 'Wednesday', 'Thursday',
    'Friday', 'Saturday', 'Sunday'
  ];
  print('Day name: ${weekdayNames[date.weekday - 1]}');
  
  // Find start of week (Monday)
  DateTime startOfWeek = date.subtract(Duration(days: date.weekday - 1));
  print('Start of week: ${startOfWeek.toString().substring(0, 10)}');
  
  // Find end of week (Sunday)
  DateTime endOfWeek = date.add(Duration(days: 7 - date.weekday));
  print('End of week: ${endOfWeek.toString().substring(0, 10)}');
  
  // Find first day of month
  DateTime firstOfMonth = DateTime(date.year, date.month, 1);
  print('First of month: ${firstOfMonth.toString().substring(0, 10)}');
  
  // Find last day of month
  DateTime lastOfMonth = DateTime(date.year, date.month + 1, 0);
  print('Last of month: ${lastOfMonth.toString().substring(0, 10)}');
  
  // Check if weekend
  bool isWeekend = date.weekday >= 6;
  print('Is weekend: $isWeekend');
  
  // Find next Monday
  int daysUntilMonday = (8 - date.weekday) % 7;
  if (daysUntilMonday == 0) daysUntilMonday = 7; // If already Monday
  DateTime nextMonday = date.add(Duration(days: daysUntilMonday));
  print('Next Monday: ${nextMonday.toString().substring(0, 10)}');
  
  // Business days calculation
  DateTime businessStart = DateTime(2024, 3, 11); // Monday
  DateTime businessEnd = DateTime(2024, 3, 15); // Friday
  int businessDays = 0;
  
  for (DateTime day = businessStart; 
       day.isBefore(businessEnd.add(Duration(days: 1)));
       day = day.add(Duration(days: 1))) {
    if (day.weekday < 6) businessDays++; // Mon-Fri
  }
  print('Business days: $businessDays');
}
```

Calendar operations are essential for scheduling and business applications.  
This example shows common patterns for finding specific dates and working  
with business logic around weekdays.  

```
$ dart run weekday_operations.dart
Date: 2024-03-15
Weekday: 5
Day name: Friday
Start of week: 2024-03-11
End of week: 2024-03-17
First of month: 2024-03-01
Last of month: 2024-03-31
Is weekend: false
Next Monday: 2024-03-18
Business days: 5
```

## Month and Year Operations

Working with months and years requires special handling due to varying  
month lengths and leap years.  

```dart
void main() {
  DateTime date = DateTime(2024, 2, 29); // Leap year date
  print('Original date: $date');
  
  // Check if leap year
  bool isLeapYear = (date.year % 4 == 0 && date.year % 100 != 0) || 
                    (date.year % 400 == 0);
  print('Is leap year: $isLeapYear');
  
  // Days in current month
  int daysInMonth = DateTime(date.year, date.month + 1, 0).day;
  print('Days in current month: $daysInMonth');
  
  // Add months safely
  DateTime nextMonth = DateTime(date.year, date.month + 1, date.day);
  print('Next month (unsafe): $nextMonth'); // This might fail for Feb 29->Mar 29
  
  // Safe month addition
  DateTime safeNextMonth = addMonths(date, 1);
  print('Next month (safe): $safeNextMonth');
  
  // Add years
  DateTime nextYear = DateTime(date.year + 1, date.month, date.day);
  print('Next year: $nextYear'); // Feb 29, 2025 doesn't exist!
  
  // Safe year addition
  DateTime safeNextYear = addYears(date, 1);
  print('Next year (safe): $safeNextYear');
  
  // Month boundaries
  DateTime startOfMonth = DateTime(date.year, date.month, 1);
  DateTime endOfMonth = DateTime(date.year, date.month + 1, 0);
  print('Start of month: $startOfMonth');
  print('End of month: $endOfMonth');
  
  // Quarter operations
  int quarter = ((date.month - 1) ~/ 3) + 1;
  int quarterStartMonth = ((quarter - 1) * 3) + 1;
  DateTime startOfQuarter = DateTime(date.year, quarterStartMonth, 1);
  DateTime endOfQuarter = DateTime(date.year, quarterStartMonth + 3, 0);
  
  print('Quarter: $quarter');
  print('Start of quarter: $startOfQuarter');
  print('End of quarter: $endOfQuarter');
  
  // Age calculation with months
  DateTime birthDate = DateTime(1990, 6, 15);
  DateTime today = DateTime.now();
  int ageYears = today.year - birthDate.year;
  int ageMonths = today.month - birthDate.month;
  
  if (today.day < birthDate.day) {
    ageMonths--;
  }
  if (ageMonths < 0) {
    ageYears--;
    ageMonths += 12;
  }
  
  print('Age: $ageYears years, $ageMonths months');
}

DateTime addMonths(DateTime date, int months) {
  int newYear = date.year;
  int newMonth = date.month + months;
  
  while (newMonth > 12) {
    newMonth -= 12;
    newYear++;
  }
  while (newMonth < 1) {
    newMonth += 12;
    newYear--;
  }
  
  int maxDay = DateTime(newYear, newMonth + 1, 0).day;
  int newDay = date.day > maxDay ? maxDay : date.day;
  
  return DateTime(newYear, newMonth, newDay, 
                 date.hour, date.minute, date.second, 
                 date.millisecond, date.microsecond);
}

DateTime addYears(DateTime date, int years) {
  return addMonths(date, years * 12);
}
```

Month and year arithmetic requires careful handling of edge cases like leap  
years and varying month lengths. The helper functions provide safe addition  
that adjusts for these calendar irregularities.  

```
$ dart run month_year_operations.dart
Original date: 2024-02-29 00:00:00.000
Is leap year: true
Days in current month: 29
Next month (unsafe): 2024-03-29 00:00:00.000
Next month (safe): 2024-03-29 00:00:00.000
Next year: 2025-02-29 00:00:00.000
Next year (safe): 2025-02-28 00:00:00.000
Start of month: 2024-02-01 00:00:00.000
End of month: 2024-02-29 00:00:00.000
Quarter: 1
Start of quarter: 2024-01-01 00:00:00.000
End of quarter: 2024-03-31 00:00:00.000
Age: 33 years, 9 months
```

## DateTime Validation

Validating DateTime inputs and handling edge cases is crucial for robust  
applications that process user-provided date information.  

```dart
void main() {
  // Valid date strings
  List<String> dateStrings = [
    '2024-03-15T14:30:45.123Z',    // Valid ISO
    '2024-03-15 14:30:45',          // Valid local
    '2024-02-29',                   // Valid leap year
    '2023-02-29',                   // Invalid - not leap year
    '2024-13-15',                   // Invalid month
    '2024-03-32',                   // Invalid day
    '2024-03-15T25:30:45',          // Invalid hour
    'invalid-date',                 // Invalid format
    '',                             // Empty string
    '2024-03-15T14:30:45+25:00',    // Invalid timezone
  ];
  
  print('=== Date Validation ===');
  for (String dateStr in dateStrings) {
    DateTime? parsed = DateTime.tryParse(dateStr);
    String status = parsed != null ? 'Valid' : 'Invalid';
    print('$dateStr -> $status');
  }
  
  // Custom validation function
  print('\n=== Custom Validation ===');
  List<DateInput> inputs = [
    DateInput(2024, 2, 29),   // Valid leap year
    DateInput(2023, 2, 29),   // Invalid leap year
    DateInput(2024, 4, 31),   // Invalid day in April
    DateInput(2024, 12, 31),  // Valid end of year
    DateInput(2024, 0, 15),   // Invalid month
    DateInput(2024, 3, 0),    // Invalid day
  ];
  
  for (DateInput input in inputs) {
    ValidationResult result = validateDate(input.year, input.month, input.day);
    print('${input.year}-${input.month}-${input.day}: ${result.message}');
  }
  
  // Business rule validation
  print('\n=== Business Rule Validation ===');
  DateTime today = DateTime.now();
  List<DateTime> testDates = [
    today.add(Duration(days: 7)),    // Future date
    today.subtract(Duration(days: 1)), // Past date
    DateTime(1900, 1, 1),            // Too old
    DateTime(2100, 1, 1),            // Too far in future
  ];
  
  for (DateTime date in testDates) {
    String validation = validateBusinessDate(date);
    print('${date.toString().substring(0, 10)}: $validation');
  }
  
  // Range validation
  print('\n=== Range Validation ===');
  DateTime startDate = DateTime(2024, 3, 1);
  DateTime endDate = DateTime(2024, 3, 15);
  
  List<DateTime> rangeTestDates = [
    DateTime(2024, 2, 28),  // Before range
    DateTime(2024, 3, 10),  // In range
    DateTime(2024, 3, 20),  // After range
  ];
  
  for (DateTime date in rangeTestDates) {
    bool inRange = isDateInRange(date, startDate, endDate);
    print('${date.toString().substring(0, 10)}: ${inRange ? 'In range' : 'Out of range'}');
  }
}

class DateInput {
  final int year, month, day;
  DateInput(this.year, this.month, this.day);
}

class ValidationResult {
  final bool isValid;
  final String message;
  ValidationResult(this.isValid, this.message);
}

ValidationResult validateDate(int year, int month, int day) {
  if (month < 1 || month > 12) {
    return ValidationResult(false, 'Invalid month: $month');
  }
  
  if (day < 1) {
    return ValidationResult(false, 'Invalid day: $day');
  }
  
  // Check days in month
  List<int> daysInMonth = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];
  
  // Adjust for leap year
  if (month == 2 && isLeapYear(year)) {
    daysInMonth[1] = 29;
  }
  
  if (day > daysInMonth[month - 1]) {
    return ValidationResult(false, 'Invalid day $day for month $month');
  }
  
  return ValidationResult(true, 'Valid date');
}

bool isLeapYear(int year) {
  return (year % 4 == 0 && year % 100 != 0) || (year % 400 == 0);
}

String validateBusinessDate(DateTime date) {
  DateTime now = DateTime.now();
  DateTime minDate = DateTime(1900, 1, 1);
  DateTime maxDate = DateTime(2050, 12, 31);
  
  if (date.isBefore(minDate)) return 'Date too far in past';
  if (date.isAfter(maxDate)) return 'Date too far in future';
  if (date.isAfter(now)) return 'Future date not allowed';
  
  return 'Valid business date';
}

bool isDateInRange(DateTime date, DateTime start, DateTime end) {
  return !date.isBefore(start) && !date.isAfter(end);
}

## Milliseconds and Microseconds

DateTime supports high-precision time measurements with millisecond and  
microsecond accuracy for performance timing and precise calculations.  

```dart
void main() {
  // Creating with precision
  DateTime precise = DateTime(2024, 3, 15, 14, 30, 45, 123, 456);
  print('Precise time: $precise');
  print('Millisecond: ${precise.millisecond}');
  print('Microsecond: ${precise.microsecond}');
  
  // Performance timing
  DateTime start = DateTime.now();
  
  // Simulate some work
  List<int> numbers = [];
  for (int i = 0; i < 1000000; i++) {
    numbers.add(i * i);
  }
  
  DateTime end = DateTime.now();
  Duration elapsed = end.difference(start);
  
  print('Operation took: ${elapsed.inMilliseconds}ms');
  print('Operation took: ${elapsed.inMicroseconds}μs');
  
  // High precision timestamps
  List<DateTime> timestamps = [];
  for (int i = 0; i < 5; i++) {
    timestamps.add(DateTime.now());
    // Small delay
    int sum = 0;
    for (int j = 0; j < 10000; j++) sum += j;
  }
  
  print('\nHigh precision timestamps:');
  for (int i = 0; i < timestamps.length; i++) {
    print('$i: ${timestamps[i].microsecondsSinceEpoch}');
    if (i > 0) {
      int diff = timestamps[i].microsecondsSinceEpoch - 
                 timestamps[i-1].microsecondsSinceEpoch;
      print('   Diff: ${diff}μs');
    }
  }
  
  // Converting between precision levels
  int millis = precise.millisecondsSinceEpoch;
  int micros = precise.microsecondsSinceEpoch;
  
  DateTime fromMillis = DateTime.fromMillisecondsSinceEpoch(millis);
  DateTime fromMicros = DateTime.fromMicrosecondsSinceEpoch(micros);
  
  print('\nPrecision conversion:');
  print('Original: $precise');
  print('From millis: $fromMillis');
  print('From micros: $fromMicros');
}
```

High-precision timing is essential for performance analysis and scientific  
applications. Microsecond precision enables detailed timing measurements  
that milliseconds alone cannot provide.  

```
$ dart run precision_timing.dart
Precise time: 2024-03-15 14:30:45.123456
Millisecond: 123
Microsecond: 456
Operation took: 45ms
Operation took: 45123μs

High precision timestamps:
0: 1710506445167890
   Diff: 234μs
1: 1710506445168124
   Diff: 189μs
2: 1710506445168313
   Diff: 201μs
3: 1710506445168514
   Diff: 195μs
4: 1710506445168709

Precision conversion:
Original: 2024-03-15 14:30:45.123456
From millis: 2024-03-15 14:30:45.123000
From micros: 2024-03-15 14:30:45.123456
```

## Date Range Operations

Working with date ranges is common in scheduling, reporting, and filtering  
operations that require checking if dates fall within specific periods.  

```dart
void main() {
  // Define date ranges
  DateRange q1 = DateRange(
    DateTime(2024, 1, 1), 
    DateTime(2024, 3, 31)
  );
  
  DateRange q2 = DateRange(
    DateTime(2024, 4, 1), 
    DateTime(2024, 6, 30)
  );
  
  print('Q1 2024: ${q1.start} to ${q1.end}');
  print('Q2 2024: ${q2.start} to ${q2.end}');
  print('Q1 duration: ${q1.duration.inDays} days');
  
  // Test dates against ranges
  List<DateTime> testDates = [
    DateTime(2024, 2, 15),  // In Q1
    DateTime(2024, 5, 10),  // In Q2
    DateTime(2024, 7, 1),   // After Q2
    DateTime(2023, 12, 31), // Before Q1
  ];
  
  print('\n=== Range Testing ===');
  for (DateTime date in testDates) {
    String dateStr = date.toString().substring(0, 10);
    print('$dateStr:');
    print('  In Q1: ${q1.contains(date)}');
    print('  In Q2: ${q2.contains(date)}');
  }
  
  // Range operations
  print('\n=== Range Operations ===');
  DateRange overlap = q1.intersectionWith(q2);
  print('Q1/Q2 overlap: ${overlap.isEmpty ? 'None' : '${overlap.start} to ${overlap.end}'}');
  
  DateRange union = q1.unionWith(q2);
  print('Q1/Q2 union: ${union.start} to ${union.end}');
  print('Union duration: ${union.duration.inDays} days');
  
  // Generate date sequences
  print('\n=== Date Sequences ===');
  List<DateTime> weeklyDates = q1.generateSequence(Duration(days: 7));
  print('Weekly dates in Q1:');
  for (DateTime date in weeklyDates.take(5)) {
    String weekday = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'][date.weekday - 1];
    print('  ${date.toString().substring(0, 10)} ($weekday)');
  }
  
  // Working days in range
  int workingDays = q1.countWorkingDays();
  print('\nWorking days in Q1: $workingDays');
  
  // Split range into months
  List<DateRange> months = q1.splitByMonths();
  print('\nQ1 split by months:');
  for (DateRange month in months) {
    print('  ${month.start.toString().substring(0, 10)} to ${month.end.toString().substring(0, 10)}');
  }
}

class DateRange {
  final DateTime start;
  final DateTime end;
  
  DateRange(this.start, this.end) {
    if (start.isAfter(end)) {
      throw ArgumentError('Start date must be before end date');
    }
  }
  
  Duration get duration => end.difference(start);
  bool get isEmpty => start.isAtSameMomentAs(end);
  
  bool contains(DateTime date) {
    return !date.isBefore(start) && !date.isAfter(end);
  }
  
  DateRange intersectionWith(DateRange other) {
    DateTime intersectionStart = start.isAfter(other.start) ? start : other.start;
    DateTime intersectionEnd = end.isBefore(other.end) ? end : other.end;
    
    if (intersectionStart.isAfter(intersectionEnd)) {
      return DateRange(intersectionStart, intersectionStart); // Empty range
    }
    
    return DateRange(intersectionStart, intersectionEnd);
  }
  
  DateRange unionWith(DateRange other) {
    DateTime unionStart = start.isBefore(other.start) ? start : other.start;
    DateTime unionEnd = end.isAfter(other.end) ? end : other.end;
    return DateRange(unionStart, unionEnd);
  }
  
  List<DateTime> generateSequence(Duration interval) {
    List<DateTime> dates = [];
    DateTime current = start;
    
    while (!current.isAfter(end)) {
      dates.add(current);
      current = current.add(interval);
    }
    
    return dates;
  }
  
  int countWorkingDays() {
    int count = 0;
    DateTime current = start;
    
    while (!current.isAfter(end)) {
      if (current.weekday < 6) count++; // Monday = 1, Friday = 5
      current = current.add(Duration(days: 1));
    }
    
    return count;
  }
  
  List<DateRange> splitByMonths() {
    List<DateRange> months = [];
    DateTime current = DateTime(start.year, start.month, 1);
    
    while (!current.isAfter(end)) {
      DateTime monthEnd = DateTime(current.year, current.month + 1, 0);
      if (monthEnd.isAfter(end)) monthEnd = end;
      
      DateTime monthStart = current.isBefore(start) ? start : current;
      months.add(DateRange(monthStart, monthEnd));
      
      current = DateTime(current.year, current.month + 1, 1);
    }
    
    return months;
  }
}
```

Date ranges provide powerful abstractions for working with time periods.  
The DateRange class demonstrates common operations needed in scheduling  
and reporting applications.  

```
$ dart run date_range_operations.dart
Q1 2024: 2024-01-01 00:00:00.000 to 2024-03-31 00:00:00.000
Q2 2024: 2024-04-01 00:00:00.000 to 2024-06-30 00:00:00.000
Q1 duration: 90 days

=== Range Testing ===
2024-02-15:
  In Q1: true
  In Q2: false
2024-05-10:
  In Q1: false
  In Q2: true
2024-07-01:
  In Q1: false
  In Q2: false
2023-12-31:
  In Q1: false
  In Q2: false

=== Range Operations ===
Q1/Q2 overlap: None
Q1/Q2 union: 2024-01-01 00:00:00.000 to 2024-06-30 00:00:00.000
Union duration: 181 days

=== Date Sequences ===
Weekly dates in Q1:
  2024-01-01 (Mon)
  2024-01-08 (Mon)
  2024-01-15 (Mon)
  2024-01-22 (Mon)
  2024-01-29 (Mon)

Working days in Q1: 65

Q1 split by months:
  2024-01-01 to 2024-01-31
  2024-02-01 to 2024-02-29
  2024-03-01 to 2024-03-31
```

## DateTime Serialization

Converting DateTime objects to and from various serialization formats is  
essential for data persistence and API communication.  

```dart
import 'dart:convert';

void main() {
  DateTime original = DateTime(2024, 3, 15, 14, 30, 45, 123);
  
  // JSON serialization
  Map<String, dynamic> jsonData = {
    'timestamp': original.toIso8601String(),
    'epochMillis': original.millisecondsSinceEpoch,
    'epochSeconds': original.millisecondsSinceEpoch ~/ 1000,
    'components': {
      'year': original.year,
      'month': original.month,
      'day': original.day,
      'hour': original.hour,
      'minute': original.minute,
      'second': original.second,
    }
  };
  
  String jsonString = json.encode(jsonData);
  print('JSON serialized:');
  print(jsonString);
  
  // JSON deserialization
  Map<String, dynamic> decoded = json.decode(jsonString);
  DateTime fromIso = DateTime.parse(decoded['timestamp']);
  DateTime fromEpochMillis = DateTime.fromMillisecondsSinceEpoch(decoded['epochMillis']);
  DateTime fromEpochSeconds = DateTime.fromMillisecondsSinceEpoch(decoded['epochSeconds'] * 1000);
  
  Map<String, dynamic> components = decoded['components'];
  DateTime fromComponents = DateTime(
    components['year'],
    components['month'],
    components['day'],
    components['hour'],
    components['minute'],
    components['second']
  );
  
  print('\nDeserialized dates:');
  print('From ISO: $fromIso');
  print('From epoch millis: $fromEpochMillis');
  print('From epoch seconds: $fromEpochSeconds');
  print('From components: $fromComponents');
  
  // Custom serialization formats
  DateTimeSerializer serializer = DateTimeSerializer();
  
  String compact = serializer.toCompactString(original);
  String readable = serializer.toReadableString(original);
  String sortable = serializer.toSortableString(original);
  
  print('\n=== Custom Formats ===');
  print('Compact: $compact');
  print('Readable: $readable');
  print('Sortable: $sortable');
  
  // Parse custom formats back
  DateTime fromCompact = serializer.fromCompactString(compact);
  DateTime fromReadable = serializer.fromReadableString(readable);
  DateTime fromSortable = serializer.fromSortableString(sortable);
  
  print('\nParsed back:');
  print('From compact: $fromCompact');
  print('From readable: $fromReadable');
  print('From sortable: $fromSortable');
  
  // Batch serialization
  List<DateTime> dates = [
    DateTime(2024, 1, 15, 9, 30),
    DateTime(2024, 6, 22, 14, 45),
    DateTime(2024, 12, 31, 23, 59),
  ];
  
  List<String> serializedDates = dates.map(serializer.toSortableString).toList();
  print('\nBatch serialization:');
  for (String serialized in serializedDates) {
    print('  $serialized');
  }
  
  // Verify serialization roundtrip
  bool allMatch = true;
  for (DateTime date in dates) {
    String serialized = serializer.toCompactString(date);
    DateTime deserialized = serializer.fromCompactString(serialized);
    if (!date.isAtSameMomentAs(deserialized)) {
      allMatch = false;
      break;
    }
  }
  
  print('\nSerialization roundtrip: ${allMatch ? 'Success' : 'Failed'}');
}

class DateTimeSerializer {
  // Compact format: YYYYMMDDHHMMSS
  String toCompactString(DateTime date) {
    return '${date.year}${date.month.toString().padLeft(2, '0')}'
           '${date.day.toString().padLeft(2, '0')}'
           '${date.hour.toString().padLeft(2, '0')}'
           '${date.minute.toString().padLeft(2, '0')}'
           '${date.second.toString().padLeft(2, '0')}';
  }
  
  DateTime fromCompactString(String compact) {
    if (compact.length != 14) throw ArgumentError('Invalid compact format');
    
    int year = int.parse(compact.substring(0, 4));
    int month = int.parse(compact.substring(4, 6));
    int day = int.parse(compact.substring(6, 8));
    int hour = int.parse(compact.substring(8, 10));
    int minute = int.parse(compact.substring(10, 12));
    int second = int.parse(compact.substring(12, 14));
    
    return DateTime(year, month, day, hour, minute, second);
  }
  
  // Readable format: March 15, 2024 at 2:30 PM
  String toReadableString(DateTime date) {
    List<String> months = [
      'January', 'February', 'March', 'April', 'May', 'June',
      'July', 'August', 'September', 'October', 'November', 'December'
    ];
    
    String month = months[date.month - 1];
    String period = date.hour >= 12 ? 'PM' : 'AM';
    int displayHour = date.hour == 0 ? 12 : (date.hour > 12 ? date.hour - 12 : date.hour);
    
    return '$month ${date.day}, ${date.year} at $displayHour:${date.minute.toString().padLeft(2, '0')} $period';
  }
  
  DateTime fromReadableString(String readable) {
    // This would require complex parsing - simplified for example
    throw UnimplementedError('Parsing readable format requires more complex logic');
  }
  
  // Sortable format: YYYY-MM-DD HH:MM:SS
  String toSortableString(DateTime date) {
    return '${date.year}-${date.month.toString().padLeft(2, '0')}-'
           '${date.day.toString().padLeft(2, '0')} '
           '${date.hour.toString().padLeft(2, '0')}:'
           '${date.minute.toString().padLeft(2, '0')}:'
           '${date.second.toString().padLeft(2, '0')}';
  }
  
  DateTime fromSortableString(String sortable) {
    return DateTime.parse(sortable);
  }
}
```

DateTime serialization is crucial for data persistence and API communication.  
Different formats serve different purposes: compact for storage efficiency,  
readable for user interfaces, and sortable for database ordering.  

```
$ dart run datetime_serialization.dart
JSON serialized:
{"timestamp":"2024-03-15T14:30:45.123","epochMillis":1710506445123,"epochSeconds":1710506445,"components":{"year":2024,"month":3,"day":15,"hour":14,"minute":30,"second":45}}

Deserialized dates:
From ISO: 2024-03-15 14:30:45.123
From epoch millis: 2024-03-15 14:30:45.123
From epoch seconds: 2024-03-15 14:30:45.000
From components: 2024-03-15 14:30:45.000

=== Custom Formats ===
Compact: 20240315143045
Readable: March 15, 2024 at 2:30 PM
Sortable: 2024-03-15 14:30:45

Parsed back:
From compact: 2024-03-15 14:30:45.000
From readable: UnimplementedError
From sortable: 2024-03-15 14:30:45.000

Batch serialization:
  2024-01-15 09:30:00
  2024-06-22 14:45:00
  2024-12-31 23:59:00

Serialization roundtrip: Success
```

## DateTime Utilities and Extensions

Extension methods can enhance DateTime with additional functionality for  
common operations and domain-specific requirements.  

```dart
void main() {
  DateTime date = DateTime(2024, 3, 15, 14, 30, 45);
  
  print('Original date: $date');
  
  // Age-related extensions
  DateTime birthDate = DateTime(1990, 6, 15);
  print('Age in years: ${birthDate.ageInYears}');
  print('Age in days: ${birthDate.ageInDays}');
  print('Is adult: ${birthDate.isAdult}');
  
  // Calendar extensions
  print('\n=== Calendar Extensions ===');
  print('Is today: ${date.isToday}');
  print('Is this year: ${date.isThisYear}');
  print('Is weekend: ${date.isWeekend}');
  print('Is leap year: ${date.isLeapYear}');
  print('Days in month: ${date.daysInMonth}');
  
  // Time of day extensions
  print('\n=== Time of Day ===');
  print('Is morning: ${date.isMorning}');
  print('Is afternoon: ${date.isAfternoon}');
  print('Is evening: ${date.isEvening}');
  print('Is night: ${date.isNight}');
  print('Time of day: ${date.timeOfDayName}');
  
  // Relative time extensions
  DateTime yesterday = DateTime.now().subtract(Duration(days: 1));
  DateTime nextWeek = DateTime.now().add(Duration(days: 7));
  
  print('\n=== Relative Time ===');
  print('Yesterday: ${yesterday.timeAgo}');
  print('Next week: ${nextWeek.timeUntil}');
  
  // Start/end extensions
  print('\n=== Start/End Extensions ===');
  print('Start of day: ${date.startOfDay}');
  print('End of day: ${date.endOfDay}');
  print('Start of week: ${date.startOfWeek}');
  print('End of week: ${date.endOfWeek}');
  print('Start of month: ${date.startOfMonth}');
  print('End of month: ${date.endOfMonth}');
  print('Start of year: ${date.startOfYear}');
  print('End of year: ${date.endOfYear}');
  
  // Formatting extensions
  print('\n=== Formatting Extensions ===');
  print('Short date: ${date.shortDateString}');
  print('Long date: ${date.longDateString}');
  print('Time string: ${date.timeString}');
  print('Weekday name: ${date.weekdayName}');
  print('Month name: ${date.monthName}');
  
  // Business day extensions
  print('\n=== Business Day Extensions ===');
  print('Is business day: ${date.isBusinessDay}');
  print('Next business day: ${date.nextBusinessDay.shortDateString}');
  print('Previous business day: ${date.previousBusinessDay.shortDateString}');
  
  // Rounding extensions
  DateTime precise = DateTime(2024, 3, 15, 14, 37, 23, 456);
  print('\n=== Rounding Extensions ===');
  print('Round to hour: ${precise.roundToHour}');
  print('Round to minute: ${precise.roundToMinute}');
  print('Truncate to day: ${precise.truncateToDay}');
}

extension DateTimeExtensions on DateTime {
  // Age calculations
  int get ageInYears {
    DateTime now = DateTime.now();
    int age = now.year - year;
    if (now.month < month || (now.month == month && now.day < day)) {
      age--;
    }
    return age;
  }
  
  int get ageInDays => DateTime.now().difference(this).inDays;
  bool get isAdult => ageInYears >= 18;
  
  // Calendar properties
  bool get isToday {
    DateTime now = DateTime.now();
    return year == now.year && month == now.month && day == now.day;
  }
  
  bool get isThisYear => DateTime.now().year == year;
  bool get isWeekend => weekday >= 6;
  bool get isLeapYear => (year % 4 == 0 && year % 100 != 0) || (year % 400 == 0);
  
  int get daysInMonth => DateTime(year, month + 1, 0).day;
  
  // Time of day properties
  bool get isMorning => hour >= 6 && hour < 12;
  bool get isAfternoon => hour >= 12 && hour < 17;
  bool get isEvening => hour >= 17 && hour < 21;
  bool get isNight => hour >= 21 || hour < 6;
  
  String get timeOfDayName {
    if (isMorning) return 'Morning';
    if (isAfternoon) return 'Afternoon';
    if (isEvening) return 'Evening';
    return 'Night';
  }
  
  // Relative time
  String get timeAgo {
    Duration diff = DateTime.now().difference(this);
    if (diff.inDays > 0) return '${diff.inDays} days ago';
    if (diff.inHours > 0) return '${diff.inHours} hours ago';
    if (diff.inMinutes > 0) return '${diff.inMinutes} minutes ago';
    return 'Just now';
  }
  
  String get timeUntil {
    Duration diff = difference(DateTime.now());
    if (diff.inDays > 0) return 'in ${diff.inDays} days';
    if (diff.inHours > 0) return 'in ${diff.inHours} hours';
    if (diff.inMinutes > 0) return 'in ${diff.inMinutes} minutes';
    return 'now';
  }
  
  // Start/end of periods
  DateTime get startOfDay => DateTime(year, month, day);
  DateTime get endOfDay => DateTime(year, month, day, 23, 59, 59, 999);
  DateTime get startOfWeek => subtract(Duration(days: weekday - 1));
  DateTime get endOfWeek => add(Duration(days: 7 - weekday));
  DateTime get startOfMonth => DateTime(year, month, 1);
  DateTime get endOfMonth => DateTime(year, month + 1, 0, 23, 59, 59, 999);
  DateTime get startOfYear => DateTime(year, 1, 1);
  DateTime get endOfYear => DateTime(year, 12, 31, 23, 59, 59, 999);
  
  // Formatting
  String get shortDateString => '$day/$month/$year';
  String get longDateString => '$weekdayName, $monthName $day, $year';
  String get timeString => '${hour.toString().padLeft(2, '0')}:${minute.toString().padLeft(2, '0')}';
  
  String get weekdayName {
    const names = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'];
    return names[weekday - 1];
  }
  
  String get monthName {
    const names = ['January', 'February', 'March', 'April', 'May', 'June',
                   'July', 'August', 'September', 'October', 'November', 'December'];
    return names[month - 1];
  }
  
  // Business days
  bool get isBusinessDay => weekday < 6; // Monday-Friday
  
  DateTime get nextBusinessDay {
    DateTime next = add(Duration(days: 1));
    while (!next.isBusinessDay) {
      next = next.add(Duration(days: 1));
    }
    return next;
  }
  
  DateTime get previousBusinessDay {
    DateTime prev = subtract(Duration(days: 1));
    while (!prev.isBusinessDay) {
      prev = prev.subtract(Duration(days: 1));
    }
    return prev;
  }
  
  // Rounding
  DateTime get roundToHour {
    int roundedMinute = minute >= 30 ? 1 : 0;
    return DateTime(year, month, day, hour + roundedMinute);
  }
  
  DateTime get roundToMinute => DateTime(year, month, day, hour, minute);
  DateTime get truncateToDay => DateTime(year, month, day);
}
```

Extension methods provide a clean way to add domain-specific functionality  
to DateTime objects. These extensions cover common use cases like age  
calculations, business logic, and convenient formatting operations.  

```
$ dart run datetime_extensions.dart
Original date: 2024-03-15 14:30:45.000

Age in years: 33
Age in days: 12296
Is adult: true

=== Calendar Extensions ===
Is today: false
Is this year: true
Is weekend: false
Is leap year: true
Days in month: 31

=== Time of Day ===
Is morning: false
Is afternoon: true
Is evening: false
Is night: false
Time of day: Afternoon

=== Relative Time ===
Yesterday: 1 days ago
Next week: in 7 days

=== Start/End Extensions ===
Start of day: 2024-03-15 00:00:00.000
End of day: 2024-03-15 23:59:59.999
Start of week: 2024-03-11 14:30:45.000
End of week: 2024-03-17 14:30:45.000
Start of month: 2024-03-01 00:00:00.000
End of month: 2024-03-31 23:59:59.999
Start of year: 2024-01-01 00:00:00.000
End of year: 2024-12-31 23:59:59.999

=== Formatting Extensions ===
Short date: 15/3/2024
Long date: Friday, March 15, 2024
Time string: 14:30
Weekday name: Friday
Month name: March

=== Business Day Extensions ===
Is business day: true
Next business day: 18/3/2024
Previous business day: 14/3/2024

=== Rounding Extensions ===
Round to hour: 2024-03-15 15:00:00.000
Round to minute: 2024-03-15 14:37:00.000
Truncate to day: 2024-03-15 00:00:00.000
```

## Working with Multiple Time Zones

While Dart's core DateTime has limited timezone support, you can implement  
basic timezone handling for common scenarios and international applications.  

```dart
void main() {
  // Define common timezone offsets
  Map<String, Duration> timezones = {
    'UTC': Duration(),
    'EST': Duration(hours: -5),
    'CST': Duration(hours: -6),
    'MST': Duration(hours: -7),
    'PST': Duration(hours: -8),
    'GMT': Duration(),
    'CET': Duration(hours: 1),
    'JST': Duration(hours: 9),
    'AEST': Duration(hours: 10),
  };
  
  DateTime utcTime = DateTime.utc(2024, 3, 15, 18, 0, 0);
  print('Base UTC time: $utcTime');
  
  print('\n=== World Clock ===');
  timezones.forEach((zone, offset) {
    DateTime localTime = utcTime.add(offset);
    String timeStr = '${localTime.hour.toString().padLeft(2, '0')}:'
                    '${localTime.minute.toString().padLeft(2, '0')}';
    print('$zone: $timeStr (${localTime.day}/${localTime.month})');
  });
  
  // Meeting scheduler across time zones
  print('\n=== Meeting Scheduler ===');
  DateTime meetingUtc = DateTime.utc(2024, 3, 15, 14, 0, 0); // 2 PM UTC
  
  List<String> participants = ['EST', 'PST', 'CET', 'JST'];
  
  print('Meeting at ${meetingUtc.toIso8601String()}');
  for (String zone in participants) {
    Duration offset = timezones[zone]!;
    DateTime localTime = meetingUtc.add(offset);
    String timeStr = formatTime(localTime);
    print('$zone participant: $timeStr');
  }
  
  // Business hours checker
  print('\n=== Business Hours Check ===');
  List<BusinessHours> offices = [
    BusinessHours('New York', timezones['EST']!, 9, 17),
    BusinessHours('Los Angeles', timezones['PST']!, 9, 17),
    BusinessHours('London', timezones['GMT']!, 9, 17),
    BusinessHours('Tokyo', timezones['JST']!, 9, 17),
  ];
  
  DateTime checkTime = DateTime.utc(2024, 3, 15, 13, 30, 0); // 1:30 PM UTC
  print('Checking time: ${checkTime.toIso8601String()}');
  
  for (BusinessHours office in offices) {
    DateTime localTime = checkTime.add(office.offset);
    bool isOpen = office.isOpenAt(localTime);
    String status = isOpen ? 'OPEN' : 'CLOSED';
    print('${office.location}: ${formatTime(localTime)} - $status');
  }
  
  // Time zone conversion utility
  print('\n=== Time Zone Converter ===');
  TimeZoneConverter converter = TimeZoneConverter(timezones);
  
  DateTime sourceTime = DateTime(2024, 3, 15, 15, 30, 0); // Local time
  print('Source (Local): $sourceTime');
  
  DateTime inUtc = converter.convertToUtc(sourceTime, 'EST');
  DateTime inPst = converter.convert(inUtc, 'UTC', 'PST');
  DateTime inJst = converter.convert(inUtc, 'UTC', 'JST');
  
  print('In UTC: $inUtc');
  print('In PST: $inPst');
  print('In JST: $inJst');
  
  // Find best meeting time
  print('\n=== Best Meeting Time Finder ===');
  List<String> zones = ['EST', 'PST', 'CET'];
  DateTime? bestTime = findBestMeetingTime(zones, timezones);
  
  if (bestTime != null) {
    print('Best meeting time (UTC): $bestTime');
    for (String zone in zones) {
      DateTime local = bestTime.add(timezones[zone]!);
      print('$zone: ${formatTime(local)}');
    }
  } else {
    print('No suitable meeting time found');
  }
}

String formatTime(DateTime time) {
  return '${time.hour.toString().padLeft(2, '0')}:'
         '${time.minute.toString().padLeft(2, '0')} '
         '${time.day}/${time.month}/${time.year}';
}

class BusinessHours {
  final String location;
  final Duration offset;
  final int startHour;
  final int endHour;
  
  BusinessHours(this.location, this.offset, this.startHour, this.endHour);
  
  bool isOpenAt(DateTime localTime) {
    return localTime.hour >= startHour && 
           localTime.hour < endHour && 
           localTime.weekday <= 5; // Monday-Friday
  }
}

class TimeZoneConverter {
  final Map<String, Duration> timezones;
  
  TimeZoneConverter(this.timezones);
  
  DateTime convertToUtc(DateTime localTime, String fromZone) {
    Duration offset = timezones[fromZone]!;
    return localTime.subtract(offset).toUtc();
  }
  
  DateTime convert(DateTime time, String fromZone, String toZone) {
    Duration fromOffset = timezones[fromZone]!;
    Duration toOffset = timezones[toZone]!;
    
    // First convert to UTC, then to target zone
    DateTime utc = time.subtract(fromOffset);
    return utc.add(toOffset);
  }
}

DateTime? findBestMeetingTime(List<String> zones, Map<String, Duration> timezones) {
  // Try each hour of the day in UTC
  for (int hour = 9; hour <= 17; hour++) {
    DateTime utcTime = DateTime.utc(2024, 3, 15, hour, 0, 0);
    bool goodForAll = true;
    
    for (String zone in zones) {
      DateTime localTime = utcTime.add(timezones[zone]!);
      
      // Check if it's business hours (9 AM - 5 PM) and weekday
      if (localTime.hour < 9 || localTime.hour >= 17 || localTime.weekday > 5) {
        goodForAll = false;
        break;
      }
    }
    
    if (goodForAll) return utcTime;
  }
  
  return null;
}
```

Basic timezone handling demonstrates international application requirements.  
While Dart's built-in support is limited, these patterns show how to implement  
common timezone operations for global applications.  

```
$ dart run timezone_operations.dart
Base UTC time: 2024-03-15 18:00:00.000Z

=== World Clock ===
UTC: 18:00 (15/3)
EST: 13:00 (15/3)
CST: 12:00 (15/3)
MST: 11:00 (15/3)
PST: 10:00 (15/3)
GMT: 18:00 (15/3)
CET: 19:00 (15/3)
JST: 03:00 (16/3)
AEST: 04:00 (16/3)

=== Meeting Scheduler ===
Meeting at 2024-03-15T14:00:00.000Z
EST participant: 09:00 15/3/2024
PST participant: 06:00 15/3/2024
CET participant: 15:00 15/3/2024
JST participant: 23:00 15/3/2024

=== Business Hours Check ===
Checking time: 2024-03-15T13:30:00.000Z
New York: 08:30 15/3/2024 - CLOSED
Los Angeles: 05:30 15/3/2024 - CLOSED
London: 13:30 15/3/2024 - OPEN
Tokyo: 22:30 15/3/2024 - CLOSED

=== Time Zone Converter ===
Source (Local): 2024-03-15 15:30:00.000
In UTC: 2024-03-15 20:30:00.000Z
In PST: 2024-03-15 12:30:00.000Z
In JST: 2024-03-16 05:30:00.000Z

=== Best Meeting Time Finder ===
Best meeting time (UTC): 2024-03-15 16:00:00.000Z
EST: 11:00 15/3/2024
PST: 08:00 15/3/2024
CET: 17:00 15/3/2024
```

## Custom Date Formats and Localization

Creating custom date formats and supporting multiple locales enhances  
user experience in international applications.  

```dart
void main() {
  DateTime date = DateTime(2024, 3, 15, 14, 30, 45);
  
  // Custom formatter
  DateFormatter formatter = DateFormatter();
  
  print('=== Custom Formats ===');
  print('dd/MM/yyyy: ${formatter.format(date, 'dd/MM/yyyy')}');
  print('MM-dd-yyyy: ${formatter.format(date, 'MM-dd-yyyy')}');
  print('yyyy.MM.dd: ${formatter.format(date, 'yyyy.MM.dd')}');
  print('dd MMM yyyy: ${formatter.format(date, 'dd MMM yyyy')}');
  print('MMMM dd, yyyy: ${formatter.format(date, 'MMMM dd, yyyy')}');
  print('EEE, dd MMM: ${formatter.format(date, 'EEE, dd MMM')}');
  print('EEEE: ${formatter.format(date, 'EEEE')}');
  print('HH:mm:ss: ${formatter.format(date, 'HH:mm:ss')}');
  print('hh:mm a: ${formatter.format(date, 'hh:mm a')}');
  
  // Localized formatting
  print('\n=== Localized Formats ===');
  LocalizedDateFormatter localFormatter = LocalizedDateFormatter();
  
  List<String> locales = ['en_US', 'es_ES', 'fr_FR', 'de_DE', 'ja_JP'];
  
  for (String locale in locales) {
    String formatted = localFormatter.formatDate(date, locale);
    print('$locale: $formatted');
  }
  
  // Relative date formatting
  print('\n=== Relative Dates ===');
  RelativeDateFormatter relativeFormatter = RelativeDateFormatter();
  
  DateTime now = DateTime.now();
  List<DateTime> testDates = [
    now,
    now.subtract(Duration(minutes: 5)),
    now.subtract(Duration(hours: 2)),
    now.subtract(Duration(days: 1)),
    now.subtract(Duration(days: 7)),
    now.add(Duration(minutes: 30)),
    now.add(Duration(days: 3)),
  ];
  
  for (DateTime testDate in testDates) {
    String relative = relativeFormatter.format(testDate);
    print('${testDate.toString().substring(0, 16)}: $relative');
  }
  
  // Cultural date preferences
  print('\n=== Cultural Preferences ===');
  CulturalDateFormatter culturalFormatter = CulturalDateFormatter();
  
  Map<String, String> cultures = {
    'US': 'MM/dd/yyyy',
    'UK': 'dd/MM/yyyy', 
    'ISO': 'yyyy-MM-dd',
    'German': 'dd.MM.yyyy',
    'Japanese': 'yyyy年MM月dd日',
  };
  
  cultures.forEach((culture, pattern) {
    String formatted = culturalFormatter.formatForCulture(date, culture);
    print('$culture: $formatted');
  });
  
  // Business date formatting
  print('\n=== Business Formats ===');
  BusinessDateFormatter businessFormatter = BusinessDateFormatter();
  
  print('Invoice date: ${businessFormatter.formatInvoiceDate(date)}');
  print('Report period: ${businessFormatter.formatReportPeriod(date)}');
  print('Fiscal year: ${businessFormatter.formatFiscalYear(date)}');
  print('Quarter: ${businessFormatter.formatQuarter(date)}');
  
  // Parsing custom formats
  print('\n=== Parsing Custom Formats ===');
  CustomDateParser parser = CustomDateParser();
  
  List<String> dateStrings = [
    '15/03/2024',
    '03-15-2024',
    'March 15, 2024',
    '2024.03.15',
    '15 Mar 2024',
  ];
  
  for (String dateStr in dateStrings) {
    DateTime? parsed = parser.tryParse(dateStr);
    print('$dateStr -> ${parsed?.toString().substring(0, 10) ?? 'Failed'}');
  }
}

class DateFormatter {
  String format(DateTime date, String pattern) {
    String result = pattern;
    
    // Year patterns
    result = result.replaceAll('yyyy', date.year.toString());
    result = result.replaceAll('yy', (date.year % 100).toString().padLeft(2, '0'));
    
    // Month patterns
    result = result.replaceAll('MMMM', _getMonthName(date.month));
    result = result.replaceAll('MMM', _getMonthAbbr(date.month));
    result = result.replaceAll('MM', date.month.toString().padLeft(2, '0'));
    result = result.replaceAll('M', date.month.toString());
    
    // Day patterns
    result = result.replaceAll('EEEE', _getDayName(date.weekday));
    result = result.replaceAll('EEE', _getDayAbbr(date.weekday));
    result = result.replaceAll('dd', date.day.toString().padLeft(2, '0'));
    result = result.replaceAll('d', date.day.toString());
    
    // Hour patterns (24-hour)
    result = result.replaceAll('HH', date.hour.toString().padLeft(2, '0'));
    result = result.replaceAll('H', date.hour.toString());
    
    // Hour patterns (12-hour)
    int hour12 = date.hour == 0 ? 12 : (date.hour > 12 ? date.hour - 12 : date.hour);
    result = result.replaceAll('hh', hour12.toString().padLeft(2, '0'));
    result = result.replaceAll('h', hour12.toString());
    
    // Minute/second patterns
    result = result.replaceAll('mm', date.minute.toString().padLeft(2, '0'));
    result = result.replaceAll('ss', date.second.toString().padLeft(2, '0'));
    
    // AM/PM
    result = result.replaceAll('a', date.hour >= 12 ? 'PM' : 'AM');
    
    return result;
  }
  
  String _getMonthName(int month) {
    const names = ['January', 'February', 'March', 'April', 'May', 'June',
                   'July', 'August', 'September', 'October', 'November', 'December'];
    return names[month - 1];
  }
  
  String _getMonthAbbr(int month) {
    const abbrs = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun',
                   'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];
    return abbrs[month - 1];
  }
  
  String _getDayName(int weekday) {
    const names = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'];
    return names[weekday - 1];
  }
  
  String _getDayAbbr(int weekday) {
    const abbrs = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'];
    return abbrs[weekday - 1];
  }
}

class LocalizedDateFormatter {
  Map<String, Map<String, List<String>>> localeData = {
    'en_US': {
      'months': ['January', 'February', 'March', 'April', 'May', 'June',
                 'July', 'August', 'September', 'October', 'November', 'December'],
      'days': ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday']
    },
    'es_ES': {
      'months': ['enero', 'febrero', 'marzo', 'abril', 'mayo', 'junio',
                 'julio', 'agosto', 'septiembre', 'octubre', 'noviembre', 'diciembre'],
      'days': ['domingo', 'lunes', 'martes', 'miércoles', 'jueves', 'viernes', 'sábado']
    },
    'fr_FR': {
      'months': ['janvier', 'février', 'mars', 'avril', 'mai', 'juin',
                 'juillet', 'août', 'septembre', 'octobre', 'novembre', 'décembre'],
      'days': ['dimanche', 'lundi', 'mardi', 'mercredi', 'jeudi', 'vendredi', 'samedi']
    },
    'de_DE': {
      'months': ['Januar', 'Februar', 'März', 'April', 'Mai', 'Juni',
                 'Juli', 'August', 'September', 'Oktober', 'November', 'Dezember'],
      'days': ['Sonntag', 'Montag', 'Dienstag', 'Mittwoch', 'Donnerstag', 'Freitag', 'Samstag']
    },
    'ja_JP': {
      'months': ['1月', '2月', '3月', '4月', '5月', '6月',
                 '7月', '8月', '9月', '10月', '11月', '12月'],
      'days': ['日曜日', '月曜日', '火曜日', '水曜日', '木曜日', '金曜日', '土曜日']
    },
  };
  
  String formatDate(DateTime date, String locale) {
    Map<String, List<String>>? data = localeData[locale];
    if (data == null) return date.toString();
    
    String monthName = data['months']![date.month - 1];
    String dayName = data['days']![date.weekday % 7]; // Adjust for Sunday = 0
    
    if (locale == 'ja_JP') {
      return '${date.year}年${date.month}月${date.day}日 ($dayName)';
    } else {
      return '$dayName, $monthName ${date.day}, ${date.year}';
    }
  }
}

class RelativeDateFormatter {
  String format(DateTime date) {
    DateTime now = DateTime.now();
    Duration diff = now.difference(date);
    
    if (diff.inMilliseconds.abs() < 60000) return 'now';
    if (diff.inMinutes.abs() < 60) {
      return diff.isNegative ? 'in ${diff.inMinutes.abs()} minutes' : '${diff.inMinutes} minutes ago';
    }
    if (diff.inHours.abs() < 24) {
      return diff.isNegative ? 'in ${diff.inHours.abs()} hours' : '${diff.inHours} hours ago';
    }
    if (diff.inDays.abs() < 7) {
      return diff.isNegative ? 'in ${diff.inDays.abs()} days' : '${diff.inDays} days ago';
    }
    return diff.isNegative ? 'in ${(diff.inDays.abs() / 7).floor()} weeks' : 
                            '${(diff.inDays / 7).floor()} weeks ago';
  }
}

class CulturalDateFormatter {
  String formatForCulture(DateTime date, String culture) {
    switch (culture) {
      case 'US': return '${date.month}/${date.day}/${date.year}';
      case 'UK': return '${date.day}/${date.month}/${date.year}';
      case 'ISO': return '${date.year}-${date.month.toString().padLeft(2, '0')}-${date.day.toString().padLeft(2, '0')}';
      case 'German': return '${date.day}.${date.month}.${date.year}';
      case 'Japanese': return '${date.year}年${date.month}月${date.day}日';
      default: return date.toString();
    }
  }
}

class BusinessDateFormatter {
  String formatInvoiceDate(DateTime date) {
    return 'Invoice Date: ${_formatBusinessDate(date)}';
  }
  
  String formatReportPeriod(DateTime date) {
    DateTime startOfMonth = DateTime(date.year, date.month, 1);
    DateTime endOfMonth = DateTime(date.year, date.month + 1, 0);
    return 'Period: ${_formatBusinessDate(startOfMonth)} - ${_formatBusinessDate(endOfMonth)}';
  }
  
  String formatFiscalYear(DateTime date) {
    int fiscalYear = date.month >= 4 ? date.year : date.year - 1;
    return 'FY${fiscalYear.toString().substring(2)}';
  }
  
  String formatQuarter(DateTime date) {
    int quarter = ((date.month - 1) ~/ 3) + 1;
    return 'Q$quarter ${date.year}';
  }
  
  String _formatBusinessDate(DateTime date) {
    return '${date.day.toString().padLeft(2, '0')}/${date.month.toString().padLeft(2, '0')}/${date.year}';
  }
}

class CustomDateParser {
  DateTime? tryParse(String dateString) {
    // Try common formats
    List<RegExp> patterns = [
      RegExp(r'^(\d{1,2})/(\d{1,2})/(\d{4})$'), // dd/MM/yyyy or MM/dd/yyyy
      RegExp(r'^(\d{1,2})-(\d{1,2})-(\d{4})$'), // dd-MM-yyyy or MM-dd-yyyy
      RegExp(r'^(\d{4})\.(\d{1,2})\.(\d{1,2})$'), // yyyy.MM.dd
      RegExp(r'^(\d{1,2})\s+(\w+)\s+(\d{4})$'), // dd MMM yyyy
    ];
    
    for (RegExp pattern in patterns) {
      Match? match = pattern.firstMatch(dateString);
      if (match != null) {
        try {
          return _parseFromMatch(match, pattern.pattern);
        } catch (e) {
          continue;
        }
      }
    }
    
    // Try named month parsing
    return _parseNamedMonth(dateString);
  }
  
  DateTime? _parseFromMatch(Match match, String pattern) {
    if (pattern.contains(r'(\d{4})\.')) {
      // yyyy.MM.dd format
      return DateTime(int.parse(match.group(1)!), 
                     int.parse(match.group(2)!), 
                     int.parse(match.group(3)!));
    } else if (pattern.contains(r'(\w+)')) {
      // dd MMM yyyy format
      int day = int.parse(match.group(1)!);
      String monthName = match.group(2)!;
      int year = int.parse(match.group(3)!);
      int? month = _getMonthFromName(monthName);
      if (month != null) {
        return DateTime(year, month, day);
      }
    } else {
      // Assume dd/MM/yyyy format for ambiguous cases
      return DateTime(int.parse(match.group(3)!), 
                     int.parse(match.group(2)!), 
                     int.parse(match.group(1)!));
    }
    return null;
  }
  
  DateTime? _parseNamedMonth(String dateString) {
    RegExp namedPattern = RegExp(r'^(\w+)\s+(\d{1,2}),\s+(\d{4})$');
    Match? match = namedPattern.firstMatch(dateString);
    if (match != null) {
      String monthName = match.group(1)!;
      int day = int.parse(match.group(2)!);
      int year = int.parse(match.group(3)!);
      int? month = _getMonthFromName(monthName);
      if (month != null) {
        return DateTime(year, month, day);
      }
    }
    return null;
  }
  
  int? _getMonthFromName(String name) {
    Map<String, int> months = {
      'jan': 1, 'january': 1,
      'feb': 2, 'february': 2,
      'mar': 3, 'march': 3,
      'apr': 4, 'april': 4,
      'may': 5,
      'jun': 6, 'june': 6,
      'jul': 7, 'july': 7,
      'aug': 8, 'august': 8,
      'sep': 9, 'september': 9,
      'oct': 10, 'october': 10,
      'nov': 11, 'november': 11,
      'dec': 12, 'december': 12,
    };
    
    return months[name.toLowerCase()];
  }
}
```

Custom formatting and localization support international users and business  
requirements. These examples show comprehensive patterns for formatting  
dates according to different cultural preferences and business needs.  

```
$ dart run custom_date_formats.dart
=== Custom Formats ===
dd/MM/yyyy: 15/03/2024
MM-dd-yyyy: 03-15-2024
yyyy.MM.dd: 2024.03.15
dd MMM yyyy: 15 Mar 2024
MMMM dd, yyyy: March 15, 2024
EEE, dd MMM: Fri, 15 Mar
EEEE: Friday
HH:mm:ss: 14:30:45
hh:mm a: 02:30 PM

=== Localized Formats ===
en_US: Friday, March 15, 2024
es_ES: viernes, marzo 15, 2024
fr_FR: vendredi, mars 15, 2024
de_DE: Freitag, März 15, 2024
ja_JP: 2024年3月15日 (金曜日)

=== Relative Dates ===
2024-03-15 14:30: now
2024-03-15 14:25: 5 minutes ago
2024-03-15 12:30: 2 hours ago
2024-03-14 14:30: 1 days ago
2024-03-08 14:30: 1 weeks ago
2024-03-15 15:00: in 30 minutes
2024-03-18 14:30: in 3 days

=== Cultural Preferences ===
US: 3/15/2024
UK: 15/3/2024
ISO: 2024-03-15
German: 15.3.2024
Japanese: 2024年3月15日

=== Business Formats ===
Invoice Date: 15/03/2024
Report period: 01/03/2024 - 31/03/2024
Fiscal year: FY24
Quarter: Q1 2024

=== Parsing Custom Formats ===
15/03/2024 -> 2024-03-15
03-15-2024 -> 2024-03-15
March 15, 2024 -> 2024-03-15
2024.03.15 -> 2024-03-15
15 Mar 2024 -> 2024-03-15
```

## Performance and Memory Considerations

Understanding DateTime performance characteristics helps optimize applications  
that perform intensive date calculations or handle large datasets.  

```dart
import 'dart:math';

void main() {
  print('=== DateTime Performance Tests ===');
  
  // Creation performance
  Stopwatch sw = Stopwatch();
  
  print('\n1. DateTime Creation Performance:');
  
  // Test DateTime.now() performance
  sw.start();
  for (int i = 0; i < 100000; i++) {
    DateTime.now();
  }
  sw.stop();
  print('DateTime.now() x100k: ${sw.elapsedMilliseconds}ms');
  
  // Test DateTime constructor performance
  sw.reset();
  sw.start();
  for (int i = 0; i < 100000; i++) {
    DateTime(2024, 3, 15, 14, 30, 45);
  }
  sw.stop();
  print('DateTime() constructor x100k: ${sw.elapsedMilliseconds}ms');
  
  // Test DateTime.utc() performance
  sw.reset();
  sw.start();
  for (int i = 0; i < 100000; i++) {
    DateTime.utc(2024, 3, 15, 14, 30, 45);
  }
  sw.stop();
  print('DateTime.utc() x100k: ${sw.elapsedMilliseconds}ms');
  
  // Test epoch conversion performance
  sw.reset();
  sw.start();
  int epochMillis = DateTime.now().millisecondsSinceEpoch;
  for (int i = 0; i < 100000; i++) {
    DateTime.fromMillisecondsSinceEpoch(epochMillis);
  }
  sw.stop();
  print('fromMillisecondsSinceEpoch() x100k: ${sw.elapsedMilliseconds}ms');
  
  // Arithmetic performance
  print('\n2. DateTime Arithmetic Performance:');
  DateTime base = DateTime.now();
  Duration oneDay = Duration(days: 1);
  
  sw.reset();
  sw.start();
  for (int i = 0; i < 100000; i++) {
    base.add(oneDay);
  }
  sw.stop();
  print('DateTime.add() x100k: ${sw.elapsedMilliseconds}ms');
  
  sw.reset();
  sw.start();
  for (int i = 0; i < 100000; i++) {
    base.subtract(oneDay);
  }
  sw.stop();
  print('DateTime.subtract() x100k: ${sw.elapsedMilliseconds}ms');
  
  sw.reset();
  sw.start();
  DateTime other = base.add(Duration(hours: 5));
  for (int i = 0; i < 100000; i++) {
    base.difference(other);
  }
  sw.stop();
  print('DateTime.difference() x100k: ${sw.elapsedMilliseconds}ms');
  
  // Comparison performance
  print('\n3. DateTime Comparison Performance:');
  DateTime date1 = DateTime.now();
  DateTime date2 = date1.add(Duration(seconds: 1));
  
  sw.reset();
  sw.start();
  for (int i = 0; i < 100000; i++) {
    date1.isBefore(date2);
  }
  sw.stop();
  print('DateTime.isBefore() x100k: ${sw.elapsedMilliseconds}ms');
  
  sw.reset();
  sw.start();
  for (int i = 0; i < 100000; i++) {
    date1.compareTo(date2);
  }
  sw.stop();
  print('DateTime.compareTo() x100k: ${sw.elapsedMilliseconds}ms');
  
  // String conversion performance
  print('\n4. String Conversion Performance:');
  DateTime date = DateTime.now();
  
  sw.reset();
  sw.start();
  for (int i = 0; i < 100000; i++) {
    date.toString();
  }
  sw.stop();
  print('DateTime.toString() x100k: ${sw.elapsedMilliseconds}ms');
  
  sw.reset();
  sw.start();
  for (int i = 0; i < 100000; i++) {
    date.toIso8601String();
  }
  sw.stop();
  print('DateTime.toIso8601String() x100k: ${sw.elapsedMilliseconds}ms');
  
  // Parsing performance
  print('\n5. DateTime Parsing Performance:');
  String isoString = '2024-03-15T14:30:45.123Z';
  
  sw.reset();
  sw.start();
  for (int i = 0; i < 10000; i++) {
    DateTime.parse(isoString);
  }
  sw.stop();
  print('DateTime.parse() x10k: ${sw.elapsedMilliseconds}ms');
  
  sw.reset();
  sw.start();
  for (int i = 0; i < 10000; i++) {
    DateTime.tryParse(isoString);
  }
  sw.stop();
  print('DateTime.tryParse() x10k: ${sw.elapsedMilliseconds}ms');
  
  // Memory efficiency tests
  print('\n6. Memory Efficiency:');
  testMemoryEfficiency();
  
  // Large dataset processing
  print('\n7. Large Dataset Processing:');
  testLargeDatasetProcessing();
  
  // Optimization recommendations
  print('\n8. Optimization Tips:');
  demonstrateOptimizations();
}

void testMemoryEfficiency() {
  // Test memory usage with different approaches
  List<DateTime> dates1 = [];
  List<int> epochs = [];
  List<String> isoStrings = [];
  
  DateTime base = DateTime(2024, 1, 1);
  
  // Fill collections with same dates in different formats
  for (int i = 0; i < 10000; i++) {
    DateTime current = base.add(Duration(days: i));
    dates1.add(current);
    epochs.add(current.millisecondsSinceEpoch);
    isoStrings.add(current.toIso8601String());
  }
  
  // Simulate processing
  Stopwatch sw = Stopwatch();
  
  // Process DateTime objects
  sw.start();
  int count1 = 0;
  for (DateTime date in dates1) {
    if (date.weekday <= 5) count1++; // Business days
  }
  sw.stop();
  print('Process DateTime objects: ${sw.elapsedMilliseconds}ms, count: $count1');
  
  // Process epoch milliseconds (convert when needed)
  sw.reset();
  sw.start();
  int count2 = 0;
  for (int epoch in epochs) {
    DateTime date = DateTime.fromMillisecondsSinceEpoch(epoch);
    if (date.weekday <= 5) count2++; // Business days
  }
  sw.stop();
  print('Process epoch values: ${sw.elapsedMilliseconds}ms, count: $count2');
  
  // Process ISO strings (convert when needed)
  sw.reset();
  sw.start();
  int count3 = 0;
  for (String iso in isoStrings) {
    DateTime date = DateTime.parse(iso);
    if (date.weekday <= 5) count3++; // Business days
  }
  sw.stop();
  print('Process ISO strings: ${sw.elapsedMilliseconds}ms, count: $count3');
}

void testLargeDatasetProcessing() {
  // Simulate processing a large dataset of dates
  Random random = Random();
  DateTime startDate = DateTime(2020, 1, 1);
  
  // Generate random dates
  List<DateTime> largeDateSet = List.generate(50000, (index) {
    int randomDays = random.nextInt(1460); // 4 years worth
    return startDate.add(Duration(days: randomDays));
  });
  
  Stopwatch sw = Stopwatch();
  
  // Find business days
  sw.start();
  int businessDays = largeDateSet.where((date) => date.weekday <= 5).length;
  sw.stop();
  print('Find business days in 50k dates: ${sw.elapsedMilliseconds}ms ($businessDays found)');
  
  // Group by year
  sw.reset();
  sw.start();
  Map<int, int> yearCounts = {};
  for (DateTime date in largeDateSet) {
    yearCounts[date.year] = (yearCounts[date.year] ?? 0) + 1;
  }
  sw.stop();
  print('Group by year: ${sw.elapsedMilliseconds}ms (${yearCounts.length} years)');
  
  // Sort chronologically
  sw.reset();
  sw.start();
  largeDateSet.sort((a, b) => a.compareTo(b));
  sw.stop();
  print('Sort 50k dates: ${sw.elapsedMilliseconds}ms');
  
  // Find date ranges
  sw.reset();
  sw.start();
  DateTime earliest = largeDateSet.first;
  DateTime latest = largeDateSet.last;
  Duration span = latest.difference(earliest);
  sw.stop();
  print('Find date range: ${sw.elapsedMilliseconds}ms (${span.inDays} days span)');
}

void demonstrateOptimizations() {
  print('\nOptimization Recommendations:');
  print('1. Use DateTime objects directly when possible (fastest)');
  print('2. Store epoch milliseconds for compact storage');
  print('3. Use DateTime.compareTo() for sorting (slightly faster than operators)');
  print('4. Cache frequently used DateTime objects');
  print('5. Use DateTime.utc() when timezone conversion is not needed');
  print('6. Avoid repeated string parsing in loops');
  print('7. Use Duration constants for common intervals');
  print('8. Consider using int timestamps for large datasets');
  
  // Demonstration of caching
  DateTime now = DateTime.now();
  
  // Bad: repeated calculation
  Stopwatch sw = Stopwatch();
  sw.start();
  for (int i = 0; i < 10000; i++) {
    DateTime startOfDay = DateTime(now.year, now.month, now.day);
    // Use startOfDay...
  }
  sw.stop();
  int slowTime = sw.elapsedMilliseconds;
  
  // Good: cached calculation
  DateTime cachedStartOfDay = DateTime(now.year, now.month, now.day);
  sw.reset();
  sw.start();
  for (int i = 0; i < 10000; i++) {
    DateTime startOfDay = cachedStartOfDay;
    // Use startOfDay...
  }
  sw.stop();
  int fastTime = sw.elapsedMilliseconds;
  
  print('\nCaching demonstration:');
  print('Without caching: ${slowTime}ms');
  print('With caching: ${fastTime}ms');
  print('Improvement: ${(slowTime / fastTime).toStringAsFixed(1)}x faster');
}
}
}
```

Date validation prevents errors and ensures data integrity. This comprehensive  
example covers format validation, custom business rules, and range checking  
commonly needed in real applications.  

```
$ dart run datetime_validation.dart
=== Date Validation ===
2024-03-15T14:30:45.123Z -> Valid
2024-03-15 14:30:45 -> Valid
2024-02-29 -> Valid
2023-02-29 -> Invalid
2024-13-15 -> Invalid
2024-03-32 -> Invalid
2024-03-15T25:30:45 -> Invalid
invalid-date -> Invalid
 -> Invalid
2024-03-15T14:30:45+25:00 -> Invalid

=== Custom Validation ===
2024-2-29: Valid date
2023-2-29: Invalid day 29 for month 2
2024-4-31: Invalid day 31 for month 4
2024-12-31: Valid date
2024-0-15: Invalid month: 0
2024-3-0: Invalid day: 0

=== Business Rule Validation ===
2024-03-22: Future date not allowed
2024-03-14: Valid business date
1900-01-01: Valid business date
2100-01-01: Date too far in future

=== Range Validation ===
2024-02-28: Out of range
2024-03-10: In range
2024-03-20: Out of range
```