# Testing

The flutter_test library is used for testing. For golden testing of widgets it is
used [alchemist](https://github.com/Betterment/alchemist).

## General recommendations

- For testing, it is recommended to use the `DuitDriver.static` driver constructor so as not to
  perform initialization of the transport layer.
- Do not try to trigger actions that are performed using the transport layer.
  Use local actions or scripts instead.
- To interact with the driver, use special methods marked
  with the `@visibleForTesting` annotation.

## Basic widget testing scenario

- Golden tests with controlled/uncontrolled widget options.
- Test for assigning the correct key to a widget based on its id.
- Test for correct updating of widget properties.
- Testing of edge cases. For example, the DuitText widget does not appear if
  the `data` attribute property was not
  assigned. [Example](https://github.com/Duit-Foundation/flutter_duit/blob/ca220c12bc95b94dc09d29ef41595a36424cef14/test/d_text_test.dart#L94C5-L94C15)
- Additionally: if the widget attribute class is a descendant of AnimatedPropertyOwner, you should
  check the correct operation of the animation of one or more
  properties. [Example](https://github.com/Duit-Foundation/flutter_duit/blob/ca220c12bc95b94dc09d29ef41595a36424cef14/test/d_text_test.dart#L177)
- Additionally: if the widget attribute class is a descendant of AttendedModel, you should check
  correct operation of the functions for assigning and obtaining values ​​from an attribute (
  methods `void update(T value)` and `T collect()`)