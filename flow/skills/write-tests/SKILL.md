---
name: write-tests
description: "Write the few unit tests a change actually needs — each at the most stable public seam, each naming the bug it catches — and triage the tests a change broke into caught-a-bug vs merely-coupled. Use when: write tests, add tests, test this, cover this, spec for X, tests broke, fix the failing tests, adapt the specs, a refactor broke tests. Not for: e2e/browser tests, testing skills."
argument-hint: "[write] <unit or change> | triage"
---

# Write tests

A test exists to catch a specific break. The suite this skill builds fails on bugs and stays green
on redesign — so it is small, sits on stable seams, and asserts outcomes. Volume is a cost, not a
result: every test is code that the next change has to carry.

**Mode:** `write` (default) — tests for a unit or a change. `triage` — an intentional change broke
existing tests. Load `reference.md` for the stack-specific seams and factory patterns.

## Two rules above all

**Black box: the contract is the oracle, never the code.** Expected values come from what the unit
must do — the spec, what its callers rely on, the domain — and are written **before** reading the
implementation's body. Read the code only for its seam: the entry point's signature and where
dependencies are injected. A test derived from the code encodes whatever the code does, bugs
included, and then defends them. When the code and the contract disagree, that is a finding: stop
and report it, never write the code's answer into the expectation.

**An awkward test is an architecture finding.** If a behaviour can only be tested by reaching into
privates, spying on internals, `as any`, mocking half the graph, sleeping, or rebuilding internal
state, the behaviour has no seam. Stop and report it with the restructure that would give it one
(extract a pure function, a use case, an injected seam). Do not contort the test around the design.
Injected time/IO/hardware seams and a framework's own test harness are standard, not workarounds.

## write

### 1. Contract, not code

List what callers rely on — from the spec, the public signature, and the callers themselves
(`grep` them). Branches of the implementation are not the list; the promises are. Include the
inputs a caller can send that the implementation may never have considered (`null`, empty,
duplicates, out of order) — the black box has to answer them too. Mark each
behaviour's risk: **actuation/safety**, **data or money**, **caller-visible refusal**, **plain logic**.

### 2. Select — every test names its break

A behaviour earns a test only when you can name the **realistic production mistake that makes it
fail**, and that mistake is a **bug, not a decision**.

| Earns a test | Earns none |
|---|---|
| The main outcome of each public entry point | Constructors, getters, constants, DTO shape |
| Each refusal / failure class (one per class, not per message) | Forwarding, wiring, framework behaviour |
| Boundaries where the risk sits: limits, `null` / empty / malformed at the edge, ordering of side effects | Log lines, field lists, "key still exists", "symbol stays removed" |
| A reproduced bug (test first, fails, then fix) | Anything whose only failure mode is an intentional change |

Two candidate tests that fail on the same mutation are one test. Write the table
`behaviour | seam | break it catches` before any test code; show it to the user when a safety behaviour
is involved or it has more than ~8 rows.

### 3. Seam — the most stable public door

Test through the highest seam that still exposes the behaviour cheaply:

1. **Pure function** → call it.
2. **Use case / service entry point** → `invoke()` with real collaborators where they are cheap
   (in-memory store, real config service); fake only IO, time, hardware, network.
3. **HTTP boundary** → when the behaviour involves validation, status codes, guards or
   serialisation, go through the framework with the **production pipe configuration**. A
   controller method called directly skips the validation that most boundary bugs live in.

Never test a private method, reach into `(x as any)._field`, or build the unit with positional
`{} as any` padding. **One factory per seam** with a named-options object and sensible real
defaults; a test overrides only what it is about. A constructor or DI change then edits the
factory, not forty tests.

### 4. Assert outcomes

- Returned value, stored state, emitted command, HTTP status — with **hand-written literals**,
  never an expectation computed by the code under test.
- `toHaveBeenCalledWith` only where the call **is** the contract (the write to hardware, the
  command sent), and then with its arguments. Never assert that a mock was present.
- Errors: assert the **status or the refusal type/kind**. Message text only where the sentence is
  itself the product contract — then one test per such sentence, matched whole, not a regex
  fragment sprinkled across tests.
- `toMatchObject` on the fields the behaviour is about. `toEqual` on a whole structure only when
  the whole structure is the contract (a wire payload), and then assert `Object.keys` too —
  `toEqual` ignores `undefined` members.
- No wall clock, randomness, network or hardware; inject them.
- **Every refusal needs a positive control on the same seam.** A 400 test passes just as well when
  the route refuses everything for an unrelated reason; a valid body that must succeed is what makes
  the refusal mean something. A refusal test that is green before the fix is suspect until explained.

### 5. Mutation check

For each test, state the mutation it kills. Then mutate the production code in your head:
flipped branch, dropped write, off-by-one on a bound, skipped validation, `null` treated as absent,
swapped order of two side effects, default/empty return. A realistic mutation on a risky behaviour
that nothing kills → add a test. A test that kills no mutation the others don't → delete it.

### 6. Hand over

`test name | break it catches` for every test written, plus the behaviours deliberately left
untested and why. No other summary.

## triage

Run whenever an intentional change turns existing tests red. Classify **every** failing test
before touching any of them:

| Class | Meaning | Action |
|---|---|---|
| **caught** | The change broke a behaviour nobody meant to change | Fix the production code. Stop and report first if it contradicts the spec. |
| **contract** | The guarded behaviour changed on purpose | Update the expectation, once, at one place |
| **coupled** | Broke on structure only — signature, constructor arity, field list, wording, internal state, harness shape — while the behaviour it names is unchanged | Do not patch in place. Move it onto the seam/factory, or delete it if another test guards the same break |
| **obsolete** | The behaviour it guards was removed | Delete it |

Report the four counts. A high **coupled** count is the finding: name the harness change that
removes that whole class of breakage (usually a missing factory or a test below the seam).

Never weaken an assertion to go green. A test edited without a class is a hidden failure.

## Temporary pins

A refactor may first pin the whole current output with one `toEqual` to prove equivalence. The pin
is scaffolding: once the refactor is accepted it is deleted, and only tests that name a break stay.

## Red flags

- The test fails on every intentional change and has never failed on a bug
- Setup and expectation come from the same builder, so they cannot disagree
- More mock setup than assertion, or the mock is what is being checked
- A list of keys, a constant's value, or a removed symbol being asserted
- The same assertion appears across many tests (it belongs in one)
- The test would still pass if the behaviour it names were deleted
