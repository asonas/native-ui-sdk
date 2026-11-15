# The core tracks a highlighted item, not platform focus

Issue #6 asked which kind of "focus" the core's rules refer to. Apple platforms have at least two kinds that do not move together: keyboard focus, which SwiftUI exposes through `@FocusState`, and accessibility focus, the element VoiceOver is on, which SwiftUI exposes through `@AccessibilityFocusState`. While VoiceOver is running, there may be no keyboard focus at all, and VoiceOver moves its focus between elements on its own.

The core tracks neither of them. Instead it tracks the highlighted item: the item in the Menu chosen in #4 that navigation currently points at. Arrow keys, typeahead, the pointer moving over an item, and VoiceOver moving onto an item all reach the core as semantic operations, as ADR 0003 requires, and each of them moves the highlight. Reflecting the highlight in keyboard focus, accessibility focus, or only in how the item is drawn is the adapter's job.

When the menu is opened from the keyboard, the first item is highlighted, as in the WAI-ARIA Menu Button pattern that #4 took as the reference. When it is opened with the pointer or by touch, nothing is highlighted until an operation moves the highlight, so that a pointer user sees no highlight they did not ask for. The pattern does not specify opening with the pointer, so this is this project's choice. If VoiceOver focus moves into the menu, that movement is the operation that sets the highlight.

Disabled items can be highlighted, so moving between items, including the initial highlight, does not skip them. This follows the WAI-ARIA pattern, which keeps disabled items focusable but not activatable. Choosing a disabled item does nothing and leaves the menu open; the pattern does not say this directly, and this project derives it from "not activatable".

When the menu closes because the user dismissed it, for example with Escape or the VoiceOver escape gesture, because the system dismissed it, for example after the user swiped away a menu presented as a sheet, or because the user chose an enabled item, the core returns an effect to return focus to the trigger. When the menu closes because the user tapped or clicked outside it, the core returns no such effect, since the user has moved on to another part of the screen. WAI-ARIA does not specify this case; not returning focus is this project's decision. Some presentations close themselves on an outside tap and report it only as a system dismissal. When the adapter knows the menu closed because of an action outside it, that case wins and focus is not returned. When the adapter cannot tell why the menu closed, it reports a system dismissal and focus is returned, since losing focus entirely is worse for keyboard and VoiceOver users than an unneeded return after a touch. Which platform focus the adapter moves to carry out the effect is the adapter's choice. If the trigger no longer exists, for example because the chosen item's action removed it, the adapter reports that the effect could not be carried out as an event, as ADR 0003 requires; where focus goes in that case is not decided here.

## Considered Options

- **Keyboard focus only.** The core would model `@FocusState`-style focus and leave VoiceOver focus to the accessibility mapping. Operations that come from VoiceOver could then not move the core's state, and behavior for VoiceOver users would live outside the core.
- **Keyboard and accessibility focus as separate states.** More complete, but the core would have to keep two states consistent with each other and with the platform, which only the adapter can observe.
- **Always highlight the first item on open.** Simpler, but a menu opened by clicking or tapping would show a highlight the user did not ask for.
- **Skip disabled items.** Fewer steps to reach an item that can be chosen, but users moving with the keyboard would not learn that a disabled item exists.
- **Always return focus to the trigger.** Simpler, but after an outside tap or click it would pull focus away from where the user just acted.

## Consequences

- Keeping keyboard focus and VoiceOver focus consistent with the highlight is the adapter's responsibility, so each adapter has to decide, per platform, which of them follow the highlight.
- Opening the menu needs two operations, one that opens it with the first item highlighted and one that opens it with nothing highlighted. Each adapter decides which platform input maps to which, so the input source itself does not reach the core.
- How reliably focus return follows the user's intent depends on how well each presentation lets the adapter tell an outside tap from other system dismissals.
- How the standard menus on macOS and iOS treat disabled items during keyboard movement has not been checked. If they differ from WAI-ARIA, an adapter that follows platform conventions may want to diverge, which would need this decision to be revisited.
