# native-ui-sdk

A headless behaviors library for Apple platforms: it defines how UI components behave (open/close, selection, keyboard navigation, focus, dismissal) and leaves rendering to the app.

## Language

**Core**:
The UI-framework-independent layer that defines the library's behaviors.
_Avoid_: engine, model

**Adapter**:
The layer that connects the core to one UI framework, such as SwiftUI.
_Avoid_: binding, bridge, renderer

**Highlighted item**:
The item the user is about to act on, tracked by the core apart from where the platform's keyboard or accessibility focus is.
_Avoid_: focus, active item, selection
