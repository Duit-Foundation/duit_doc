# Custom widgets

## Pre-requirements

To work with DuitRegistry and register custom widgets, you need to install duit_kernel in
as a project dependency.

```
flutter pub add duit_kernel
```

## Description of the custom widget on the backend

At this point, you should create two main objects: the attributes object and the widget model
itself.

Select the required super class for inheritance depending on your requirements
widget. `CustomSingleChildWidget` for widgets with one child, `CustomMultiChildWidget`
for widgets with multiple child elements, or `CustomWidget` for widgets without children
elements.

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

After this, the class can be used to build the UI on the backend side, or you can wrap it in
function for more convenient use - this is how all the widgets that are provided are implemented
adapter libraries.

## Registering a custom widget in DuitRegistry

The DuitRegistry class is responsible for custom widgets, components, working in concurrent mode and
embedding these mechanisms into the operation of Duit.
Entities in DuitRegistry are registered once and will be used for all created in
driver application.

To register a new custom component, use a static
method `DuitRegistry.register`

```dart
static void register
(
String key, {
required ModelFactory modelFactory,
required BuildFactory buildFactory,
required AttributesFactory attributesFactory,
});
```

Let's look at the function parameters in more detail.

- `key` is the key string by which the custom widget will be identified, saved and stored in
  will be used in the future at different stages of the Duit life cycle.
- `modelFactory` - a function responsible for creating an instance of a model object for the widget
  and passing
  basic Duit structures (attributes, controller) into a custom model.
- `buildFactory` - a function analogous to the `build` method of the `Widget` class. Responsible for
  building the Ui
  from basic Flutter widgets or widgets exported by third-party libraries.
- `attributesFactory` - a function that implements JSON mapping to a custom object model
  attributes. Used for initial JSON processing and later for updating the UI.

### An example of the implementation of the functions and models described above:

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

After implementing the necessary functions, you can register a new custom widget.

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

## Mapping JSON to attribute objects

An important part of Duit's work is working with data. To facilitate this, in addition to adapters
for
backend, flutter_duit exports the AttributeValueMapper class with a set of methods to simplify the
creation
attributes from JSON if you use properties in the form in which they are provided
adapter.

### Example of casting an array of two elements to an EdgeInsets.symmetric object

```dart
final insets = AttributeValueMapper.toEdgeInsets(json["padding"]); //padding: [16, 16];
```

To achieve fault tolerance and minimize errors, it is recommended to use this approach.