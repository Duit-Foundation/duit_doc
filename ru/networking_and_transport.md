# Работа с сетью

### Транспортный слой

Транспортный слой драйвера обеспечивает работу с сетью: подключение к бэкенду и получение начальной
разметки UI, парсинг входящих данных, подготовку данных для запросов, выполнение действий по сети и
передача результатов выполнения обратно в драйвер для обработки.

Транспортный слой может работать в многопоточном (конкурентном) режиме, если определен экземпляр
WorkerPool.

Для конфигурации транспорта используются объекты наследующие свойства класса TransportOptions из
библиотеки duit_kernel.

### Поддерживаемые сетевые протоколы

В настоящее время Duit поддерживает два основных сетевых протокола — HTTP и WebSocket. HTTP наиболее
подходит для раздачи «статического» контента, не требующего получения реал-тайм обновлений UI. В то
же время остается возможность обновления атрибутов пользовательского интерфейса в рамках обработки
действий на стороне сервера и при получении событий обновлений.

Чтобы использовать конкретный транспорт, необходимо определить соответствующие параметры транспорта
при создании экземпляра драйвера.

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

### Переопределение объекта транспорта для драйвера

При встраивании Duit в существующее Flutter приложение, может возникнуть необходимость
использовать другой http или ws клиент. Для этого потребуется создать расширение, которое
переопределит объект транспорта при инициализации драйвера.

1. Создадим наследника класса `TransportOptions` для конфигурации нового транспорта.

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

2. Создадим наследника класса `Transport`

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
    return jsonDecode(response.data) as Map<String, dynamic>;
  }

  @override
  FutureOr<Map<String, dynamic>?> request(String url, Map<String, dynamic> meta,
      Map<String, dynamic> body) {
    <...>
  }
}
```

3. Создадим расширение для класса UIDriver (супер-класса для DuitDriver).

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

4. Используем новое расширение для переопределения транспорта при инициализации двайвера.

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