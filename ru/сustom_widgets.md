# Пользовательские виджеты

## Предварительные требования

Для работы с DuitRegistry и регистрации кастомных виджетов требуется установить duit_kernel в
качестве зависимости проекта.

```
flutter pub add duit_kernel
```

## Описание пользовательского виджета на бэкенде

На этом этапе следует создать два основных объекта: объект атрибутов и саму модель виджета.

Выберете необходимый супер-класс для наследования в зависимости от требований к
виджету. `CustomSingleChildWidget` для виджетов с одним дочерним элементом, `CustomMultiChildWidget`
для виджетов с множетсом дочерних элементов, либо `CustomWidget` для виджетов без дочерних
элементов.

```typescript
import { CustomWidget, BaseAction } from "duit_js";


interface ExampleCustomWidgetAttributes {
    random?: string;
}

export class ExampleCustomWidget extends CustomWidget<ExampleCustomWidgetAttributes> {

    constructor(attrs: ExampleCustomWidgetAttributes, tag: string, id?: string, action?: BaseAction, controlled?: boolean) {
       super(attrs, tag, id, action, controlled);
    }
 }
```

После этого класс можно использовать для простроения UI на строне бэкенда, или же обернуть его в
функцию для более удобного использования - так реализованы все виджеты, которые предоставляются
библиотеками адаптеров.

## Регистрация пользовательского виджета в DuitRegistry

Класс DuitRegistry отвечает за пользовательских виджетов, компонентов, работу в concurrent mode и
встраивание этих механизмов в процесс работы Duit.
Сущности в DuitRegistry регистрируются единожды и будут использованы для всех создаваемых в
приложении драйверов.

Для регистрации нового пользовательского компонента используется статический
метод `DuitRegistry.register`

```dart
static void register
(
String key, {
required ModelFactory modelFactory,
required BuildFactory buildFactory,
required AttributesFactory attributesFactory,
});
```

Рассмотрим парамеры функции более детально.

- `key` - это строка-ключ, по которому пользовательский виджет будет идентифицирован, сохранент и в
  будущем использован на разных этапаж жизненного цикла Duit.
- `modelFactory` - функция, отвечающая за создание экземпляра объекта-модели для виджета и передачу
  базовых структур Duit (атрибуты, контроллер) в пользовательскую модель.
- `buildFactory` - функция-аналог метода `buildFactory` класса `Widget`. Отвечает за построение Ui
  из базовых виджетов Flutter или виджетов, экспортируемых сторонними библиотеками.
- `attributesFactory` - функция, реализующая маппинг JSON на пользовательскую модель объекта
  атрибутов. Используется при начальной обработке JSON и, в дальнейшем, для обновления UI.

### Пример реализации описанных выше функций и моделей:

```dart
import 'package:duit_kernel/duit_kernel.dart';
import 'package:flutter/material.dart';
import 'package:flutter_duit/flutter_duit.dart';

final class ExampleCustomWidget extends DuitElement<dynamic> {
  ExampleCustomWidget({
    required super.id,
    required super.attributes,
    required super.viewController,
    required super.controlled,
  }) : super(
    type: "Custom",
    tag: "ExampleCustomWidget",
  );
}

class ExampleCustomWidgetAttributes
    implements DuitAttributes<ExampleCustomWidgetAttributes> {
  String? random;

  ExampleCustomWidgetAttributes({required this.random});

  @override
  ExampleCustomWidgetAttributes copyWith(other) {
    return ExampleCustomWidgetAttributes(
      random: other.random ?? random,
    );
  }

  @override
  ReturnT dispatchInternalCall<ReturnT>(String methodName, {
    Iterable? positionalParams,
    Map<String, dynamic>? namedParams,
  }) {
    // TODO: implement dispatchInternalCall
    throw UnimplementedError();
  }
}

DuitAttributes exampleAttributeMapper(String type,
    Map<String, dynamic>? json,) {
  return ExampleCustomWidgetAttributes(random: json?["random"] ?? "no random")
  as DuitAttributes;
}

class ExampleWidget extends StatefulWidget {
  final UIElementController controller;

  const ExampleWidget({
    super.key,
    required this.controller,
  });

  @override
  State<ExampleWidget> createState() => _ExampleWidgetState();
}

class _ExampleWidgetState extends State<ExampleWidget>
    with ViewControllerChangeListener<ExampleWidget, dynamic> {
  @override
  void initState() {
    attachStateToController(widget.controller);
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Text(attributes.random ?? "");
  }
}

Widget exampleRenderer(TreeElement model) {
  final m = model as ExampleCustomWidget;
  return ExampleWidget(
    controller: m.viewController!,
  );
}

TreeElement<dynamic> modelMapperExample(String id,
    bool controlled,
    ViewAttribute attributes,
    UIElementController? controller,) {
  return ExampleCustomWidget(
    id: id,
    attributes: attributes,
    viewController: controller,
    controlled: controlled,
  );
}
```

После реализации необходимых функций, можно зарегистрировать новый пользовательский виджет.

```dart
void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  DuitRegistry.register(
    "ExampleCustomWidget",
    modelFactory: modelMapperExample,
    buildFactory: exampleRenderer,
    attributesFactory: exampleAttributeMapper,
  );
  runApp(const MyApp());
}
```

## Маппинг JSON в объекты атрибутов

Важной частью работы Duit является работа с данными. Для облегчения этого, помимо адаптеров для
бэкенда, flutter_duit экспортирует класс AttributeValueMapper с набором методов, упрощающих создание
атрибутов из JSON в случае, если вы используете свойста в том виде, в котором их предоставляет
адаптер.

### Пример приведения массива из двух элементов к объекту EdgeInsets.symmetric

```dart

final insets = AttributeValueMapper.toEdgeInsets(json["padding"]) //padding: [16, 16];
```

Для достижения отказоустойчивости и минимизации ошибок рекомендуется использовать этот подход.