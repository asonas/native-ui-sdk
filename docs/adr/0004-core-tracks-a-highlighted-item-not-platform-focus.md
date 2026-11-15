# The core tracks a highlighted item, not platform focus

Issue #6 asked which kind of "focus" the core's rules refer to. Apple platforms have at least two kinds that do not move together: keyboard focus, which SwiftUI exposes through `@FocusState`, and accessibility focus, the element VoiceOver is on, which SwiftUI exposes through `@AccessibilityFocusState`. While VoiceOver is running, there may be no keyboard focus at all, and VoiceOver moves its focus between elements on its own.

The core tracks neither of them. Instead it tracks the highlighted item: the item in the Menu chosen in #4 that the user is about to act on. Arrow keys, typeahead, the pointer moving over an item, and VoiceOver moving onto an item all reach the core as semantic operations, as ADR 0003 requires, and each of them moves the highlight. Reflecting the highlight in keyboard focus, accessibility focus, or only in how the item is drawn is the adapter's job.

Disabled items can be highlighted, so moving between items does not skip them, but choosing a disabled item does nothing. This follows the WAI-ARIA Menu Button pattern that #4 took as the reference.

When the menu closes because the user dismissed it, for example with Escape or the VoiceOver escape gesture, or because the user chose an item, the core returns an effect to return focus to the trigger. When the menu closes because the user tapped or clicked outside it, the core returns no such effect, since the user has moved on to another part of the screen. Which platform focus the adapter moves to carry out the effect is the adapter's choice.

## Considered Options

- **Keyboard focus only.** The core would model `@FocusState`-style focus and leave VoiceOver focus to the accessibility mapping. Operations that come from VoiceOver could then not move the core's state, and behavior for VoiceOver users would live outside the core.
- **Keyboard and accessibility focus as separate states.** More complete, but the core would have to keep two states consistent with each other and with the platform, which only the adapter can observe.
- **Skip disabled items.** Fewer steps to reach an item that can be chosen, but users moving with the keyboard would not learn that a disabled item exists.
- **Always return focus to the trigger.** Simpler, but after an outside tap or click it would pull focus away from where the user just acted.

## Consequences

- Keeping keyboard focus and VoiceOver focus consistent with the highlight is the adapter's responsibility, so each adapter has to decide, per platform, which of them follow the highlight.
- How the standard menus on macOS and iOS treat disabled items during keyboard movement has not been checked. If they differ from WAI-ARIA, an adapter that follows platform conventions may want to diverge, which would need this decision to be revisited.
