# The core takes events and returns state and effects

Issue #5 asked what crosses the boundary between the core and an adapter. It also inherited two questions from ADR 0001: which non-UI frameworks the core may use, and whether the SwiftUI adapter may use AppKit or UIKit internally.

The core receives events from the adapter and returns the new state together with effects, which are instructions such as "move focus to the second item" or "dismiss". The core never performs effects itself; the adapter carries them out. This keeps the core testable without a UI and lets an AppKit or UIKit adapter be added later without changing how the core is driven. How transitions are written inside the core is left to #7.

Keyboard input reaches the core as semantic operations such as "next item", "dismiss", or "typeahead with this character", not as raw keys. Each adapter maps keys to these operations according to its platform's conventions, so iOS and macOS can differ without the core knowing about either.

The core may use the standard library and Foundation, for example for locale-aware string comparison in typeahead, and nothing beyond them, such as CoreGraphics. In particular it does not use Observation or Combine: observing state changes and updating views is the adapter's job.

The SwiftUI adapter may use AppKit or UIKit internally where SwiftUI cannot provide a needed behavior, but these frameworks must not appear in the SwiftUI adapter's public API.

## Considered Options

- **The core calls back into the adapter.** The core would perform effects itself through closures or protocols supplied by the adapter. Less plumbing, but testing the core would need stand-in implementations, and the core would tend to lean towards one framework's model.
- **Raw, platform-neutral keys as input.** The core would own the mapping from keys to operations, putting more behavior in the core, but it would then need to know each platform's conventions.
- **Standard library only.** Avoids Foundation, but locale-aware comparison for typeahead would become harder to do inside the core, or would have to be pushed out to the adapter.
- **Observation in the core.** The adapter could observe core state directly, but the core would then decide how changes are observed, which belongs to the adapter.
- **No AppKit or UIKit inside the SwiftUI adapter.** Simpler, but behaviors that SwiftUI cannot express would have to be dropped.

## Consequences

- Effects become part of each primitive's contract: adding a new kind of effect means every adapter has to carry it out.
- Which key maps to which operation is defined per adapter, so keyboard behavior is specified in two places: the operations in the core and the key bindings in each adapter.
- How the Menu chosen in #4 is presented (`popover`, `overlay`, or otherwise) is an implementation detail of the SwiftUI adapter and does not affect this boundary.
