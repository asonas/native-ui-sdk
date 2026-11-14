# Ship a SwiftUI adapter first, targeting iOS and macOS

The library targets iOS and macOS, and the only adapter we build for now is the SwiftUI one. SwiftUI runs on both platforms, so one adapter can serve both, while building an AppKit adapter alongside it would add a second implementation to build and validate while the pattern is still unsettled. AppKit (macOS) and UIKit (iOS) adapters are deferred, not ruled out: the core must not import any UI framework or expose SwiftUI types, so that adding either adapter later stays possible.

This does not decide whether the SwiftUI adapter may use AppKit or UIKit internally to fill gaps in SwiftUI; that is left to the core/adapter boundary question (#5).

## Considered Options

- **SwiftUI and AppKit from the start.** Would validate the core/adapter boundary against two implementations at once, but SwiftUI and AppKit differ fundamentally in lifecycle and event model, which adds work to the first primitive before the pattern has settled.
- **SwiftUI only, with SwiftUI types allowed in the core.** Simpler at first, but could require refactoring the core before adding an AppKit or UIKit adapter.
- **A single platform (macOS only or iOS only).** Narrower scope, but would postpone the question of whether one core can serve the different input styles of iOS and macOS, which is central to the library's value.

## Consequences

- One SwiftUI adapter still needs platform-specific handling and testing on each OS. The first primitive has to handle the input each platform uses: keyboard and pointer on macOS, and touch on iOS, plus hardware keyboards on iOS.
- Keeping SwiftUI types out of the core is a constraint on the core/adapter boundary (#5).
