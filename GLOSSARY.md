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
The item that navigation currently points at, which may differ from where the platform's keyboard or accessibility focus is.
_Avoid_: focus, active item, selection

**Event**:
Something that happened, reported to the core: a user operation, a change the system made on its own, the outcome of an effect, or the passage of time.
_Avoid_: action, message, input

**Effect**:
An instruction the core returns for the adapter to carry out, such as returning focus to the trigger.
_Avoid_: command, side effect, instruction
