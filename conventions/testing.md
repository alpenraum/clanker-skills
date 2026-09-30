# Testing

## Rules

- Every risky behaviour has a test that names the break it catches. A unit with risky logic and no
  such test is unfinished work; trivial code earns no test. Selection, seams and triage: `/write-tests`.
- **Quality over quantity.** Coverage is not the goal and is not gamed — a meaningful assertion per behaviour is.
- **Black box.** Expectations come from the contract (spec, callers, domain), written before reading
  the implementation — never derived from the code, which would encode its bugs as the expected result.
- A test that needs a non-standard shape (privates, internal spies, `as any`, sleeps, half the graph
  mocked) is an architecture finding: restructure the code to give the behaviour a seam.
- Tests target behaviour and edge cases. No tests asserting that the language, the framework, or a generated getter works.
- A bug fix ships with the test that reproduces the bug. A fix without a failing-then-passing test is not a fix.
- Test names describe the behaviour under test. No comment above a test explaining what the name should have said.
- No test depends on wall-clock time, real network, or real hardware. Inject seams instead.
- **A test double never lives in `src/`.** It goes in a directory the build and the linter already
  exclude (`test/` in a NestJS lib). Then a fake cannot reach a production bundle by construction
  rather than by someone remembering to keep it out of a barrel export. Shared fakes live there once
  and every layer's specs import them; a second copy per layer drifts.
- A fake mirrors the real seam's laziness. A hot fake standing in for a cold command lets a test
  pass while the code under test never subscribed.

## Testability is a design constraint

- Dependencies are injected. No global or static mutable state.
- Time, randomness, IO and network sit behind seams.
- A central mapping (errors → text, domain → view model) must be callable without a UI framework.

## TODO — answer on first hit

- Coverage expectation per layer (use case vs repository vs widget vs integration).
- Mocking approach per stack (mockito vs hand-written fakes in Dart; what in Kotlin).
- Which suites must pass locally before a gate hand-over, and which are CI-only because they are slow.
