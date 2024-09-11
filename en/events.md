# Events

## What is this?

Events in the context of Duit are special objects that represent a reaction to
performed action (on the server or locally) and describe what the framework should do on
based on this data.

## Event types

| Event Type       | Description                                                                                                                                                           |
|------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| update           | Describes changes to widget attributes. Contains a Map, where the keys are the ids of the controlled widgets, and the values are the data sets required for updating. |
| updateLayout     | The event contains a new layout that completely replaces the existing widget tree.                                                                                    |
| navigation       | An event that triggers a callback responsible for navigating between application screens. Handled by the implementation of the `ExternalEventHandler` interface       |
| openUrl          | An event that triggers a callback responsible for working with links to external web resources. Handled by the implementation of the `ExternalEventHandler` interface |
| custom           | An event that calls a callback responsible for processing a user event. Handled by the implementation of the `ExternalEventHandler` interface                         |
| sequenced        | A wrapper object for a group of events that will be processed in turn after a specified period of time.                                                               |
| grouped          | A wrapper object for a group of events that will be called in turn. The order of execution is not guaranteed.                                                         |
| animationTrigger | An event that causes the animation to start in the builder with the specified id                                                                                      |
| timer            | Event that will be called when the timer expires                                                                                                                      |

## Links

[TS package events implementations](https://github.com/Duit-Foundation/duit_js/blob/main/src/lib/event.ts)
[GO package events implementation](https://github.com/Duit-Foundation/duit_go/blob/main/pkg/duit_core/event.go)