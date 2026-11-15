# Primitives are pure transition functions over Swift enums

Issue #7 asked how state machines in the core are expressed. It also inherited from ADR 0003 how transitions are written and whether the core holds its state or receives it with each event, and from the review of ADR 0003 the names of the types that cross the boundary.

Each primitive is written in plain Swift, with no DSL. Its events and effects are enums, its state is a value type, and its transitions are one pure function that takes the current state and an event and returns the new state together with a list of effects. Transition functions switch over events without a `default:` clause, so the compiler checks that every event is handled: adding an event without handling it is a compile error. A `default:` clause would silently absorb new events, which is why it is not allowed there. This check covers events only; adding a state is not caught unless the code switches over the state in the same way. That gives people and AI agents writing a primitive the same immediate feedback, which serves the goal in #7 that agents can write behaviors.

The core holds no state. The adapter keeps each primitive's state as a value, passes it in with every event, stores the state that comes back, and carries out the returned effects in the order they are listed. The order is part of the contract, so the core can rely on it when the result depends on it, for example when one effect dismisses the menu and the next returns focus to the trigger. This follows from ADR 0003, where observing state changes and updating views is the adapter's job: the adapter already holds the state it observes, so the core needs no way to announce changes. It also means a transition can be tested by comparing inputs and outputs alone, and value types make it easier to satisfy Swift's concurrency checking.

The input type is called Event and the instruction type is called Effect, the words ADR 0003 and ADR 0004 already use. The Composable Architecture also has a type named `Effect`; an app that uses both can tell them apart by module name.

All primitives conform to one protocol with exactly four requirements: the state type, the event type, the effect type, and the transition function. It has no default implementations or other machinery. Its purpose is to make the compiler enforce that every primitive has the same shape, so that a person or an agent who has read one primitive can read or write the next. Its Swift name is left to the implementation.

Whether to introduce a DSL will be revisited once there are about three primitives and the duplication between them is visible.

## Considered Options

- **A small dedicated DSL.** A result builder or macros would make definitions uniform and possibly easier for agents to read. With one primitive, though, it is not yet clear what should be shared, and a DSL designed now would encode guesses.
- **The core holds state.** Callers would only send events, but the core would then need a way to announce changes, and ADR 0003 rules out Observation and Combine for that.
- **Command.** The same idea as Elm's `Cmd` and the closest in meaning, but SwiftUI calls the content of the macOS menu bar commands (`.commands`, `CommandMenu`), which would be confusing in a library whose first primitive is a Menu.
- **Instruction.** No naming clash, but less familiar than Effect and different from the word the ADRs already use.
- **No shared protocol yet.** Primitives would share their shape by convention until a second primitive proves what is common. Nothing would stop the shapes from drifting apart, and agents would have no type to follow.

## Consequences

- The two questions ADR 0003 left to #7, how transitions are written and who holds state, are settled here.
- Every adapter is responsible for storing each primitive's state, feeding events in order, and carrying out the returned effects in order.
- Transition functions handle every event explicitly, including events that change nothing in a given state, so they grow with the number of events.
- The behavior details ADR 0004 left to the Menu's interaction specification, such as where the first key moves the highlight, are not decided by choosing a representation and still have to be specified before the Menu is implemented.
