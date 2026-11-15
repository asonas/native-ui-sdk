# Require iOS 26 and macOS 26, distribute through SwiftPM only

Issue #3 asked for the minimum iOS / macOS versions and whether SwiftPM is the only distribution channel; it also inherited the Mac Catalyst question from ADR 0001.

The library supports iOS 26, iPadOS 26, and macOS 26 or later. This is a policy choice rather than a technical requirement: as a new library, it focuses on recent OS generations and keeps compatibility work to a minimum. The focus and keyboard APIs it is expected to rely on, such as `onKeyPress` and the interaction-aware `focusable`, already exist from iOS 17 / macOS 14, so they need no availability checks at this minimum either way.

The library is distributed only as a Swift package, shipped as source rather than a prebuilt XCFramework. Source lets adopters read and debug the behavior implementations, and SwiftPM, being the package manager built into Xcode, avoids maintaining additional distribution channels. For background, CocoaPods trunk is scheduled to stop accepting new podspecs on 2 December 2026.

Mac Catalyst, left open by ADR 0001, is not supported for now. Catalyst brings UIKit-based iPad apps to the Mac and would add another environment to validate; supporting it can be revisited in a separate issue. Here and below, "not supported" means outside what we support and validate, not that builds are blocked: SwiftPM does not prevent building for platforms the package does not list, such as Mac Catalyst or visionOS.

The Swift tools version and language mode are a separate compatibility decision and will be set when `Package.swift` is created.

## Considered Options

- **iOS 17 / macOS 14.** The oldest generation where `onKeyPress`, the interaction-aware `focusable`, and Observation are all available, so it would reach more apps without availability checks for those APIs. Rejected in favour of focusing on recent generations and minimising compatibility work.
- **iOS 18 / macOS 15.** A middle ground between the two. Rejected for the same reason.
- **Supporting iOS 16 / macOS 13 or earlier.** Keyboard handling would need alternative implementations for the missing APIs, and how to provide them is a matter for the core/adapter boundary question (#5).

## Consequences

- Apps that still support iOS 18 / macOS 15 or earlier (the versions before Apple's jump to 26) cannot adopt the library until they raise their own deployment target, and users on devices that cannot update to the 26 releases are left out.
- Building for these targets requires Xcode 26 or later. Declaring them with the `.iOS(.v26)` / `.macOS(.v26)` constants requires tools version 6.2 or later; a version string such as `.iOS("26.0")` also works with older tools versions.
- APIs introduced after the 26 releases will still need availability checks.
- Lowering the minimum later would need re-validation on the older OSes, and possibly availability checks or alternative implementations for any newer APIs the adapter has come to use.
