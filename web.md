# Dart Web Development

Dart provides comprehensive support for web development through the dart:html  
library and modern web APIs. Originally designed as a JavaScript alternative,  
Dart compiles to optimized JavaScript for browser deployment while offering  
strong typing, excellent tooling, and modern language features.  

Web development with Dart enables building interactive, performant applications  
that run in all modern browsers. The dart:html library provides direct access  
to DOM manipulation, event handling, and browser APIs, making it straightforward  
to create dynamic web experiences.  

This guide covers practical web development patterns including DOM manipulation,  
form handling, browser API integration, and best practices for building  
maintainable web applications with Dart.  

## Selecting DOM Elements

The dart:html library provides multiple methods for selecting elements from  
the DOM, similar to JavaScript's querySelector and getElement methods.  

```dart
import 'dart:html';

void main() {
  // Select element by ID
  var header = querySelector('#main-header');
  if (header != null) {
    print('Found header: ${header.text}');
  }
  
  // Select element by class
  var container = querySelector('.container');
  print('Container: ${container?.className}');
  
  // Select by tag name
  var firstParagraph = querySelector('p');
  print('First paragraph: ${firstParagraph?.text}');
  
  // Select all elements with a class
  var buttons = querySelectorAll('.btn');
  print('Found ${buttons.length} buttons');
  
  // Iterate through selected elements
  for (var button in buttons) {
    print('Button text: ${button.text}');
  }
  
  // Complex selector
  var navLinks = querySelectorAll('nav a.active');
  print('Active nav links: ${navLinks.length}');
  
  // Direct element access by ID
  var footer = document.getElementById('footer');
  print('Footer: ${footer?.text}');
}
```

The querySelector method returns the first matching element, while  
querySelectorAll returns all matches. Both support CSS selector syntax for  
flexible element selection. Always check for null since elements might not exist.  


## Modifying Element Styles

Dart provides direct access to element styles through the style property,  
enabling dynamic visual changes to web page elements.  

```dart
import 'dart:html';

void main() {
  // Get element
  var box = querySelector('#box');
  
  if (box != null) {
    // Set individual styles
    box.style.backgroundColor = 'blue';
    box.style.color = 'white';
    box.style.padding = '20px';
    box.style.borderRadius = '10px';
    
    // Set multiple styles
    box.style
      ..width = '200px'
      ..height = '200px'
      ..margin = '10px auto'
      ..display = 'flex'
      ..alignItems = 'center'
      ..justifyContent = 'center';
    
    // Toggle visibility
    box.style.display = 'none';  // Hide
    box.style.display = 'block';  // Show
    
    // Modify with transition
    box.style.transition = 'all 0.3s ease';
    box.style.transform = 'scale(1.1)';
    
    // Read computed styles
    var computedStyle = box.getComputedStyle();
    print('Current width: ${computedStyle.width}');
    print('Current background: ${computedStyle.backgroundColor}');
  }
  
  // Style multiple elements
  var cards = querySelectorAll('.card');
  for (var card in cards) {
    card.style
      ..boxShadow = '0 2px 4px rgba(0,0,0,0.1)'
      ..borderRadius = '8px'
      ..padding = '16px';
  }
}
```

Style modifications use camelCase property names (backgroundColor instead of  
background-color). The cascade operator (..) enables setting multiple properties  
efficiently. Changes apply immediately, triggering browser reflows when necessary.  

## Handling Click Events

Event handling in Dart uses streams, providing a powerful and type-safe approach  
to responding to user interactions.  

```dart
import 'dart:html';

void main() {
  // Simple click handler
  var button = querySelector('#myButton');
  button?.onClick.listen((event) {
    print('Button clicked!');
  });
  
  // Click with element modification
  var toggleBtn = querySelector('#toggleBtn');
  var content = querySelector('#content');
  
  toggleBtn?.onClick.listen((event) {
    if (content?.style.display == 'none') {
      content?.style.display = 'block';
      toggleBtn?.text = 'Hide Content';
    } else {
      content?.style.display = 'none';
      toggleBtn?.text = 'Show Content';
    }
  });
  
  // Click counter
  var counterBtn = querySelector('#counterBtn');
  var countDisplay = querySelector('#count');
  var count = 0;
  
  counterBtn?.onClick.listen((event) {
    count++;
    countDisplay?.text = count.toString();
  });
  
  // Click with event details
  var detailBtn = querySelector('#detailBtn');
  detailBtn?.onClick.listen((MouseEvent event) {
    print('Click at: ${event.client.x}, ${event.client.y}');
    print('Alt key: ${event.altKey}');
    print('Ctrl key: ${event.ctrlKey}');
    print('Target: ${event.target}');
  });
  
  // Multiple elements with same handler
  var items = querySelectorAll('.list-item');
  for (var item in items) {
    item.onClick.listen((event) {
      item.classes.toggle('selected');
    });
  }
  
  // Prevent default behavior
  var link = querySelector('#noFollow');
  link?.onClick.listen((event) {
    event.preventDefault();
    print('Link click prevented');
  });
}
```

Click events use the onClick stream property. The listen method registers  
callbacks that execute when events occur. Event objects provide details like  
mouse position and modifier keys. The preventDefault method stops default actions.  

## Creating and Removing Elements

Dynamic element creation and removal enables building interactive interfaces  
that respond to user actions and data changes.  

```dart
import 'dart:html';

void main() {
  // Create new element
  var newDiv = DivElement()
    ..id = 'dynamicDiv'
    ..className = 'box'
    ..text = 'Hello there!';
  
  // Append to body
  document.body?.append(newDiv);
  
  // Create with HTML
  var container = querySelector('#container');
  var paragraph = ParagraphElement()
    ..text = 'This is a new paragraph'
    ..style.color = 'blue';
  container?.append(paragraph);
  
  // Create multiple elements
  var list = UListElement();
  for (var i = 1; i <= 5; i++) {
    var item = LIElement()
      ..text = 'Item $i'
      ..className = 'list-item';
    list.append(item);
  }
  container?.append(list);
  
  // Create with inner HTML (use carefully)
  var htmlDiv = DivElement()
    ..innerHtml = '<strong>Bold text</strong> and <em>italic text</em>';
  container?.append(htmlDiv);
  
  // Remove element
  var removeBtn = querySelector('#removeBtn');
  removeBtn?.onClick.listen((event) {
    newDiv.remove();
    print('Element removed');
  });
  
  // Remove all children
  var clearBtn = querySelector('#clearBtn');
  clearBtn?.onClick.listen((event) {
    container?.children.clear();
    print('All children removed');
  });
  
  // Replace element
  var oldElement = querySelector('#oldElement');
  var newElement = DivElement()..text = 'New element';
  oldElement?.replaceWith(newElement);
  
  // Insert before
  var reference = querySelector('#reference');
  var inserted = DivElement()..text = 'Inserted before reference';
  reference?.parent?.insertBefore(inserted, reference);
}
```

Elements are created using type-specific constructors like DivElement or  
ParagraphElement. The cascade operator configures properties before insertion.  
The append method adds elements as children. Always validate parent elements  
exist before manipulation.  


## Handling Input Events

Input events enable real-time response to user typing and form field changes,  
essential for interactive forms and search features.  

```dart
import 'dart:html';

void main() {
  // Text input onChange
  var nameInput = querySelector('#name') as InputElement?;
  nameInput?.onChange.listen((event) {
    print('Name changed to: ${nameInput.value}');
  });
  
  // Real-time input with onInput
  var searchInput = querySelector('#search') as InputElement?;
  var searchResults = querySelector('#results');
  
  searchInput?.onInput.listen((event) {
    var query = searchInput.value ?? '';
    searchResults?.text = 'Searching for: $query';
    // Perform search logic here
  });
  
  // Checkbox handling
  var checkbox = querySelector('#agree') as CheckboxInputElement?;
  var submitBtn = querySelector('#submit') as ButtonElement?;
  
  checkbox?.onChange.listen((event) {
    submitBtn?.disabled = !(checkbox.checked ?? false);
  });
  
  // Radio button handling
  var radios = querySelectorAll('input[name="option"]');
  for (var radio in radios) {
    radio.onChange.listen((event) {
      var selected = (radio as RadioButtonInputElement).value;
      print('Selected option: $selected');
    });
  }
  
  // Select dropdown
  var dropdown = querySelector('#category') as SelectElement?;
  dropdown?.onChange.listen((event) {
    var selected = dropdown.selectedOptions;
    if (selected.isNotEmpty) {
      print('Category: ${selected.first.text}');
    }
  });
  
  // Textarea with character counter
  var textarea = querySelector('#message') as TextAreaElement?;
  var counter = querySelector('#charCount');
  
  textarea?.onInput.listen((event) {
    var length = textarea.value?.length ?? 0;
    counter?.text = '$length characters';
  });
  
  // Focus and blur events
  var emailInput = querySelector('#email') as InputElement?;
  
  emailInput?.onFocus.listen((event) {
    emailInput.style.borderColor = 'blue';
  });
  
  emailInput?.onBlur.listen((event) {
    emailInput.style.borderColor = 'gray';
    // Validate email
    var email = emailInput.value ?? '';
    if (email.isNotEmpty && !email.contains('@')) {
      print('Invalid email format');
    }
  });
}
```

Input events fire for different user interactions. onChange triggers when  
input loses focus after value changes. onInput fires immediately on each  
keystroke, ideal for real-time features. Always cast elements to specific  
input types for accessing specialized properties.  

## Form Validation and Submission

Form validation ensures data quality before submission, providing immediate  
feedback to users about input errors.  

```dart
import 'dart:html';

void main() {
  var form = querySelector('#registrationForm') as FormElement?;
  var nameInput = querySelector('#username') as InputElement?;
  var emailInput = querySelector('#email') as InputElement?;
  var passwordInput = querySelector('#password') as InputElement?;
  var errorDisplay = querySelector('#errors');
  
  form?.onSubmit.listen((event) {
    event.preventDefault();  // Stop default submission
    
    var errors = <String>[];
    
    // Validate username
    var username = nameInput?.value ?? '';
    if (username.isEmpty) {
      errors.add('Username is required');
    } else if (username.length < 3) {
      errors.add('Username must be at least 3 characters');
    }
    
    // Validate email
    var email = emailInput?.value ?? '';
    var emailRegex = RegExp(r'^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$');
    if (email.isEmpty) {
      errors.add('Email is required');
    } else if (!emailRegex.hasMatch(email)) {
      errors.add('Invalid email format');
    }
    
    // Validate password
    var password = passwordInput?.value ?? '';
    if (password.isEmpty) {
      errors.add('Password is required');
    } else if (password.length < 8) {
      errors.add('Password must be at least 8 characters');
    } else if (!password.contains(RegExp(r'[0-9]'))) {
      errors.add('Password must contain at least one number');
    }
    
    // Display errors or submit
    if (errors.isNotEmpty) {
      errorDisplay?.innerHtml = errors.map((e) => '<li>$e</li>').join();
      errorDisplay?.style.color = 'red';
    } else {
      errorDisplay?.text = 'Form is valid! Submitting...';
      errorDisplay?.style.color = 'green';
      // Submit form data here
      print('Submitting: $username, $email');
    }
  });
  
  // Real-time validation
  emailInput?.onBlur.listen((event) {
    var email = emailInput.value ?? '';
    var emailError = querySelector('#emailError');
    
    if (email.isNotEmpty && !email.contains('@')) {
      emailError?.text = 'Email must contain @';
      emailError?.style.color = 'red';
      emailInput.classes.add('invalid');
    } else {
      emailError?.text = '';
      emailInput.classes.remove('invalid');
    }
  });
  
  // Password strength indicator
  passwordInput?.onInput.listen((event) {
    var password = passwordInput.value ?? '';
    var strength = querySelector('#passwordStrength');
    
    if (password.length < 4) {
      strength?.text = 'Weak';
      strength?.style.color = 'red';
    } else if (password.length < 8) {
      strength?.text = 'Medium';
      strength?.style.color = 'orange';
    } else {
      strength?.text = 'Strong';
      strength?.style.color = 'green';
    }
  });
}
```

Form validation combines onSubmit prevention with custom validation logic.  
Regular expressions validate email formats and password requirements. Real-time  
validation provides immediate feedback as users type. Error messages guide users  
to correct input issues.  

## Dynamic Content Updates

Dynamic content updates enable building responsive interfaces that change based  
on user actions or data without full page reloads.  

```dart
import 'dart:html';

void main() {
  // Update text content
  var updateBtn = querySelector('#updateText');
  var display = querySelector('#display');
  
  updateBtn?.onClick.listen((event) {
    display?.text = 'Updated at ${DateTime.now()}';
  });
  
  // Update with HTML content
  var updateHtmlBtn = querySelector('#updateHtml');
  var htmlDisplay = querySelector('#htmlDisplay');
  
  updateHtmlBtn?.onClick.listen((event) {
    htmlDisplay?.innerHtml = '''
      <h3>Dynamic Content</h3>
      <p>Generated at <strong>${DateTime.now()}</strong></p>
      <ul>
        <li>Item 1</li>
        <li>Item 2</li>
        <li>Item 3</li>
      </ul>
    ''';
  });
  
  // Build list from data
  var loadDataBtn = querySelector('#loadData');
  var dataList = querySelector('#dataList');
  
  loadDataBtn?.onClick.listen((event) {
    var users = [
      {'name': 'Alice', 'age': 30},
      {'name': 'Bob', 'age': 25},
      {'name': 'Charlie', 'age': 35},
    ];
    
    dataList?.children.clear();
    
    for (var user in users) {
      var item = LIElement()
        ..text = '${user['name']} - ${user['age']} years old'
        ..className = 'user-item';
      dataList?.append(item);
    }
  });
  
  // Update with template
  var renderBtn = querySelector('#render');
  var output = querySelector('#output');
  
  renderBtn?.onClick.listen((event) {
    var data = {
      'title': 'User Dashboard',
      'count': 42,
      'items': ['Home', 'Profile', 'Settings']
    };
    
    var html = '''
      <div class="dashboard">
        <h2>${data['title']}</h2>
        <p>Total items: ${data['count']}</p>
        <nav>
          ${(data['items'] as List).map((item) => 
            '<a href="#">$item</a>').join(' | ')}
        </nav>
      </div>
    ''';
    
    output?.innerHtml = html;
  });
  
  // Incremental updates
  var addItemBtn = querySelector('#addItem');
  var itemList = querySelector('#itemList');
  var itemCount = 0;
  
  addItemBtn?.onClick.listen((event) {
    itemCount++;
    var newItem = DivElement()
      ..className = 'item'
      ..text = 'Item #$itemCount'
      ..onClick.listen((e) {
        e.currentTarget?.remove();
      });
    itemList?.append(newItem);
  });
}
```

Content updates use the text property for plain text and innerHtml for HTML  
markup. Clear existing content with children.clear() before rebuilding. Build  
elements programmatically for type safety or use innerHtml for convenience.  
Event listeners on dynamic elements enable interactive behaviors.  


## Client-Side Routing

Client-side routing enables single-page applications to handle navigation  
without full page reloads, using the browser's History API.  

```dart
import 'dart:html';

void main() {
  var contentArea = querySelector('#content');
  
  // Define routes
  Map<String, String Function()> routes = {
    '/': () => '<h2>Home Page</h2><p>Welcome to the home page!</p>',
    '/about': () => '<h2>About</h2><p>Learn more about us.</p>',
    '/contact': () => '<h2>Contact</h2><p>Get in touch with us.</p>',
    '/products': () => '''
      <h2>Products</h2>
      <ul>
        <li>Product A</li>
        <li>Product B</li>
        <li>Product C</li>
      </ul>
    ''',
  };
  
  // Route handler
  void navigate(String path) {
    var handler = routes[path];
    if (handler != null) {
      contentArea?.innerHtml = handler();
      window.history.pushState(null, '', path);
    } else {
      contentArea?.innerHtml = '<h2>404</h2><p>Page not found</p>';
    }
  }
  
  // Handle navigation links
  var navLinks = querySelectorAll('.nav-link');
  for (var link in navLinks) {
    link.onClick.listen((event) {
      event.preventDefault();
      var path = link.getAttribute('href') ?? '/';
      navigate(path);
    });
  }
  
  // Handle browser back/forward buttons
  window.onPopState.listen((event) {
    var path = window.location.pathname;
    var handler = routes[path];
    if (handler != null) {
      contentArea?.innerHtml = handler();
    }
  });
  
  // Load initial route
  navigate(window.location.pathname);
}
```

Client-side routing uses window.history.pushState to update URLs without  
reloading. Routes map paths to content generators. preventDefault stops  
default link behavior. onPopState handles browser navigation buttons.  
This creates seamless single-page application navigation.  

## Simple State Management

State management tracks application data and triggers UI updates when state  
changes, essential for reactive applications.  

```dart
import 'dart:html';

class AppState {
  int _count = 0;
  List<String> _items = [];
  final List<Function()> _listeners = [];
  
  int get count => _count;
  List<String> get items => List.unmodifiable(_items);
  
  void increment() {
    _count++;
    _notifyListeners();
  }
  
  void decrement() {
    if (_count > 0) {
      _count--;
      _notifyListeners();
    }
  }
  
  void addItem(String item) {
    _items.add(item);
    _notifyListeners();
  }
  
  void removeItem(int index) {
    if (index >= 0 && index < _items.length) {
      _items.removeAt(index);
      _notifyListeners();
    }
  }
  
  void subscribe(Function() listener) {
    _listeners.add(listener);
  }
  
  void _notifyListeners() {
    for (var listener in _listeners) {
      listener();
    }
  }
}

void main() {
  var state = AppState();
  
  // UI elements
  var countDisplay = querySelector('#countDisplay');
  var itemList = querySelector('#itemList');
  var itemInput = querySelector('#itemInput') as InputElement?;
  
  // Subscribe to state changes
  state.subscribe(() {
    // Update count display
    countDisplay?.text = 'Count: ${state.count}';
    
    // Update item list
    itemList?.children.clear();
    for (var i = 0; i < state.items.length; i++) {
      var item = state.items[i];
      var li = LIElement()
        ..text = item
        ..onClick.listen((event) {
          state.removeItem(i);
        });
      itemList?.append(li);
    }
  });
  
  // Connect UI actions to state
  querySelector('#increment')?.onClick.listen((event) {
    state.increment();
  });
  
  querySelector('#decrement')?.onClick.listen((event) {
    state.decrement();
  });
  
  querySelector('#addItem')?.onClick.listen((event) {
    var value = itemInput?.value ?? '';
    if (value.isNotEmpty) {
      state.addItem(value);
      itemInput?.value = '';
    }
  });
  
  // Initial render
  state.subscribe(() {});  // Trigger initial update
}
```

State management centralizes data and coordinates UI updates. The state class  
encapsulates data with private fields and public getters. Mutation methods  
notify subscribers of changes. UI components subscribe to state changes and  
update accordingly. This pattern scales to complex applications.  

## Working with Local Storage

Local storage provides persistent data storage in the browser, surviving  
page reloads and browser sessions.  

```dart
import 'dart:html';
import 'dart:convert';

void main() {
  // Save simple value
  var saveBtn = querySelector('#save');
  var nameInput = querySelector('#name') as InputElement?;
  
  saveBtn?.onClick.listen((event) {
    var name = nameInput?.value ?? '';
    window.localStorage['userName'] = name;
    print('Saved: $name');
  });
  
  // Load value
  var loadBtn = querySelector('#load');
  var display = querySelector('#display');
  
  loadBtn?.onClick.listen((event) {
    var name = window.localStorage['userName'];
    display?.text = name != null ? 'Hello there, $name!' : 'No name saved';
  });
  
  // Remove item
  var clearBtn = querySelector('#clear');
  clearBtn?.onClick.listen((event) {
    window.localStorage.remove('userName');
    display?.text = 'Storage cleared';
  });
  
  // Save complex data with JSON
  var saveSettingsBtn = querySelector('#saveSettings');
  
  saveSettingsBtn?.onClick.listen((event) {
    var settings = {
      'theme': 'dark',
      'fontSize': 16,
      'notifications': true,
      'lastVisit': DateTime.now().toIso8601String(),
    };
    
    window.localStorage['appSettings'] = jsonEncode(settings);
    print('Settings saved');
  });
  
  // Load complex data
  var loadSettingsBtn = querySelector('#loadSettings');
  var settingsDisplay = querySelector('#settings');
  
  loadSettingsBtn?.onClick.listen((event) {
    var settingsJson = window.localStorage['appSettings'];
    if (settingsJson != null) {
      var settings = jsonDecode(settingsJson);
      settingsDisplay?.text = '''
        Theme: ${settings['theme']}
        Font Size: ${settings['fontSize']}
        Notifications: ${settings['notifications']}
        Last Visit: ${settings['lastVisit']}
      ''';
    }
  });
  
  // Save array
  var todos = <String>[];
  var todoInput = querySelector('#todo') as InputElement?;
  var addTodoBtn = querySelector('#addTodo');
  var todoList = querySelector('#todoList');
  
  // Load existing todos
  var todosJson = window.localStorage['todos'];
  if (todosJson != null) {
    todos = List<String>.from(jsonDecode(todosJson));
  }
  
  void saveTodos() {
    window.localStorage['todos'] = jsonEncode(todos);
  }
  
  void renderTodos() {
    todoList?.children.clear();
    for (var i = 0; i < todos.length; i++) {
      var li = LIElement()
        ..text = todos[i]
        ..onClick.listen((event) {
          todos.removeAt(i);
          saveTodos();
          renderTodos();
        });
      todoList?.append(li);
    }
  }
  
  addTodoBtn?.onClick.listen((event) {
    var todo = todoInput?.value ?? '';
    if (todo.isNotEmpty) {
      todos.add(todo);
      saveTodos();
      renderTodos();
      todoInput?.value = '';
    }
  });
  
  renderTodos();  // Initial render
  
  // Check storage size
  var checkSizeBtn = querySelector('#checkSize');
  checkSizeBtn?.onClick.listen((event) {
    var totalSize = 0;
    for (var key in window.localStorage.keys) {
      var value = window.localStorage[key] ?? '';
      totalSize += key.length + value.length;
    }
    print('Approximate storage size: $totalSize characters');
  });
}
```

Local storage uses simple key-value pairs accessed through window.localStorage.  
Values must be strings, so use jsonEncode/jsonDecode for complex data. Data  
persists across sessions. Storage is limited (typically 5-10MB) and  
synchronous, so avoid storing large data sets.  


## Working with Cookies

Cookies store small amounts of data with expiration dates, useful for  
session management and user preferences.  

```dart
import 'dart:html';

void main() {
  // Set simple cookie
  var setCookieBtn = querySelector('#setCookie');
  setCookieBtn?.onClick.listen((event) {
    document.cookie = 'username=Alice';
    print('Cookie set');
  });
  
  // Set cookie with expiration
  var setExpiringBtn = querySelector('#setExpiring');
  setExpiringBtn?.onClick.listen((event) {
    var expires = DateTime.now().add(Duration(days: 7));
    document.cookie = 'session=abc123; expires=${expires.toUtc()}';
    print('Cookie set with expiration');
  });
  
  // Read cookies
  var readBtn = querySelector('#readCookies');
  var cookieDisplay = querySelector('#cookieDisplay');
  
  readBtn?.onClick.listen((event) {
    var cookies = document.cookie;
    cookieDisplay?.text = cookies.isNotEmpty ? cookies : 'No cookies found';
  });
  
  // Parse cookies into map
  Map<String, String> parseCookies() {
    var cookies = <String, String>{};
    var cookieString = document.cookie;
    
    if (cookieString.isNotEmpty) {
      for (var cookie in cookieString.split('; ')) {
        var parts = cookie.split('=');
        if (parts.length == 2) {
          cookies[parts[0]] = parts[1];
        }
      }
    }
    return cookies;
  }
  
  // Get specific cookie
  var getBtn = querySelector('#getCookie');
  getBtn?.onClick.listen((event) {
    var cookies = parseCookies();
    var username = cookies['username'];
    print('Username: ${username ?? 'Not found'}');
  });
  
  // Delete cookie
  var deleteBtn = querySelector('#deleteCookie');
  deleteBtn?.onClick.listen((event) {
    document.cookie = 'username=; expires=Thu, 01 Jan 1970 00:00:00 UTC';
    print('Cookie deleted');
  });
}
```

Cookies are set through document.cookie with key=value format. Set expiration  
with the expires attribute. Read all cookies from document.cookie as a string.  
Parse the string to access individual values. Delete by setting past expiration.  

## Geolocation API

The Geolocation API provides access to the user's geographic location with  
their permission, useful for location-based features.  

```dart
import 'dart:html';

void main() {
  var getLocationBtn = querySelector('#getLocation');
  var locationDisplay = querySelector('#locationDisplay');
  
  getLocationBtn?.onClick.listen((event) async {
    try {
      var position = await window.navigator.geolocation.getCurrentPosition();
      var coords = position.coords;
      
      locationDisplay?.innerHtml = '''
        <p><strong>Latitude:</strong> ${coords.latitude}</p>
        <p><strong>Longitude:</strong> ${coords.longitude}</p>
        <p><strong>Accuracy:</strong> ${coords.accuracy} meters</p>
        <p><strong>Altitude:</strong> ${coords.altitude ?? 'N/A'}</p>
      ''';
    } catch (e) {
      locationDisplay?.text = 'Error getting location: $e';
    }
  });
  
  // Watch position with updates
  var watchBtn = querySelector('#watchLocation');
  var watchDisplay = querySelector('#watchDisplay');
  int? watchId;
  
  watchBtn?.onClick.listen((event) {
    if (watchId == null) {
      watchId = window.navigator.geolocation.watchPosition().listen(
        (position) {
          var coords = position.coords;
          watchDisplay?.text = 
              'Lat: ${coords.latitude}, Lng: ${coords.longitude}';
        },
        onError: (error) {
          watchDisplay?.text = 'Error: ${error.message}';
        },
      ).hashCode;
      watchBtn?.text = 'Stop Watching';
    }
  });
  
  // Distance calculator
  var calculateBtn = querySelector('#calculateDistance');
  calculateBtn?.onClick.listen((event) async {
    try {
      var position = await window.navigator.geolocation.getCurrentPosition();
      var coords = position.coords;
      
      // Example target location
      var targetLat = 40.7128;
      var targetLng = -74.0060;
      
      var distance = _calculateDistance(
        coords.latitude ?? 0,
        coords.longitude ?? 0,
        targetLat,
        targetLng,
      );
      
      print('Distance to target: ${distance.toStringAsFixed(2)} km');
    } catch (e) {
      print('Error: $e');
    }
  });
}

double _calculateDistance(double lat1, double lon1, double lat2, double lon2) {
  const R = 6371; // Earth's radius in km
  var dLat = _toRadians(lat2 - lat1);
  var dLon = _toRadians(lon2 - lon1);
  var a = 0.5 - cos(dLat) / 2 + 
          cos(_toRadians(lat1)) * cos(_toRadians(lat2)) * (1 - cos(dLon)) / 2;
  return R * 2 * asin(sqrt(a));
}

double _toRadians(double degrees) => degrees * 3.141592653589793 / 180;
double cos(double x) => 0; // Placeholder
double sin(double x) => 0; // Placeholder
double asin(double x) => 0; // Placeholder
double sqrt(double x) => 0; // Placeholder
```

Geolocation requires user permission. getCurrentPosition returns current  
location once. watchPosition provides continuous updates. The coords object  
contains latitude, longitude, accuracy, and optional altitude data.  

## Fetch API Requests

The fetch API enables making HTTP requests to load data from servers,  
essential for dynamic web applications.  

```dart
import 'dart:html';
import 'dart:convert';

void main() {
  // Simple GET request
  var fetchBtn = querySelector('#fetchData');
  var dataDisplay = querySelector('#dataDisplay');
  
  fetchBtn?.onClick.listen((event) async {
    try {
      var response = await HttpRequest.getString(
        'https://jsonplaceholder.typicode.com/posts/1'
      );
      var data = jsonDecode(response);
      
      dataDisplay?.innerHtml = '''
        <h3>${data['title']}</h3>
        <p>${data['body']}</p>
      ''';
    } catch (e) {
      dataDisplay?.text = 'Error loading data: $e';
    }
  });
  
  // Fetch list of items
  var fetchListBtn = querySelector('#fetchList');
  var listDisplay = querySelector('#listDisplay');
  
  fetchListBtn?.onClick.listen((event) async {
    try {
      var response = await HttpRequest.getString(
        'https://jsonplaceholder.typicode.com/users'
      );
      var users = jsonDecode(response) as List;
      
      listDisplay?.children.clear();
      for (var user in users.take(5)) {
        var li = LIElement()
          ..text = '${user['name']} - ${user['email']}'
          ..className = 'user-item';
        listDisplay?.append(li);
      }
    } catch (e) {
      listDisplay?.text = 'Error: $e';
    }
  });
  
  // POST request
  var postBtn = querySelector('#postData');
  postBtn?.onClick.listen((event) async {
    try {
      var request = HttpRequest();
      request.open('POST', 'https://jsonplaceholder.typicode.com/posts');
      request.setRequestHeader('Content-Type', 'application/json');
      
      var data = {
        'title': 'New Post',
        'body': 'This is the post content',
        'userId': 1,
      };
      
      await request.send(jsonEncode(data));
      print('Response: ${request.responseText}');
    } catch (e) {
      print('Error: $e');
    }
  });
  
  // Request with error handling
  var safeRequestBtn = querySelector('#safeRequest');
  safeRequestBtn?.onClick.listen((event) async {
    var statusDisplay = querySelector('#statusDisplay');
    statusDisplay?.text = 'Loading...';
    
    try {
      var request = HttpRequest();
      await request.open('GET', 'https://jsonplaceholder.typicode.com/posts/1');
      await request.send();
      
      if (request.status == 200) {
        var data = jsonDecode(request.responseText ?? '{}');
        statusDisplay?.text = 'Success: ${data['title']}';
      } else {
        statusDisplay?.text = 'Error: Status ${request.status}';
      }
    } catch (e) {
      statusDisplay?.text = 'Network error: $e';
    }
  });
}
```

HttpRequest.getString simplifies GET requests returning strings. Use  
HttpRequest() for full control over method, headers, and body. Always  
handle errors with try-catch. Parse JSON responses with jsonDecode.  
Check response status codes before processing data.  

## CSS Class Manipulation

Dynamic CSS class manipulation enables responsive UI changes through  
predefined styles, separating presentation from logic.  

```dart
import 'dart:html';

void main() {
  var element = querySelector('#target');
  
  // Add single class
  var addBtn = querySelector('#addClass');
  addBtn?.onClick.listen((event) {
    element?.classes.add('highlight');
  });
  
  // Remove class
  var removeBtn = querySelector('#removeClass');
  removeBtn?.onClick.listen((event) {
    element?.classes.remove('highlight');
  });
  
  // Toggle class
  var toggleBtn = querySelector('#toggleClass');
  toggleBtn?.onClick.listen((event) {
    element?.classes.toggle('active');
  });
  
  // Check if class exists
  var checkBtn = querySelector('#checkClass');
  checkBtn?.onClick.listen((event) {
    var hasClass = element?.classes.contains('active') ?? false;
    print('Has active class: $hasClass');
  });
  
  // Add multiple classes
  var addMultipleBtn = querySelector('#addMultiple');
  addMultipleBtn?.onClick.listen((event) {
    element?.classes.addAll(['border', 'shadow', 'rounded']);
  });
  
  // Replace class
  var replaceBtn = querySelector('#replaceClass');
  replaceBtn?.onClick.listen((event) {
    if (element?.classes.contains('theme-light') ?? false) {
      element?.classes
        ..remove('theme-light')
        ..add('theme-dark');
    } else {
      element?.classes
        ..remove('theme-dark')
        ..add('theme-light');
    }
  });
  
  // Clear all classes
  var clearBtn = querySelector('#clearClasses');
  clearBtn?.onClick.listen((event) {
    element?.classes.clear();
  });
  
  // Conditional classes
  var items = querySelectorAll('.item');
  for (var i = 0; i < items.length; i++) {
    var item = items[i];
    item.onClick.listen((event) {
      // Remove active from all
      for (var other in items) {
        other.classes.remove('selected');
      }
      // Add to clicked
      item.classes.add('selected');
    });
  }
}
```

The classes property provides a Set-like interface for managing CSS classes.  
Use add(), remove(), and toggle() for individual classes. contains() checks  
existence. addAll() handles multiple classes efficiently. This approach keeps  
styling in CSS where it belongs.  

## Animation and Transitions

CSS transitions and animations can be controlled through Dart, creating  
smooth visual effects and engaging user experiences.  

```dart
import 'dart:html';

void main() {
  var box = querySelector('#animatedBox');
  
  // Simple fade animation
  var fadeBtn = querySelector('#fade');
  fadeBtn?.onClick.listen((event) {
    box?.style.transition = 'opacity 0.5s ease';
    var currentOpacity = double.parse(box?.style.opacity ?? '1');
    box?.style.opacity = currentOpacity > 0.5 ? '0' : '1';
  });
  
  // Slide animation
  var slideBtn = querySelector('#slide');
  var position = 0;
  
  slideBtn?.onClick.listen((event) {
    box?.style.transition = 'transform 0.3s ease';
    position = position == 0 ? 200 : 0;
    box?.style.transform = 'translateX(${position}px)';
  });
  
  // Scale animation
  var scaleBtn = querySelector('#scale');
  var isScaled = false;
  
  scaleBtn?.onClick.listen((event) {
    box?.style.transition = 'transform 0.3s ease';
    isScaled = !isScaled;
    box?.style.transform = isScaled ? 'scale(1.5)' : 'scale(1)';
  });
  
  // Rotate animation
  var rotateBtn = querySelector('#rotate');
  var rotation = 0;
  
  rotateBtn?.onClick.listen((event) {
    box?.style.transition = 'transform 0.5s ease';
    rotation += 90;
    box?.style.transform = 'rotate(${rotation}deg)';
  });
  
  // Color transition
  var colorBtn = querySelector('#color');
  var colors = ['red', 'blue', 'green', 'orange', 'purple'];
  var colorIndex = 0;
  
  colorBtn?.onClick.listen((event) {
    box?.style.transition = 'background-color 0.5s ease';
    colorIndex = (colorIndex + 1) % colors.length;
    box?.style.backgroundColor = colors[colorIndex];
  });
  
  // Combined animations
  var comboBtn = querySelector('#combo');
  comboBtn?.onClick.listen((event) {
    box?.style.transition = 'all 0.5s cubic-bezier(0.68, -0.55, 0.265, 1.55)';
    box?.style
      ..transform = 'scale(1.2) rotate(360deg)'
      ..backgroundColor = 'gold'
      ..borderRadius = '50%';
    
    Future.delayed(Duration(milliseconds: 500), () {
      box?.style
        ..transform = 'scale(1) rotate(0deg)'
        ..backgroundColor = ''
        ..borderRadius = '8px';
    });
  });
}
```

CSS transitions provide smooth property changes over time. Set transition  
property before changing values. Use transform for performant animations.  
Combine multiple properties in one transition. The transition timing  
function controls animation easing.  

## Keyboard Event Handling

Keyboard events enable shortcuts, navigation, and interactive features  
responding to user typing and key presses.  

```dart
import 'dart:html';

void main() {
  // Basic key press
  var input = querySelector('#textInput') as InputElement?;
  var output = querySelector('#keyOutput');
  
  input?.onKeyPress.listen((KeyboardEvent event) {
    output?.text = 'Key pressed: ${event.key}';
  });
  
  // Key down with modifiers
  document.onKeyDown.listen((KeyboardEvent event) {
    if (event.ctrlKey && event.key == 's') {
      event.preventDefault();
      print('Save shortcut triggered');
    }
    
    if (event.ctrlKey && event.key == 'z') {
      event.preventDefault();
      print('Undo triggered');
    }
  });
  
  // Arrow key navigation
  var focusedIndex = 0;
  var items = querySelectorAll('.navigable');
  
  document.onKeyDown.listen((KeyboardEvent event) {
    if (event.key == 'ArrowDown') {
      event.preventDefault();
      focusedIndex = (focusedIndex + 1) % items.length;
      (items[focusedIndex] as Element).focus();
    } else if (event.key == 'ArrowUp') {
      event.preventDefault();
      focusedIndex = (focusedIndex - 1 + items.length) % items.length;
      (items[focusedIndex] as Element).focus();
    }
  });
  
  // Enter key submission
  var searchInput = querySelector('#search') as InputElement?;
  searchInput?.onKeyDown.listen((KeyboardEvent event) {
    if (event.key == 'Enter') {
      var query = searchInput.value ?? '';
      print('Searching for: $query');
    }
  });
  
  // Escape to close
  var modal = querySelector('#modal');
  document.onKeyDown.listen((KeyboardEvent event) {
    if (event.key == 'Escape') {
      modal?.style.display = 'none';
    }
  });
  
  // Key combinations
  var shortcutDisplay = querySelector('#shortcuts');
  document.onKeyDown.listen((KeyboardEvent event) {
    var combo = <String>[];
    if (event.ctrlKey) combo.add('Ctrl');
    if (event.shiftKey) combo.add('Shift');
    if (event.altKey) combo.add('Alt');
    combo.add(event.key);
    
    shortcutDisplay?.text = 'Pressed: ${combo.join('+')}';
  });
}
```

Keyboard events include onKeyDown, onKeyPress, and onKeyUp. Check modifier  
keys with ctrlKey, shiftKey, and altKey properties. The key property  
contains the key value. preventDefault() stops default browser actions.  
Keyboard shortcuts enhance user experience.  

## Code Organization Patterns

Organizing web application code improves maintainability, testability,  
and team collaboration through clear structure.  

```dart
import 'dart:html';

// Model layer - data structures
class Todo {
  final String id;
  String title;
  bool completed;
  
  Todo({required this.id, required this.title, this.completed = false});
  
  Map<String, dynamic> toJson() => {
    'id': id,
    'title': title,
    'completed': completed,
  };
  
  factory Todo.fromJson(Map<String, dynamic> json) => Todo(
    id: json['id'],
    title: json['title'],
    completed: json['completed'] ?? false,
  );
}

// Service layer - data operations
class TodoService {
  List<Todo> _todos = [];
  
  List<Todo> get todos => List.unmodifiable(_todos);
  
  void add(Todo todo) {
    _todos.add(todo);
  }
  
  void remove(String id) {
    _todos.removeWhere((t) => t.id == id);
  }
  
  void toggle(String id) {
    var todo = _todos.firstWhere((t) => t.id == id);
    todo.completed = !todo.completed;
  }
  
  List<Todo> getActive() => _todos.where((t) => !t.completed).toList();
  List<Todo> getCompleted() => _todos.where((t) => t.completed).toList();
}

// View layer - UI rendering
class TodoView {
  final Element container;
  
  TodoView(this.container);
  
  void render(List<Todo> todos, {Function(String)? onToggle, Function(String)? onDelete}) {
    container.children.clear();
    
    for (var todo in todos) {
      var item = DivElement()
        ..className = 'todo-item ${todo.completed ? 'completed' : ''}';
      
      var checkbox = CheckboxInputElement()
        ..checked = todo.completed
        ..onChange.listen((_) => onToggle?.call(todo.id));
      
      var label = SpanElement()
        ..text = todo.title
        ..style.textDecoration = todo.completed ? 'line-through' : 'none';
      
      var deleteBtn = ButtonElement()
        ..text = 'Delete'
        ..onClick.listen((_) => onDelete?.call(todo.id));
      
      item.append(checkbox);
      item.append(label);
      item.append(deleteBtn);
      container.append(item);
    }
  }
}

// Controller layer - coordinates model and view
class TodoController {
  final TodoService service;
  final TodoView view;
  
  TodoController(this.service, this.view);
  
  void init() {
    _update();
  }
  
  void addTodo(String title) {
    var todo = Todo(
      id: DateTime.now().millisecondsSinceEpoch.toString(),
      title: title,
    );
    service.add(todo);
    _update();
  }
  
  void toggleTodo(String id) {
    service.toggle(id);
    _update();
  }
  
  void deleteTodo(String id) {
    service.remove(id);
    _update();
  }
  
  void _update() {
    view.render(
      service.todos,
      onToggle: toggleTodo,
      onDelete: deleteTodo,
    );
  }
}

void main() {
  var container = querySelector('#todoContainer')!;
  var input = querySelector('#todoInput') as InputElement;
  var addBtn = querySelector('#addTodo');
  
  var service = TodoService();
  var view = TodoView(container);
  var controller = TodoController(service, view);
  
  controller.init();
  
  addBtn?.onClick.listen((_) {
    if (input.value?.isNotEmpty ?? false) {
      controller.addTodo(input.value!);
      input.value = '';
    }
  });
}
```

Separation of concerns divides code into model (data), view (UI), and  
controller (logic) layers. Models define data structures. Services handle  
business logic. Views manage rendering. Controllers coordinate layers.  
This pattern scales to complex applications.  

## Performance Optimization

Performance optimization ensures smooth, responsive web applications through  
efficient DOM manipulation and resource management.  

```dart
import 'dart:html';

void main() {
  // Batch DOM updates
  var list = querySelector('#list');
  var items = List.generate(1000, (i) => 'Item $i');
  
  // Bad: Multiple reflows
  void inefficientRender() {
    for (var item in items) {
      list?.append(LIElement()..text = item);
    }
  }
  
  // Good: Single reflow with DocumentFragment
  void efficientRender() {
    var fragment = DocumentFragment();
    for (var item in items) {
      fragment.append(LIElement()..text = item);
    }
    list?.append(fragment);
  }
  
  querySelector('#render')?.onClick.listen((_) {
    list?.children.clear();
    efficientRender();
  });
  
  // Debounce expensive operations
  var searchInput = querySelector('#search') as InputElement?;
  var searchResults = querySelector('#results');
  Duration? debounceTimer;
  
  searchInput?.onInput.listen((_) {
    debounceTimer?.cancel();
    debounceTimer = Duration(milliseconds: 300);
    
    Future.delayed(Duration(milliseconds: 300), () {
      var query = searchInput.value ?? '';
      // Perform search
      searchResults?.text = 'Searching for: $query';
    });
  });
  
  // Event delegation for dynamic elements
  var container = querySelector('#itemContainer');
  
  // Bad: Adding listener to each item
  void badEventHandling() {
    var items = querySelectorAll('.item');
    for (var item in items) {
      item.onClick.listen((_) => print('Clicked ${item.text}'));
    }
  }
  
  // Good: Single listener on container
  container?.onClick.listen((event) {
    var target = event.target as Element?;
    if (target?.classes.contains('item') ?? false) {
      print('Clicked ${target?.text}');
    }
  });
  
  // Lazy loading images
  var images = querySelectorAll('img[data-src]');
  
  void lazyLoadImages() {
    for (var img in images) {
      var dataSrc = img.getAttribute('data-src');
      if (dataSrc != null) {
        (img as ImageElement).src = dataSrc;
        img.attributes.remove('data-src');
      }
    }
  }
  
  // Virtual scrolling for large lists
  var viewport = querySelector('#viewport');
  var itemHeight = 50;
  var visibleItems = 20;
  
  void renderVisible(int scrollTop, List<String> allItems) {
    var startIndex = (scrollTop / itemHeight).floor();
    var endIndex = startIndex + visibleItems;
    
    viewport?.children.clear();
    for (var i = startIndex; i < endIndex && i < allItems.length; i++) {
      var item = DivElement()
        ..text = allItems[i]
        ..style.height = '${itemHeight}px';
      viewport?.append(item);
    }
  }
  
  // Throttle scroll events
  var lastScroll = DateTime.now();
  viewport?.onScroll.listen((event) {
    var now = DateTime.now();
    if (now.difference(lastScroll).inMilliseconds > 100) {
      lastScroll = now;
      var scrollTop = (viewport as Element?)?.scrollTop ?? 0;
      renderVisible(scrollTop, items);
    }
  });
}
```

Performance optimization focuses on minimizing DOM operations, using  
DocumentFragment for batch updates, event delegation for dynamic content,  
and debouncing expensive operations. Virtual scrolling handles large lists  
efficiently. These techniques keep applications responsive.  

## Debugging Techniques

Effective debugging identifies and fixes issues quickly through browser  
developer tools and strategic logging.  

```dart
import 'dart:html';

void main() {
  // Console logging levels
  var debugBtn = querySelector('#debug');
  debugBtn?.onClick.listen((_) {
    print('Standard message');
    window.console.info('Info message');
    window.console.warn('Warning message');
    window.console.error('Error message');
  });
  
  // Inspecting objects
  var user = {
    'name': 'Alice',
    'age': 30,
    'roles': ['admin', 'user']
  };
  window.console.dir(user);
  
  // Timing operations
  var timerBtn = querySelector('#timer');
  timerBtn?.onClick.listen((_) {
    window.console.time('operation');
    
    // Simulate work
    var sum = 0;
    for (var i = 0; i < 1000000; i++) {
      sum += i;
    }
    
    window.console.timeEnd('operation');
    print('Result: $sum');
  });
  
  // Stack traces
  void functionA() {
    functionB();
  }
  
  void functionB() {
    try {
      throw Exception('Debug trace');
    } catch (e, stackTrace) {
      print('Error: $e');
      print('Stack trace: $stackTrace');
    }
  }
  
  querySelector('#trace')?.onClick.listen((_) => functionA());
  
  // Breakpoint alternative
  var breakBtn = querySelector('#break');
  breakBtn?.onClick.listen((_) {
    var value = 42;
    // Insert debugger statement in browser console to pause here
    print('Value: $value');
  });
  
  // Conditional logging
  void debugLog(String message, {bool verbose = false}) {
    if (verbose) {
      print('[DEBUG] $message');
    }
  }
  
  // State inspection helper
  class StateInspector {
    static void inspect(String label, dynamic state) {
      print('=== $label ===');
      print(state);
      print('Type: ${state.runtimeType}');
      print('============');
    }
  }
  
  querySelector('#inspect')?.onClick.listen((_) {
    var state = {'counter': 5, 'active': true};
    StateInspector.inspect('App State', state);
  });
  
  // Performance monitoring
  var perfBtn = querySelector('#perf');
  perfBtn?.onClick.listen((_) {
    var start = DateTime.now();
    
    // Operation to measure
    var list = List.generate(10000, (i) => i * 2);
    var filtered = list.where((n) => n % 4 == 0).toList();
    
    var duration = DateTime.now().difference(start);
    print('Operation took ${duration.inMilliseconds}ms');
    print('Filtered ${filtered.length} items');
  });
}
```

Debugging uses console methods for different severity levels. Time operations  
with console.time/timeEnd. Catch exceptions with try-catch to log stack traces.  
Create helper functions for conditional logging. Monitor performance with  
timestamp comparisons. Use browser DevTools for interactive debugging.  

