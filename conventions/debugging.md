# Debugging a live system

Applies whenever the thing under investigation is running and not fully controlled: hardware, a
device on a network, a deployed service, anything whose state changes while you look at it.

## Instruments

- **Read the target's own log before its API.** An API tells you what the system reports about itself
  at one instant; the log tells you what it did. Locate the log in the first minutes of the phase, not
  after the inference fails.
- **When the driver exposes no log accessor, the log is still there.** Reach it over SSH, the
  filesystem, or the management interface. "The abstraction does not surface it" is not "it is
  unavailable".
- **Suspect your own instrument before the target.** A guard, timeout, retry or poll that you added is
  a candidate cause of any symptom you observe. Check the code you wrote this session first, not last.

## Before the first run, establish three things

Without all three, no sample can be interpreted. State them explicitly.

1. **Where the log is** and how to read it.
2. **How long the success signal takes to appear** — computed, not guessed. If a signal is probabilistic,
   compute its expected interval from the actual rate.
3. **How long the system takes to restart or settle** after a write.

## Evidence

- **A sample taken inside a write, restart or settle window is not evidence.** Wait longer than the
  settle time measured above, with no writes in the window.
- **Absence is only evidence once you know how long presence should take.** Before concluding "no X
  happened", compute how long X should take to happen. A 300 s observation of an event with a 512 s
  expected interval proves nothing.
- **One sample is never a verdict.** Sustained observation across a window distinguishes a steady state
  from a point in a cycle.
- **Record observations continuously, write the verdict once.** A verdict committed to a durable
  artefact mid-investigation gets rewritten every time the picture changes, and each rewrite reads as
  confident as the last.

## Acting on the system

- **Use the cheapest write that reaches the state you want to observe.** A measurement run is not an
  API-exercise run; reserve the public seam for when the seam itself is under test.
- **Count the side effects of each write.** If a write restarts the system, a three-write switch costs
  three restarts — and manufactures the very restart windows that then corrupt your samples.
- **Safety-restore scripts and experiment scripts are different tools.** An unattended harness that
  auto-restores in a `finally` will cut short an attended experiment.
- **Verify restoration explicitly after every run**, by comparing against the state captured before it.
- **Keep the recovery tooling until the phase is closed.** Do not clean up the script that puts the
  system back while the system is still in an experimental state.

## Guards you add

- **A guard the spec did not ask for carries an assumption. Write the assumption down.** "A quiet
  socket is a dead socket" is false for any protocol with idle periods; the timeout encoding it will
  disconnect healthy peers.
- **When a constant's job is to suppress something, ask what else it suppresses.** A threshold set so
  an event "practically never happens" also removes that event as a liveness signal.
- **Two constants that must agree get a test asserting the relationship**, not just their values.
