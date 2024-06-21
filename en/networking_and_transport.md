# Working with the network

### Transport layer

The transport layer of the driver ensures working with the network: connecting to the backend and
receiving the initial
UI markup, parsing incoming data, preparing data for requests, performing actions over the network
and
passing execution results back to the driver for processing.

The transport layer can operate in multi-threaded (concurrent) mode if an instance is defined
WorkerPool.

To configure transport, objects that inherit the properties of the TransportOptions class from
duit_kernel library

### Supported network protocols

Duit currently supports two major network protocols - HTTP and WebSocket. HTTP is the most
suitable for distributing “static” content that does not require receiving real-time UI updates. At
that
At the same time, it remains possible to update user interface attributes as part of processing
actions on the server side and when receiving update events.

To use a specific transport, you must define the appropriate transport parameters
when creating a driver instance.

```dart

final driver = DuitDriver(
  "/layout1",
  transportOptions: HttpTransportOptions(
    defaultHeaders: {"Content-Type": "application/json"},
    baseUrl: "http://localhost:8999",
  ),
);

//or

final driver = DuitDriver(
  "ws://localhost:8999",
  transportOptions: WebSocketTransportOptions(),
);
```

### Overriding the transport object for the driver

When embedding Duit into an existing Flutter application, you may need to
use another http or ws client. To do this you will need to create an extension that
will override the transport object when the driver is initialized.

1. Create a successor to the `TransportOptions` class to configure a new transport.

```dart
class DioTransportOptions extends TransportOptions {
  @override
  String? baseUrl;

  @override
  Map<String, String> defaultHeaders;

  @override
  String type = "dio";

  final Dio dio;

  DioTransportOptions({
    required this.baseUrl,
    required this.dio,
    this.defaultHeaders = const {},
  });
}
```

2. Create a inheritor to the `Transport` class.

```dart
class DioTransport extends Transport {
  final DioTransportOptions options;

  DioTransport(super.url, this.options);

  @override
  Future<Map<String, dynamic>?> connect() async {
    final res = await options.dio.get(url);
    return jsonDecode(res.data) as Map<String, dynamic>;
  }

  @override
  void dispose() {
    <...>
  }

  @override
  FutureOr<Map<String, dynamic>?> execute(ServerAction action,
      Map<String, dynamic> payload,) async {
    <...>
    return jsonDecode(response.data) as Map<
    String
    ,
    dynamic
    >;
  }

  @override
  FutureOr<Map<String, dynamic>?> request(String url, Map<String, dynamic> meta,
      Map<String, dynamic> body) {
    <...>
  }
}
```

3. Let's create an extension for the UIDriver class (super class for DuitDriver).

```dart
extension DioTransportExtension on UIDriver {
  applyDioTransportExtension() {
    transport = DioTransport(
      source,
      transportOptions as DioTransportOptions,
    );
  }
}
```

4. We use the new extension to override the transport when initializing the driver.

```dart
<...>
_driver = DuitDriver(
      "/main",
      transportOptions: DioTransportOptions(
        baseUrl: "http://localhost:8999", 
        dio: dioInstance,
      ),
    )..applyDioTransportExtension();
<...>
```