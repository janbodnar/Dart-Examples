# Dart Networking

Networking is a fundamental aspect of modern applications, enabling  
communication with web services, APIs, and remote resources. Dart provides  
comprehensive networking capabilities through the dart:io and dart:html  
libraries, along with powerful third-party packages like http and dio.  
These tools enable developers to make HTTP requests, handle WebSockets,  
process responses, and manage complex networking scenarios.  

Dart's asynchronous programming model makes it exceptionally well-suited  
for networking tasks. Futures and Streams provide elegant abstractions for  
handling network requests and responses, while async-await syntax keeps  
code readable and maintainable. Whether building mobile apps with Flutter,  
server applications, or command-line tools, understanding Dart's networking  
capabilities is essential for creating robust, responsive applications.  

This comprehensive guide covers HTTP requests, RESTful API interactions,  
WebSocket connections, error handling, authentication, file transfers, and  
advanced networking patterns. Each example demonstrates best practices and  
idiomatic Dart code suitable for production applications. Examples progress  
from basic concepts to advanced techniques, covering both dart:io for  
server/CLI and popular packages for enhanced functionality.  

## Basic HTTP GET Request

The simplest HTTP operation fetches data from a URL using GET requests.  
This foundational pattern is essential for all networking tasks.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  HttpClient client = HttpClient();
  
  try {
    // Create the request
    HttpClientRequest request = await client.getUrl(
      Uri.parse('https://api.github.com/users/dart-lang')
    );
    
    // Send the request and get the response
    HttpClientResponse response = await request.close();
    
    print('Status code: ${response.statusCode}');
    print('Content type: ${response.headers.contentType}');
    
    // Read response body
    String responseBody = await response.transform(utf8.decoder).join();
    print('Response: $responseBody');
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

HttpClient creates connections to servers. The getUrl method initiates a  
GET request to the specified URL. The request.close() method sends the  
request and returns the response. Always close the client to free resources.  
The response body is a stream that must be decoded and joined.  

## HTTP GET with JSON Parsing

Most modern APIs return JSON data that needs to be parsed into Dart  
objects for use in applications.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  HttpClient client = HttpClient();
  
  try {
    HttpClientRequest request = await client.getUrl(
      Uri.parse('https://jsonplaceholder.typicode.com/users/1')
    );
    
    HttpClientResponse response = await request.close();
    
    if (response.statusCode == 200) {
      String jsonString = await response.transform(utf8.decoder).join();
      Map<String, dynamic> user = jsonDecode(jsonString);
      
      print('User Information:');
      print('  Name: ${user['name']}');
      print('  Email: ${user['email']}');
      print('  Phone: ${user['phone']}');
      print('  Website: ${user['website']}');
      
      // Access nested data
      Map<String, dynamic> address = user['address'];
      print('  City: ${address['city']}');
    } else {
      print('Failed to load user: ${response.statusCode}');
    }
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

The jsonDecode function from dart:convert parses JSON strings into Dart  
maps and lists. Always check the status code before parsing. Type casting  
ensures proper data access. Nested JSON objects are accessed as nested maps.  

## HTTP POST Request

POST requests send data to servers, commonly used for creating resources  
or submitting form data to APIs.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  HttpClient client = HttpClient();
  
  try {
    // Create POST request
    HttpClientRequest request = await client.postUrl(
      Uri.parse('https://jsonplaceholder.typicode.com/posts')
    );
    
    // Set headers
    request.headers.contentType = ContentType.json;
    
    // Prepare data
    Map<String, dynamic> postData = {
      'title': 'Hello there!',
      'body': 'This is a sample post from Dart',
      'userId': 1,
    };
    
    // Write JSON data to request
    request.write(jsonEncode(postData));
    
    // Send request
    HttpClientResponse response = await request.close();
    
    print('Status code: ${response.statusCode}');
    
    String responseBody = await response.transform(utf8.decoder).join();
    Map<String, dynamic> responseData = jsonDecode(responseBody);
    print('Created post ID: ${responseData['id']}');
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

POST requests use postUrl instead of getUrl. Setting the content type  
header to application/json tells the server we're sending JSON data.  
The request.write method sends the request body. Always encode data as  
JSON before sending.  


## HTTP PUT Request

PUT requests update existing resources on the server, typically replacing  
the entire resource with new data.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  HttpClient client = HttpClient();
  
  try {
    HttpClientRequest request = await client.putUrl(
      Uri.parse('https://jsonplaceholder.typicode.com/posts/1')
    );
    
    request.headers.contentType = ContentType.json;
    
    Map<String, dynamic> updatedData = {
      'id': 1,
      'title': 'Updated Title',
      'body': 'This post has been updated',
      'userId': 1,
    };
    
    request.write(jsonEncode(updatedData));
    
    HttpClientResponse response = await request.close();
    
    print('Status code: ${response.statusCode}');
    
    String responseBody = await response.transform(utf8.decoder).join();
    print('Updated resource: $responseBody');
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

PUT requests use putUrl and typically include the resource ID in the URL.  
The request replaces the entire resource, so all fields should be included.  
The server usually returns the updated resource in the response body.  

## HTTP DELETE Request

DELETE requests remove resources from the server, identified by their  
URL or ID.  

```dart
import 'dart:io';

void main() async {
  HttpClient client = HttpClient();
  
  try {
    HttpClientRequest request = await client.deleteUrl(
      Uri.parse('https://jsonplaceholder.typicode.com/posts/1')
    );
    
    HttpClientResponse response = await request.close();
    
    print('Status code: ${response.statusCode}');
    
    if (response.statusCode == 200 || response.statusCode == 204) {
      print('Resource deleted successfully');
    } else {
      print('Failed to delete resource');
    }
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

DELETE requests use deleteUrl with the resource's URL. Successful deletions  
typically return status code 200 (OK) with response body or 204 (No Content)  
without a body. Always check both status codes for success.  

## HTTP PATCH Request

PATCH requests partially update resources, modifying only specific fields  
rather than replacing the entire resource.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  HttpClient client = HttpClient();
  
  try {
    HttpClientRequest request = await client.patchUrl(
      Uri.parse('https://jsonplaceholder.typicode.com/posts/1')
    );
    
    request.headers.contentType = ContentType.json;
    
    // Only update specific fields
    Map<String, dynamic> patchData = {
      'title': 'Patched Title',
    };
    
    request.write(jsonEncode(patchData));
    
    HttpClientResponse response = await request.close();
    
    print('Status code: ${response.statusCode}');
    
    String responseBody = await response.transform(utf8.decoder).join();
    Map<String, dynamic> updated = jsonDecode(responseBody);
    print('Patched resource: ${updated['title']}');
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

PATCH uses patchUrl and sends only the fields to be updated. This is more  
efficient than PUT when you only need to modify specific attributes. The  
server merges the provided data with the existing resource.  

## Request with Custom Headers

Custom headers enable authentication, content negotiation, and API-specific  
requirements like API keys and authentication tokens.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  HttpClient client = HttpClient();
  
  try {
    HttpClientRequest request = await client.getUrl(
      Uri.parse('https://api.github.com/user/repos')
    );
    
    // Add custom headers
    request.headers.set('Authorization', 'Bearer YOUR_TOKEN_HERE');
    request.headers.set('Accept', 'application/vnd.github.v3+json');
    request.headers.set('User-Agent', 'Dart-Example-App');
    
    // Add multiple header values
    request.headers.add('X-Custom-Header', 'value1');
    request.headers.add('X-Custom-Header', 'value2');
    
    HttpClientResponse response = await request.close();
    
    print('Status code: ${response.statusCode}');
    
    // Read response headers
    print('\nResponse headers:');
    response.headers.forEach((name, values) {
      print('  $name: ${values.join(', ')}');
    });
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

Headers are set using request.headers.set() for single values or add() for  
multiple values. Common headers include Authorization for authentication,  
Accept for content type negotiation, and User-Agent for client identification.  

## Query Parameters in URLs

Query parameters pass data in URLs for filtering, searching, and pagination  
in GET requests.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  HttpClient client = HttpClient();
  
  try {
    // Build URL with query parameters
    Uri url = Uri.https(
      'jsonplaceholder.typicode.com',
      '/comments',
      {
        'postId': '1',
        'limit': '5',
      }
    );
    
    print('Request URL: $url');
    
    HttpClientRequest request = await client.getUrl(url);
    HttpClientResponse response = await request.close();
    
    String responseBody = await response.transform(utf8.decoder).join();
    List<dynamic> comments = jsonDecode(responseBody);
    
    print('\nFound ${comments.length} comments:');
    for (var comment in comments.take(3)) {
      print('  - ${comment['name']}');
    }
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

Uri.https() and Uri.http() construct URLs with proper encoding of query  
parameters. The third parameter is a map of query parameters. This approach  
handles special characters and encoding automatically.  

## Handling HTTP Errors

Proper error handling distinguishes between network errors, HTTP errors,  
and parsing errors for robust applications.  

```dart
import 'dart:io';
import 'dart:convert';

Future<Map<String, dynamic>> fetchUser(int userId) async {
  HttpClient client = HttpClient();
  
  try {
    HttpClientRequest request = await client.getUrl(
      Uri.parse('https://jsonplaceholder.typicode.com/users/$userId')
    );
    
    HttpClientResponse response = await request.close();
    
    // Check status code
    if (response.statusCode == 200) {
      String body = await response.transform(utf8.decoder).join();
      return jsonDecode(body);
    } else if (response.statusCode == 404) {
      throw Exception('User not found');
    } else if (response.statusCode >= 400 && response.statusCode < 500) {
      throw Exception('Client error: ${response.statusCode}');
    } else if (response.statusCode >= 500) {
      throw Exception('Server error: ${response.statusCode}');
    } else {
      throw Exception('Unexpected status: ${response.statusCode}');
    }
    
  } on SocketException catch (e) {
    throw Exception('Network error: No internet connection');
  } on HttpException catch (e) {
    throw Exception('HTTP error: $e');
  } on FormatException catch (e) {
    throw Exception('Invalid JSON format: $e');
  } finally {
    client.close();
  }
}

void main() async {
  try {
    Map<String, dynamic> user = await fetchUser(1);
    print('User: ${user['name']}');
  } catch (e) {
    print('Error fetching user: $e');
  }
}
```

Different exception types handle network failures, HTTP errors, and parsing  
issues. Check status codes explicitly for proper error categorization.  
SocketException indicates network problems, HttpException indicates protocol  
issues, and FormatException indicates parsing errors.  

## Request Timeout

Timeouts prevent requests from hanging indefinitely when servers are  
unresponsive or network conditions are poor.  

```dart
import 'dart:io';
import 'dart:async';
import 'dart:convert';

void main() async {
  HttpClient client = HttpClient();
  client.connectionTimeout = Duration(seconds: 5);
  
  try {
    HttpClientRequest request = await client.getUrl(
      Uri.parse('https://jsonplaceholder.typicode.com/posts/1')
    ).timeout(Duration(seconds: 10));
    
    HttpClientResponse response = await request.close()
      .timeout(Duration(seconds: 10));
    
    String responseBody = await response.transform(utf8.decoder).join()
      .timeout(Duration(seconds: 10));
    
    print('Response: $responseBody');
    
  } on TimeoutException catch (e) {
    print('Request timed out: $e');
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

The connectionTimeout property sets the maximum time to establish a  
connection. The timeout() method on futures sets operation timeouts.  
Multiple timeout points ensure the request completes within expected time.  


## Retry Logic

Retry logic handles transient network failures by automatically retrying  
failed requests with exponential backoff.  

```dart
import 'dart:io';
import 'dart:convert';

Future<String> fetchWithRetry(String url, {int maxRetries = 3}) async {
  int attempt = 0;
  
  while (attempt < maxRetries) {
    HttpClient client = HttpClient();
    
    try {
      attempt++;
      print('Attempt $attempt of $maxRetries');
      
      HttpClientRequest request = await client.getUrl(Uri.parse(url));
      HttpClientResponse response = await request.close();
      
      if (response.statusCode == 200) {
        String body = await response.transform(utf8.decoder).join();
        client.close();
        return body;
      } else if (response.statusCode >= 500) {
        throw HttpException('Server error: ${response.statusCode}');
      } else {
        throw HttpException('Request failed: ${response.statusCode}');
      }
      
    } catch (e) {
      client.close();
      
      if (attempt >= maxRetries) {
        rethrow;
      }
      
      // Exponential backoff
      int waitSeconds = attempt * 2;
      print('Waiting $waitSeconds seconds before retry...');
      await Future.delayed(Duration(seconds: waitSeconds));
    }
  }
  
  throw Exception('Max retries exceeded');
}

void main() async {
  try {
    String response = await fetchWithRetry(
      'https://jsonplaceholder.typicode.com/posts/1'
    );
    print('Success: ${response.substring(0, 50)}...');
  } catch (e) {
    print('Failed after retries: $e');
  }
}
```

Retry logic uses a loop with increasing delays between attempts. Exponential  
backoff prevents overwhelming the server. Server errors (5xx) are retried  
while client errors (4xx) are not. The pattern balances persistence with  
resource conservation.  

## Cancellable Requests

Cancellable requests allow stopping in-progress network operations when  
they're no longer needed.  

```dart
import 'dart:io';
import 'dart:async';

class CancellableRequest {
  final HttpClient _client = HttpClient();
  bool _cancelled = false;
  
  Future<String> fetch(String url) async {
    try {
      if (_cancelled) throw Exception('Request cancelled');
      
      HttpClientRequest request = await _client.getUrl(Uri.parse(url));
      
      if (_cancelled) {
        request.abort();
        throw Exception('Request cancelled');
      }
      
      HttpClientResponse response = await request.close();
      
      if (_cancelled) {
        throw Exception('Request cancelled');
      }
      
      return await response.transform(utf8.decoder).join();
      
    } finally {
      _client.close();
    }
  }
  
  void cancel() {
    _cancelled = true;
  }
}

void main() async {
  CancellableRequest request = CancellableRequest();
  
  // Start request
  Future<String> fetchFuture = request.fetch(
    'https://jsonplaceholder.typicode.com/posts/1'
  );
  
  // Cancel after 100ms
  Future.delayed(Duration(milliseconds: 100), () {
    print('Cancelling request...');
    request.cancel();
  });
  
  try {
    String result = await fetchFuture;
    print('Result: $result');
  } catch (e) {
    print('Error: $e');
  }
}
```

The cancel method sets a flag checked at multiple points. The abort()  
method on HttpClientRequest immediately terminates the connection. Check  
cancellation state before each async operation to stop promptly.  

## Parallel Requests

Parallel requests fetch multiple resources simultaneously, improving  
performance when data sources are independent.  

```dart
import 'dart:io';
import 'dart:convert';

Future<Map<String, dynamic>> fetchUser(int id) async {
  HttpClient client = HttpClient();
  
  try {
    HttpClientRequest request = await client.getUrl(
      Uri.parse('https://jsonplaceholder.typicode.com/users/$id')
    );
    
    HttpClientResponse response = await request.close();
    String body = await response.transform(utf8.decoder).join();
    return jsonDecode(body);
  } finally {
    client.close();
  }
}

void main() async {
  print('Fetching multiple users in parallel...');
  
  // Start all requests simultaneously
  List<Future<Map<String, dynamic>>> futures = [
    fetchUser(1),
    fetchUser(2),
    fetchUser(3),
  ];
  
  try {
    // Wait for all to complete
    List<Map<String, dynamic>> users = await Future.wait(futures);
    
    print('\nFetched ${users.length} users:');
    for (var user in users) {
      print('  - ${user['name']} (${user['email']})');
    }
  } catch (e) {
    print('Error fetching users: $e');
  }
}
```

Future.wait executes multiple futures concurrently and returns when all  
complete. This is significantly faster than sequential requests. If any  
future fails, Future.wait throws the first error. Use Future.wait with  
eagerError: false to collect all results even if some fail.  

## Sequential Dependent Requests

Sequential requests handle scenarios where each request depends on the  
result of the previous one.  

```dart
import 'dart:io';
import 'dart:convert';

Future<Map<String, dynamic>> fetchData(String url) async {
  HttpClient client = HttpClient();
  
  try {
    HttpClientRequest request = await client.getUrl(Uri.parse(url));
    HttpClientResponse response = await request.close();
    String body = await response.transform(utf8.decoder).join();
    return jsonDecode(body);
  } finally {
    client.close();
  }
}

void main() async {
  try {
    // First request - get a post
    print('Fetching post...');
    Map<String, dynamic> post = await fetchData(
      'https://jsonplaceholder.typicode.com/posts/1'
    );
    print('Post title: ${post['title']}');
    
    // Second request depends on first - get post's user
    print('\nFetching user...');
    int userId = post['userId'];
    Map<String, dynamic> user = await fetchData(
      'https://jsonplaceholder.typicode.com/users/$userId'
    );
    print('Author: ${user['name']}');
    
    // Third request depends on second - get user's todos
    print('\nFetching todos...');
    List<dynamic> todos = await fetchData(
      'https://jsonplaceholder.typicode.com/users/$userId/todos'
    ) as List;
    
    print('User has ${todos.length} todos');
    int completed = todos.where((t) => t['completed'] == true).length;
    print('Completed: $completed');
    
  } catch (e) {
    print('Error: $e');
  }
}
```

Sequential requests use await to ensure each request completes before  
starting the next. This pattern is necessary when request parameters depend  
on previous responses. The async-await syntax makes the dependencies clear.  

## Streaming Response

Streaming responses process large response bodies incrementally without  
loading everything into memory at once.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  HttpClient client = HttpClient();
  
  try {
    HttpClientRequest request = await client.getUrl(
      Uri.parse('https://jsonplaceholder.typicode.com/posts')
    );
    
    HttpClientResponse response = await request.close();
    
    print('Status: ${response.statusCode}');
    print('Streaming response...\n');
    
    int bytesReceived = 0;
    
    // Process response as a stream
    await for (var chunk in response) {
      bytesReceived += chunk.length;
      print('Received $bytesReceived bytes');
    }
    
    print('\nTotal bytes: $bytesReceived');
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

The HttpClientResponse is a Stream<List<int>> that emits data chunks as  
they arrive. Processing data incrementally reduces memory usage for large  
responses. The await for loop consumes the stream chunk by chunk.  

## Download Progress Tracking

Progress tracking provides feedback during large file downloads or data  
transfers.  

```dart
import 'dart:io';

Future<void> downloadWithProgress(String url) async {
  HttpClient client = HttpClient();
  
  try {
    HttpClientRequest request = await client.getUrl(Uri.parse(url));
    HttpClientResponse response = await request.close();
    
    int? contentLength = response.contentLength;
    int bytesReceived = 0;
    List<int> bytes = [];
    
    print('Downloading from $url');
    if (contentLength != null && contentLength > 0) {
      print('Total size: $contentLength bytes\n');
    }
    
    await for (var chunk in response) {
      bytes.addAll(chunk);
      bytesReceived += chunk.length;
      
      if (contentLength != null && contentLength > 0) {
        double progress = (bytesReceived / contentLength) * 100;
        stdout.write('\rProgress: ${progress.toStringAsFixed(1)}%');
      } else {
        stdout.write('\rReceived: $bytesReceived bytes');
      }
    }
    
    print('\n\nDownload complete: $bytesReceived bytes');
    
  } catch (e) {
    print('\nError: $e');
  } finally {
    client.close();
  }
}

void main() async {
  await downloadWithProgress(
    'https://jsonplaceholder.typicode.com/posts'
  );
}
```

The contentLength property provides the total size when available. Track  
bytesReceived to calculate progress percentage. Use stdout.write with \r  
to update the same line, creating a simple progress indicator.  


## Form Data Submission

Form data submission sends key-value pairs in URL-encoded format, commonly  
used for HTML form submissions.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  HttpClient client = HttpClient();
  
  try {
    HttpClientRequest request = await client.postUrl(
      Uri.parse('https://httpbin.org/post')
    );
    
    // Set content type for form data
    request.headers.contentType = ContentType(
      'application',
      'x-www-form-urlencoded'
    );
    
    // Prepare form data
    Map<String, String> formData = {
      'username': 'dartuser',
      'email': 'user@example.com',
      'message': 'Hello there!',
    };
    
    // URL encode the form data
    String body = formData.entries
      .map((e) => '${Uri.encodeComponent(e.key)}=${Uri.encodeComponent(e.value)}')
      .join('&');
    
    request.write(body);
    
    HttpClientResponse response = await request.close();
    String responseBody = await response.transform(utf8.decoder).join();
    
    print('Status: ${response.statusCode}');
    print('Response: $responseBody');
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

Form data uses application/x-www-form-urlencoded content type. Each field  
is URL-encoded and joined with & characters. Uri.encodeComponent properly  
escapes special characters in keys and values.  

## Multipart File Upload

Multipart requests upload files along with other form fields, commonly  
used for image and document uploads.  

```dart
import 'dart:io';

Future<void> uploadFile(File file, String url) async {
  HttpClient client = HttpClient();
  
  try {
    HttpClientRequest request = await client.postUrl(Uri.parse(url));
    
    String boundary = 'dart-boundary-${DateTime.now().millisecondsSinceEpoch}';
    request.headers.contentType = ContentType(
      'multipart',
      'form-data',
      parameters: {'boundary': boundary}
    );
    
    // Start multipart body
    List<int> body = [];
    
    // Add file field
    body.addAll(utf8.encode('--$boundary\r\n'));
    body.addAll(utf8.encode(
      'Content-Disposition: form-data; name="file"; filename="${file.path.split('/').last}"\r\n'
    ));
    body.addAll(utf8.encode('Content-Type: application/octet-stream\r\n\r\n'));
    body.addAll(await file.readAsBytes());
    body.addAll(utf8.encode('\r\n'));
    
    // Add text field
    body.addAll(utf8.encode('--$boundary\r\n'));
    body.addAll(utf8.encode('Content-Disposition: form-data; name="description"\r\n\r\n'));
    body.addAll(utf8.encode('Sample file upload\r\n'));
    
    // End boundary
    body.addAll(utf8.encode('--$boundary--\r\n'));
    
    // Send
    request.contentLength = body.length;
    request.add(body);
    
    HttpClientResponse response = await request.close();
    print('Upload status: ${response.statusCode}');
    
  } catch (e) {
    print('Upload error: $e');
  } finally {
    client.close();
  }
}

void main() async {
  // Create a sample file
  File file = File('/tmp/sample.txt');
  await file.writeAsString('Hello there! This is a test file.');
  
  await uploadFile(file, 'https://httpbin.org/post');
}
```

Multipart requests use multipart/form-data content type with a boundary  
string. Each part has headers followed by content. File parts include the  
filename in Content-Disposition. The boundary separates parts and marks  
the end of the request body.  

## Basic Authentication

Basic authentication sends credentials encoded in the Authorization header  
for simple server authentication.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  HttpClient client = HttpClient();
  
  try {
    String username = 'user';
    String password = 'pass';
    
    // Encode credentials
    String credentials = '$username:$password';
    String encoded = base64Encode(utf8.encode(credentials));
    
    HttpClientRequest request = await client.getUrl(
      Uri.parse('https://httpbin.org/basic-auth/user/pass')
    );
    
    // Set authorization header
    request.headers.set('Authorization', 'Basic $encoded');
    
    HttpClientResponse response = await request.close();
    
    if (response.statusCode == 200) {
      String body = await response.transform(utf8.decoder).join();
      print('Authentication successful!');
      print('Response: $body');
    } else if (response.statusCode == 401) {
      print('Authentication failed');
    }
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

Basic authentication encodes username:password in Base64 and sends it in  
the Authorization header with "Basic" prefix. This is simple but insecure  
over non-HTTPS connections. Always use HTTPS with Basic authentication.  

## Bearer Token Authentication

Bearer tokens authenticate API requests using OAuth tokens or API keys in  
the Authorization header.  

```dart
import 'dart:io';
import 'dart:convert';

class ApiClient {
  final String baseUrl;
  final String? token;
  
  ApiClient(this.baseUrl, {this.token});
  
  Future<Map<String, dynamic>> get(String endpoint) async {
    HttpClient client = HttpClient();
    
    try {
      HttpClientRequest request = await client.getUrl(
        Uri.parse('$baseUrl$endpoint')
      );
      
      // Add bearer token if available
      if (token != null) {
        request.headers.set('Authorization', 'Bearer $token');
      }
      
      HttpClientResponse response = await request.close();
      
      if (response.statusCode == 200) {
        String body = await response.transform(utf8.decoder).join();
        return jsonDecode(body);
      } else if (response.statusCode == 401) {
        throw Exception('Unauthorized: Invalid or expired token');
      } else {
        throw Exception('Request failed: ${response.statusCode}');
      }
      
    } finally {
      client.close();
    }
  }
}

void main() async {
  ApiClient api = ApiClient(
    'https://api.github.com',
    token: 'YOUR_TOKEN_HERE'
  );
  
  try {
    Map<String, dynamic> data = await api.get('/user');
    print('Authenticated user: ${data['login']}');
  } catch (e) {
    print('Error: $e');
  }
}
```

Bearer tokens are sent in the Authorization header with "Bearer" prefix.  
This pattern is widely used with OAuth 2.0 and API key authentication.  
Tokens should be stored securely and never committed to source control.  

## API Key in Query Parameters

Some APIs require authentication through API keys passed as query parameters  
in the URL.  

```dart
import 'dart:io';
import 'dart:convert';

class ApiClient {
  final String baseUrl;
  final String apiKey;
  
  ApiClient(this.baseUrl, this.apiKey);
  
  Future<Map<String, dynamic>> get(
    String endpoint,
    Map<String, String> params
  ) async {
    HttpClient client = HttpClient();
    
    try {
      // Add API key to parameters
      Map<String, String> allParams = {
        ...params,
        'api_key': apiKey,
      };
      
      Uri url = Uri.parse(baseUrl + endpoint).replace(
        queryParameters: allParams
      );
      
      print('Request URL: $url');
      
      HttpClientRequest request = await client.getUrl(url);
      HttpClientResponse response = await request.close();
      
      if (response.statusCode == 200) {
        String body = await response.transform(utf8.decoder).join();
        return jsonDecode(body);
      } else {
        throw Exception('Request failed: ${response.statusCode}');
      }
      
    } finally {
      client.close();
    }
  }
}

void main() async {
  ApiClient api = ApiClient(
    'https://api.example.com',
    'your-api-key-here'
  );
  
  try {
    Map<String, dynamic> data = await api.get(
      '/weather',
      {'city': 'London', 'units': 'metric'}
    );
    print('Data: $data');
  } catch (e) {
    print('Error: $e');
  }
}
```

API keys in query parameters are appended to the URL. The Uri.replace  
method adds query parameters while preserving the base URL. This approach  
is common for public APIs but less secure than header-based authentication.  

## Custom User Agent

Setting a custom User-Agent helps identify your application and comply  
with API terms of service.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  HttpClient client = HttpClient();
  client.userAgent = 'DartApp/1.0 (https://example.com)';
  
  try {
    HttpClientRequest request = await client.getUrl(
      Uri.parse('https://api.github.com/repos/dart-lang/sdk')
    );
    
    // Can also set per-request
    request.headers.set('User-Agent', 'DartApp/1.0');
    
    HttpClientResponse response = await request.close();
    
    if (response.statusCode == 200) {
      String body = await response.transform(utf8.decoder).join();
      Map<String, dynamic> repo = jsonDecode(body);
      
      print('Repository: ${repo['name']}');
      print('Description: ${repo['description']}');
      print('Stars: ${repo['stargazers_count']}');
    }
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

The userAgent property sets the default User-Agent for all requests from  
the client. Individual requests can override it using headers.set(). Many  
APIs require or recommend identifying your application via User-Agent.  


## Response Cookies

Cookies store session data and preferences. Reading and managing response  
cookies enables session handling and state management.  

```dart
import 'dart:io';

void main() async {
  HttpClient client = HttpClient();
  
  try {
    HttpClientRequest request = await client.getUrl(
      Uri.parse('https://httpbin.org/cookies/set?session=abc123')
    );
    
    HttpClientResponse response = await request.close();
    
    print('Status: ${response.statusCode}');
    
    // Read cookies from response
    List<Cookie> cookies = response.cookies;
    print('\nReceived ${cookies.length} cookies:');
    
    for (Cookie cookie in cookies) {
      print('  Name: ${cookie.name}');
      print('  Value: ${cookie.value}');
      print('  Domain: ${cookie.domain}');
      print('  Path: ${cookie.path}');
      print('  Expires: ${cookie.expires}');
      print('  Secure: ${cookie.secure}');
      print('  HttpOnly: ${cookie.httpOnly}');
      print('');
    }
    
    await response.drain();
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

Response cookies are accessed via the cookies property on HttpClientResponse.  
Each Cookie object contains the name, value, and metadata like domain, path,  
and expiration. The drain() method consumes the response body when not needed.  

## Sending Cookies

Sending cookies in requests maintains session state and user preferences  
across multiple requests to the same server.  

```dart
import 'dart:io';

void main() async {
  HttpClient client = HttpClient();
  
  try {
    // First request - get cookies
    print('Getting cookies...');
    HttpClientRequest request1 = await client.getUrl(
      Uri.parse('https://httpbin.org/cookies/set?user=dartdev')
    );
    
    HttpClientResponse response1 = await request1.close();
    await response1.drain();
    
    print('Cookies set!\n');
    
    // Second request - cookies are automatically sent
    print('Making authenticated request...');
    HttpClientRequest request2 = await client.getUrl(
      Uri.parse('https://httpbin.org/cookies')
    );
    
    // Manually add cookie if needed
    Cookie customCookie = Cookie('custom', 'value123');
    request2.cookies.add(customCookie);
    
    HttpClientResponse response2 = await request2.close();
    String body = await response2.transform(utf8.decoder).join();
    
    print('Response: $body');
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

HttpClient automatically stores and sends cookies by default. The cookies  
property on HttpClientRequest allows adding custom cookies. Cookies are  
domain-scoped and path-scoped, sent only to matching URLs.  

## HTTPS/SSL Requests

HTTPS requests encrypt data in transit. Understanding SSL/TLS configuration  
is important for secure communications.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  HttpClient client = HttpClient();
  
  // Configure SSL/TLS
  client.badCertificateCallback = (cert, host, port) {
    print('Warning: Bad certificate for $host:$port');
    // Only return true for development/testing
    // In production, verify certificates properly
    return false;
  };
  
  try {
    HttpClientRequest request = await client.getUrl(
      Uri.parse('https://jsonplaceholder.typicode.com/posts/1')
    );
    
    HttpClientResponse response = await request.close();
    
    // Check if connection was secure
    print('Secure connection: ${response.isRedirect}');
    print('Status: ${response.statusCode}');
    
    String body = await response.transform(utf8.decoder).join();
    print('Response received: ${body.substring(0, 50)}...');
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

HTTPS uses Uri.parse with https:// scheme. The badCertificateCallback  
handles invalid certificates. In production, validate certificates properly.  
Never blindly accept bad certificates as it undermines security.  

## Certificate Pinning

Certificate pinning validates server certificates against known good  
certificates, preventing man-in-the-middle attacks.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  HttpClient client = HttpClient();
  
  // Pin certificate
  client.badCertificateCallback = (cert, host, port) {
    // Get certificate fingerprint
    String certString = cert.pem;
    
    print('Certificate for $host:');
    print('Subject: ${cert.subject}');
    print('Issuer: ${cert.issuer}');
    print('Valid from: ${cert.startValidity}');
    print('Valid until: ${cert.endValidity}');
    
    // In production, compare cert hash/fingerprint to known good value
    // This is a simplified example
    bool isValid = cert.subject.contains('O=GitHub');
    
    if (!isValid) {
      print('Certificate validation failed!');
    }
    
    return isValid;
  };
  
  try {
    HttpClientRequest request = await client.getUrl(
      Uri.parse('https://api.github.com')
    );
    
    HttpClientResponse response = await request.close();
    print('\nConnection status: ${response.statusCode}');
    await response.drain();
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

Certificate pinning compares the server's certificate against expected  
values. The badCertificateCallback receives the X509Certificate object  
containing certificate details. Check subject, issuer, or fingerprint  
against known good values.  

## Redirect Handling

HTTP redirects move resources to new locations. Understanding redirect  
handling ensures requests reach the correct destination.  

```dart
import 'dart:io';

void main() async {
  HttpClient client = HttpClient();
  
  // Configure redirect behavior
  client.maxRedirects = 5;
  
  try {
    HttpClientRequest request = await client.getUrl(
      Uri.parse('https://httpbin.org/redirect/3')
    );
    
    // Follow redirects manually
    request.followRedirects = true;
    
    HttpClientResponse response = await request.close();
    
    print('Final URL: ${response.redirects.isNotEmpty ? 
      response.redirects.last.location : "No redirects"}');
    
    print('Status: ${response.statusCode}');
    print('Number of redirects: ${response.redirects.length}');
    
    // Show redirect chain
    if (response.redirects.isNotEmpty) {
      print('\nRedirect chain:');
      for (var redirect in response.redirects) {
        print('  ${redirect.statusCode} -> ${redirect.location}');
      }
    }
    
    await response.drain();
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

The followRedirects property controls automatic redirect following.  
maxRedirects limits redirect chains to prevent loops. The redirects  
property contains information about each redirect in the chain.  

## Compression Support

HTTP compression reduces bandwidth by compressing response bodies using  
gzip or deflate encoding.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  HttpClient client = HttpClient();
  
  // Enable automatic decompression
  client.autoUncompress = true;
  
  try {
    HttpClientRequest request = await client.getUrl(
      Uri.parse('https://jsonplaceholder.typicode.com/posts')
    );
    
    // Request compression
    request.headers.set('Accept-Encoding', 'gzip, deflate');
    
    HttpClientResponse response = await request.close();
    
    print('Status: ${response.statusCode}');
    print('Content encoding: ${response.headers.value('content-encoding')}');
    print('Content length: ${response.contentLength}');
    
    // Response is automatically decompressed
    String body = await response.transform(utf8.decoder).join();
    print('Received ${body.length} characters');
    
    List<dynamic> posts = jsonDecode(body);
    print('Parsed ${posts.length} posts');
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

The autoUncompress property enables automatic decompression. The  
Accept-Encoding header tells the server which compressions are supported.  
Compressed responses are transparently decompressed when autoUncompress  
is true.  

## Connection Pooling

Connection pooling reuses TCP connections across requests, improving  
performance for multiple requests to the same host.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  HttpClient client = HttpClient();
  
  // Configure connection pooling
  client.maxConnectionsPerHost = 10;
  client.idleTimeout = Duration(seconds: 30);
  
  try {
    print('Making multiple requests with connection pooling...\n');
    
    List<Future<void>> requests = [];
    
    for (int i = 1; i <= 5; i++) {
      Future<void> request = makeRequest(client, i);
      requests.add(request);
    }
    
    await Future.wait(requests);
    
    print('\nAll requests completed');
    
  } catch (e) {
    print('Error: $e');
  } finally {
    // Close after all requests
    client.close(force: false);
  }
}

Future<void> makeRequest(HttpClient client, int id) async {
  HttpClientRequest request = await client.getUrl(
    Uri.parse('https://jsonplaceholder.typicode.com/posts/$id')
  );
  
  HttpClientResponse response = await request.close();
  
  String body = await response.transform(utf8.decoder).join();
  Map<String, dynamic> post = jsonDecode(body);
  
  print('Request $id: ${post['title']}');
}
```

The maxConnectionsPerHost property limits concurrent connections per host.  
idleTimeout determines how long idle connections are kept alive. Pooling  
connections reduces overhead by reusing TCP connections. Call close(force:  
false) to close gracefully after requests complete.  


## WebSocket Connection

WebSockets provide full-duplex communication channels over a single TCP  
connection, ideal for real-time applications.  

```dart
import 'dart:io';

void main() async {
  try {
    // Connect to WebSocket server
    WebSocket socket = await WebSocket.connect(
      'wss://echo.websocket.org'
    );
    
    print('Connected to WebSocket server');
    
    // Send message
    socket.add('Hello there!');
    socket.add('This is a WebSocket message');
    
    // Listen for messages
    socket.listen(
      (message) {
        print('Received: $message');
      },
      onDone: () {
        print('Connection closed');
      },
      onError: (error) {
        print('Error: $error');
      },
    );
    
    // Send messages periodically
    for (int i = 1; i <= 3; i++) {
      await Future.delayed(Duration(seconds: 1));
      socket.add('Message $i');
    }
    
    // Close after delay
    await Future.delayed(Duration(seconds: 5));
    await socket.close();
    
  } catch (e) {
    print('WebSocket error: $e');
  }
}
```

WebSocket.connect establishes the connection to a WebSocket server. The  
socket is both a Stream (for receiving) and Sink (for sending). Use add()  
to send messages and listen() to receive them. Close with close() when done.  

## WebSocket Ping-Pong

Ping-pong frames keep WebSocket connections alive and detect disconnections  
in long-lived connections.  

```dart
import 'dart:io';
import 'dart:async';

void main() async {
  try {
    WebSocket socket = await WebSocket.connect(
      'wss://echo.websocket.org'
    );
    
    print('WebSocket connected');
    
    // Set ping interval
    socket.pingInterval = Duration(seconds: 5);
    
    // Track connection health
    Timer? healthCheck;
    DateTime lastPong = DateTime.now();
    
    socket.listen(
      (message) {
        print('Received: $message');
        lastPong = DateTime.now();
      },
      onDone: () {
        print('Connection closed');
        healthCheck?.cancel();
      },
      onError: (error) {
        print('Error: $error');
        healthCheck?.cancel();
      },
    );
    
    // Monitor connection health
    healthCheck = Timer.periodic(Duration(seconds: 10), (timer) {
      Duration timeSinceLastPong = DateTime.now().difference(lastPong);
      
      if (timeSinceLastPong.inSeconds > 30) {
        print('Connection appears dead, closing...');
        socket.close();
        timer.cancel();
      } else {
        print('Connection healthy (${timeSinceLastPong.inSeconds}s since last activity)');
      }
    });
    
    // Send test messages
    socket.add('Test message 1');
    
    await Future.delayed(Duration(seconds: 30));
    await socket.close();
    healthCheck?.cancel();
    
  } catch (e) {
    print('Error: $e');
  }
}
```

The pingInterval property sets automatic ping frequency. Ping frames are  
automatically handled by the WebSocket implementation. Monitor activity  
timestamps to detect stale connections. Implement reconnection logic for  
production applications.  

## WebSocket JSON Messages

WebSocket messages often carry JSON data for structured communication  
between client and server.  

```dart
import 'dart:io';
import 'dart:convert';

class WebSocketMessage {
  final String type;
  final Map<String, dynamic> data;
  
  WebSocketMessage(this.type, this.data);
  
  String toJson() {
    return jsonEncode({
      'type': type,
      'data': data,
      'timestamp': DateTime.now().toIso8601String(),
    });
  }
  
  factory WebSocketMessage.fromJson(String json) {
    Map<String, dynamic> map = jsonDecode(json);
    return WebSocketMessage(
      map['type'],
      map['data'],
    );
  }
}

void main() async {
  try {
    WebSocket socket = await WebSocket.connect(
      'wss://echo.websocket.org'
    );
    
    print('Connected');
    
    // Listen for messages
    socket.listen((message) {
      try {
        WebSocketMessage msg = WebSocketMessage.fromJson(message);
        print('Received ${msg.type}: ${msg.data}');
      } catch (e) {
        print('Non-JSON message: $message');
      }
    });
    
    // Send structured messages
    socket.add(WebSocketMessage('greeting', {'text': 'Hello there!'}).toJson());
    socket.add(WebSocketMessage('data', {'value': 42, 'unit': 'kg'}).toJson());
    
    await Future.delayed(Duration(seconds: 3));
    await socket.close();
    
  } catch (e) {
    print('Error: $e');
  }
}
```

Structured messages use JSON encoding for complex data. Create message  
classes to encapsulate type and data. Include timestamps for debugging  
and ordering. Handle both JSON and non-JSON messages gracefully.  

## WebSocket Reconnection

Automatic reconnection handles network interruptions in WebSocket  
connections, essential for reliable real-time applications.  

```dart
import 'dart:io';
import 'dart:async';

class ReconnectingWebSocket {
  final String url;
  WebSocket? _socket;
  bool _shouldReconnect = true;
  int _reconnectAttempts = 0;
  
  final StreamController<String> _messageController = 
    StreamController<String>.broadcast();
  
  Stream<String> get messages => _messageController.stream;
  
  ReconnectingWebSocket(this.url);
  
  Future<void> connect() async {
    while (_shouldReconnect) {
      try {
        print('Connecting to $url (attempt ${_reconnectAttempts + 1})...');
        
        _socket = await WebSocket.connect(url);
        _reconnectAttempts = 0;
        
        print('Connected!');
        
        _socket!.listen(
          (message) {
            _messageController.add(message);
          },
          onDone: () {
            print('Connection closed');
            _reconnect();
          },
          onError: (error) {
            print('Error: $error');
            _reconnect();
          },
        );
        
        break;
        
      } catch (e) {
        _reconnectAttempts++;
        int delay = _reconnectAttempts * 2;
        print('Connection failed, retrying in $delay seconds...');
        await Future.delayed(Duration(seconds: delay));
      }
    }
  }
  
  void _reconnect() {
    if (_shouldReconnect) {
      Future.delayed(Duration(seconds: 2), connect);
    }
  }
  
  void send(String message) {
    _socket?.add(message);
  }
  
  void close() {
    _shouldReconnect = false;
    _socket?.close();
    _messageController.close();
  }
}

void main() async {
  ReconnectingWebSocket ws = ReconnectingWebSocket(
    'wss://echo.websocket.org'
  );
  
  ws.messages.listen((message) {
    print('Received: $message');
  });
  
  await ws.connect();
  
  ws.send('Hello there!');
  
  await Future.delayed(Duration(seconds: 5));
  ws.close();
}
```

Reconnecting wrappers handle connection failures automatically. Use  
exponential backoff to avoid overwhelming servers. Broadcast streams  
allow multiple listeners. Track connection state and attempt count for  
retry logic.  

## HTTP Package Basic GET

The http package provides a simpler, higher-level API than dart:io for  
common HTTP operations.  

```dart
// Add to pubspec.yaml:
// dependencies:
//   http: ^1.1.0

import 'package:http/http.dart' as http;
import 'dart:convert';

void main() async {
  try {
    // Simple GET request
    http.Response response = await http.get(
      Uri.parse('https://jsonplaceholder.typicode.com/posts/1')
    );
    
    print('Status code: ${response.statusCode}');
    print('Headers: ${response.headers}');
    print('Content length: ${response.contentLength}');
    
    if (response.statusCode == 200) {
      Map<String, dynamic> post = jsonDecode(response.body);
      print('\nPost title: ${post['title']}');
      print('Post body: ${post['body']}');
    } else {
      print('Request failed: ${response.statusCode}');
    }
    
  } catch (e) {
    print('Error: $e');
  }
}
```

The http package provides simple functions for each HTTP method. Responses  
include the full body as a string. No need to manually close connections.  
Perfect for simple HTTP operations without advanced configuration needs.  

## HTTP Package POST Request

The http package simplifies POST requests with automatic body encoding  
and content-type headers.  

```dart
import 'package:http/http.dart' as http;
import 'dart:convert';

void main() async {
  try {
    // POST with JSON body
    http.Response response = await http.post(
      Uri.parse('https://jsonplaceholder.typicode.com/posts'),
      headers: {
        'Content-Type': 'application/json; charset=UTF-8',
      },
      body: jsonEncode({
        'title': 'Hello there!',
        'body': 'This is a new post',
        'userId': 1,
      }),
    );
    
    print('Status: ${response.statusCode}');
    
    if (response.statusCode == 201) {
      Map<String, dynamic> created = jsonDecode(response.body);
      print('Created post with ID: ${created['id']}');
    }
    
  } catch (e) {
    print('Error: $e');
  }
}
```

The http.post function accepts headers and body parameters. Encode the  
body as JSON and set the Content-Type header. The response includes the  
full body as a string for easy parsing.  

## HTTP Package with Client

Using http.Client enables connection pooling and request customization  
across multiple requests.  

```dart
import 'package:http/http.dart' as http;
import 'dart:convert';

void main() async {
  http.Client client = http.Client();
  
  try {
    // Multiple requests with the same client
    for (int i = 1; i <= 3; i++) {
      http.Response response = await client.get(
        Uri.parse('https://jsonplaceholder.typicode.com/posts/$i')
      );
      
      if (response.statusCode == 200) {
        Map<String, dynamic> post = jsonDecode(response.body);
        print('Post $i: ${post['title']}');
      }
      
      await Future.delayed(Duration(milliseconds: 500));
    }
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

Creating a Client object enables connection reuse across requests. This  
improves performance when making multiple requests. Always close the  
client when done to release resources. Clients can be configured with  
custom settings.  


## Dio Package Basic Request

Dio provides advanced HTTP features including interceptors, global  
configuration, and FormData support out of the box.  

```dart
// Add to pubspec.yaml:
// dependencies:
//   dio: ^5.4.0

import 'package:dio/dio.dart';

void main() async {
  Dio dio = Dio();
  
  try {
    // Simple GET request
    Response response = await dio.get(
      'https://jsonplaceholder.typicode.com/posts/1'
    );
    
    print('Status: ${response.statusCode}');
    print('Headers: ${response.headers}');
    
    // Response data is automatically parsed
    Map<String, dynamic> post = response.data;
    print('\nPost title: ${post['title']}');
    print('User ID: ${post['userId']}');
    
  } on DioException catch (e) {
    if (e.response != null) {
      print('Error response: ${e.response?.statusCode}');
      print('Error data: ${e.response?.data}');
    } else {
      print('Error: ${e.message}');
    }
  }
}
```

Dio automatically parses JSON responses into Dart objects. DioException  
provides detailed error information including the response if available.  
Configuration options are set on the Dio instance.  

## Dio with Interceptors

Interceptors allow modifying requests and responses globally, perfect  
for logging, authentication, and error handling.  

```dart
import 'package:dio/dio.dart';

class LoggingInterceptor extends Interceptor {
  @override
  void onRequest(RequestOptions options, RequestInterceptorHandler handler) {
    print('REQUEST[${options.method}] => ${options.uri}');
    print('Headers: ${options.headers}');
    super.onRequest(options, handler);
  }
  
  @override
  void onResponse(Response response, ResponseInterceptorHandler handler) {
    print('RESPONSE[${response.statusCode}] => ${response.requestOptions.uri}');
    super.onResponse(response, handler);
  }
  
  @override
  void onError(DioException err, ErrorInterceptorHandler handler) {
    print('ERROR[${err.response?.statusCode}] => ${err.requestOptions.uri}');
    print('Message: ${err.message}');
    super.onError(err, handler);
  }
}

void main() async {
  Dio dio = Dio();
  
  // Add interceptor
  dio.interceptors.add(LoggingInterceptor());
  
  try {
    await dio.get('https://jsonplaceholder.typicode.com/posts/1');
    print('Request completed');
  } catch (e) {
    print('Request failed: $e');
  }
}
```

Interceptors have three hooks: onRequest, onResponse, and onError. They  
execute for every request made by the Dio instance. Call handler methods  
to continue the chain or throw to stop processing.  

## Dio Authentication Interceptor

Authentication interceptors automatically add tokens to requests and  
handle token refresh on expiration.  

```dart
import 'package:dio/dio.dart';

class AuthInterceptor extends Interceptor {
  String? _accessToken;
  
  AuthInterceptor(this._accessToken);
  
  @override
  void onRequest(RequestOptions options, RequestInterceptorHandler handler) {
    // Add token to all requests
    if (_accessToken != null) {
      options.headers['Authorization'] = 'Bearer $_accessToken';
    }
    super.onRequest(options, handler);
  }
  
  @override
  void onError(DioException err, ErrorInterceptorHandler handler) async {
    // Handle 401 unauthorized
    if (err.response?.statusCode == 401) {
      print('Token expired, refreshing...');
      
      // Refresh token logic here
      // _accessToken = await refreshToken();
      
      // Retry the request with new token
      try {
        RequestOptions options = err.requestOptions;
        options.headers['Authorization'] = 'Bearer $_accessToken';
        
        Response response = await Dio().fetch(options);
        handler.resolve(response);
        return;
      } catch (e) {
        handler.reject(err);
        return;
      }
    }
    
    super.onError(err, handler);
  }
}

void main() async {
  Dio dio = Dio();
  dio.interceptors.add(AuthInterceptor('sample-token-123'));
  
  try {
    Response response = await dio.get(
      'https://api.github.com/user'
    );
    print('Authenticated request successful');
  } catch (e) {
    print('Error: $e');
  }
}
```

Authentication interceptors centralize auth logic. They add tokens to  
requests and can implement automatic token refresh on 401 responses.  
Use handler.resolve to return a new response or handler.reject to  
propagate the error.  

## Dio File Download

Dio simplifies file downloads with progress tracking and automatic  
file writing.  

```dart
import 'package:dio/dio.dart';
import 'dart:io';

void main() async {
  Dio dio = Dio();
  
  try {
    String url = 'https://jsonplaceholder.typicode.com/posts';
    String savePath = '/tmp/posts.json';
    
    print('Downloading file...');
    
    await dio.download(
      url,
      savePath,
      onReceiveProgress: (received, total) {
        if (total != -1) {
          double progress = (received / total) * 100;
          stdout.write('\rProgress: ${progress.toStringAsFixed(1)}%');
        }
      },
    );
    
    print('\n\nDownload complete!');
    print('File saved to: $savePath');
    
    // Verify file
    File file = File(savePath);
    int size = await file.length();
    print('File size: $size bytes');
    
  } on DioException catch (e) {
    print('\nDownload failed: ${e.message}');
  }
}
```

The download method writes responses directly to files. The  
onReceiveProgress callback provides download progress. The total parameter  
is -1 if the server doesn't provide Content-Length. Use for efficient  
large file downloads.  

## Dio File Upload

Dio's FormData class simplifies multipart file uploads with automatic  
boundary handling.  

```dart
import 'package:dio/dio.dart';
import 'dart:io';

void main() async {
  Dio dio = Dio();
  
  try {
    // Create a sample file
    File file = File('/tmp/upload.txt');
    await file.writeAsString('Hello there! This is test content.');
    
    // Prepare form data
    FormData formData = FormData.fromMap({
      'description': 'Sample file upload',
      'category': 'test',
      'file': await MultipartFile.fromFile(
        file.path,
        filename: 'upload.txt',
      ),
    });
    
    print('Uploading file...');
    
    Response response = await dio.post(
      'https://httpbin.org/post',
      data: formData,
      onSendProgress: (sent, total) {
        double progress = (sent / total) * 100;
        stdout.write('\rUpload progress: ${progress.toStringAsFixed(1)}%');
      },
    );
    
    print('\n\nUpload complete!');
    print('Response status: ${response.statusCode}');
    
  } on DioException catch (e) {
    print('\nUpload failed: ${e.message}');
  }
}
```

FormData handles multipart encoding automatically. MultipartFile.fromFile  
reads file content. The onSendProgress callback tracks upload progress.  
Multiple files can be included in the same FormData.  

## Dio Request Cancellation

Dio supports request cancellation using CancelToken, useful for  
implementing request timeout or user cancellation.  

```dart
import 'package:dio/dio.dart';

void main() async {
  Dio dio = Dio();
  CancelToken cancelToken = CancelToken();
  
  try {
    // Start a slow request
    print('Starting request...');
    
    Future<Response> requestFuture = dio.get(
      'https://httpbin.org/delay/10',
      cancelToken: cancelToken,
    );
    
    // Cancel after 2 seconds
    Future.delayed(Duration(seconds: 2), () {
      print('Cancelling request...');
      cancelToken.cancel('Request cancelled by user');
    });
    
    Response response = await requestFuture;
    print('Response: ${response.statusCode}');
    
  } on DioException catch (e) {
    if (e.type == DioExceptionType.cancel) {
      print('Request was cancelled: ${e.message}');
    } else {
      print('Error: ${e.message}');
    }
  }
}
```

CancelToken enables request cancellation. Call cancel() with an optional  
message. Cancelled requests throw DioException with type CANCEL. Check  
the exception type to distinguish cancellation from other errors.  

## Dio Base Options Configuration

BaseOptions configure global settings for all requests made by a Dio  
instance including timeouts, headers, and base URLs.  

```dart
import 'package:dio/dio.dart';

void main() async {
  Dio dio = Dio(
    BaseOptions(
      baseUrl: 'https://jsonplaceholder.typicode.com',
      connectTimeout: Duration(seconds: 5),
      receiveTimeout: Duration(seconds: 10),
      headers: {
        'User-Agent': 'DartApp/1.0',
        'Accept': 'application/json',
      },
      validateStatus: (status) {
        // Accept all status codes less than 500
        return status != null && status < 500;
      },
    ),
  );
  
  try {
    // Relative URLs use baseUrl
    Response response1 = await dio.get('/posts/1');
    print('Post: ${response1.data['title']}');
    
    // Can override options per request
    Response response2 = await dio.get(
      '/posts/2',
      options: Options(
        headers: {'X-Custom-Header': 'value'},
        receiveTimeout: Duration(seconds: 5),
      ),
    );
    print('Post: ${response2.data['title']}');
    
  } on DioException catch (e) {
    print('Error: ${e.message}');
  }
}
```

BaseOptions set defaults for all requests. The baseUrl combines with  
relative paths. Timeouts prevent hanging requests. validateStatus  
determines which status codes throw errors. Per-request options override  
base options.  

## Response Streaming

Processing large responses as streams prevents loading entire payloads  
into memory at once.  

```dart
import 'package:dio/dio.dart';
import 'dart:convert';

void main() async {
  Dio dio = Dio();
  
  try {
    Response<ResponseBody> response = await dio.get<ResponseBody>(
      'https://jsonplaceholder.typicode.com/posts',
      options: Options(
        responseType: ResponseType.stream,
      ),
    );
    
    print('Status: ${response.statusCode}');
    print('Streaming response...\n');
    
    int bytesReceived = 0;
    List<int> bytes = [];
    
    // Process stream
    await for (var chunk in response.data!.stream) {
      bytes.addAll(chunk);
      bytesReceived += chunk.length;
      print('Received $bytesReceived bytes');
    }
    
    // Parse complete response
    String body = utf8.decode(bytes);
    List<dynamic> posts = jsonDecode(body);
    print('\nParsed ${posts.length} posts');
    
  } on DioException catch (e) {
    print('Error: ${e.message}');
  }
}
```

Set responseType to ResponseType.stream to get a ResponseBody stream.  
The stream emits data chunks as they arrive. Accumulate chunks for  
complete parsing or process incrementally. Useful for large downloads.  


## GraphQL Query

GraphQL queries fetch exactly the data needed using a query language  
instead of fixed REST endpoints.  

```dart
import 'package:dio/dio.dart';

void main() async {
  Dio dio = Dio();
  
  try {
    String query = '''
      query GetUser(\$login: String!) {
        user(login: \$login) {
          name
          bio
          repositories(first: 5) {
            nodes {
              name
              stargazerCount
            }
          }
        }
      }
    ''';
    
    Response response = await dio.post(
      'https://api.github.com/graphql',
      data: {
        'query': query,
        'variables': {
          'login': 'dart-lang',
        },
      },
      options: Options(
        headers: {
          'Authorization': 'Bearer YOUR_TOKEN_HERE',
          'Content-Type': 'application/json',
        },
      ),
    );
    
    Map<String, dynamic> data = response.data['data'];
    Map<String, dynamic> user = data['user'];
    
    print('User: ${user['name']}');
    print('Bio: ${user['bio']}');
    print('\nRepositories:');
    
    List<dynamic> repos = user['repositories']['nodes'];
    for (var repo in repos) {
      print('  ${repo['name']} - ${repo['stargazerCount']} stars');
    }
    
  } on DioException catch (e) {
    print('Error: ${e.message}');
  }
}
```

GraphQL uses POST requests with query and variables in the body. Queries  
define the exact structure of data to fetch. Variables parameterize  
queries for reusability. The response mirrors the query structure.  

## REST API Client Class

Organizing API calls into a client class provides reusable, maintainable  
networking code.  

```dart
import 'package:dio/dio.dart';

class JsonPlaceholderApi {
  final Dio _dio;
  
  JsonPlaceholderApi() : _dio = Dio(
    BaseOptions(
      baseUrl: 'https://jsonplaceholder.typicode.com',
      connectTimeout: Duration(seconds: 5),
      receiveTimeout: Duration(seconds: 10),
    ),
  );
  
  Future<List<dynamic>> getPosts() async {
    Response response = await _dio.get('/posts');
    return response.data;
  }
  
  Future<Map<String, dynamic>> getPost(int id) async {
    Response response = await _dio.get('/posts/$id');
    return response.data;
  }
  
  Future<Map<String, dynamic>> createPost({
    required String title,
    required String body,
    required int userId,
  }) async {
    Response response = await _dio.post(
      '/posts',
      data: {
        'title': title,
        'body': body,
        'userId': userId,
      },
    );
    return response.data;
  }
  
  Future<void> deletePost(int id) async {
    await _dio.delete('/posts/$id');
  }
}

void main() async {
  JsonPlaceholderApi api = JsonPlaceholderApi();
  
  try {
    // Get all posts
    List<dynamic> posts = await api.getPosts();
    print('Found ${posts.length} posts');
    
    // Get specific post
    Map<String, dynamic> post = await api.getPost(1);
    print('Post: ${post['title']}');
    
    // Create new post
    Map<String, dynamic> newPost = await api.createPost(
      title: 'Hello there!',
      body: 'New post content',
      userId: 1,
    );
    print('Created post with ID: ${newPost['id']}');
    
  } catch (e) {
    print('Error: $e');
  }
}
```

API client classes encapsulate all networking logic for a service. Methods  
provide type-safe interfaces to endpoints. BaseOptions configure common  
settings. This pattern improves code organization and testability.  

## Pagination Handling

Handling paginated API responses enables fetching large datasets in  
manageable chunks.  

```dart
import 'package:dio/dio.dart';

class PaginatedApi {
  final Dio _dio = Dio();
  
  Future<List<dynamic>> getAllPosts() async {
    List<dynamic> allPosts = [];
    int page = 1;
    int perPage = 10;
    
    while (true) {
      print('Fetching page $page...');
      
      Response response = await _dio.get(
        'https://jsonplaceholder.typicode.com/posts',
        queryParameters: {
          '_page': page,
          '_limit': perPage,
        },
      );
      
      List<dynamic> posts = response.data;
      
      if (posts.isEmpty) {
        break;
      }
      
      allPosts.addAll(posts);
      print('  Received ${posts.length} posts (total: ${allPosts.length})');
      
      // Check if there are more pages
      if (posts.length < perPage) {
        break;
      }
      
      page++;
      
      // Safety limit
      if (page > 20) {
        print('Reached page limit');
        break;
      }
    }
    
    return allPosts;
  }
}

void main() async {
  PaginatedApi api = PaginatedApi();
  
  try {
    List<dynamic> allPosts = await api.getAllPosts();
    print('\nTotal posts fetched: ${allPosts.length}');
  } catch (e) {
    print('Error: $e');
  }
}
```

Pagination loops fetch pages until no more data is available. Query  
parameters control page number and size. Check response length to detect  
the last page. Include safety limits to prevent infinite loops.  

## Rate Limiting

Rate limiting prevents overwhelming APIs with too many requests,  
respecting API limits and quotas.  

```dart
import 'package:dio/dio.dart';

class RateLimitedApi {
  final Dio _dio = Dio();
  final int _requestsPerSecond;
  DateTime _lastRequest = DateTime.now();
  
  RateLimitedApi({int requestsPerSecond = 2}) 
    : _requestsPerSecond = requestsPerSecond;
  
  Future<void> _waitForRateLimit() async {
    Duration minInterval = Duration(
      milliseconds: 1000 ~/ _requestsPerSecond
    );
    
    Duration elapsed = DateTime.now().difference(_lastRequest);
    
    if (elapsed < minInterval) {
      Duration waitTime = minInterval - elapsed;
      await Future.delayed(waitTime);
    }
    
    _lastRequest = DateTime.now();
  }
  
  Future<Map<String, dynamic>> getPost(int id) async {
    await _waitForRateLimit();
    
    Response response = await _dio.get(
      'https://jsonplaceholder.typicode.com/posts/$id'
    );
    
    return response.data;
  }
}

void main() async {
  RateLimitedApi api = RateLimitedApi(requestsPerSecond: 2);
  
  print('Fetching posts with rate limiting...\n');
  
  for (int i = 1; i <= 5; i++) {
    DateTime start = DateTime.now();
    Map<String, dynamic> post = await api.getPost(i);
    Duration elapsed = DateTime.now().difference(start);
    
    print('Post $i: ${post['title']}');
    print('  Time: ${elapsed.inMilliseconds}ms\n');
  }
  
  print('All requests completed');
}
```

Rate limiting calculates minimum time between requests. The waitForRateLimit  
method enforces delays when needed. Track the last request time to compute  
delays. This prevents API throttling and respects usage limits.  

## Caching Responses

Caching network responses improves performance and reduces API calls for  
frequently accessed data.  

```dart
import 'package:dio/dio.dart';

class CachedApi {
  final Dio _dio = Dio();
  final Map<String, _CacheEntry> _cache = {};
  final Duration _cacheDuration = Duration(minutes: 5);
  
  Future<Map<String, dynamic>> getPost(int id) async {
    String key = 'post-$id';
    
    // Check cache
    if (_cache.containsKey(key)) {
      _CacheEntry entry = _cache[key]!;
      
      if (DateTime.now().difference(entry.timestamp) < _cacheDuration) {
        print('Cache hit for $key');
        return entry.data;
      } else {
        print('Cache expired for $key');
        _cache.remove(key);
      }
    }
    
    // Fetch from API
    print('Fetching $key from API');
    Response response = await _dio.get(
      'https://jsonplaceholder.typicode.com/posts/$id'
    );
    
    Map<String, dynamic> data = response.data;
    
    // Store in cache
    _cache[key] = _CacheEntry(data);
    
    return data;
  }
  
  void clearCache() {
    _cache.clear();
  }
}

class _CacheEntry {
  final Map<String, dynamic> data;
  final DateTime timestamp;
  
  _CacheEntry(this.data) : timestamp = DateTime.now();
}

void main() async {
  CachedApi api = CachedApi();
  
  try {
    // First request - fetches from API
    print('Request 1:');
    await api.getPost(1);
    
    print('\nRequest 2:');
    await api.getPost(1);
    
    print('\nRequest 3:');
    await api.getPost(2);
    
  } catch (e) {
    print('Error: $e');
  }
}
```

Caching stores responses with timestamps. Check cache before making  
requests. Expire entries after a duration. This reduces network calls  
and improves response times for repeated requests.  

## Request Queue Management

Request queues manage concurrent requests, preventing too many simultaneous  
connections and enabling prioritization.  

```dart
import 'package:dio/dio.dart';
import 'dart:collection';

class QueuedApi {
  final Dio _dio = Dio();
  final Queue<_QueuedRequest> _queue = Queue();
  int _activeRequests = 0;
  final int _maxConcurrent = 3;
  
  Future<Map<String, dynamic>> getPost(int id) async {
    _QueuedRequest queuedRequest = _QueuedRequest(id);
    _queue.add(queuedRequest);
    
    _processQueue();
    
    return await queuedRequest.completer.future;
  }
  
  void _processQueue() {
    while (_queue.isNotEmpty && _activeRequests < _maxConcurrent) {
      _QueuedRequest request = _queue.removeFirst();
      _activeRequests++;
      
      _executeRequest(request);
    }
  }
  
  void _executeRequest(_QueuedRequest request) async {
    try {
      print('Executing request for post ${request.id} ($_activeRequests active)');
      
      Response response = await _dio.get(
        'https://jsonplaceholder.typicode.com/posts/${request.id}'
      );
      
      request.completer.complete(response.data);
      
    } catch (e) {
      request.completer.completeError(e);
    } finally {
      _activeRequests--;
      _processQueue();
    }
  }
}

class _QueuedRequest {
  final int id;
  final Completer<Map<String, dynamic>> completer = Completer();
  
  _QueuedRequest(this.id);
}

void main() async {
  QueuedApi api = QueuedApi();
  
  print('Queuing 10 requests with max 3 concurrent...\n');
  
  List<Future<Map<String, dynamic>>> futures = [];
  
  for (int i = 1; i <= 10; i++) {
    futures.add(api.getPost(i));
  }
  
  List<Map<String, dynamic>> results = await Future.wait(futures);
  
  print('\nAll requests completed');
  print('Total: ${results.length} posts');
}
```

Request queues use a Queue to manage pending requests. Track active  
request count to enforce concurrency limits. Process queued requests as  
slots become available. Completers bridge queuing and async/await.  


## Network Connectivity Check

Checking network connectivity before making requests prevents errors and  
improves user experience.  

```dart
import 'dart:io';

Future<bool> checkConnectivity() async {
  try {
    // Try to lookup a reliable host
    List<InternetAddress> result = await InternetAddress.lookup('google.com');
    
    if (result.isNotEmpty && result[0].rawAddress.isNotEmpty) {
      return true;
    }
    
    return false;
    
  } on SocketException catch (_) {
    return false;
  }
}

Future<void> makeNetworkRequest() async {
  print('Checking network connectivity...');
  
  bool isConnected = await checkConnectivity();
  
  if (!isConnected) {
    print('No network connection available');
    return;
  }
  
  print('Network connected, making request...');
  
  HttpClient client = HttpClient();
  
  try {
    HttpClientRequest request = await client.getUrl(
      Uri.parse('https://jsonplaceholder.typicode.com/posts/1')
    );
    
    HttpClientResponse response = await request.close();
    String body = await response.transform(utf8.decoder).join();
    
    print('Request successful!');
    print('Body length: ${body.length} characters');
    
  } catch (e) {
    print('Request failed: $e');
  } finally {
    client.close();
  }
}

void main() async {
  await makeNetworkRequest();
}
```

Network connectivity checks use DNS lookups to reliable hosts. This  
detects internet availability before making requests. Handle SocketException  
for offline scenarios. Show appropriate UI feedback based on connectivity.  

## Batch Request Processing

Batch processing groups multiple related requests for efficient execution  
and result aggregation.  

```dart
import 'package:dio/dio.dart';

class BatchApi {
  final Dio _dio = Dio();
  
  Future<Map<String, dynamic>> batchGetPosts(List<int> ids) async {
    print('Fetching ${ids.length} posts in batch...\n');
    
    // Create futures for all requests
    List<Future<Response>> futures = ids.map((id) {
      return _dio.get('https://jsonplaceholder.typicode.com/posts/$id');
    }).toList();
    
    // Execute in parallel
    List<Response> responses = await Future.wait(
      futures,
      eagerError: false,
    );
    
    // Process results
    Map<String, dynamic> results = {
      'successful': [],
      'failed': [],
    };
    
    for (int i = 0; i < responses.length; i++) {
      try {
        Response response = responses[i];
        
        if (response.statusCode == 200) {
          results['successful'].add({
            'id': ids[i],
            'data': response.data,
          });
        } else {
          results['failed'].add({
            'id': ids[i],
            'error': 'Status ${response.statusCode}',
          });
        }
      } catch (e) {
        results['failed'].add({
          'id': ids[i],
          'error': e.toString(),
        });
      }
    }
    
    return results;
  }
}

void main() async {
  BatchApi api = BatchApi();
  
  try {
    Map<String, dynamic> results = await api.batchGetPosts([1, 2, 3, 4, 5]);
    
    print('Successful: ${results['successful'].length}');
    print('Failed: ${results['failed'].length}');
    
    for (var item in results['successful']) {
      print('  Post ${item['id']}: ${item['data']['title']}');
    }
    
  } catch (e) {
    print('Batch error: $e');
  }
}
```

Batch processing uses Future.wait with eagerError: false to collect all  
results even if some fail. Separate successful and failed requests for  
proper handling. This pattern efficiently processes multiple related  
requests.  

## Conditional Requests (ETags)

ETags enable conditional requests that save bandwidth by only transferring  
data when it has changed.  

```dart
import 'dart:io';
import 'dart:convert';

class ETagCache {
  String? _etag;
  String? _cachedData;
  
  Future<String?> fetchData(String url) async {
    HttpClient client = HttpClient();
    
    try {
      HttpClientRequest request = await client.getUrl(Uri.parse(url));
      
      // Send If-None-Match header if we have an ETag
      if (_etag != null) {
        request.headers.set('If-None-Match', _etag!);
        print('Sending If-None-Match: $_etag');
      }
      
      HttpClientResponse response = await request.close();
      
      if (response.statusCode == 304) {
        // Not Modified - use cached data
        print('Status 304: Data not modified, using cache');
        return _cachedData;
        
      } else if (response.statusCode == 200) {
        // New data available
        print('Status 200: Fetching new data');
        
        // Store new ETag
        _etag = response.headers.value('etag');
        print('New ETag: $_etag');
        
        // Store response
        _cachedData = await response.transform(utf8.decoder).join();
        return _cachedData;
        
      } else {
        throw Exception('Unexpected status: ${response.statusCode}');
      }
      
    } finally {
      client.close();
    }
  }
}

void main() async {
  ETagCache cache = ETagCache();
  
  String url = 'https://httpbin.org/etag/test-etag';
  
  print('First request:');
  await cache.fetchData(url);
  
  print('\nSecond request:');
  await cache.fetchData(url);
  
  print('\nThird request:');
  await cache.fetchData(url);
}
```

ETags identify specific versions of resources. Send the If-None-Match  
header with stored ETag. Status 304 means data hasn't changed, use cached  
version. Status 200 means new data, update cache and ETag.  

## Proxy Configuration

Proxy configuration routes requests through intermediate servers for  
security, caching, or network policy compliance.  

```dart
import 'dart:io';

void main() async {
  HttpClient client = HttpClient();
  
  // Configure proxy
  client.findProxy = (uri) {
    // Use proxy for all requests
    return 'PROXY proxy.example.com:8080';
    
    // Or use different proxies based on URI
    // if (uri.host.contains('api.example.com')) {
    //   return 'PROXY api-proxy.example.com:8080';
    // }
    // return 'DIRECT'; // No proxy
  };
  
  // Configure proxy authentication
  client.authenticateProxy = (host, port, scheme, realm) async {
    return HttpClientCredentials.basic('username', 'password');
  };
  
  try {
    print('Making request through proxy...');
    
    HttpClientRequest request = await client.getUrl(
      Uri.parse('https://jsonplaceholder.typicode.com/posts/1')
    );
    
    HttpClientResponse response = await request.close();
    
    print('Status: ${response.statusCode}');
    await response.drain();
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

The findProxy callback returns proxy configuration for each request. Return  
'DIRECT' for no proxy, 'PROXY host:port' for a proxy. The authenticateProxy  
callback provides credentials. Useful for corporate networks.  

## Custom Request Headers Per Environment

Managing different configurations for development, staging, and production  
environments.  

```dart
import 'package:dio/dio.dart';

enum Environment { development, staging, production }

class EnvironmentConfig {
  final String baseUrl;
  final String apiKey;
  final Duration timeout;
  final bool debug;
  
  EnvironmentConfig({
    required this.baseUrl,
    required this.apiKey,
    required this.timeout,
    required this.debug,
  });
  
  factory EnvironmentConfig.fromEnvironment(Environment env) {
    switch (env) {
      case Environment.development:
        return EnvironmentConfig(
          baseUrl: 'https://dev-api.example.com',
          apiKey: 'dev-key-123',
          timeout: Duration(seconds: 30),
          debug: true,
        );
      case Environment.staging:
        return EnvironmentConfig(
          baseUrl: 'https://staging-api.example.com',
          apiKey: 'staging-key-456',
          timeout: Duration(seconds: 15),
          debug: true,
        );
      case Environment.production:
        return EnvironmentConfig(
          baseUrl: 'https://api.example.com',
          apiKey: 'prod-key-789',
          timeout: Duration(seconds: 10),
          debug: false,
        );
    }
  }
}

class ApiClient {
  final Dio _dio;
  final EnvironmentConfig _config;
  
  ApiClient(Environment env) 
    : _config = EnvironmentConfig.fromEnvironment(env),
      _dio = Dio() {
    
    _dio.options.baseUrl = _config.baseUrl;
    _dio.options.connectTimeout = _config.timeout;
    _dio.options.headers['X-API-Key'] = _config.apiKey;
    
    if (_config.debug) {
      _dio.interceptors.add(LogInterceptor(
        responseBody: true,
        requestBody: true,
      ));
    }
  }
  
  Future<List<dynamic>> getPosts() async {
    Response response = await _dio.get('/posts');
    return response.data;
  }
}

void main() async {
  // Use different environments
  ApiClient devApi = ApiClient(Environment.development);
  ApiClient prodApi = ApiClient(Environment.production);
  
  try {
    print('Development environment:');
    print('  Base URL: ${devApi._config.baseUrl}');
    print('  Debug: ${devApi._config.debug}');
    
    print('\nProduction environment:');
    print('  Base URL: ${prodApi._config.baseUrl}');
    print('  Debug: ${prodApi._config.debug}');
    
  } catch (e) {
    print('Error: $e');
  }
}
```

Environment configurations centralize settings for different deployment  
targets. Use factory constructors to create environment-specific configs.  
Apply different timeouts, URLs, and debug settings per environment.  

## Request Retry with Circuit Breaker

Circuit breaker pattern prevents cascading failures by stopping requests  
to failing services temporarily.  

```dart
import 'package:dio/dio.dart';

enum CircuitState { closed, open, halfOpen }

class CircuitBreaker {
  CircuitState _state = CircuitState.closed;
  int _failureCount = 0;
  DateTime? _openedAt;
  
  final int failureThreshold = 3;
  final Duration resetTimeout = Duration(seconds: 30);
  
  Future<T> execute<T>(Future<T> Function() operation) async {
    if (_state == CircuitState.open) {
      // Check if we should try half-open
      if (_openedAt != null &&
          DateTime.now().difference(_openedAt!) > resetTimeout) {
        print('Circuit breaker: Trying half-open state');
        _state = CircuitState.halfOpen;
      } else {
        throw Exception('Circuit breaker is OPEN - service unavailable');
      }
    }
    
    try {
      T result = await operation();
      _onSuccess();
      return result;
      
    } catch (e) {
      _onFailure();
      rethrow;
    }
  }
  
  void _onSuccess() {
    _failureCount = 0;
    if (_state == CircuitState.halfOpen) {
      print('Circuit breaker: Closed (recovered)');
      _state = CircuitState.closed;
    }
  }
  
  void _onFailure() {
    _failureCount++;
    
    if (_failureCount >= failureThreshold) {
      print('Circuit breaker: OPEN (too many failures)');
      _state = CircuitState.open;
      _openedAt = DateTime.now();
    }
  }
}

void main() async {
  Dio dio = Dio();
  CircuitBreaker breaker = CircuitBreaker();
  
  for (int i = 1; i <= 10; i++) {
    try {
      print('\nAttempt $i:');
      
      await breaker.execute(() async {
        // Simulate failing service
        if (i <= 5) {
          throw DioException(
            requestOptions: RequestOptions(path: '/test'),
            message: 'Simulated failure',
          );
        }
        
        Response response = await dio.get(
          'https://jsonplaceholder.typicode.com/posts/1'
        );
        
        print('  Success: ${response.statusCode}');
        return response;
      });
      
    } catch (e) {
      print('  Error: $e');
    }
    
    await Future.delayed(Duration(seconds: 1));
  }
}
```

Circuit breakers track failure counts and open after a threshold. The  
open state rejects requests immediately. After a timeout, the half-open  
state tries requests again. Success in half-open closes the circuit.  

## Mock HTTP Server for Testing

Creating mock servers enables testing network code without real API  
dependencies.  

```dart
import 'dart:io';
import 'dart:convert';

class MockServer {
  HttpServer? _server;
  
  Future<void> start({int port = 8080}) async {
    _server = await HttpServer.bind('localhost', port);
    print('Mock server running on http://localhost:$port');
    
    await for (HttpRequest request in _server!) {
      _handleRequest(request);
    }
  }
  
  void _handleRequest(HttpRequest request) {
    print('${request.method} ${request.uri.path}');
    
    if (request.method == 'GET' && request.uri.path == '/posts/1') {
      // Mock GET response
      Map<String, dynamic> post = {
        'id': 1,
        'title': 'Hello there!',
        'body': 'This is a mock post',
        'userId': 1,
      };
      
      request.response
        ..statusCode = HttpStatus.ok
        ..headers.contentType = ContentType.json
        ..write(jsonEncode(post))
        ..close();
        
    } else if (request.method == 'POST' && request.uri.path == '/posts') {
      // Mock POST response
      request.transform(utf8.decoder).join().then((body) {
        Map<String, dynamic> data = jsonDecode(body);
        data['id'] = 101;
        
        request.response
          ..statusCode = HttpStatus.created
          ..headers.contentType = ContentType.json
          ..write(jsonEncode(data))
          ..close();
      });
      
    } else {
      // 404 for unknown endpoints
      request.response
        ..statusCode = HttpStatus.notFound
        ..write('Not found')
        ..close();
    }
  }
  
  Future<void> stop() async {
    await _server?.close();
    print('Mock server stopped');
  }
}

Future<void> testApi() async {
  HttpClient client = HttpClient();
  
  try {
    // Test GET
    HttpClientRequest request = await client.getUrl(
      Uri.parse('http://localhost:8080/posts/1')
    );
    
    HttpClientResponse response = await request.close();
    String body = await response.transform(utf8.decoder).join();
    
    print('\nGET response:');
    print(body);
    
  } finally {
    client.close();
  }
}

void main() async {
  MockServer server = MockServer();
  
  // Start server in background
  Future serverFuture = server.start();
  
  // Wait for server to start
  await Future.delayed(Duration(seconds: 1));
  
  // Test the API
  await testApi();
  
  // Stop server
  await server.stop();
}
```

Mock servers simulate API behavior for testing. Handle different HTTP  
methods and paths. Return appropriate status codes and JSON responses.  
Useful for integration tests and offline development.  

## Bandwidth Throttling

Bandwidth throttling limits transfer speeds to simulate slow connections  
or conserve resources.  

```dart
import 'dart:io';
import 'dart:async';

class ThrottledStream extends Stream<List<int>> {
  final Stream<List<int>> _source;
  final int _bytesPerSecond;
  
  ThrottledStream(this._source, this._bytesPerSecond);
  
  @override
  StreamSubscription<List<int>> listen(
    void Function(List<int> event)? onData, {
    Function? onError,
    void Function()? onDone,
    bool? cancelOnError,
  }) {
    StreamController<List<int>> controller = StreamController();
    int bytesThisSecond = 0;
    DateTime secondStart = DateTime.now();
    
    _source.listen(
      (chunk) async {
        // Reset counter each second
        DateTime now = DateTime.now();
        if (now.difference(secondStart).inSeconds >= 1) {
          bytesThisSecond = 0;
          secondStart = now;
        }
        
        // Check if we need to throttle
        if (bytesThisSecond + chunk.length > _bytesPerSecond) {
          Duration waitTime = Duration(seconds: 1) -
            now.difference(secondStart);
          
          if (waitTime.inMilliseconds > 0) {
            await Future.delayed(waitTime);
            bytesThisSecond = 0;
            secondStart = DateTime.now();
          }
        }
        
        bytesThisSecond += chunk.length;
        controller.add(chunk);
      },
      onError: controller.addError,
      onDone: controller.close,
      cancelOnError: cancelOnError,
    );
    
    return controller.stream.listen(
      onData,
      onError: onError,
      onDone: onDone,
      cancelOnError: cancelOnError,
    );
  }
}

void main() async {
  HttpClient client = HttpClient();
  
  try {
    print('Downloading with bandwidth throttling...\n');
    
    HttpClientRequest request = await client.getUrl(
      Uri.parse('https://jsonplaceholder.typicode.com/posts')
    );
    
    HttpClientResponse response = await request.close();
    
    // Throttle to 1KB per second
    ThrottledStream throttled = ThrottledStream(
      response,
      1024, // bytes per second
    );
    
    int totalBytes = 0;
    DateTime start = DateTime.now();
    
    await for (var chunk in throttled) {
      totalBytes += chunk.length;
      Duration elapsed = DateTime.now().difference(start);
      double rate = totalBytes / elapsed.inSeconds;
      
      print('Received $totalBytes bytes (${rate.toStringAsFixed(0)} B/s)');
    }
    
    print('\nDownload complete: $totalBytes bytes');
    
  } catch (e) {
    print('Error: $e');
  } finally {
    client.close();
  }
}
```

Throttled streams delay chunks to limit transfer rate. Track bytes per  
second and insert delays when limits are exceeded. Useful for simulating  
slow networks during testing and development.  

## Network Request Metrics

Collecting metrics about network requests helps monitor performance and  
diagnose issues.  

```dart
import 'package:dio/dio.dart';

class NetworkMetrics {
  int totalRequests = 0;
  int successfulRequests = 0;
  int failedRequests = 0;
  List<Duration> requestDurations = [];
  
  void recordRequest({
    required Duration duration,
    required bool success,
  }) {
    totalRequests++;
    
    if (success) {
      successfulRequests++;
    } else {
      failedRequests++;
    }
    
    requestDurations.add(duration);
  }
  
  Duration get averageDuration {
    if (requestDurations.isEmpty) return Duration.zero;
    
    int total = requestDurations
      .map((d) => d.inMilliseconds)
      .reduce((a, b) => a + b);
    
    return Duration(milliseconds: total ~/ requestDurations.length);
  }
  
  double get successRate {
    if (totalRequests == 0) return 0.0;
    return (successfulRequests / totalRequests) * 100;
  }
  
  void printReport() {
    print('\n=== Network Metrics Report ===');
    print('Total requests: $totalRequests');
    print('Successful: $successfulRequests');
    print('Failed: $failedRequests');
    print('Success rate: ${successRate.toStringAsFixed(1)}%');
    print('Average duration: ${averageDuration.inMilliseconds}ms');
    
    if (requestDurations.isNotEmpty) {
      Duration min = requestDurations.reduce(
        (a, b) => a.inMilliseconds < b.inMilliseconds ? a : b
      );
      Duration max = requestDurations.reduce(
        (a, b) => a.inMilliseconds > b.inMilliseconds ? a : b
      );
      
      print('Min duration: ${min.inMilliseconds}ms');
      print('Max duration: ${max.inMilliseconds}ms');
    }
  }
}

class MetricsInterceptor extends Interceptor {
  final NetworkMetrics metrics;
  
  MetricsInterceptor(this.metrics);
  
  @override
  void onRequest(RequestOptions options, RequestInterceptorHandler handler) {
    options.extra['start_time'] = DateTime.now();
    super.onRequest(options, handler);
  }
  
  @override
  void onResponse(Response response, ResponseInterceptorHandler handler) {
    DateTime start = response.requestOptions.extra['start_time'];
    Duration duration = DateTime.now().difference(start);
    
    metrics.recordRequest(duration: duration, success: true);
    super.onResponse(response, handler);
  }
  
  @override
  void onError(DioException err, ErrorInterceptorHandler handler) {
    DateTime start = err.requestOptions.extra['start_time'];
    Duration duration = DateTime.now().difference(start);
    
    metrics.recordRequest(duration: duration, success: false);
    super.onError(err, handler);
  }
}

void main() async {
  NetworkMetrics metrics = NetworkMetrics();
  
  Dio dio = Dio();
  dio.interceptors.add(MetricsInterceptor(metrics));
  
  // Make several requests
  for (int i = 1; i <= 5; i++) {
    try {
      await dio.get('https://jsonplaceholder.typicode.com/posts/$i');
      print('Request $i completed');
    } catch (e) {
      print('Request $i failed');
    }
    
    await Future.delayed(Duration(milliseconds: 200));
  }
  
  metrics.printReport();
}
```

Metrics track request counts, success rates, and durations. Interceptors  
automatically collect timing data. Calculate statistics like average,  
min, and max durations. Use metrics for monitoring and optimization.  


## Advanced Error Recovery Strategy

Comprehensive error recovery combines multiple strategies for maximum  
reliability in production networking code.  

```dart
import 'package:dio/dio.dart';

class RobustApiClient {
  final Dio _dio;
  final int maxRetries = 3;
  final Duration retryDelay = Duration(seconds: 2);
  
  RobustApiClient() : _dio = Dio(
    BaseOptions(
      baseUrl: 'https://jsonplaceholder.typicode.com',
      connectTimeout: Duration(seconds: 10),
      receiveTimeout: Duration(seconds: 30),
    ),
  );
  
  Future<T> executeWithRecovery<T>({
    required Future<Response<T>> Function() operation,
    T Function(Response<T>)? transform,
  }) async {
    int attempt = 0;
    List<String> errors = [];
    
    while (attempt < maxRetries) {
      attempt++;
      
      try {
        print('Attempt $attempt of $maxRetries');
        
        Response<T> response = await operation();
        
        // Validate response
        if (response.statusCode == null || response.statusCode! < 200 || 
            response.statusCode! >= 300) {
          throw DioException(
            requestOptions: response.requestOptions,
            response: response,
            message: 'Invalid status code: ${response.statusCode}',
          );
        }
        
        // Transform if needed
        if (transform != null) {
          return transform(response);
        }
        
        return response.data as T;
        
      } on DioException catch (e) {
        String errorMsg = _categorizeError(e);
        errors.add('Attempt $attempt: $errorMsg');
        
        print('Error: $errorMsg');
        
        // Don't retry client errors (4xx)
        if (e.response?.statusCode != null &&
            e.response!.statusCode! >= 400 &&
            e.response!.statusCode! < 500) {
          print('Client error - not retrying');
          rethrow;
        }
        
        // Last attempt - throw
        if (attempt >= maxRetries) {
          print('Max retries exceeded');
          throw Exception('Failed after $maxRetries attempts:\n${errors.join('\n')}');
        }
        
        // Wait before retry with exponential backoff
        Duration delay = retryDelay * attempt;
        print('Waiting ${delay.inSeconds}s before retry...\n');
        await Future.delayed(delay);
        
      } catch (e) {
        errors.add('Attempt $attempt: Unexpected error: $e');
        
        if (attempt >= maxRetries) {
          throw Exception('Failed after $maxRetries attempts:\n${errors.join('\n')}');
        }
        
        await Future.delayed(retryDelay * attempt);
      }
    }
    
    throw Exception('Failed to execute request');
  }
  
  String _categorizeError(DioException e) {
    switch (e.type) {
      case DioExceptionType.connectionTimeout:
        return 'Connection timeout';
      case DioExceptionType.sendTimeout:
        return 'Send timeout';
      case DioExceptionType.receiveTimeout:
        return 'Receive timeout';
      case DioExceptionType.badResponse:
        return 'Bad response: ${e.response?.statusCode}';
      case DioExceptionType.cancel:
        return 'Request cancelled';
      case DioExceptionType.connectionError:
        return 'Connection error';
      default:
        return 'Unknown error: ${e.message}';
    }
  }
  
  Future<Map<String, dynamic>> getPost(int id) async {
    return await executeWithRecovery(
      operation: () => _dio.get<Map<String, dynamic>>('/posts/$id'),
      transform: (response) => response.data!,
    );
  }
}

void main() async {
  RobustApiClient client = RobustApiClient();
  
  try {
    print('Fetching post with advanced error recovery...\n');
    
    Map<String, dynamic> post = await client.getPost(1);
    
    print('\nSuccess!');
    print('Title: ${post['title']}');
    print('Body: ${post['body']}');
    
  } catch (e) {
    print('\nFinal error: $e');
  }
}
```

Advanced error recovery combines retries, exponential backoff, error  
categorization, and selective retry logic. Track all errors for debugging.  
Avoid retrying client errors but retry network and server errors. Use  
generic type parameters for flexibility. This production-ready pattern  
maximizes reliability while respecting server resources.  

