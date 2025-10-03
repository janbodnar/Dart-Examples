# Low-Level Networking in Dart

Low-level networking in Dart provides direct access to socket programming,  
TCP/UDP protocols, raw data transmission, and custom protocol implementation.  
Unlike high-level HTTP libraries, low-level networking gives developers  
complete control over network communication, enabling the creation of custom  
protocols, real-time applications, game servers, IoT devices, and  
high-performance network services.  

Dart's dart:io library offers comprehensive support for socket programming  
through classes like Socket, ServerSocket, RawSocket, and RawDatagramSocket.  
These APIs provide both stream-based and event-driven interfaces for network  
communication, allowing developers to choose the most appropriate abstraction  
for their use case. The asynchronous nature of Dart makes it exceptionally  
well-suited for handling multiple concurrent connections efficiently.  

Socket programming involves establishing connections between client and  
server applications over TCP or UDP protocols. TCP provides reliable,  
ordered, connection-oriented communication ideal for applications requiring  
guaranteed delivery. UDP offers connectionless, lightweight communication  
suitable for real-time applications where speed matters more than reliability.  

Understanding low-level networking is essential for building custom protocols,  
implementing network services, debugging network issues, optimizing performance,  
and creating applications that require fine-grained control over network  
behavior. This knowledge enables developers to implement protocols like HTTP,  
WebSocket, custom RPC mechanisms, game networking, and IoT communication.  

This comprehensive guide demonstrates 60 practical examples covering socket  
creation, connection management, data transmission, protocol implementation,  
stream manipulation, error handling, and performance optimization. Each  
example builds progressively from basic concepts to advanced techniques,  
providing both client and server implementations where applicable.  

## Basic TCP Socket Client

The simplest TCP client connects to a server, sends data, and receives  
a response using Dart's Socket class.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  try {
    // Connect to a TCP server
    Socket socket = await Socket.connect('echo.websocket.org', 80);
    
    print('Connected to ${socket.remoteAddress.address}:${socket.remotePort}');
    print('Local port: ${socket.port}');
    
    // Send HTTP request
    socket.write('GET / HTTP/1.1\r\n');
    socket.write('Host: echo.websocket.org\r\n');
    socket.write('Connection: close\r\n');
    socket.write('\r\n');
    
    // Listen for response
    await for (var data in socket) {
      print(utf8.decode(data));
    }
    
    await socket.close();
    print('Connection closed');
    
  } catch (e) {
    print('Error: $e');
  }
}
```

Socket.connect establishes a TCP connection to the specified host and port.  
The socket acts as both a Stream (for receiving) and IOSink (for sending).  
Data is transmitted as bytes. Always close sockets to free system resources.  

## Basic TCP Socket Server

A TCP server listens for incoming connections and handles client requests  
using ServerSocket to accept multiple concurrent connections.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  try {
    // Bind server to port 8080
    ServerSocket server = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      8080,
    );
    
    print('Server listening on port ${server.port}');
    
    // Handle incoming connections
    await for (Socket client in server) {
      print('Client connected from ${client.remoteAddress.address}:${client.remotePort}');
      
      // Handle client in separate async function
      handleClient(client);
    }
    
  } catch (e) {
    print('Server error: $e');
  }
}

void handleClient(Socket client) {
  client.listen(
    (data) {
      // Echo received data back to client
      String message = utf8.decode(data);
      print('Received: $message');
      client.write('Echo: $message');
    },
    onDone: () {
      print('Client disconnected');
      client.close();
    },
    onError: (error) {
      print('Client error: $error');
      client.close();
    },
  );
}
```

ServerSocket.bind creates a server listening on the specified address and  
port. The server is a Stream of Socket connections. Each client connection  
runs concurrently. Use InternetAddress.anyIPv4 to listen on all interfaces.  

## TCP Client with Timeout

Connection timeouts prevent indefinite waiting when servers are unavailable  
or network issues occur.  

```dart
import 'dart:io';
import 'dart:async';
import 'dart:convert';

void main() async {
  try {
    // Connect with 5-second timeout
    Socket socket = await Socket.connect(
      'example.com',
      80,
    ).timeout(
      Duration(seconds: 5),
      onTimeout: () {
        throw TimeoutException('Connection timeout');
      },
    );
    
    print('Connected successfully');
    
    // Set timeout for operations
    socket.write('GET / HTTP/1.1\r\nHost: example.com\r\n\r\n');
    
    // Read with timeout
    String response = '';
    await for (var data in socket.timeout(Duration(seconds: 10))) {
      response += utf8.decode(data);
      if (response.contains('</html>')) break;
    }
    
    print('Received ${response.length} bytes');
    await socket.close();
    
  } on TimeoutException catch (e) {
    print('Timeout: $e');
  } catch (e) {
    print('Error: $e');
  }
}
```

The timeout method wraps Future and Stream operations with time limits.  
TimeoutException is thrown when operations exceed the duration. Always set  
appropriate timeouts to prevent hanging connections.  

## TCP Server with Connection Limit

Limiting concurrent connections prevents resource exhaustion and ensures  
server stability under load.  

```dart
import 'dart:io';
import 'dart:convert';

class LimitedServer {
  final int maxConnections;
  int _activeConnections = 0;
  
  LimitedServer({this.maxConnections = 10});
  
  Future<void> start(int port) async {
    ServerSocket server = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Server running on port $port (max $maxConnections connections)');
    
    await for (Socket client in server) {
      if (_activeConnections >= maxConnections) {
        print('Connection limit reached, rejecting client');
        client.write('Server full\n');
        client.close();
        continue;
      }
      
      _activeConnections++;
      print('Connection accepted ($_activeConnections/$maxConnections)');
      
      handleClient(client);
    }
  }
  
  void handleClient(Socket client) {
    client.listen(
      (data) {
        String message = utf8.decode(data).trim();
        print('Received: $message');
        client.write('Echo: $message\n');
      },
      onDone: () {
        _activeConnections--;
        print('Client disconnected ($_activeConnections/$maxConnections)');
        client.close();
      },
      onError: (error) {
        _activeConnections--;
        print('Error: $error');
        client.close();
      },
    );
  }
}

void main() async {
  LimitedServer server = LimitedServer(maxConnections: 5);
  await server.start(8080);
}
```

Track active connections and reject new ones when the limit is reached.  
This prevents server overload and ensures fair resource allocation. Always  
decrement the counter when connections close.  

## UDP Datagram Client

UDP provides connectionless communication ideal for real-time applications  
where speed matters more than reliability.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  // Create UDP socket
  RawDatagramSocket socket = await RawDatagramSocket.bind(
    InternetAddress.anyIPv4,
    0, // Any available port
  );
  
  print('UDP client bound to port ${socket.port}');
  
  // Destination
  InternetAddress destination = InternetAddress('8.8.8.8');
  int destinationPort = 53; // DNS
  
  // Send datagram
  String message = 'Hello there!';
  List<int> data = utf8.encode(message);
  
  int sent = socket.send(data, destination, destinationPort);
  print('Sent $sent bytes to $destination:$destinationPort');
  
  // Listen for responses (with timeout)
  bool received = false;
  socket.listen((event) {
    if (event == RawSocketEvent.read) {
      Datagram? datagram = socket.receive();
      if (datagram != null) {
        String response = utf8.decode(datagram.data);
        print('Received from ${datagram.address}:${datagram.port}: $response');
        received = true;
      }
    }
  });
  
  // Wait briefly then close
  await Future.delayed(Duration(seconds: 2));
  socket.close();
  
  if (!received) {
    print('No response received');
  }
}
```

RawDatagramSocket handles UDP communication. Unlike TCP, UDP is connectionless  
and doesn't guarantee delivery. Use send() to transmit datagrams and listen  
for RawSocketEvent.read to receive them.  

## UDP Datagram Server

A UDP server receives datagrams from multiple clients without maintaining  
persistent connections.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  // Bind UDP socket
  RawDatagramSocket socket = await RawDatagramSocket.bind(
    InternetAddress.anyIPv4,
    8888,
  );
  
  print('UDP server listening on port ${socket.port}');
  
  socket.listen((event) {
    if (event == RawSocketEvent.read) {
      Datagram? datagram = socket.receive();
      
      if (datagram != null) {
        String message = utf8.decode(datagram.data);
        print('Received from ${datagram.address}:${datagram.port}: $message');
        
        // Echo back to sender
        String response = 'Echo: $message';
        List<int> responseData = utf8.encode(response);
        socket.send(responseData, datagram.address, datagram.port);
        
        print('Sent response to ${datagram.address}:${datagram.port}');
      }
    }
  });
  
  print('Server running. Press Ctrl+C to stop.');
}
```

UDP servers don't accept connections but receive datagrams from any source.  
Each datagram includes the sender's address and port for replies. No  
connection state is maintained between datagrams.  

## Socket with KeepAlive

TCP keep-alive probes detect dead connections and prevent resource leaks  
from abandoned sockets.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  try {
    Socket socket = await Socket.connect('example.com', 80);
    
    // Enable keep-alive
    socket.setOption(SocketOption.tcpKeepAlive, true);
    
    print('Connected with keep-alive enabled');
    print('Remote: ${socket.remoteAddress.address}:${socket.remotePort}');
    
    // Send request
    socket.write('GET / HTTP/1.1\r\n');
    socket.write('Host: example.com\r\n');
    socket.write('Connection: keep-alive\r\n');
    socket.write('\r\n');
    
    // Listen for data
    await for (var data in socket) {
      print('Received ${data.length} bytes');
    }
    
    await socket.close();
    
  } catch (e) {
    print('Error: $e');
  }
}
```

SocketOption.tcpKeepAlive enables TCP keep-alive probes that detect broken  
connections. This is essential for long-lived connections to detect network  
failures or crashed remote hosts.  

## Socket with No Delay (Nagle Algorithm Disable)

Disabling Nagle's algorithm reduces latency for small, frequent messages  
critical in real-time applications.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  try {
    Socket socket = await Socket.connect('localhost', 8080);
    
    // Disable Nagle's algorithm for lower latency
    socket.setOption(SocketOption.tcpNoDelay, true);
    
    print('Connected with TCP_NODELAY');
    
    // Send multiple small messages without buffering
    for (int i = 0; i < 10; i++) {
      String message = 'Message $i\n';
      socket.write(message);
      print('Sent: $message');
      
      // Small delay between messages
      await Future.delayed(Duration(milliseconds: 10));
    }
    
    await Future.delayed(Duration(seconds: 1));
    await socket.close();
    
  } catch (e) {
    print('Error: $e');
  }
}
```

SocketOption.tcpNoDelay disables Nagle's algorithm, which batches small  
packets. This reduces latency at the cost of increased packet count. Use  
for real-time, interactive applications where latency matters.  

## RawSocket Event-Driven Programming

RawSocket provides event-driven I/O for fine-grained control over socket  
operations and non-blocking communication.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  try {
    // Connect using RawSocket
    RawSocket socket = await RawSocket.connect('example.com', 80);
    
    print('Connected to ${socket.remoteAddress.address}');
    
    bool requestSent = false;
    String response = '';
    
    socket.listen((event) {
      switch (event) {
        case RawSocketEvent.read:
          // Data available to read
          var data = socket.read();
          if (data != null) {
            response += utf8.decode(data);
            print('Read ${data.length} bytes');
          }
          break;
          
        case RawSocketEvent.write:
          // Socket ready for writing
          if (!requestSent) {
            socket.write(utf8.encode(
              'GET / HTTP/1.1\r\nHost: example.com\r\n\r\n'
            ));
            requestSent = true;
            print('Request sent');
          }
          break;
          
        case RawSocketEvent.readClosed:
          // Remote end closed reading
          print('Read side closed');
          socket.close();
          break;
          
        case RawSocketEvent.closed:
          // Socket closed
          print('Socket closed');
          print('Total response: ${response.length} bytes');
          break;
      }
    });
    
  } catch (e) {
    print('Error: $e');
  }
}
```

RawSocket provides event-based I/O with explicit read/write control.  
RawSocketEvent indicates socket state changes. This low-level API enables  
non-blocking I/O and precise control over data flow.  

## Raw Socket Server with Event Loop

Event-driven server handling enables efficient multiplexing of multiple  
client connections without blocking.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  RawServerSocket server = await RawServerSocket.bind(
    InternetAddress.anyIPv4,
    8080,
  );
  
  print('Raw server listening on port ${server.port}');
  
  server.listen((RawSocket client) {
    print('Client connected from ${client.remoteAddress.address}');
    
    client.listen((event) {
      switch (event) {
        case RawSocketEvent.read:
          var data = client.read();
          if (data != null) {
            String message = utf8.decode(data);
            print('Received: $message');
            
            // Echo back
            String response = 'Echo: $message';
            client.write(utf8.encode(response));
          }
          break;
          
        case RawSocketEvent.write:
          // Ready for writing - could buffer data here
          break;
          
        case RawSocketEvent.readClosed:
          print('Client closed read side');
          client.close();
          break;
          
        case RawSocketEvent.closed:
          print('Client disconnected');
          break;
      }
    });
  });
}
```

RawServerSocket accepts RawSocket connections. Event-driven architecture  
handles multiple clients efficiently. Each client generates events  
independently, enabling concurrent processing without threads.  

## Binary Data Transmission

Transmitting raw binary data requires careful handling of byte arrays  
and data encoding.  

```dart
import 'dart:io';
import 'dart:typed_data';

void main() async {
  try {
    Socket socket = await Socket.connect('localhost', 8080);
    
    // Create binary data
    ByteData header = ByteData(8);
    header.setUint32(0, 0x12345678); // Magic number
    header.setUint32(4, 100); // Data length
    
    // Payload
    Uint8List payload = Uint8List(100);
    for (int i = 0; i < 100; i++) {
      payload[i] = i & 0xFF;
    }
    
    // Send header
    socket.add(header.buffer.asUint8List());
    print('Sent header: 8 bytes');
    
    // Send payload
    socket.add(payload);
    print('Sent payload: ${payload.length} bytes');
    
    await socket.flush();
    
    // Read response
    await for (var data in socket) {
      print('Received ${data.length} bytes');
    }
    
    await socket.close();
    
  } catch (e) {
    print('Error: $e');
  }
}
```

Use ByteData for structured binary data with specific byte ordering.  
Uint8List represents raw byte arrays. Socket.add() sends binary data  
directly. Use flush() to ensure data is sent immediately.  

## Binary Data Reception and Parsing

Receiving binary data requires buffering and parsing structured formats  
from the byte stream.  

```dart
import 'dart:io';
import 'dart:typed_data';
import 'dart:async';

class BinaryServer {
  Future<void> start(int port) async {
    ServerSocket server = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Binary server listening on port $port');
    
    await for (Socket client in server) {
      print('Client connected');
      handleBinaryClient(client);
    }
  }
  
  void handleBinaryClient(Socket client) {
    List<int> buffer = [];
    
    client.listen(
      (data) {
        buffer.addAll(data);
        
        // Try to parse complete messages
        while (buffer.length >= 8) {
          // Read header
          ByteData header = ByteData.sublistView(
            Uint8List.fromList(buffer.sublist(0, 8))
          );
          
          int magic = header.getUint32(0);
          int length = header.getUint32(4);
          
          print('Header: magic=0x${magic.toRadixString(16)}, length=$length');
          
          // Check if we have the full payload
          if (buffer.length >= 8 + length) {
            Uint8List payload = Uint8List.fromList(
              buffer.sublist(8, 8 + length)
            );
            
            print('Received payload: ${payload.length} bytes');
            
            // Process message
            processMessage(magic, payload, client);
            
            // Remove processed data from buffer
            buffer = buffer.sublist(8 + length);
          } else {
            // Wait for more data
            break;
          }
        }
      },
      onDone: () {
        print('Client disconnected');
        client.close();
      },
      onError: (error) {
        print('Error: $error');
        client.close();
      },
    );
  }
  
  void processMessage(int magic, Uint8List payload, Socket client) {
    print('Processing message with magic 0x${magic.toRadixString(16)}');
    
    // Echo back
    ByteData response = ByteData(8);
    response.setUint32(0, magic);
    response.setUint32(4, payload.length);
    
    client.add(response.buffer.asUint8List());
    client.add(payload);
  }
}

void main() async {
  BinaryServer server = BinaryServer();
  await server.start(8080);
}
```

Buffer incoming data until complete messages are received. Parse headers  
to determine payload size. Handle partial messages gracefully. This pattern  
is essential for binary protocols with variable-length messages.  

## Line-Based Protocol

Line-based protocols delimit messages with newline characters, common in  
text-based network protocols.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  try {
    Socket socket = await Socket.connect('localhost', 8080);
    
    // Transform socket stream to lines
    Stream<String> lines = socket
        .transform(utf8.decoder)
        .transform(LineSplitter());
    
    // Send commands
    socket.writeln('HELLO');
    socket.writeln('GET status');
    socket.writeln('QUIT');
    
    // Process responses line by line
    await for (String line in lines) {
      print('Received: $line');
      
      if (line == 'BYE') {
        break;
      }
    }
    
    await socket.close();
    print('Connection closed');
    
  } catch (e) {
    print('Error: $e');
  }
}
```

Transform byte streams into line-delimited strings using utf8.decoder and  
LineSplitter. This simplifies implementing text-based protocols like SMTP,  
POP3, or custom command protocols.  

## Line-Based Protocol Server

Implementing a server for line-based protocols demonstrates command  
processing and response generation.  

```dart
import 'dart:io';
import 'dart:convert';

class LineProtocolServer {
  Future<void> start(int port) async {
    ServerSocket server = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Line protocol server listening on port $port');
    
    await for (Socket client in server) {
      print('Client connected from ${client.remoteAddress.address}');
      handleClient(client);
    }
  }
  
  void handleClient(Socket client) {
    // Send greeting
    client.writeln('WELCOME');
    
    // Process commands
    client
        .transform(utf8.decoder)
        .transform(LineSplitter())
        .listen(
          (line) {
            String command = line.trim().toUpperCase();
            print('Command: $command');
            
            switch (command) {
              case 'HELLO':
                client.writeln('HELLO there!');
                break;
                
              case 'GET STATUS':
                client.writeln('STATUS OK');
                break;
                
              case 'GET TIME':
                client.writeln('TIME ${DateTime.now().toIso8601String()}');
                break;
                
              case 'QUIT':
                client.writeln('BYE');
                client.close();
                break;
                
              default:
                client.writeln('ERROR Unknown command');
            }
          },
          onDone: () {
            print('Client disconnected');
            client.close();
          },
          onError: (error) {
            print('Error: $error');
            client.close();
          },
        );
  }
}

void main() async {
  LineProtocolServer server = LineProtocolServer();
  await server.start(8080);
}
```

Parse incoming lines as commands and generate appropriate responses. Use  
writeln() for line-based replies. This pattern is perfect for interactive  
protocols and debugging network services.  

## Custom Length-Prefixed Protocol

Length-prefixed protocols include message size in headers, enabling  
efficient parsing of variable-length messages.  

```dart
import 'dart:io';
import 'dart:typed_data';
import 'dart:convert';

class LengthPrefixedClient {
  late Socket _socket;
  
  Future<void> connect(String host, int port) async {
    _socket = await Socket.connect(host, port);
    print('Connected to $host:$port');
  }
  
  void sendMessage(String message) {
    // Encode message
    List<int> messageBytes = utf8.encode(message);
    int length = messageBytes.length;
    
    // Create length-prefixed packet
    ByteData header = ByteData(4);
    header.setUint32(0, length, Endian.big);
    
    // Send length then message
    _socket.add(header.buffer.asUint8List());
    _socket.add(messageBytes);
    
    print('Sent message: $length bytes');
  }
  
  Stream<String> receiveMessages() async* {
    List<int> buffer = [];
    
    await for (var data in _socket) {
      buffer.addAll(data);
      
      while (buffer.length >= 4) {
        // Read length prefix
        ByteData lengthData = ByteData.sublistView(
          Uint8List.fromList(buffer.sublist(0, 4))
        );
        int messageLength = lengthData.getUint32(0, Endian.big);
        
        // Check if full message is available
        if (buffer.length >= 4 + messageLength) {
          // Extract message
          List<int> messageBytes = buffer.sublist(4, 4 + messageLength);
          String message = utf8.decode(messageBytes);
          
          yield message;
          
          // Remove from buffer
          buffer = buffer.sublist(4 + messageLength);
        } else {
          break;
        }
      }
    }
  }
  
  Future<void> close() async {
    await _socket.close();
  }
}

void main() async {
  LengthPrefixedClient client = LengthPrefixedClient();
  
  try {
    await client.connect('localhost', 8080);
    
    // Send messages
    client.sendMessage('Hello there!');
    client.sendMessage('This is a test message');
    client.sendMessage('Goodbye');
    
    // Receive responses
    await for (String message in client.receiveMessages()) {
      print('Received: $message');
    }
    
  } catch (e) {
    print('Error: $e');
  } finally {
    await client.close();
  }
}
```

Length-prefixed protocols send message size before content. This eliminates  
ambiguity in parsing variable-length messages. Use ByteData for precise  
control over integer encoding and endianness.  

## Custom Length-Prefixed Protocol Server

Server implementation for length-prefixed protocols handles message  
framing and processing.  

```dart
import 'dart:io';
import 'dart:typed_data';
import 'dart:convert';

class LengthPrefixedServer {
  Future<void> start(int port) async {
    ServerSocket server = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Length-prefixed server listening on port $port');
    
    await for (Socket client in server) {
      print('Client connected');
      handleClient(client);
    }
  }
  
  void handleClient(Socket client) {
    List<int> buffer = [];
    
    client.listen(
      (data) {
        buffer.addAll(data);
        
        while (buffer.length >= 4) {
          // Read length
          ByteData lengthData = ByteData.sublistView(
            Uint8List.fromList(buffer.sublist(0, 4))
          );
          int length = lengthData.getUint32(0, Endian.big);
          
          // Validate length
          if (length > 1024 * 1024) {
            print('Message too large: $length bytes');
            client.close();
            return;
          }
          
          // Check if complete message is available
          if (buffer.length >= 4 + length) {
            List<int> messageBytes = buffer.sublist(4, 4 + length);
            String message = utf8.decode(messageBytes);
            
            print('Received: $message');
            
            // Process and respond
            String response = 'Echo: $message';
            sendMessage(client, response);
            
            buffer = buffer.sublist(4 + length);
          } else {
            break;
          }
        }
      },
      onDone: () {
        print('Client disconnected');
        client.close();
      },
      onError: (error) {
        print('Error: $error');
        client.close();
      },
    );
  }
  
  void sendMessage(Socket client, String message) {
    List<int> messageBytes = utf8.encode(message);
    int length = messageBytes.length;
    
    ByteData header = ByteData(4);
    header.setUint32(0, length, Endian.big);
    
    client.add(header.buffer.asUint8List());
    client.add(messageBytes);
  }
}

void main() async {
  LengthPrefixedServer server = LengthPrefixedServer();
  await server.start(8080);
}
```

Validate message lengths to prevent memory exhaustion attacks. Buffer data  
until complete messages are available. This protocol design is robust and  
efficient for variable-length message transmission.  

## Multicast UDP Communication

Multicast enables one-to-many communication where a single message reaches  
multiple recipients simultaneously.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  // Multicast group
  InternetAddress multicastAddress = InternetAddress('239.1.1.1');
  int port = 9999;
  
  // Create UDP socket
  RawDatagramSocket socket = await RawDatagramSocket.bind(
    InternetAddress.anyIPv4,
    port,
  );
  
  // Join multicast group
  socket.joinMulticast(multicastAddress);
  print('Joined multicast group ${multicastAddress.address}:$port');
  
  // Send multicast message
  String message = 'Hello there, multicast group!';
  List<int> data = utf8.encode(message);
  socket.send(data, multicastAddress, port);
  print('Sent multicast message');
  
  // Listen for multicast messages
  socket.listen((event) {
    if (event == RawSocketEvent.read) {
      Datagram? datagram = socket.receive();
      if (datagram != null) {
        String received = utf8.decode(datagram.data);
        print('Received from ${datagram.address}: $received');
      }
    }
  });
  
  // Send periodic messages
  int count = 0;
  while (count < 5) {
    await Future.delayed(Duration(seconds: 2));
    String msg = 'Multicast message $count';
    socket.send(utf8.encode(msg), multicastAddress, port);
    print('Sent: $msg');
    count++;
  }
  
  socket.close();
}
```

Multicast uses special IP addresses (224.0.0.0 to 239.255.255.255 for IPv4).  
joinMulticast() subscribes to a group. All group members receive sent  
messages. Useful for service discovery and pub/sub patterns.  

## Broadcast UDP Communication

Broadcast sends messages to all devices on a local network segment  
simultaneously.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  // Create UDP socket
  RawDatagramSocket socket = await RawDatagramSocket.bind(
    InternetAddress.anyIPv4,
    0,
  );
  
  // Enable broadcast
  socket.broadcastEnabled = true;
  print('Broadcast enabled on port ${socket.port}');
  
  // Broadcast address (local network)
  InternetAddress broadcastAddress = InternetAddress('255.255.255.255');
  int targetPort = 9999;
  
  // Send broadcast message
  String message = 'Discovery request - who is there?';
  List<int> data = utf8.encode(message);
  
  int sent = socket.send(data, broadcastAddress, targetPort);
  print('Sent broadcast: $sent bytes to $broadcastAddress:$targetPort');
  
  // Listen for responses
  int responseCount = 0;
  socket.listen((event) {
    if (event == RawSocketEvent.read) {
      Datagram? datagram = socket.receive();
      if (datagram != null) {
        String response = utf8.decode(datagram.data);
        print('Response from ${datagram.address}: $response');
        responseCount++;
      }
    }
  });
  
  // Wait for responses
  await Future.delayed(Duration(seconds: 3));
  print('Received $responseCount responses');
  
  socket.close();
}
```

Enable broadcasting with broadcastEnabled property. Broadcast to  
255.255.255.255 for local network or subnet-specific addresses. Useful  
for network discovery and zero-configuration networking.  

## Stream Buffering and Backpressure

Managing backpressure prevents memory exhaustion when producers send  
data faster than consumers can process.  

```dart
import 'dart:io';
import 'dart:convert';
import 'dart:async';

class BufferedServer {
  Future<void> start(int port) async {
    ServerSocket server = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Buffered server listening on port $port');
    
    await for (Socket client in server) {
      print('Client connected');
      handleClientWithBackpressure(client);
    }
  }
  
  void handleClientWithBackpressure(Socket client) {
    // Create buffered stream with backpressure
    StreamController<String> controller = StreamController<String>(
      onPause: () {
        print('Stream paused - backpressure active');
      },
      onResume: () {
        print('Stream resumed');
      },
    );
    
    // Convert socket to lines
    client
        .transform(utf8.decoder)
        .transform(LineSplitter())
        .listen(
          (line) {
            controller.add(line);
          },
          onDone: () {
            controller.close();
          },
        );
    
    // Process with controlled rate
    controller.stream.listen(
      (line) async {
        print('Processing: $line');
        
        // Simulate slow processing
        await Future.delayed(Duration(milliseconds: 500));
        
        // Send response
        client.writeln('Processed: $line');
      },
      onDone: () {
        print('Processing complete');
        client.close();
      },
    );
  }
}

void main() async {
  BufferedServer server = BufferedServer();
  await server.start(8080);
}
```

StreamController handles backpressure automatically when consumers can't  
keep up with producers. The stream pauses when buffering limits are reached,  
preventing memory overflow.  

## Stream Transformation Pipeline

Transform network data through processing pipelines for filtering,  
modification, and routing.  

```dart
import 'dart:io';
import 'dart:convert';
import 'dart:async';

class TransformServer {
  Future<void> start(int port) async {
    ServerSocket server = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Transform server listening on port $port');
    
    await for (Socket client in server) {
      print('Client connected');
      handleClientWithTransforms(client);
    }
  }
  
  void handleClientWithTransforms(Socket client) {
    client
        .transform(utf8.decoder)
        .transform(LineSplitter())
        .where((line) => line.isNotEmpty) // Filter empty lines
        .map((line) => line.trim().toUpperCase()) // Transform to uppercase
        .map((line) => 'ECHO: $line') // Add prefix
        .listen(
          (transformed) {
            print('Transformed: $transformed');
            client.writeln(transformed);
          },
          onDone: () {
            print('Client disconnected');
            client.close();
          },
          onError: (error) {
            print('Error: $error');
            client.close();
          },
        );
  }
}

void main() async {
  TransformServer server = TransformServer();
  await server.start(8080);
}
```

Chain stream transformers using where, map, and custom transformers.  
Each transformation processes data before passing to the next stage.  
This pattern enables clean separation of concerns in data processing.  

## Custom Stream Transformer

Creating custom stream transformers enables reusable data processing  
logic for network protocols.  

```dart
import 'dart:io';
import 'dart:convert';
import 'dart:async';

class MessageFrameTransformer extends StreamTransformerBase<List<int>, String> {
  @override
  Stream<String> bind(Stream<List<int>> stream) {
    return Stream<String>.eventTransformed(
      stream,
      (sink) => MessageFrameSink(sink),
    );
  }
}

class MessageFrameSink implements EventSink<List<int>> {
  final EventSink<String> _output;
  final List<int> _buffer = [];
  
  MessageFrameSink(this._output);
  
  @override
  void add(List<int> data) {
    _buffer.addAll(data);
    
    // Process complete frames (length-prefixed)
    while (_buffer.length >= 4) {
      int length = (_buffer[0] << 24) |
                   (_buffer[1] << 16) |
                   (_buffer[2] << 8) |
                   _buffer[3];
      
      if (_buffer.length >= 4 + length) {
        List<int> message = _buffer.sublist(4, 4 + length);
        String decoded = utf8.decode(message);
        _output.add(decoded);
        
        _buffer.removeRange(0, 4 + length);
      } else {
        break;
      }
    }
  }
  
  @override
  void addError(Object error, [StackTrace? stackTrace]) {
    _output.addError(error, stackTrace);
  }
  
  @override
  void close() {
    _output.close();
  }
}

void main() async {
  try {
    Socket socket = await Socket.connect('localhost', 8080);
    
    // Use custom transformer
    socket
        .transform(MessageFrameTransformer())
        .listen(
          (message) {
            print('Received message: $message');
          },
          onDone: () {
            print('Stream closed');
          },
        );
    
    // Send framed messages
    void sendFramedMessage(String msg) {
      List<int> bytes = utf8.encode(msg);
      int length = bytes.length;
      
      List<int> frame = [
        (length >> 24) & 0xFF,
        (length >> 16) & 0xFF,
        (length >> 8) & 0xFF,
        length & 0xFF,
        ...bytes,
      ];
      
      socket.add(frame);
    }
    
    sendFramedMessage('Hello there!');
    sendFramedMessage('Custom transformer working');
    
    await Future.delayed(Duration(seconds: 2));
    await socket.close();
    
  } catch (e) {
    print('Error: $e');
  }
}
```

Custom transformers implement StreamTransformerBase and EventSink for  
complex protocol handling. This encapsulates framing logic and makes it  
reusable across different socket connections.  

## Connection Pooling

Connection pools reuse sockets across multiple operations, improving  
performance and resource utilization.  

```dart
import 'dart:io';
import 'dart:collection';
import 'dart:async';

class SocketPool {
  final String host;
  final int port;
  final int maxConnections;
  
  final Queue<Socket> _availableSockets = Queue<Socket>();
  int _activeConnections = 0;
  
  SocketPool({
    required this.host,
    required this.port,
    this.maxConnections = 10,
  });
  
  Future<Socket> acquire() async {
    // Reuse available socket
    if (_availableSockets.isNotEmpty) {
      print('Reusing pooled connection');
      return _availableSockets.removeFirst();
    }
    
    // Create new connection if under limit
    if (_activeConnections < maxConnections) {
      print('Creating new connection');
      _activeConnections++;
      Socket socket = await Socket.connect(host, port);
      return socket;
    }
    
    // Wait for available connection
    print('Waiting for available connection');
    while (_availableSockets.isEmpty) {
      await Future.delayed(Duration(milliseconds: 100));
    }
    
    return _availableSockets.removeFirst();
  }
  
  void release(Socket socket) {
    // Return socket to pool
    if (_availableSockets.length < maxConnections) {
      print('Returning connection to pool');
      _availableSockets.add(socket);
    } else {
      print('Pool full, closing connection');
      socket.close();
      _activeConnections--;
    }
  }
  
  Future<void> closeAll() async {
    print('Closing all pooled connections');
    
    while (_availableSockets.isNotEmpty) {
      Socket socket = _availableSockets.removeFirst();
      await socket.close();
    }
    
    _activeConnections = 0;
  }
}

void main() async {
  SocketPool pool = SocketPool(
    host: 'localhost',
    port: 8080,
    maxConnections: 5,
  );
  
  try {
    // Make multiple requests using pool
    for (int i = 0; i < 10; i++) {
      Socket socket = await pool.acquire();
      
      socket.writeln('Request $i');
      
      // Simulate work
      await Future.delayed(Duration(milliseconds: 100));
      
      pool.release(socket);
    }
    
  } finally {
    await pool.closeAll();
  }
}
```

Connection pooling reduces overhead of creating new connections. Reuse  
idle sockets for subsequent requests. Limit total connections to prevent  
resource exhaustion. Essential for high-performance network applications.  

## Request-Response Pattern

Implementing request-response correlation enables multiple concurrent  
requests on a single connection.  

```dart
import 'dart:io';
import 'dart:convert';
import 'dart:async';

class RequestResponseClient {
  late Socket _socket;
  int _nextRequestId = 0;
  final Map<int, Completer<String>> _pendingRequests = {};
  
  Future<void> connect(String host, int port) async {
    _socket = await Socket.connect(host, port);
    print('Connected to $host:$port');
    
    // Listen for responses
    _socket
        .transform(utf8.decoder)
        .transform(LineSplitter())
        .listen(_handleResponse);
  }
  
  void _handleResponse(String line) {
    // Parse response: "ID:message"
    List<String> parts = line.split(':');
    if (parts.length >= 2) {
      int id = int.parse(parts[0]);
      String message = parts.sublist(1).join(':');
      
      // Complete corresponding request
      Completer<String>? completer = _pendingRequests.remove(id);
      if (completer != null) {
        completer.complete(message);
      }
    }
  }
  
  Future<String> sendRequest(String message) {
    int requestId = _nextRequestId++;
    Completer<String> completer = Completer<String>();
    
    _pendingRequests[requestId] = completer;
    
    // Send request with ID
    _socket.writeln('$requestId:$message');
    
    return completer.future;
  }
  
  Future<void> close() async {
    await _socket.close();
  }
}

void main() async {
  RequestResponseClient client = RequestResponseClient();
  
  try {
    await client.connect('localhost', 8080);
    
    // Send multiple concurrent requests
    List<Future<String>> requests = [
      client.sendRequest('QUERY 1'),
      client.sendRequest('QUERY 2'),
      client.sendRequest('QUERY 3'),
    ];
    
    // Wait for all responses
    List<String> responses = await Future.wait(requests);
    
    for (int i = 0; i < responses.length; i++) {
      print('Response $i: ${responses[i]}');
    }
    
  } finally {
    await client.close();
  }
}
```

Request-response correlation uses IDs to match responses with requests.  
Completers enable async/await for responses. This pattern allows multiple  
concurrent requests without blocking, improving throughput.  

## Request-Response Server

Server implementation for request-response pattern processes concurrent  
requests and returns correlated responses.  

```dart
import 'dart:io';
import 'dart:convert';
import 'dart:async';

class RequestResponseServer {
  Future<void> start(int port) async {
    ServerSocket server = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Request-response server listening on port $port');
    
    await for (Socket client in server) {
      print('Client connected');
      handleClient(client);
    }
  }
  
  void handleClient(Socket client) {
    client
        .transform(utf8.decoder)
        .transform(LineSplitter())
        .listen(
          (line) async {
            // Parse request: "ID:message"
            List<String> parts = line.split(':');
            if (parts.length >= 2) {
              String id = parts[0];
              String message = parts.sublist(1).join(':');
              
              print('Request $id: $message');
              
              // Process request (simulated async work)
              String response = await processRequest(message);
              
              // Send response with same ID
              client.writeln('$id:$response');
            }
          },
          onDone: () {
            print('Client disconnected');
            client.close();
          },
          onError: (error) {
            print('Error: $error');
            client.close();
          },
        );
  }
  
  Future<String> processRequest(String request) async {
    // Simulate async processing
    await Future.delayed(Duration(milliseconds: 100));
    
    if (request.startsWith('QUERY')) {
      return 'RESULT for $request';
    } else {
      return 'UNKNOWN COMMAND';
    }
  }
}

void main() async {
  RequestResponseServer server = RequestResponseServer();
  await server.start(8080);
}
```

Match response IDs to request IDs for proper correlation. Process requests  
asynchronously to handle multiple concurrent requests. This server pattern  
enables efficient multiplexing of operations.  

## Heartbeat and Keep-Alive

Heartbeat messages detect dead connections and maintain idle connections  
through firewalls and NAT gateways.  

```dart
import 'dart:io';
import 'dart:async';

class HeartbeatClient {
  late Socket _socket;
  Timer? _heartbeatTimer;
  Timer? _timeoutTimer;
  final Duration heartbeatInterval = Duration(seconds: 5);
  final Duration timeout = Duration(seconds: 15);
  
  Future<void> connect(String host, int port) async {
    _socket = await Socket.connect(host, port);
    print('Connected to $host:$port');
    
    // Start heartbeat
    _startHeartbeat();
    
    // Listen for messages
    _socket.listen(
      (data) {
        String message = String.fromCharCodes(data).trim();
        print('Received: $message');
        
        if (message == 'PONG') {
          _resetTimeout();
        }
      },
      onDone: () {
        print('Connection closed');
        _stopHeartbeat();
      },
      onError: (error) {
        print('Error: $error');
        _stopHeartbeat();
      },
    );
  }
  
  void _startHeartbeat() {
    _heartbeatTimer = Timer.periodic(heartbeatInterval, (timer) {
      print('Sending PING');
      _socket.writeln('PING');
      _startTimeout();
    });
  }
  
  void _startTimeout() {
    _timeoutTimer?.cancel();
    _timeoutTimer = Timer(timeout, () {
      print('Heartbeat timeout - connection dead');
      _socket.close();
    });
  }
  
  void _resetTimeout() {
    _timeoutTimer?.cancel();
    print('Heartbeat acknowledged');
  }
  
  void _stopHeartbeat() {
    _heartbeatTimer?.cancel();
    _timeoutTimer?.cancel();
  }
  
  Future<void> close() async {
    _stopHeartbeat();
    await _socket.close();
  }
}

void main() async {
  HeartbeatClient client = HeartbeatClient();
  
  try {
    await client.connect('localhost', 8080);
    
    // Keep alive for testing
    await Future.delayed(Duration(seconds: 30));
    
  } finally {
    await client.close();
  }
}
```

Send periodic heartbeat messages to detect connection failures. Set  
timeouts for expected responses. Cancel timeout when heartbeat is  
acknowledged. This pattern is essential for long-lived connections.  

## Heartbeat Server

Server implementation responds to heartbeats and tracks client connection  
health.  

```dart
import 'dart:io';
import 'dart:convert';
import 'dart:async';

class HeartbeatServer {
  final Map<Socket, Timer> _clientTimeouts = {};
  final Duration clientTimeout = Duration(seconds: 20);
  
  Future<void> start(int port) async {
    ServerSocket server = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Heartbeat server listening on port $port');
    
    await for (Socket client in server) {
      print('Client connected from ${client.remoteAddress.address}');
      handleClient(client);
    }
  }
  
  void handleClient(Socket client) {
    // Set initial timeout
    _resetClientTimeout(client);
    
    client
        .transform(utf8.decoder)
        .transform(LineSplitter())
        .listen(
          (line) {
            print('Received from ${client.remoteAddress.address}: $line');
            
            if (line == 'PING') {
              // Respond to heartbeat
              client.writeln('PONG');
              print('Sent PONG');
              
              // Reset timeout
              _resetClientTimeout(client);
            } else {
              // Handle other messages
              client.writeln('Echo: $line');
            }
          },
          onDone: () {
            print('Client disconnected');
            _cancelClientTimeout(client);
            client.close();
          },
          onError: (error) {
            print('Error: $error');
            _cancelClientTimeout(client);
            client.close();
          },
        );
  }
  
  void _resetClientTimeout(Socket client) {
    _cancelClientTimeout(client);
    
    _clientTimeouts[client] = Timer(clientTimeout, () {
      print('Client timeout - closing connection');
      client.close();
    });
  }
  
  void _cancelClientTimeout(Socket client) {
    _clientTimeouts[client]?.cancel();
    _clientTimeouts.remove(client);
  }
}

void main() async {
  HeartbeatServer server = HeartbeatServer();
  await server.start(8080);
}
```

Track timeouts per client connection. Reset timeout on each heartbeat  
received. Close connections that don't send heartbeats within the timeout  
period. This maintains clean connection state.  

## Secure Socket (TLS/SSL) Client

Secure sockets encrypt network communication using TLS/SSL protocols for  
confidentiality and authentication.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  try {
    // Connect using secure socket
    SecureSocket socket = await SecureSocket.connect(
      'www.google.com',
      443,
      onBadCertificate: (certificate) {
        // Custom certificate validation
        print('Certificate: ${certificate.subject}');
        return true; // Accept for demo - validate properly in production
      },
    );
    
    print('Secure connection established');
    print('Protocol: ${socket.selectedProtocol}');
    
    // Send HTTPS request
    socket.write('GET / HTTP/1.1\r\n');
    socket.write('Host: www.google.com\r\n');
    socket.write('Connection: close\r\n');
    socket.write('\r\n');
    
    // Read response
    await for (var data in socket) {
      print(utf8.decode(data));
    }
    
    await socket.close();
    
  } catch (e) {
    print('Error: $e');
  }
}
```

SecureSocket provides TLS/SSL encryption. Always validate certificates  
in production. The selectedProtocol indicates negotiated TLS version.  
Use for HTTPS, secure MQTT, or any protocol requiring encryption.  

## Secure Socket (TLS/SSL) Server

Creating a secure server requires certificates and supports encrypted  
client connections.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  try {
    // Load server certificate and key
    SecurityContext context = SecurityContext();
    
    // In production, use real certificates
    // context.useCertificateChain('server_cert.pem');
    // context.usePrivateKey('server_key.pem');
    
    // For demo, create self-signed cert
    // In real use, obtain proper certificates
    print('Note: Use proper certificates in production');
    
    // Create secure server
    SecureServerSocket server = await SecureServerSocket.bind(
      InternetAddress.anyIPv4,
      8443,
      context,
      requestClientCertificate: false,
    );
    
    print('Secure server listening on port ${server.port}');
    
    await for (SecureSocket client in server) {
      print('Secure client connected');
      handleSecureClient(client);
    }
    
  } catch (e) {
    print('Server error: $e');
  }
}

void handleSecureClient(SecureSocket client) {
  client
      .transform(utf8.decoder)
      .transform(LineSplitter())
      .listen(
        (line) {
          print('Received: $line');
          client.writeln('Secure echo: $line');
        },
        onDone: () {
          print('Client disconnected');
          client.close();
        },
        onError: (error) {
          print('Error: $error');
          client.close();
        },
      );
}
```

SecureServerSocket requires SecurityContext with certificates and private  
keys. Use useCertificateChain() and usePrivateKey() to load credentials.  
Always use proper certificates from a trusted CA in production.  

## Socket Options and Tuning

Fine-tuning socket options optimizes performance for specific use cases  
and network conditions.  

```dart
import 'dart:io';

void main() async {
  try {
    Socket socket = await Socket.connect('example.com', 80);
    
    print('Configuring socket options:');
    
    // Disable Nagle's algorithm for low latency
    socket.setOption(SocketOption.tcpNoDelay, true);
    print('  TCP_NODELAY: enabled');
    
    // Enable keep-alive
    socket.setOption(SocketOption.tcpKeepAlive, true);
    print('  TCP_KEEPALIVE: enabled');
    
    // Set receive buffer size
    socket.setOption(SocketOption.tcpReceiveBufferSize, 65536);
    print('  Receive buffer: 64KB');
    
    // Set send buffer size
    socket.setOption(SocketOption.tcpSendBufferSize, 65536);
    print('  Send buffer: 64KB');
    
    print('\nSocket configuration complete');
    print('Remote: ${socket.remoteAddress.address}:${socket.remotePort}');
    print('Local: ${socket.address.address}:${socket.port}');
    
    // Use socket...
    socket.write('GET / HTTP/1.1\r\nHost: example.com\r\n\r\n');
    
    await Future.delayed(Duration(seconds: 1));
    await socket.close();
    
  } catch (e) {
    print('Error: $e');
  }
}
```

Socket options control TCP behavior and performance characteristics.  
TCP_NODELAY reduces latency, keep-alive detects dead connections, and  
buffer sizes affect throughput. Tune based on application requirements.  

## Non-Blocking I/O with RawSocket

Non-blocking I/O enables handling multiple connections without threads,  
crucial for high-performance servers.  

```dart
import 'dart:io';
import 'dart:typed_data';

class NonBlockingServer {
  final Map<RawSocket, Uint8List> _readBuffers = {};
  final Map<RawSocket, List<Uint8List>> _writeQueues = {};
  
  Future<void> start(int port) async {
    RawServerSocket server = await RawServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Non-blocking server on port $port');
    
    server.listen((RawSocket client) {
      print('Client connected');
      
      _readBuffers[client] = Uint8List(0);
      _writeQueues[client] = [];
      
      client.listen((event) {
        switch (event) {
          case RawSocketEvent.read:
            _handleRead(client);
            break;
            
          case RawSocketEvent.write:
            _handleWrite(client);
            break;
            
          case RawSocketEvent.readClosed:
          case RawSocketEvent.closed:
            _cleanup(client);
            break;
        }
      });
    });
  }
  
  void _handleRead(RawSocket client) {
    Uint8List? data = client.read();
    if (data != null) {
      print('Read ${data.length} bytes');
      
      // Echo back
      _writeQueues[client]!.add(data);
      client.writeEventsEnabled = true;
    }
  }
  
  void _handleWrite(RawSocket client) {
    List<Uint8List> queue = _writeQueues[client]!;
    
    while (queue.isNotEmpty) {
      Uint8List data = queue.first;
      int written = client.write(data);
      
      if (written < data.length) {
        // Partial write - queue remaining data
        queue[0] = Uint8List.fromList(
          data.sublist(written)
        );
        break;
      } else {
        // Complete write
        queue.removeAt(0);
      }
    }
    
    if (queue.isEmpty) {
      client.writeEventsEnabled = false;
    }
  }
  
  void _cleanup(RawSocket client) {
    print('Client disconnected');
    _readBuffers.remove(client);
    _writeQueues.remove(client);
    client.close();
  }
}

void main() async {
  NonBlockingServer server = NonBlockingServer();
  await server.start(8080);
}
```

RawSocket enables non-blocking I/O with explicit event handling. Manage  
read/write buffers manually. Enable write events only when data is queued.  
This pattern achieves high concurrency without threads.  

## Zero-Copy Buffer Management

Zero-copy techniques minimize memory allocation and copying for improved  
performance in high-throughput scenarios.  

```dart
import 'dart:io';
import 'dart:typed_data';

class ZeroCopyServer {
  // Reusable buffer pool
  final List<Uint8List> _bufferPool = [];
  final int bufferSize = 8192;
  
  Uint8List _acquireBuffer() {
    if (_bufferPool.isNotEmpty) {
      return _bufferPool.removeLast();
    }
    return Uint8List(bufferSize);
  }
  
  void _releaseBuffer(Uint8List buffer) {
    if (_bufferPool.length < 100) {
      _bufferPool.add(buffer);
    }
  }
  
  Future<void> start(int port) async {
    RawServerSocket server = await RawServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Zero-copy server on port $port');
    
    server.listen((RawSocket client) {
      print('Client connected');
      handleClient(client);
    });
  }
  
  void handleClient(RawSocket client) {
    client.listen((event) {
      if (event == RawSocketEvent.read) {
        // Read directly into pooled buffer
        Uint8List? data = client.read();
        
        if (data != null) {
          // Create view instead of copying
          ByteBuffer buffer = data.buffer;
          Uint8List view = buffer.asUint8List(
            data.offsetInBytes,
            data.length,
          );
          
          print('Processing ${view.length} bytes (zero-copy)');
          
          // Echo back using same buffer
          client.write(view);
        }
      } else if (event == RawSocketEvent.closed) {
        print('Client disconnected');
        client.close();
      }
    });
  }
}

void main() async {
  ZeroCopyServer server = ZeroCopyServer();
  await server.start(8080);
}
```

Buffer pooling reduces GC pressure by reusing allocations. Use buffer  
views instead of copying data. This minimizes memory operations and  
improves throughput for high-performance servers.  

## Protocol State Machine

Implementing protocols as state machines provides clear structure for  
complex multi-step communication patterns.  

```dart
import 'dart:io';
import 'dart:convert';

enum ProtocolState {
  waitingForAuth,
  authenticated,
  processingCommand,
  disconnecting,
}

class StatefulProtocolServer {
  final Map<Socket, ProtocolState> _clientStates = {};
  
  Future<void> start(int port) async {
    ServerSocket server = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Stateful protocol server on port $port');
    
    await for (Socket client in server) {
      print('Client connected');
      _clientStates[client] = ProtocolState.waitingForAuth;
      
      // Send greeting
      client.writeln('READY - Send AUTH <password>');
      
      handleClient(client);
    }
  }
  
  void handleClient(Socket client) {
    client
        .transform(utf8.decoder)
        .transform(LineSplitter())
        .listen(
          (line) {
            processCommand(client, line);
          },
          onDone: () {
            print('Client disconnected');
            _clientStates.remove(client);
            client.close();
          },
        );
  }
  
  void processCommand(Socket client, String command) {
    ProtocolState state = _clientStates[client]!;
    
    print('State: $state, Command: $command');
    
    switch (state) {
      case ProtocolState.waitingForAuth:
        if (command.startsWith('AUTH ')) {
          String password = command.substring(5);
          if (password == 'secret') {
            _clientStates[client] = ProtocolState.authenticated;
            client.writeln('OK - Authenticated');
          } else {
            client.writeln('ERROR - Invalid password');
            _clientStates[client] = ProtocolState.disconnecting;
            client.close();
          }
        } else {
          client.writeln('ERROR - Authentication required');
        }
        break;
        
      case ProtocolState.authenticated:
        if (command == 'QUIT') {
          _clientStates[client] = ProtocolState.disconnecting;
          client.writeln('BYE');
          client.close();
        } else if (command.startsWith('ECHO ')) {
          String message = command.substring(5);
          client.writeln('ECHO: $message');
        } else {
          client.writeln('UNKNOWN COMMAND');
        }
        break;
        
      case ProtocolState.processingCommand:
      case ProtocolState.disconnecting:
        // Ignore commands in these states
        break;
    }
  }
}

void main() async {
  StatefulProtocolServer server = StatefulProtocolServer();
  await server.start(8080);
}
```

State machines model protocol flows with clear transitions. Track state  
per connection. Validate commands based on current state. This pattern  
prevents protocol violations and improves security.  

## Chunked Transfer Encoding

Chunked transfer enables streaming variable-length content without  
knowing total size upfront.  

```dart
import 'dart:io';
import 'dart:convert';
import 'dart:async';

class ChunkedServer {
  Future<void> start(int port) async {
    ServerSocket server = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Chunked transfer server on port $port');
    
    await for (Socket client in server) {
      print('Client connected');
      handleClient(client);
    }
  }
  
  void handleClient(Socket client) async {
    // Send HTTP response with chunked encoding
    client.write('HTTP/1.1 200 OK\r\n');
    client.write('Transfer-Encoding: chunked\r\n');
    client.write('Content-Type: text/plain\r\n');
    client.write('\r\n');
    
    // Send data in chunks
    for (int i = 0; i < 10; i++) {
      await Future.delayed(Duration(milliseconds: 500));
      
      String data = 'Chunk $i: Hello there!\n';
      List<int> bytes = utf8.encode(data);
      
      // Send chunk size in hex
      client.write('${bytes.length.toRadixString(16)}\r\n');
      // Send chunk data
      client.add(bytes);
      client.write('\r\n');
      
      print('Sent chunk $i (${bytes.length} bytes)');
    }
    
    // Send final chunk (size 0)
    client.write('0\r\n\r\n');
    print('Sent final chunk');
    
    await client.close();
  }
}

void main() async {
  ChunkedServer server = ChunkedServer();
  await server.start(8080);
}
```

Chunked encoding sends data in pieces with size prefixes. Each chunk  
includes hex size, data, and CRLF. Final chunk has size 0. Enables  
streaming content of unknown length.  

## Parsing Chunked Transfer

Receiving chunked data requires parsing chunk sizes and reassembling  
content from the stream.  

```dart
import 'dart:io';
import 'dart:convert';
import 'dart:async';

class ChunkedClient {
  Future<void> connect(String host, int port) async {
    Socket socket = await Socket.connect(host, port);
    
    // Send HTTP request
    socket.write('GET / HTTP/1.1\r\n');
    socket.write('Host: $host\r\n');
    socket.write('\r\n');
    
    // Parse chunked response
    await parseChunkedResponse(socket);
    
    await socket.close();
  }
  
  Future<void> parseChunkedResponse(Socket socket) async {
    List<int> buffer = [];
    bool inHeaders = true;
    StringBuffer content = StringBuffer();
    
    await for (var data in socket) {
      buffer.addAll(data);
      
      if (inHeaders) {
        // Skip headers
        String text = utf8.decode(buffer);
        int headerEnd = text.indexOf('\r\n\r\n');
        
        if (headerEnd != -1) {
          inHeaders = false;
          buffer = buffer.sublist(headerEnd + 4);
          print('Headers complete, parsing chunks...\n');
        }
        continue;
      }
      
      // Parse chunks
      while (true) {
        String text = utf8.decode(buffer, allowMalformed: true);
        int crlfPos = text.indexOf('\r\n');
        
        if (crlfPos == -1) break;
        
        // Parse chunk size
        String sizeHex = text.substring(0, crlfPos);
        int chunkSize = int.parse(sizeHex, radix: 16);
        
        print('Chunk size: $chunkSize bytes');
        
        if (chunkSize == 0) {
          print('\nAll chunks received');
          print('Total content:\n$content');
          return;
        }
        
        // Check if full chunk is available
        int chunkStart = crlfPos + 2;
        int chunkEnd = chunkStart + chunkSize;
        
        if (buffer.length >= chunkEnd + 2) {
          // Extract chunk data
          List<int> chunkData = buffer.sublist(chunkStart, chunkEnd);
          String chunkText = utf8.decode(chunkData);
          content.write(chunkText);
          
          print('Received: $chunkText');
          
          // Remove processed chunk
          buffer = buffer.sublist(chunkEnd + 2);
        } else {
          break;
        }
      }
    }
  }
}

void main() async {
  ChunkedClient client = ChunkedClient();
  await client.connect('localhost', 8080);
}
```

Parse chunk size from hex, extract chunk data, accumulate content.  
Handle partial chunks by buffering. Chunk size 0 indicates end of stream.  
This pattern is essential for HTTP/1.1 and similar protocols.  

## UDP Hole Punching for NAT Traversal

NAT traversal enables peer-to-peer communication between devices behind  
NAT routers.  

```dart
import 'dart:io';
import 'dart:convert';

class UdpHolePuncher {
  late RawDatagramSocket _socket;
  
  Future<void> bind(int port) async {
    _socket = await RawDatagramSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('UDP socket bound to port ${_socket.port}');
  }
  
  void sendPunchPackets(InternetAddress target, int targetPort) {
    // Send multiple packets to punch through NAT
    for (int i = 0; i < 5; i++) {
      String message = 'PUNCH $i';
      List<int> data = utf8.encode(message);
      
      _socket.send(data, target, targetPort);
      print('Sent punch packet $i to $target:$targetPort');
    }
  }
  
  void listen() {
    _socket.listen((event) {
      if (event == RawSocketEvent.read) {
        Datagram? datagram = _socket.receive();
        
        if (datagram != null) {
          String message = utf8.decode(datagram.data);
          print('Received from ${datagram.address}:${datagram.port}: $message');
          
          // Respond to establish bidirectional hole
          if (message.startsWith('PUNCH')) {
            String response = 'PUNCH_ACK';
            _socket.send(
              utf8.encode(response),
              datagram.address,
              datagram.port,
            );
            print('Sent acknowledgment');
          }
        }
      }
    });
  }
  
  void close() {
    _socket.close();
  }
}

void main() async {
  UdpHolePuncher puncher = UdpHolePuncher();
  
  await puncher.bind(5000);
  puncher.listen();
  
  // Simulate NAT hole punching
  // In real scenario, exchange addresses via rendezvous server
  InternetAddress target = InternetAddress('peer.example.com');
  int targetPort = 5000;
  
  puncher.sendPunchPackets(target, targetPort);
  
  print('Hole punching initiated');
}
```

UDP hole punching creates NAT mappings by sending outbound packets.  
Send multiple packets to increase success rate. Both peers must punch  
simultaneously. Requires rendezvous server to exchange addresses.  

## Connection Multiplexing

Multiplexing enables multiple logical streams over a single physical  
connection, reducing overhead.  

```dart
import 'dart:io';
import 'dart:convert';
import 'dart:async';

class MultiplexedConnection {
  final Socket _socket;
  final Map<int, StreamController<String>> _streams = {};
  int _nextStreamId = 0;
  
  MultiplexedConnection(this._socket) {
    _listen();
  }
  
  void _listen() {
    _socket
        .transform(utf8.decoder)
        .transform(LineSplitter())
        .listen((line) {
          // Parse: "streamId:data"
          int colonPos = line.indexOf(':');
          if (colonPos != -1) {
            int streamId = int.parse(line.substring(0, colonPos));
            String data = line.substring(colonPos + 1);
            
            StreamController<String>? controller = _streams[streamId];
            if (controller != null) {
              controller.add(data);
            }
          }
        });
  }
  
  Stream<String> openStream() {
    int streamId = _nextStreamId++;
    StreamController<String> controller = StreamController<String>();
    
    _streams[streamId] = controller;
    
    print('Opened stream $streamId');
    
    return controller.stream;
  }
  
  void send(int streamId, String message) {
    _socket.writeln('$streamId:$message');
  }
  
  void closeStream(int streamId) {
    StreamController<String>? controller = _streams.remove(streamId);
    controller?.close();
    print('Closed stream $streamId');
  }
  
  Future<void> close() async {
    for (var controller in _streams.values) {
      await controller.close();
    }
    await _socket.close();
  }
}

void main() async {
  Socket socket = await Socket.connect('localhost', 8080);
  MultiplexedConnection mux = MultiplexedConnection(socket);
  
  // Open multiple streams
  Stream<String> stream1 = mux.openStream();
  Stream<String> stream2 = mux.openStream();
  
  // Listen to streams
  stream1.listen((data) => print('Stream 1: $data'));
  stream2.listen((data) => print('Stream 2: $data'));
  
  // Send on different streams
  mux.send(0, 'Message on stream 0');
  mux.send(1, 'Message on stream 1');
  mux.send(0, 'Another message on stream 0');
  
  await Future.delayed(Duration(seconds: 2));
  await mux.close();
}
```

Multiplexing uses stream IDs to separate logical channels. Single physical  
connection carries multiple streams. Reduces connection overhead and  
simplifies resource management.  

## Bandwidth Throttling

Rate limiting controls bandwidth usage to prevent network congestion and  
ensure fair resource allocation.  

```dart
import 'dart:io';
import 'dart:async';

class ThrottledSocket {
  final Socket _socket;
  final int bytesPerSecond;
  int _bytesThisSecond = 0;
  DateTime _windowStart = DateTime.now();
  
  ThrottledSocket(this._socket, {this.bytesPerSecond = 10240}) {
    print('Throttled to $bytesPerSecond bytes/second');
  }
  
  Future<void> write(List<int> data) async {
    int offset = 0;
    
    while (offset < data.length) {
      // Check if we need to wait
      DateTime now = DateTime.now();
      Duration elapsed = now.difference(_windowStart);
      
      if (elapsed.inSeconds >= 1) {
        // New window
        _windowStart = now;
        _bytesThisSecond = 0;
      }
      
      int available = bytesPerSecond - _bytesThisSecond;
      
      if (available <= 0) {
        // Wait for next window
        Duration waitTime = Duration(seconds: 1) - elapsed;
        print('Throttling: waiting ${waitTime.inMilliseconds}ms');
        await Future.delayed(waitTime);
        continue;
      }
      
      // Send chunk
      int chunkSize = (data.length - offset).clamp(0, available);
      List<int> chunk = data.sublist(offset, offset + chunkSize);
      
      _socket.add(chunk);
      _bytesThisSecond += chunkSize;
      offset += chunkSize;
      
      print('Sent $chunkSize bytes ($_bytesThisSecond/$bytesPerSecond this second)');
    }
    
    await _socket.flush();
  }
  
  Future<void> close() async {
    await _socket.close();
  }
}

void main() async {
  Socket socket = await Socket.connect('localhost', 8080);
  ThrottledSocket throttled = ThrottledSocket(
    socket,
    bytesPerSecond: 1024, // 1KB/s
  );
  
  // Send large data with throttling
  List<int> data = List.generate(10240, (i) => i & 0xFF);
  
  print('Sending 10KB with 1KB/s throttling...');
  Stopwatch sw = Stopwatch()..start();
  
  await throttled.write(data);
  
  sw.stop();
  print('Completed in ${sw.elapsed.inSeconds} seconds');
  
  await throttled.close();
}
```

Track bytes sent per time window. Delay when limit is reached. This  
prevents overwhelming slow receivers and enables QoS. Essential for  
fair resource sharing in multi-client scenarios.  

## Scatter-Gather I/O

Scatter-gather I/O (vectored I/O) efficiently handles multiple buffers  
in a single operation.  

```dart
import 'dart:io';
import 'dart:typed_data';

class ScatterGatherSocket {
  final Socket _socket;
  
  ScatterGatherSocket(this._socket);
  
  void writeGather(List<Uint8List> buffers) {
    // Gather multiple buffers into single write
    int totalSize = 0;
    for (var buffer in buffers) {
      totalSize += buffer.length;
    }
    
    print('Gathering ${buffers.length} buffers (total: $totalSize bytes)');
    
    // Write all buffers
    for (var buffer in buffers) {
      _socket.add(buffer);
    }
  }
  
  Future<List<Uint8List>> readScatter(int count, int bufferSize) async {
    // Scatter incoming data into multiple buffers
    List<Uint8List> buffers = [];
    int received = 0;
    
    await for (var data in _socket) {
      Uint8List buffer = Uint8List.fromList(data);
      buffers.add(buffer);
      received += buffer.length;
      
      print('Scattered chunk ${buffers.length}: ${buffer.length} bytes');
      
      if (buffers.length >= count || received >= bufferSize * count) {
        break;
      }
    }
    
    return buffers;
  }
  
  Future<void> close() async {
    await _socket.close();
  }
}

void main() async {
  Socket socket = await Socket.connect('localhost', 8080);
  ScatterGatherSocket sg = ScatterGatherSocket(socket);
  
  // Prepare multiple buffers
  Uint8List header = Uint8List.fromList([0x01, 0x02, 0x03, 0x04]);
  Uint8List payload1 = Uint8List.fromList('Hello there!'.codeUnits);
  Uint8List payload2 = Uint8List.fromList('Scatter-gather'.codeUnits);
  
  // Gather write
  sg.writeGather([header, payload1, payload2]);
  
  await Future.delayed(Duration(seconds: 1));
  await sg.close();
}
```

Gather combines multiple buffers into single send operation. Scatter  
distributes received data across multiple buffers. Reduces system calls  
and improves performance for structured I/O.  

## Connection Load Balancing

Load balancing distributes connections across multiple backend servers  
for improved performance and reliability.  

```dart
import 'dart:io';
import 'dart:async';

class LoadBalancer {
  final List<ServerInfo> _servers;
  int _currentIndex = 0;
  
  LoadBalancer(this._servers);
  
  Future<void> start(int port) async {
    ServerSocket frontend = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Load balancer listening on port $port');
    print('Backend servers: ${_servers.length}');
    
    await for (Socket client in frontend) {
      print('New connection from ${client.remoteAddress.address}');
      
      // Select backend server (round-robin)
      ServerInfo backend = _selectBackend();
      
      // Forward connection
      forwardConnection(client, backend);
    }
  }
  
  ServerInfo _selectBackend() {
    ServerInfo server = _servers[_currentIndex];
    _currentIndex = (_currentIndex + 1) % _servers.length;
    
    print('Selected backend: ${server.host}:${server.port}');
    return server;
  }
  
  void forwardConnection(Socket client, ServerInfo backend) async {
    try {
      // Connect to backend
      Socket backendSocket = await Socket.connect(
        backend.host,
        backend.port,
      );
      
      print('Connected to backend ${backend.host}:${backend.port}');
      
      // Bidirectional forwarding
      client.pipe(backendSocket);
      backendSocket.pipe(client);
      
    } catch (e) {
      print('Backend connection failed: $e');
      client.close();
    }
  }
}

class ServerInfo {
  final String host;
  final int port;
  
  ServerInfo(this.host, this.port);
}

void main() async {
  List<ServerInfo> backends = [
    ServerInfo('localhost', 8081),
    ServerInfo('localhost', 8082),
    ServerInfo('localhost', 8083),
  ];
  
  LoadBalancer balancer = LoadBalancer(backends);
  await balancer.start(8080);
}
```

Round-robin selection distributes load evenly. Socket.pipe() efficiently  
forwards data bidirectionally. Add health checks and weighted selection  
for production use. This pattern enables horizontal scaling.  

## TCP Proxy with Logging

Transparent proxies intercept and log traffic while forwarding between  
client and server.  

```dart
import 'dart:io';
import 'dart:convert';

class TcpProxy {
  Future<void> start(int listenPort, String targetHost, int targetPort) async {
    ServerSocket server = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      listenPort,
    );
    
    print('TCP Proxy: localhost:$listenPort -> $targetHost:$targetPort');
    
    await for (Socket client in server) {
      print('\n[${DateTime.now()}] New connection from ${client.remoteAddress.address}');
      handleConnection(client, targetHost, targetPort);
    }
  }
  
  void handleConnection(Socket client, String targetHost, int targetPort) async {
    try {
      // Connect to target
      Socket target = await Socket.connect(targetHost, targetPort);
      
      print('[${DateTime.now()}] Connected to $targetHost:$targetPort');
      
      // Client -> Target
      client.listen(
        (data) {
          String decoded = utf8.decode(data, allowMalformed: true);
          print('[${DateTime.now()}] Client -> Server: ${data.length} bytes');
          print('  ${decoded.substring(0, decoded.length.clamp(0, 100))}');
          target.add(data);
        },
        onDone: () {
          print('[${DateTime.now()}] Client disconnected');
          target.close();
        },
        onError: (error) {
          print('[${DateTime.now()}] Client error: $error');
          target.close();
        },
      );
      
      // Target -> Client
      target.listen(
        (data) {
          String decoded = utf8.decode(data, allowMalformed: true);
          print('[${DateTime.now()}] Server -> Client: ${data.length} bytes');
          print('  ${decoded.substring(0, decoded.length.clamp(0, 100))}');
          client.add(data);
        },
        onDone: () {
          print('[${DateTime.now()}] Server disconnected');
          client.close();
        },
        onError: (error) {
          print('[${DateTime.now()}] Server error: $error');
          client.close();
        },
      );
      
    } catch (e) {
      print('[${DateTime.now()}] Proxy error: $e');
      client.close();
    }
  }
}

void main() async {
  TcpProxy proxy = TcpProxy();
  await proxy.start(8080, 'example.com', 80);
}
```

Transparent proxies log all traffic. Forward data bidirectionally between  
client and target. Useful for debugging, monitoring, and protocol analysis.  
Can modify data for testing or security.  

## Socket Reconnection with Exponential Backoff

Automatic reconnection with exponential backoff handles transient network  
failures gracefully.  

```dart
import 'dart:io';
import 'dart:async';
import 'dart:math';

class ReconnectingSocket {
  final String host;
  final int port;
  Socket? _socket;
  
  bool _shouldReconnect = true;
  int _reconnectAttempts = 0;
  final int maxReconnectAttempts = 10;
  final int baseDelayMs = 1000;
  final int maxDelayMs = 30000;
  
  final StreamController<String> _messageController = 
      StreamController<String>.broadcast();
  
  Stream<String> get messages => _messageController.stream;
  
  ReconnectingSocket(this.host, this.port);
  
  Future<void> connect() async {
    while (_shouldReconnect && _reconnectAttempts < maxReconnectAttempts) {
      try {
        print('Connecting to $host:$port (attempt ${_reconnectAttempts + 1})');
        
        _socket = await Socket.connect(host, port)
            .timeout(Duration(seconds: 5));
        
        print('Connected successfully');
        _reconnectAttempts = 0;
        
        // Listen for data
        _socket!.listen(
          (data) {
            String message = String.fromCharCodes(data);
            _messageController.add(message);
          },
          onDone: () {
            print('Connection closed');
            if (_shouldReconnect) {
              _reconnect();
            }
          },
          onError: (error) {
            print('Socket error: $error');
            if (_shouldReconnect) {
              _reconnect();
            }
          },
        );
        
        break;
        
      } catch (e) {
        _reconnectAttempts++;
        
        if (_reconnectAttempts >= maxReconnectAttempts) {
          print('Max reconnection attempts reached');
          _messageController.addError('Connection failed permanently');
          break;
        }
        
        // Exponential backoff with jitter
        int delay = min(
          baseDelayMs * pow(2, _reconnectAttempts).toInt(),
          maxDelayMs,
        );
        
        // Add random jitter (±20%)
        Random random = Random();
        int jitter = (delay * 0.2 * (random.nextDouble() - 0.5)).toInt();
        delay += jitter;
        
        print('Connection failed: $e');
        print('Retrying in ${delay}ms...');
        
        await Future.delayed(Duration(milliseconds: delay));
      }
    }
  }
  
  void _reconnect() {
    print('Reconnecting...');
    connect();
  }
  
  void send(String message) {
    _socket?.write(message);
  }
  
  Future<void> close() async {
    _shouldReconnect = false;
    await _socket?.close();
    await _messageController.close();
  }
}

void main() async {
  ReconnectingSocket socket = ReconnectingSocket('localhost', 8080);
  
  socket.messages.listen(
    (message) {
      print('Received: $message');
    },
    onError: (error) {
      print('Stream error: $error');
    },
  );
  
  await socket.connect();
  
  // Send test messages
  for (int i = 0; i < 5; i++) {
    await Future.delayed(Duration(seconds: 2));
    socket.send('Message $i\n');
  }
  
  await socket.close();
}
```

Exponential backoff prevents overwhelming failed servers. Add jitter to  
avoid thundering herd. Limit retry attempts to prevent infinite loops.  
This pattern is essential for resilient network clients.  

## Ping-Pong Protocol

Simple ping-pong protocol demonstrates bidirectional communication and  
latency measurement.  

```dart
import 'dart:io';
import 'dart:convert';

class PingPongClient {
  late Socket _socket;
  final Stopwatch _stopwatch = Stopwatch();
  
  Future<void> connect(String host, int port) async {
    _socket = await Socket.connect(host, port);
    print('Connected to $host:$port');
    
    _socket
        .transform(utf8.decoder)
        .transform(LineSplitter())
        .listen((line) {
          if (line == 'PONG') {
            _stopwatch.stop();
            print('RTT: ${_stopwatch.elapsedMilliseconds}ms');
          }
        });
  }
  
  Future<void> ping() async {
    _stopwatch.reset();
    _stopwatch.start();
    _socket.writeln('PING');
    print('Sent PING');
  }
  
  Future<void> close() async {
    await _socket.close();
  }
}

class PingPongServer {
  Future<void> start(int port) async {
    ServerSocket server = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Ping-pong server listening on port $port');
    
    await for (Socket client in server) {
      print('Client connected');
      handleClient(client);
    }
  }
  
  void handleClient(Socket client) {
    client
        .transform(utf8.decoder)
        .transform(LineSplitter())
        .listen(
          (line) {
            if (line == 'PING') {
              print('Received PING, sending PONG');
              client.writeln('PONG');
            }
          },
          onDone: () {
            print('Client disconnected');
            client.close();
          },
        );
  }
}

void main() async {
  // Start server
  PingPongServer server = PingPongServer();
  server.start(8080);
  
  // Wait for server to start
  await Future.delayed(Duration(seconds: 1));
  
  // Connect client
  PingPongClient client = PingPongClient();
  await client.connect('localhost', 8080);
  
  // Send pings
  for (int i = 0; i < 5; i++) {
    await Future.delayed(Duration(seconds: 1));
    await client.ping();
  }
  
  await client.close();
}
```

Ping-pong measures round-trip time and verifies connectivity. Use  
Stopwatch for precise timing. This pattern validates connection health  
and network latency.  

## Socket Connection Timeout Detection

Detecting and handling connection timeouts prevents resource leaks from  
abandoned connections.  

```dart
import 'dart:io';
import 'dart:async';

class TimeoutDetector {
  final Duration idleTimeout;
  final Map<Socket, Timer> _clientTimers = {};
  
  TimeoutDetector({this.idleTimeout = const Duration(seconds: 30)});
  
  Future<void> start(int port) async {
    ServerSocket server = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Server with timeout detection on port $port');
    print('Idle timeout: ${idleTimeout.inSeconds} seconds');
    
    await for (Socket client in server) {
      print('Client connected from ${client.remoteAddress.address}');
      handleClient(client);
    }
  }
  
  void handleClient(Socket client) {
    // Set initial timeout
    _resetTimeout(client);
    
    client.listen(
      (data) {
        print('Received ${data.length} bytes');
        
        // Reset timeout on activity
        _resetTimeout(client);
        
        // Echo back
        client.add(data);
      },
      onDone: () {
        print('Client disconnected normally');
        _cancelTimeout(client);
        client.close();
      },
      onError: (error) {
        print('Client error: $error');
        _cancelTimeout(client);
        client.close();
      },
    );
  }
  
  void _resetTimeout(Socket client) {
    _cancelTimeout(client);
    
    _clientTimers[client] = Timer(idleTimeout, () {
      print('Client timeout - closing connection');
      print('Remote: ${client.remoteAddress.address}:${client.remotePort}');
      client.close();
    });
  }
  
  void _cancelTimeout(Socket client) {
    _clientTimers[client]?.cancel();
    _clientTimers.remove(client);
  }
}

void main() async {
  TimeoutDetector detector = TimeoutDetector(
    idleTimeout: Duration(seconds: 10),
  );
  
  await detector.start(8080);
}
```

Track activity per connection with timers. Reset timeout on each message.  
Close idle connections automatically. This prevents resource exhaustion  
from inactive clients.  

## Raw Packet Inspection

Inspecting raw packet data enables protocol debugging and network  
analysis.  

```dart
import 'dart:io';
import 'dart:typed_data';

class PacketInspector {
  Future<void> start(int port) async {
    RawServerSocket server = await RawServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Packet inspector listening on port $port');
    
    server.listen((RawSocket client) {
      print('\n=== New connection ===');
      print('Remote: ${client.remoteAddress.address}:${client.remotePort}');
      print('Local: ${client.address.address}:${client.port}');
      
      client.listen((event) {
        if (event == RawSocketEvent.read) {
          Uint8List? data = client.read();
          
          if (data != null) {
            inspectPacket(data);
          }
        }
      });
    });
  }
  
  void inspectPacket(Uint8List data) {
    print('\n--- Packet Received ---');
    print('Length: ${data.length} bytes');
    
    // Hex dump
    print('Hex dump:');
    for (int i = 0; i < data.length; i += 16) {
      String offset = i.toRadixString(16).padLeft(4, '0');
      StringBuffer hex = StringBuffer();
      StringBuffer ascii = StringBuffer();
      
      for (int j = 0; j < 16 && i + j < data.length; j++) {
        int byte = data[i + j];
        hex.write('${byte.toRadixString(16).padLeft(2, '0')} ');
        
        // ASCII representation
        if (byte >= 32 && byte <= 126) {
          ascii.write(String.fromCharCode(byte));
        } else {
          ascii.write('.');
        }
      }
      
      print('  $offset  ${hex.toString().padRight(48)}  $ascii');
    }
    
    // Try to decode as text
    try {
      String text = String.fromCharCodes(data);
      if (text.contains(RegExp(r'^[\x20-\x7E\s]+$'))) {
        print('\nText interpretation:');
        print('  $text');
      }
    } catch (e) {
      print('\nNot valid text data');
    }
  }
}

void main() async {
  PacketInspector inspector = PacketInspector();
  await inspector.start(8080);
}
```

Hex dump shows raw byte values. ASCII column displays printable characters.  
This tool is invaluable for debugging binary protocols and analyzing  
network traffic patterns.  

## Custom Binary Protocol with Checksums

Implementing checksums ensures data integrity for custom protocols over  
unreliable networks.  

```dart
import 'dart:io';
import 'dart:typed_data';

class ChecksumProtocol {
  static const int MAGIC = 0xDEADBEEF;
  
  static Uint8List createMessage(String payload) {
    List<int> payloadBytes = payload.codeUnits;
    int totalLength = 12 + payloadBytes.length; // Header(12) + payload
    
    ByteData message = ByteData(totalLength);
    
    // Magic number (4 bytes)
    message.setUint32(0, MAGIC, Endian.big);
    
    // Payload length (4 bytes)
    message.setUint32(4, payloadBytes.length, Endian.big);
    
    // Payload
    for (int i = 0; i < payloadBytes.length; i++) {
      message.setUint8(12 + i, payloadBytes[i]);
    }
    
    // Calculate checksum (simple sum)
    int checksum = 0;
    for (int i = 0; i < 8; i++) {
      checksum += message.getUint8(i);
    }
    for (int i = 0; i < payloadBytes.length; i++) {
      checksum += payloadBytes[i];
    }
    checksum = checksum & 0xFFFFFFFF;
    
    // Checksum (4 bytes)
    message.setUint32(8, checksum, Endian.big);
    
    return message.buffer.asUint8List();
  }
  
  static Map<String, dynamic>? parseMessage(Uint8List data) {
    if (data.length < 12) {
      print('Message too short');
      return null;
    }
    
    ByteData view = ByteData.view(data.buffer);
    
    // Verify magic number
    int magic = view.getUint32(0, Endian.big);
    if (magic != MAGIC) {
      print('Invalid magic number: 0x${magic.toRadixString(16)}');
      return null;
    }
    
    // Read payload length
    int payloadLength = view.getUint32(4, Endian.big);
    
    if (data.length < 12 + payloadLength) {
      print('Incomplete message');
      return null;
    }
    
    // Read checksum
    int storedChecksum = view.getUint32(8, Endian.big);
    
    // Calculate checksum
    int calculatedChecksum = 0;
    for (int i = 0; i < 8; i++) {
      calculatedChecksum += view.getUint8(i);
    }
    for (int i = 0; i < payloadLength; i++) {
      calculatedChecksum += view.getUint8(12 + i);
    }
    calculatedChecksum = calculatedChecksum & 0xFFFFFFFF;
    
    // Verify checksum
    if (storedChecksum != calculatedChecksum) {
      print('Checksum mismatch: stored=$storedChecksum, calculated=$calculatedChecksum');
      return null;
    }
    
    // Extract payload
    List<int> payloadBytes = data.sublist(12, 12 + payloadLength);
    String payload = String.fromCharCodes(payloadBytes);
    
    return {
      'payload': payload,
      'checksum': storedChecksum,
    };
  }
}

void main() async {
  // Create message
  Uint8List message = ChecksumProtocol.createMessage('Hello there!');
  
  print('Created message: ${message.length} bytes');
  print('Hex: ${message.map((b) => b.toRadixString(16).padLeft(2, '0')).join(' ')}');
  
  // Parse message
  Map<String, dynamic>? parsed = ChecksumProtocol.parseMessage(message);
  
  if (parsed != null) {
    print('\nParsed successfully:');
    print('  Payload: ${parsed['payload']}');
    print('  Checksum: 0x${parsed['checksum'].toRadixString(16)}');
  }
  
  // Test corrupted message
  print('\nTesting corrupted message:');
  Uint8List corrupted = Uint8List.fromList(message);
  corrupted[12] = 0xFF; // Corrupt first payload byte
  
  ChecksumProtocol.parseMessage(corrupted);
}
```

Checksums detect transmission errors. Magic numbers identify protocol  
frames. Validate all fields before processing. This pattern ensures  
reliability for custom protocols.  

## Socket Pool with Health Checks

Advanced connection pooling includes health checks to remove failed  
connections automatically.  

```dart
import 'dart:io';
import 'dart:async';

class HealthCheckedSocketPool {
  final String host;
  final int port;
  final int poolSize;
  final Duration checkInterval;
  
  final List<Socket> _pool = [];
  Timer? _healthCheckTimer;
  
  HealthCheckedSocketPool({
    required this.host,
    required this.port,
    this.poolSize = 5,
    this.checkInterval = const Duration(seconds: 30),
  });
  
  Future<void> initialize() async {
    print('Initializing pool with $poolSize connections');
    
    for (int i = 0; i < poolSize; i++) {
      try {
        Socket socket = await Socket.connect(host, port);
        _pool.add(socket);
        print('Added connection ${i + 1}/$poolSize');
      } catch (e) {
        print('Failed to create connection: $e');
      }
    }
    
    // Start health checks
    _startHealthChecks();
  }
  
  void _startHealthChecks() {
    _healthCheckTimer = Timer.periodic(checkInterval, (timer) async {
      print('\nPerforming health check...');
      await _healthCheck();
    });
  }
  
  Future<void> _healthCheck() async {
    List<Socket> deadSockets = [];
    
    for (Socket socket in _pool) {
      try {
        // Try to write zero bytes to check if socket is alive
        socket.add([]);
        await socket.flush();
      } catch (e) {
        print('Dead socket detected: $e');
        deadSockets.add(socket);
      }
    }
    
    // Remove dead sockets
    for (Socket dead in deadSockets) {
      _pool.remove(dead);
      try {
        await dead.close();
      } catch (e) {
        // Already closed
      }
      
      // Replace with new connection
      try {
        Socket newSocket = await Socket.connect(host, port);
        _pool.add(newSocket);
        print('Replaced dead socket');
      } catch (e) {
        print('Failed to replace socket: $e');
      }
    }
    
    print('Health check complete: ${_pool.length}/$poolSize healthy');
  }
  
  Socket? acquire() {
    if (_pool.isEmpty) {
      print('Pool exhausted');
      return null;
    }
    
    return _pool.removeAt(0);
  }
  
  void release(Socket socket) {
    if (_pool.length < poolSize) {
      _pool.add(socket);
    } else {
      socket.close();
    }
  }
  
  Future<void> shutdown() async {
    _healthCheckTimer?.cancel();
    
    for (Socket socket in _pool) {
      await socket.close();
    }
    
    _pool.clear();
    print('Pool shutdown complete');
  }
}

void main() async {
  HealthCheckedSocketPool pool = HealthCheckedSocketPool(
    host: 'localhost',
    port: 8080,
    poolSize: 3,
    checkInterval: Duration(seconds: 10),
  );
  
  await pool.initialize();
  
  // Use pool
  Socket? socket = pool.acquire();
  if (socket != null) {
    socket.writeln('Test message');
    await Future.delayed(Duration(milliseconds: 100));
    pool.release(socket);
  }
  
  // Keep running to see health checks
  await Future.delayed(Duration(seconds: 35));
  
  await pool.shutdown();
}
```

Health checks detect and replace failed connections. Periodic validation  
maintains pool integrity. This ensures connection quality and prevents  
using dead sockets.  

## High-Performance Echo Server

Optimized echo server demonstrates best practices for throughput and  
efficiency.  

```dart
import 'dart:io';
import 'dart:typed_data';

class HighPerformanceEchoServer {
  int _bytesProcessed = 0;
  int _connectionsHandled = 0;
  final Stopwatch _uptime = Stopwatch();
  
  Future<void> start(int port) async {
    // Configure server socket
    RawServerSocket server = await RawServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
      backlog: 128, // Increase connection backlog
    );
    
    print('High-performance echo server on port $port');
    _uptime.start();
    
    // Start stats reporting
    _startStatsReporting();
    
    server.listen((RawSocket client) {
      _connectionsHandled++;
      
      // Configure client socket for performance
      client.setOption(SocketOption.tcpNoDelay, true);
      
      handleClient(client);
    });
  }
  
  void handleClient(RawSocket client) {
    client.listen((event) {
      if (event == RawSocketEvent.read) {
        // Read data
        Uint8List? data = client.read();
        
        if (data != null) {
          _bytesProcessed += data.length;
          
          // Echo back immediately (zero-copy)
          client.write(data);
        }
      } else if (event == RawSocketEvent.closed) {
        client.close();
      }
    });
  }
  
  void _startStatsReporting() {
    Timer.periodic(Duration(seconds: 5), (timer) {
      double seconds = _uptime.elapsed.inMilliseconds / 1000.0;
      double throughput = _bytesProcessed / seconds;
      
      print('\n=== Server Statistics ===');
      print('Uptime: ${_uptime.elapsed.inSeconds}s');
      print('Connections: $_connectionsHandled');
      print('Bytes processed: $_bytesProcessed');
      print('Throughput: ${(throughput / 1024).toStringAsFixed(2)} KB/s');
    });
  }
}

void main() async {
  HighPerformanceEchoServer server = HighPerformanceEchoServer();
  await server.start(8080);
}
```

Use RawSocket for minimal overhead. Enable TCP_NODELAY for low latency.  
Track metrics for performance monitoring. This server handles high  
connection rates efficiently.  

## Graceful Shutdown

Graceful shutdown ensures all connections close cleanly and resources  
are released properly.  

```dart
import 'dart:io';
import 'dart:async';
import 'dart:convert';

class GracefulServer {
  ServerSocket? _server;
  final List<Socket> _clients = [];
  bool _shuttingDown = false;
  
  Future<void> start(int port) async {
    _server = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Server listening on port $port');
    print('Press Ctrl+C to shutdown gracefully');
    
    // Listen for shutdown signal
    ProcessSignal.sigint.watch().listen((_) {
      print('\nReceived shutdown signal');
      shutdown();
    });
    
    await for (Socket client in _server!) {
      if (_shuttingDown) {
        client.writeln('Server shutting down');
        client.close();
        continue;
      }
      
      print('Client connected');
      _clients.add(client);
      handleClient(client);
    }
  }
  
  void handleClient(Socket client) {
    client
        .transform(utf8.decoder)
        .transform(LineSplitter())
        .listen(
          (line) {
            if (!_shuttingDown) {
              client.writeln('Echo: $line');
            }
          },
          onDone: () {
            print('Client disconnected');
            _clients.remove(client);
          },
        );
  }
  
  Future<void> shutdown() async {
    if (_shuttingDown) return;
    
    _shuttingDown = true;
    print('Starting graceful shutdown...');
    
    // Stop accepting new connections
    await _server?.close();
    print('Stopped accepting connections');
    
    // Notify all clients
    for (Socket client in _clients) {
      client.writeln('Server shutting down - goodbye!');
    }
    
    // Wait for clients to disconnect
    print('Waiting for ${_clients.length} clients to disconnect...');
    
    int timeout = 10;
    while (_clients.isNotEmpty && timeout > 0) {
      await Future.delayed(Duration(seconds: 1));
      timeout--;
      print('  ${_clients.length} clients remaining...');
    }
    
    // Force close remaining clients
    for (Socket client in _clients) {
      await client.close();
    }
    
    _clients.clear();
    print('Shutdown complete');
    
    exit(0);
  }
}

void main() async {
  GracefulServer server = GracefulServer();
  await server.start(8080);
}
```

Stop accepting new connections first. Notify existing clients of shutdown.  
Wait for graceful disconnect with timeout. Force close remaining connections.  
This prevents data loss and connection errors.  

## IPv6 Socket Support

IPv6 provides expanded address space and is essential for modern network  
applications supporting both IPv4 and IPv6.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  try {
    // Connect using IPv6
    Socket socket = await Socket.connect(
      InternetAddress('2001:4860:4860::8888'), // Google DNS IPv6
      53,
      timeout: Duration(seconds: 5),
    );
    
    print('Connected via IPv6');
    print('Remote: ${socket.remoteAddress.address}');
    print('Address type: ${socket.remoteAddress.type}');
    
    await socket.close();
    
  } catch (e) {
    print('IPv6 connection failed: $e');
  }
  
  // Create server supporting both IPv4 and IPv6
  try {
    ServerSocket server = await ServerSocket.bind(
      InternetAddress.anyIPv6,
      8080,
      v6Only: false, // Accept both IPv4 and IPv6
    );
    
    print('\nServer listening on [::]:${server.port}');
    print('Accepts both IPv4 and IPv6 connections');
    
    await for (Socket client in server) {
      print('Client: ${client.remoteAddress.address}');
      print('Type: ${client.remoteAddress.type}');
      client.writeln('Hello there!');
      client.close();
    }
    
  } catch (e) {
    print('Server error: $e');
  }
}
```

Use InternetAddress.anyIPv6 with v6Only: false for dual-stack servers.  
Check InternetAddress.type to determine IPv4 vs IPv6. Always test both  
address families for compatibility.  

## Unix Domain Sockets

Unix domain sockets provide IPC on the same machine with better performance  
than TCP loopback.  

```dart
import 'dart:io';
import 'dart:convert';

void main() async {
  String socketPath = '/tmp/dart_unix_socket.sock';
  
  // Remove existing socket file
  try {
    await File(socketPath).delete();
  } catch (e) {
    // File doesn't exist
  }
  
  // Create Unix domain socket server
  ServerSocket server = await ServerSocket.bind(
    InternetAddress(socketPath, type: InternetAddressType.unix),
    0,
  );
  
  print('Unix socket server: $socketPath');
  
  // Handle connections in background
  server.listen((Socket client) async {
    print('Client connected');
    
    await for (var data in client) {
      String message = utf8.decode(data).trim();
      print('Received: $message');
      client.writeln('Echo: $message');
    }
  });
  
  // Give server time to start
  await Future.delayed(Duration(milliseconds: 100));
  
  // Connect client
  Socket client = await Socket.connect(
    InternetAddress(socketPath, type: InternetAddressType.unix),
    0,
  );
  
  print('Client connected to Unix socket');
  
  client.writeln('Hello there!');
  
  await for (var data in client) {
    print('Response: ${utf8.decode(data)}');
    break;
  }
  
  await client.close();
  await server.close();
  
  // Cleanup
  await File(socketPath).delete();
}
```

Unix domain sockets are faster than TCP for local IPC. Use  
InternetAddressType.unix for Unix sockets. Remember to delete socket  
files when done.  

## Socket Address Resolution

DNS resolution converts hostnames to IP addresses for network connections.  

```dart
import 'dart:io';

void main() async {
  String hostname = 'www.google.com';
  
  print('Resolving $hostname...\n');
  
  try {
    // Lookup all addresses
    List<InternetAddress> addresses = 
        await InternetAddress.lookup(hostname);
    
    print('Found ${addresses.length} addresses:');
    
    for (InternetAddress addr in addresses) {
      print('  ${addr.address} (${addr.type})');
    }
    
    // Reverse lookup
    print('\nReverse lookup:');
    for (InternetAddress addr in addresses) {
      try {
        String? reversed = await addr.reverse();
        print('  ${addr.address} -> $reversed');
      } catch (e) {
        print('  ${addr.address} -> No PTR record');
      }
    }
    
  } catch (e) {
    print('Lookup failed: $e');
  }
  
  // Check if address is loopback, multicast, etc.
  print('\nAddress properties:');
  
  InternetAddress loopback = InternetAddress.loopbackIPv4;
  print('Loopback: ${loopback.address}');
  print('  isLoopback: ${loopback.isLoopback}');
  print('  isMulticast: ${loopback.isMulticast}');
  
  InternetAddress multicast = InternetAddress('224.0.0.1');
  print('\nMulticast: ${multicast.address}');
  print('  isMulticast: ${multicast.isMulticast}');
}
```

InternetAddress.lookup() performs DNS resolution. Returns all addresses  
(IPv4 and IPv6). Use reverse() for PTR lookups. Check address properties  
with isLoopback, isMulticast, etc.  

## Socket Buffer Size Configuration

Configuring buffer sizes optimizes throughput for different network  
conditions and application requirements.  

```dart
import 'dart:io';

void main() async {
  try {
    Socket socket = await Socket.connect('example.com', 80);
    
    print('Default buffer configuration:');
    print('  Remote: ${socket.remoteAddress.address}:${socket.remotePort}');
    
    // Configure send buffer
    int sendBufferSize = 128 * 1024; // 128KB
    socket.setOption(SocketOption.tcpSendBufferSize, sendBufferSize);
    print('\nSet send buffer: ${sendBufferSize / 1024}KB');
    
    // Configure receive buffer
    int receiveBufferSize = 128 * 1024; // 128KB
    socket.setOption(SocketOption.tcpReceiveBufferSize, receiveBufferSize);
    print('Set receive buffer: ${receiveBufferSize / 1024}KB');
    
    // For high-bandwidth connections, use larger buffers
    // For low-latency, use smaller buffers
    
    print('\nBuffer configuration complete');
    
    await socket.close();
    
  } catch (e) {
    print('Error: $e');
  }
}
```

Larger buffers improve throughput for high-bandwidth connections. Smaller  
buffers reduce latency for interactive applications. Tune based on network  
conditions and application needs.  

## Socket Linger Options

Linger controls socket closure behavior, determining how buffered data  
is handled when closing.  

```dart
import 'dart:io';

void main() async {
  try {
    Socket socket = await Socket.connect('localhost', 8080);
    
    print('Testing linger options:\n');
    
    // Option 1: Immediate close (discard buffered data)
    print('1. SO_LINGER disabled (default):');
    print('   Socket closes immediately, RST sent if data pending');
    
    // Option 2: Graceful close with timeout
    // Note: Dart doesn't directly expose SO_LINGER, but demonstrates concept
    
    socket.write('Some data to send\n');
    await socket.flush();
    
    print('\n2. Graceful close:');
    print('   Flush buffered data before closing');
    
    await socket.close();
    print('   Socket closed gracefully');
    
  } catch (e) {
    print('Error: $e');
  }
  
  // Demonstrate abortive close
  try {
    Socket socket2 = await Socket.connect('localhost', 8080);
    
    socket2.write('Data\n');
    
    print('\n3. Abortive close (destroy):');
    socket2.destroy();
    print('   Socket destroyed immediately (RST sent)');
    
  } catch (e) {
    print('Error: $e');
  }
}
```

Use close() for graceful shutdown with buffered data. Use destroy() for  
immediate closure discarding data. Graceful close prevents data loss but  
may block briefly.  

## SO_REUSEADDR and SO_REUSEPORT

Address reuse options enable binding to recently used ports and load  
balancing across processes.  

```dart
import 'dart:io';

void main() async {
  int port = 8080;
  
  try {
    // Create server with shared address
    RawServerSocket server = await RawServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
      shared: true, // Enables SO_REUSEPORT
    );
    
    print('Server bound to port $port (shared: true)');
    print('Multiple processes can bind to same port');
    
    server.listen((RawSocket client) {
      print('Connection from ${client.remoteAddress.address}');
      client.close();
    });
    
    // Demonstrate binding to same port (in different process)
    print('\nWith shared:true, another process can bind to same port');
    print('Connections are distributed across processes');
    
  } catch (e) {
    print('Error: $e');
  }
  
  // Without shared flag
  try {
    ServerSocket server2 = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      port + 1,
      shared: false,
    );
    
    print('\nServer on port ${port + 1} (shared: false)');
    print('Only this process can bind to this port');
    
  } catch (e) {
    print('Error: $e');
  }
}
```

shared: true enables SO_REUSEPORT for load balancing. Multiple processes  
can bind to the same port. Kernel distributes connections. Useful for  
zero-downtime deployments.  

## Non-Blocking Connect

Non-blocking connection attempts enable timeout handling and parallel  
connection attempts.  

```dart
import 'dart:io';
import 'dart:async';

class NonBlockingConnector {
  Future<Socket?> connectWithTimeout(
    String host,
    int port,
    Duration timeout,
  ) async {
    try {
      Socket socket = await Socket.connect(host, port)
          .timeout(timeout);
      
      print('Connected to $host:$port');
      return socket;
      
    } on TimeoutException {
      print('Connection timeout to $host:$port');
      return null;
    } catch (e) {
      print('Connection failed to $host:$port: $e');
      return null;
    }
  }
  
  Future<Socket?> connectMultiple(
    List<String> hosts,
    int port,
  ) async {
    print('Attempting parallel connections to ${hosts.length} hosts');
    
    // Try all hosts in parallel
    List<Future<Socket?>> futures = hosts.map(
      (host) => connectWithTimeout(host, port, Duration(seconds: 3))
    ).toList();
    
    // Return first successful connection
    for (Future<Socket?> future in futures) {
      Socket? socket = await future;
      if (socket != null) {
        print('First connection succeeded');
        return socket;
      }
    }
    
    print('All connections failed');
    return null;
  }
}

void main() async {
  NonBlockingConnector connector = NonBlockingConnector();
  
  // Single connection with timeout
  Socket? socket1 = await connector.connectWithTimeout(
    'example.com',
    80,
    Duration(seconds: 5),
  );
  
  await socket1?.close();
  
  // Parallel connection attempts
  Socket? socket2 = await connector.connectMultiple(
    ['primary.example.com', 'backup.example.com', 'fallback.example.com'],
    80,
  );
  
  await socket2?.close();
}
```

Non-blocking connects enable timeout handling. Parallel attempts to multiple  
hosts reduce connection latency. Use the first successful connection and  
cancel others.  

## Socket Statistics and Monitoring

Collecting socket statistics enables performance monitoring and debugging  
network issues.  

```dart
import 'dart:io';
import 'dart:async';

class SocketStatistics {
  int bytesReceived = 0;
  int bytesSent = 0;
  int messagesReceived = 0;
  int messagesSent = 0;
  DateTime? connectedAt;
  DateTime? lastActivityAt;
  
  Duration get uptime {
    if (connectedAt == null) return Duration.zero;
    return DateTime.now().difference(connectedAt!);
  }
  
  Duration get idleTime {
    if (lastActivityAt == null) return Duration.zero;
    return DateTime.now().difference(lastActivityAt!);
  }
  
  void recordReceived(int bytes) {
    bytesReceived += bytes;
    messagesReceived++;
    lastActivityAt = DateTime.now();
  }
  
  void recordSent(int bytes) {
    bytesSent += bytes;
    messagesSent++;
    lastActivityAt = DateTime.now();
  }
  
  void printStats() {
    print('\n=== Socket Statistics ===');
    print('Uptime: ${uptime.inSeconds}s');
    print('Idle time: ${idleTime.inSeconds}s');
    print('Bytes received: $bytesReceived');
    print('Bytes sent: $bytesSent');
    print('Messages received: $messagesReceived');
    print('Messages sent: $messagesSent');
    
    if (uptime.inSeconds > 0) {
      double rxRate = bytesReceived / uptime.inSeconds;
      double txRate = bytesSent / uptime.inSeconds;
      print('RX rate: ${(rxRate / 1024).toStringAsFixed(2)} KB/s');
      print('TX rate: ${(txRate / 1024).toStringAsFixed(2)} KB/s');
    }
  }
}

class MonitoredServer {
  final Map<Socket, SocketStatistics> _stats = {};
  
  Future<void> start(int port) async {
    ServerSocket server = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Monitored server on port $port');
    
    // Print stats periodically
    Timer.periodic(Duration(seconds: 10), (_) {
      print('\n=== Server Statistics ===');
      print('Active connections: ${_stats.length}');
      
      int totalRx = 0;
      int totalTx = 0;
      
      for (var stats in _stats.values) {
        totalRx += stats.bytesReceived;
        totalTx += stats.bytesSent;
      }
      
      print('Total RX: ${(totalRx / 1024).toStringAsFixed(2)} KB');
      print('Total TX: ${(totalTx / 1024).toStringAsFixed(2)} KB');
    });
    
    await for (Socket client in server) {
      handleClient(client);
    }
  }
  
  void handleClient(Socket client) {
    SocketStatistics stats = SocketStatistics();
    stats.connectedAt = DateTime.now();
    _stats[client] = stats;
    
    print('Client connected: ${client.remoteAddress.address}');
    
    client.listen(
      (data) {
        stats.recordReceived(data.length);
        
        // Echo back
        client.add(data);
        stats.recordSent(data.length);
      },
      onDone: () {
        print('\nClient disconnected:');
        stats.printStats();
        _stats.remove(client);
        client.close();
      },
    );
  }
}

void main() async {
  MonitoredServer server = MonitoredServer();
  await server.start(8080);
}
```

Track bytes, messages, and timing per connection. Calculate throughput  
rates. Monitor idle time for timeout detection. Statistics are essential  
for debugging and optimization.  

## WebSocket Protocol Implementation

Implementing WebSocket protocol demonstrates framing and upgrading from  
HTTP connections.  

```dart
import 'dart:io';
import 'dart:convert';
import 'dart:typed_data';
import 'package:crypto/crypto.dart';

class SimpleWebSocketServer {
  Future<void> start(int port) async {
    HttpServer server = await HttpServer.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('WebSocket server on port $port');
    
    await for (HttpRequest request in server) {
      if (request.uri.path == '/ws') {
        handleWebSocketUpgrade(request);
      } else {
        request.response
          ..statusCode = HttpStatus.notFound
          ..write('Not found')
          ..close();
      }
    }
  }
  
  void handleWebSocketUpgrade(HttpRequest request) {
    String? key = request.headers.value('Sec-WebSocket-Key');
    
    if (key == null) {
      request.response
        ..statusCode = HttpStatus.badRequest
        ..close();
      return;
    }
    
    // Calculate accept key
    String accept = calculateWebSocketAccept(key);
    
    // Send upgrade response
    request.response
      ..statusCode = HttpStatus.switchingProtocols
      ..headers.add('Upgrade', 'websocket')
      ..headers.add('Connection', 'Upgrade')
      ..headers.add('Sec-WebSocket-Accept', accept);
    
    request.response.headers.contentLength = 0;
    
    print('WebSocket upgrade accepted');
    
    // Note: Full WebSocket implementation requires frame parsing
    // This is a simplified demonstration
  }
  
  String calculateWebSocketAccept(String key) {
    const String guid = '258EAFA5-E914-47DA-95CA-C5AB0DC85B11';
    String combined = key + guid;
    
    List<int> bytes = utf8.encode(combined);
    Digest digest = sha1.convert(bytes);
    
    return base64.encode(digest.bytes);
  }
}

void main() async {
  SimpleWebSocketServer server = SimpleWebSocketServer();
  await server.start(8080);
}
```

WebSocket upgrade requires HTTP handshake. Calculate Sec-WebSocket-Accept  
from client key. Full implementation needs frame parsing with masking.  
Dart's built-in WebSocket class handles this automatically.  

## Connection Rate Limiting

Rate limiting prevents abuse by restricting connections per IP address  
or globally.  

```dart
import 'dart:io';
import 'dart:collection';
import 'dart:async';

class RateLimiter {
  final int maxConnectionsPerIP;
  final Duration windowDuration;
  final HashMap<String, Queue<DateTime>> _connectionLog = HashMap();
  
  RateLimiter({
    this.maxConnectionsPerIP = 10,
    this.windowDuration = const Duration(minutes: 1),
  });
  
  bool allowConnection(String ipAddress) {
    DateTime now = DateTime.now();
    
    // Get or create log for this IP
    Queue<DateTime> log = _connectionLog.putIfAbsent(
      ipAddress,
      () => Queue<DateTime>(),
    );
    
    // Remove old entries
    while (log.isNotEmpty && 
           now.difference(log.first) > windowDuration) {
      log.removeFirst();
    }
    
    // Check limit
    if (log.length >= maxConnectionsPerIP) {
      return false;
    }
    
    // Record connection
    log.add(now);
    return true;
  }
  
  void cleanup() {
    DateTime now = DateTime.now();
    
    _connectionLog.removeWhere((ip, log) {
      // Remove expired entries
      while (log.isNotEmpty && 
             now.difference(log.first) > windowDuration) {
        log.removeFirst();
      }
      
      // Remove empty logs
      return log.isEmpty;
    });
  }
}

class RateLimitedServer {
  final RateLimiter _rateLimiter;
  
  RateLimitedServer({
    int maxConnectionsPerIP = 10,
    Duration windowDuration = const Duration(minutes: 1),
  }) : _rateLimiter = RateLimiter(
         maxConnectionsPerIP: maxConnectionsPerIP,
         windowDuration: windowDuration,
       );
  
  Future<void> start(int port) async {
    ServerSocket server = await ServerSocket.bind(
      InternetAddress.anyIPv4,
      port,
    );
    
    print('Rate-limited server on port $port');
    print('Max ${_rateLimiter.maxConnectionsPerIP} connections per IP');
    
    // Periodic cleanup
    Timer.periodic(Duration(seconds: 30), (_) {
      _rateLimiter.cleanup();
    });
    
    await for (Socket client in server) {
      String ip = client.remoteAddress.address;
      
      if (_rateLimiter.allowConnection(ip)) {
        print('Accepted connection from $ip');
        handleClient(client);
      } else {
        print('Rate limit exceeded for $ip - rejecting');
        client.writeln('ERROR: Rate limit exceeded');
        client.close();
      }
    }
  }
  
  void handleClient(Socket client) {
    client.listen(
      (data) {
        client.add(data); // Echo
      },
      onDone: () {
        client.close();
      },
    );
  }
}

void main() async {
  RateLimitedServer server = RateLimitedServer(
    maxConnectionsPerIP: 5,
    windowDuration: Duration(seconds: 30),
  );
  
  await server.start(8080);
}
```

Track connections per IP in sliding time window. Reject when limit exceeded.  
Periodically clean old entries. Prevents DoS attacks and ensures fair  
resource allocation.  

## Custom Network Protocol Parser

Building a parser for custom binary protocols demonstrates state-based  
packet processing.  

```dart
import 'dart:io';
import 'dart:typed_data';

enum PacketType {
  handshake,
  data,
  ack,
  close,
}

class Packet {
  final PacketType type;
  final int sequence;
  final Uint8List payload;
  
  Packet({
    required this.type,
    required this.sequence,
    required this.payload,
  });
  
  Uint8List encode() {
    int totalLength = 9 + payload.length;
    ByteData data = ByteData(totalLength);
    
    // Type (1 byte)
    data.setUint8(0, type.index);
    
    // Sequence (4 bytes)
    data.setUint32(1, sequence, Endian.big);
    
    // Payload length (4 bytes)
    data.setUint32(5, payload.length, Endian.big);
    
    // Payload
    Uint8List buffer = data.buffer.asUint8List();
    buffer.setRange(9, totalLength, payload);
    
    return buffer;
  }
  
  static Packet? decode(Uint8List data) {
    if (data.length < 9) return null;
    
    ByteData view = ByteData.view(data.buffer);
    
    int typeIndex = view.getUint8(0);
    if (typeIndex >= PacketType.values.length) return null;
    
    PacketType type = PacketType.values[typeIndex];
    int sequence = view.getUint32(1, Endian.big);
    int payloadLength = view.getUint32(5, Endian.big);
    
    if (data.length < 9 + payloadLength) return null;
    
    Uint8List payload = data.sublist(9, 9 + payloadLength);
    
    return Packet(
      type: type,
      sequence: sequence,
      payload: payload,
    );
  }
}

class ProtocolParser {
  final List<int> _buffer = [];
  
  List<Packet> parse(Uint8List data) {
    _buffer.addAll(data);
    
    List<Packet> packets = [];
    
    while (_buffer.length >= 9) {
      // Check payload length
      int payloadLength = (_buffer[5] << 24) |
                          (_buffer[6] << 16) |
                          (_buffer[7] << 8) |
                          _buffer[8];
      
      int totalLength = 9 + payloadLength;
      
      if (_buffer.length < totalLength) {
        // Wait for more data
        break;
      }
      
      // Extract packet
      Uint8List packetData = Uint8List.fromList(
        _buffer.sublist(0, totalLength)
      );
      
      Packet? packet = Packet.decode(packetData);
      if (packet != null) {
        packets.add(packet);
      }
      
      // Remove from buffer
      _buffer.removeRange(0, totalLength);
    }
    
    return packets;
  }
}

void main() async {
  // Create packets
  Packet handshake = Packet(
    type: PacketType.handshake,
    sequence: 1,
    payload: Uint8List.fromList('HELLO'.codeUnits),
  );
  
  Packet dataPacket = Packet(
    type: PacketType.data,
    sequence: 2,
    payload: Uint8List.fromList('Hello there!'.codeUnits),
  );
  
  // Encode
  Uint8List encoded1 = handshake.encode();
  Uint8List encoded2 = dataPacket.encode();
  
  print('Encoded packets:');
  print('  Handshake: ${encoded1.length} bytes');
  print('  Data: ${encoded2.length} bytes');
  
  // Parse
  ProtocolParser parser = ProtocolParser();
  
  // Simulate partial data arrival
  Uint8List combined = Uint8List.fromList([...encoded1, ...encoded2]);
  
  // Parse in chunks
  List<Packet> packets1 = parser.parse(combined.sublist(0, 10));
  print('\nParsed from first chunk: ${packets1.length} packets');
  
  List<Packet> packets2 = parser.parse(combined.sublist(10));
  print('Parsed from second chunk: ${packets2.length} packets');
  
  for (Packet p in [...packets1, ...packets2]) {
    String payload = String.fromCharCodes(p.payload);
    print('  ${p.type} #${p.sequence}: $payload');
  }
}
```

Custom protocol parser handles partial data with buffering. State machine  
tracks parsing progress. Validates packet structure before processing.  
This pattern applies to any binary protocol.  

## Summary

Low-level networking in Dart provides powerful capabilities for building  
custom protocols, high-performance servers, and specialized network  
applications. The dart:io library offers comprehensive socket programming  
APIs including TCP sockets (Socket, ServerSocket), UDP datagrams  
(RawDatagramSocket), secure connections (SecureSocket), and low-level  
event-driven I/O (RawSocket).  

These 60 examples covered essential concepts and patterns:  

**Basic Socket Operations:** TCP client/server, UDP communication, connection  
lifecycle management, timeouts, and keep-alive mechanisms.  

**Data Transmission:** Binary data handling, length-prefixed protocols,  
line-based protocols, chunked transfer, and custom framing.  

**Stream Processing:** Stream transformers, buffering, backpressure handling,  
and processing pipelines for efficient data manipulation.  

**Advanced Patterns:** Connection pooling, request-response correlation,  
multiplexing, load balancing, proxying, and health checks.  

**Protocol Implementation:** State machines, checksums for integrity,  
custom binary protocols, and secure TLS/SSL connections.  

**Performance Optimization:** Non-blocking I/O, zero-copy techniques,  
bandwidth throttling, socket tuning, and scatter-gather operations.  

**Reliability:** Reconnection with exponential backoff, heartbeat mechanisms,  
timeout detection, and graceful shutdown procedures.  

Key considerations for production networking code include proper error  
handling (SocketException, TimeoutException), resource management (always  
close sockets), security (validate input, use TLS, check certificates),  
performance tuning (buffer sizes, TCP options), and monitoring (track  
metrics, log events).  

Dart's asynchronous programming model makes it exceptionally well-suited  
for network programming. Futures and Streams provide clean abstractions  
for async operations, while async/await syntax keeps code readable. The  
event loop efficiently handles multiple concurrent connections without  
threading complexity.  

These patterns and techniques form the foundation for implementing network  
protocols, building custom servers, creating real-time applications, and  
developing high-performance networked systems in Dart. Whether building  
game servers, IoT devices, custom APIs, or network tools, understanding  
low-level networking enables creating efficient, reliable, and scalable  
network applications.  
