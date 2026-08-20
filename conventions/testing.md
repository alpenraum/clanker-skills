# Testing

## Rules

- Everything is at least unit tested. A unit with logic and no test is unfinished work.
- **Quality over quantity.** Coverage is not the goal and is not gamed — a meaningful assertion per behaviour is.
- Tests target behaviour and edge cases. No tests asserting that the language, the framework, or a generated getter works.
- A bug fix ships with the test that reproduces the bug. A fix without a failing-then-passing test is not a fix.
- Test names describe the behaviour under test. No comment above a test explaining what the name should have said.
- No test depends on wall-clock time, real network, or real hardware. Inject seams instead.

## Testability is a design constraint

- Dependencies are injected. No global or static mutable state.
- Time, randomness, IO and network sit behind seams.
- A central mapping (errors → text, domain → view model) must be callable without a UI framework.

## TODO — answer on first hit

- Coverage expectation per layer (use case vs repository vs widget vs integration).
- Mocking approach per stack (mockito vs hand-written fakes in Dart; what in Kotlin).
- Which suites must pass locally before a gate hand-over, and which are CI-only because they are slow.
- Where hardware-dependent behaviour gets faked (miner gRPC, BLE peripheral).
