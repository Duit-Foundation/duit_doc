# Kernel library overview

## Introduction

The flutter_duit library depends on the duit_kernel package. It contains basic interfaces and
classes that serve to standardize the implementation of framework element. This package solves
several important problems:

- Separating interfaces from implementations for more flexible solution development
- Providing the ability to create your own implementations of the framework within a given contract
- The ability to write your own extensions of the framework's functionality without the need to
  install flutter_duit as a dependency

This division of the library into two components, which are updated for different reasons, creates a
more stable framework design.

## Overview

| Model                                      | Description                                                                                                                                                                                                             |
|--------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Action                                     | It is a collection of classes that define the properties of server events (ServerActions), their dependencies and json parsing                                                                                          |
| Attributes                                 | Defining a base interface to be implemented by objects that represent Flutter widget properties (attributes)                                                                                                            |
| Component description                      | A class that provides validation and parsing of json, used to describe component models                                                                                                                                 |
| Attribute parser                           | Base class for implementing an attribute object parser                                                                                                                                                                  |
| Registry                                   | A class that implements functionality that can be used on different layers both within the framework and within third-party solutions. Defines a set of interfaces for implementing and registering custom duit widgets |
| Streamer                                   | Interface for interaction with those modes of transport that implement their interaction with the network based on messages/streams of data                                                                             |
| Transport                                  | This class provides a common interface for different transport implementations, such as WebSocket, HTTP, etc. It defines methods and properties that need to be implemented by subclasses.                              |
| Transport options                          | The base options for configuring a transport.                                                                                                                                                                           |
| Abstract tree                              | Represents an abstract tree structure for rendering duit elements. It holds a JSON object representing the DUIT element tree, as well as a UIDriver for interacting with the UI.                                        |
| UI element model                           | Basic interface for implementing methods and properties of UI elements                                                                                                                                                  |
| UI controller                              | This class serves as the base class for all ViewController objects in your application. It provides common properties and methods that can be used by subclasses.                                                       |
| Driver                                     | An interface that defines the properties and methods of the driver object, which is used to interact the UI with the server (raising events), processing server events, as well as updating the UI                      |
| Value reference holder                     | An object that stores data about which value and for which property should be replaced in the attributes object                                                                                                         |
| Attribute wrapper                          | Employee object interface for top-level interaction with attribute objects                                                                                                                                              |
| WorkerPool, Task & WorkerPoolConfiguration | A set of classes for implementing work with isolates and embedding duit into existing classes that provide work with isolates                                                                                           |
| AnimatedPropertyOwner                      | A class that defines additional properties for attribute objects that can be modified to animate widgets                                                                                                                |
| ScriptRunner                               | Abstraction for implementing script execution                                                                                                                                                                           |
