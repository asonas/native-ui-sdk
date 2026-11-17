# native-ui-sdk

A headless behaviors library for Apple platforms: it defines how UI components behave (open/close, selection, keyboard navigation, focus, dismissal) and leaves rendering to the app.

## Language

**Core**:
The UI-framework-independent layer that defines the library's behaviors.
_Avoid_: engine, model

**Adapter**:
The layer that connects the core to one UI framework, such as SwiftUI.
_Avoid_: binding, bridge, renderer

**Primitive**:
One behavior the library provides as a unit, such as the Menu: defined in the core and connected to a UI framework by an adapter.
_Avoid_: component, widget

**Highlighted item**:
The item that navigation currently points at, which may differ from where the platform's keyboard or accessibility focus is.
_Avoid_: focus, active item, selection

**Action**:
The work the app attaches to an item, performed when the item is chosen. The library decides when an item is chosen, not what its action does.
_Avoid_: handler, callback

**Event**:
Something that happened, reported to the core: a semantic operation by the user, a change the system made on its own, the outcome of an effect, or the passage of time.
_Avoid_: action, message, input

**Effect**:
An instruction the core returns for the adapter to carry out, such as returning focus to the trigger.
_Avoid_: command, side effect, instruction
