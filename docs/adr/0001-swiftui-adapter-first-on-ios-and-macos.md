# Ship a SwiftUI adapter first, targeting iOS and macOS

Issue #2 asked whether to target SwiftUI only or AppKit as well. Because the non-SwiftUI framework on iOS is UIKit rather than AppKit, we answered it as two questions: which OSes to target, and when to build adapters other than SwiftUI.

The library targets iOS (including iPadOS) and macOS, and the only adapter we build for now is the SwiftUI one. SwiftUI runs on both platforms, so one adapter can serve both, while building an AppKit adapter alongside it would add a second implementation to build and validate while the pattern is still unsettled. AppKit (macOS) and UIKit (iOS) adapters are deferred, not ruled out: the core must stay independent of UI frameworks: it must not import SwiftUI, AppKit, UIKit, or any other UI framework, or expose their types, so that adding either adapter later stays possible. Whether the core may depend on other frameworks such as Foundation, CoreGraphics, Observation, or Combine is left to #5 and #7.

This does not decide whether the SwiftUI adapter may use AppKit or UIKit internally to fill gaps in SwiftUI; that is left to the core/adapter boundary question (#5). Whether Mac Catalyst counts as a supported macOS environment is left to #3.

## Considered Options

- **SwiftUI and AppKit from the start.** Would validate the core/adapter boundary against two implementations at once, but adds a second framework with its own view and event handling to the first primitive before the pattern has settled.
- **SwiftUI only, with SwiftUI types allowed in the core.** A separate variant of #2's "SwiftUI only" option: that option lets the adapter use SwiftUI idioms freely, while this one also lets SwiftUI types into the core. Simpler at first, but could require refactoring the core before adding an AppKit or UIKit adapter.
- **A single platform (macOS only or iOS only).** Narrower scope, but would postpone the question of whether one core can serve the different input styles of iOS and macOS, which is central to the library's value.

## Consequences

- One SwiftUI adapter still needs platform-specific handling and testing on each OS. The first primitive has to handle the input each platform uses: keyboard and pointer on macOS; touch on iOS, plus hardware keyboards and, on iPadOS, pointer input from a trackpad or mouse.
- Keeping UI framework imports and types out of the core is a constraint on the core/adapter boundary (#5).
