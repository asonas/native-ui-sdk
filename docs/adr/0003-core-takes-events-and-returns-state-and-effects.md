# The core takes events and returns state and effects

Issue #5 asked what crosses the boundary between the core and an adapter. It also inherited two questions from ADR 0001: which non-UI frameworks the core may use, and whether the SwiftUI adapter may use AppKit or UIKit internally.

The core receives events from the adapter and returns the new state together with effects, which are instructions such as "return focus to the trigger" or "dismiss". The core never performs effects itself; the adapter carries them out. This keeps the core testable without a UI and lets an AppKit or UIKit adapter be added later without changing how the core is driven. How transitions are written inside the core, and whether the core holds its state or receives it with each event, are left to #7.

Everything the adapter observes also reaches the core as events: changes the system makes on its own (for example, the user swiping a presented menu away), the outcome of an effect, and the passage of time. The core does not read clocks or schedule timers. Any time information it needs arrives with events, and a timer is requested as an effect whose expiry the adapter reports back as an event.

User input reaches the core as semantic operations, which are a kind of event, not as raw platform input. Keyboard input becomes operations such as "next item", "dismiss", or "typeahead with this character"; pointer, touch, and system focus changes become operations such as "item pointed at", "item chosen", or "focus left the menu". Each adapter maps its platform's input to these operations according to that platform's conventions, so iOS and macOS can differ without the core knowing about either. Coordinates and platform-specific event types do not reach the core.

The core may use the standard library and Foundation, for example for locale-aware string comparison in typeahead, and nothing beyond them, such as CoreGraphics. In particular it does not use Observation or Combine: observing state changes and updating views is the adapter's job. This settles the framework question that ADR 0001 left to #5 and #7, so none of it remains for #7.

The SwiftUI adapter may use AppKit or UIKit internally where SwiftUI cannot provide a needed behavior, but these frameworks must not appear in the SwiftUI adapter's public API.

## Considered Options

- **The core calls back into the adapter.** The core would perform effects itself through closures or protocols supplied by the adapter. Less plumbing, but testing the core would need stand-in implementations, and the core would tend to lean towards one framework's model.
- **Raw, platform-neutral input.** The core would own the mapping from keys, pointer positions, and focus notifications to operations, putting more behavior in the core, but it would then need to know each platform's conventions.
- **Standard library only.** Avoids Foundation, but locale-aware comparison for typeahead would become harder to do inside the core, or would have to be pushed out to the adapter.
- **Observation in the core.** The adapter could observe core state directly, but the core would then decide how changes are observed, which belongs to the adapter.
- **No AppKit or UIKit inside the SwiftUI adapter.** Simpler, but behaviors that SwiftUI cannot express would have to be dropped.

## Consequences

- Effects become part of each primitive's contract: adding a new kind of effect means every adapter has to carry it out.
- Which input maps to which operation is defined per adapter, so interaction behavior is specified in two places: the operations in the core and the input bindings in each adapter.
- How the Menu chosen in #4 is presented (`popover`, `overlay`, or otherwise) is the SwiftUI adapter's choice, but the changes it brings, such as a presented menu being dismissed by the system, cross the boundary as events.
