# Ship a Skill for app developers' agents with the first primitive

Issue #8 asked whether agent-facing entry points, such as an Agent Skill or an MCP server, ship from the start. The issue's background mixed two kinds of agents: agents in an app developer's project that use the library's primitives to build screens, and agents that write new primitives in the shape ADR 0005 defines.

The entry point is for the first kind: agents that build app screens with the primitives the library provides, starting with the Menu chosen in #4. Agents writing primitives inside this repository already have AGENTS.md, the glossary, and the ADRs to follow. The issue's "agent-writable" value, that agents can write primitives, rests on the shape ADR 0005 defines; this ADR decides how agents that use the library get their guidance. Whether app developers may define their own primitives, which would make ADR 0005's protocol part of the public API, is a larger product decision and is not made here.

An Agent Skill ships together with the first primitive and is kept in the same repository as the Swift package. The Skill shipped here consists of documents only, Markdown instructions that agents read, even though the Agent Skills format also allows scripts. Documents cost little to write and little to change while the API is still settling, and shipping them early gives agents in app projects guidance from the first primitive on, instead of only after the pattern settles. The library itself is still distributed only through SwiftPM, as ADR 0002 decided. Installing the Skill is a separate step from adding the package, and how app developers install it is decided when the first primitive is implemented.

An MCP server does not ship for now. Unlike the Skill, it is a separate program that has to be implemented, built, distributed, and kept running alongside the library, which adds maintenance work before there is evidence it is needed. ADR 0002 does not rule it out, since it concerns how the library is distributed, but the same wish to keep maintenance small applies. An MCP server will be considered if a Skill turns out not to be enough, for example if agents need to query something a static document cannot answer.

## Considered Options

- **Also target agents that write primitives.** Agents in app projects could define their own primitives, but that requires deciding to make ADR 0005's protocol public before a second primitive has tested it.
- **No entry points until the pattern settles.** Less to keep in sync while the first primitives change, but agents in app projects would have no guidance while the first primitives are adopted, and a Skill made of documents is cheap to change anyway.
- **Both a Skill and an MCP server from the start.** Agents would get an interactive way to query the library, at the cost of implementing and maintaining a second program before there is evidence a Skill is not enough.

## Consequences

- Each library release provides a Skill that matches that version's API and behavior. When a primitive's API or behavior changes, the Skill is checked and updated as needed. How the Skill is fetched and how its version is matched to the library's are decided with the installation method.
- Where the Skill lives in the repository and how app developers install it are decided when the first primitive is implemented.
- The Skill's instructions steer agents in app projects, so how app developers can trust its source, check it has not been tampered with, and handle updates are decided with the installation method.
- The Skill describes only the primitives the library provides. If app developers are later allowed to define primitives, the Skill will need a section for that, and the protocol's public status has to be decided first.
- An MCP server stays out until a concrete need that a Skill cannot meet appears.
