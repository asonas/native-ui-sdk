# Require iOS 26 and macOS 26, distribute through SwiftPM only

The library supports iOS 26, iPadOS 26, and macOS 26 or later, and is distributed only as a Swift package. As a new library, it focuses on recent OS generations and keeps compatibility work to a minimum: the SwiftUI adapter can call focus and keyboard APIs such as `onKeyPress` and the interaction-aware `focusable` without availability checks. SwiftPM is the only distribution channel, shipped as source rather than a prebuilt XCFramework; CocoaPods trunk is scheduled to stop accepting new podspecs on 2 December 2026, so publishing there would not be a lasting option anyway.

Mac Catalyst, left open by ADR 0001, is not supported for now. Catalyst brings UIKit-based iPad apps to the Mac and would add another environment to validate; supporting it can be revisited in a separate issue.

The Swift tools version and language mode are a separate compatibility decision and will be set when `Package.swift` is created.

## Considered Options

- **iOS 17 / macOS 14.** The oldest generation where `onKeyPress`, the interaction-aware `focusable`, and Observation are all available, so it would reach more apps without availability checks for those APIs. Rejected in favour of focusing on recent generations and minimising compatibility work.
- **iOS 18 / macOS 15.** A middle ground between the two. Rejected for the same reason.
- **Supporting iOS 16 / macOS 13 or earlier.** Keyboard handling would need workarounds for the missing APIs, which would pre-empt the core/adapter boundary question (#5).

## Consequences

- Apps that still support iOS 18 / macOS 15 or earlier (the versions before Apple's jump to 26) cannot adopt the library until they raise their own deployment target.
- Lowering the minimum later would need re-validation on the older OSes, and possibly availability checks or alternative implementations for any newer APIs the adapter has come to use.
