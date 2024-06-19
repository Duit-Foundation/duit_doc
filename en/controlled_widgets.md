# The concept of managed widgets and UI state updating

### Introduction

Within Duit, all widgets can have two main implementations: controlled and uncontrolled.
When we say that a widget is controllable, we mean that the widget's data can
be updated in
consequence of processing the update event, after which it will be rebuilt.

Under the hood, controlled widgets are always StateFull widgets and an explicit widget definition
like
controlled - necessary optimization so as not to create unnecessary load using
exclusively StateFull widgets.

If the widget is controllable, a separate UiController is created for it, which
attaches to UiDriver and ensures widget attributes are updated when necessary and allows
perform related actions.

### Widgets that are controllable by default:

Some widgets are always controllable by default due to the fact that they provide
processing user input, requesting additional data for filling, or simply performing
related actions.

- ElevatedButton
- ListView.builder & ListView.separated
- Checkbox
- Component
- GestureDetector
- LifecycleStateListener
- Meta
- Radio
- Subtree
- Slider
- Switch
- TextField
- AnimationBuilder