# Ship a Skill for app developers' agents with the first primitive

Issue #8 asked whether agent-facing entry points, such as an Agent Skill or an MCP server, ship from the start. The issue's background mixed two kinds of agents: agents in an app developer's project that use the library's primitives to build screens, and agents that write new primitives in the shape ADR 0005 defines.

The entry point is for the first kind: agents that build app screens with the primitives the library provides, starting with the Menu chosen in #4. Agents writing primitives inside this repository already have AGENTS.md, the glossary, and the ADRs to follow. Whether app developers may define their own primitives, which would make ADR 0005's protocol part of the public API, is a larger product decision and is not made here.

An Agent Skill ships together with the first primitive and lives in this repository. A Skill is a set of Markdown instructions that agents read, so it costs little to write and little to change while the API is still settling, and shipping it early shows from the start that agents can work with the library. It adds no distribution channel: it is served from the same repository as the Swift package.

An MCP server does not ship for now. It is a program that has to be built, distributed, and kept running, which would add the kind of extra distribution channel ADR 0002 avoids by shipping only through SwiftPM. It will be considered if a Skill turns out not to be enough, for example if agents need to query something a static document cannot answer.

## Considered Options

- **Also target agents that write primitives.** Agents in app projects could define their own primitives, but that requires deciding to make ADR 0005's protocol public before a second primitive has tested it.
- **No entry points until the pattern settles.** Less to keep in sync while the first primitives change, but the goal that agents can work with the library would not be demonstrated early, and a Skill is cheap to change anyway.
- **Both a Skill and an MCP server from the start.** Agents would get an interactive way to query the library, at the cost of building and distributing a second program, against ADR 0002's single channel, before there is evidence a Skill is not enough.

## Consequences

- The Skill is part of each release: when a primitive's API or behavior changes, the Skill has to change with it.
- Where the Skill lives in the repository and how app developers install it are decided when the first primitive is implemented.
- The Skill describes only the primitives the library provides. If app developers are later allowed to define primitives, the Skill will need a section for that, and the protocol's public status has to be decided first.
- An MCP server stays out until a concrete need that a Skill cannot meet appears; adding it would also mean revisiting ADR 0002's single distribution channel.
