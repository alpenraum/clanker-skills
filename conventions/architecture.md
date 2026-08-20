# Architecture

Architecture is the biggest decision in a change, never a byproduct of implementation.

## Deciding it

- Architecture gets designed and approved in the spec, before code.
- Any change that moves a boundary — layer, module, ownership of state, transport — is a `full`-tier spec regardless of line count.
- The spec names the layers a change touches and the direction of every new dependency. An unnamed boundary crossing is a spec defect.
- Architectural drift found mid-implementation stops the gate. It is never absorbed silently.

## Separation of concerns

- One module, one reason to change. Two reasons to change means two modules.
- UI holds no business logic. Business logic holds no transport or persistence detail. Transport and persistence hold no domain rules.
- **Use-case pattern is mandatory** for business logic: one class, one job, a single entry point (`invoke` / `execute`). No business-logic monoliths in services, controllers, or ViewModels.
- A use case orchestrates repositories and other use cases. It never touches a framework type (HTTP client, DAO, socket, `BuildContext`).
- ViewModel / controller / notifier is orchestration only: hold state, dispatch intents, call use cases, map results to view state.
- Repositories are thin: query, map, return **domain** types. No business rules, no locally invented caching policy, no wire or DB types leaking upward.
- Cross-layer dependencies point inward only. A domain type importing from UI or infrastructure is a defect, not a shortcut.
- Shared code splits by kind: platform/infrastructure primitives vs product-level features. Match the character of what already lives in each.

## Presentation

- Unidirectional data flow: one immutable state in, one intent channel out, one event channel for one-shot effects (Kotlin: `XContract : UnidirectionalViewModel<State, Intent, Event>`; Flutter: notifier + sealed state + event stream).
- State, Intent and Event are declared inside the feature's contract type, not as loose top-level declarations. The contract is the readable summary of the screen.
- State is a closed set of variants (sealed), each immutable, one variant per real screen condition — never a bag of nullable fields and booleans.
- The UI dispatches intents through one helper binding state + dispatch + events. Screens never call ViewModel methods directly.
- Navigation is decided by the presentation layer but executed with a navigator passed in. The ViewModel never holds a navigation handle (Kotlin: `intent(intent, navController)`).
- Exposing a stream as state goes through one shared helper with a defined sharing policy and initial value, not ad-hoc conversion at each site (Kotlin: `inViewModelScope` / `stateIn` + `WhileSubscribed`).
- A failing data stream degrades the screen rather than killing it: emit empty/last-good, surface the failure as state.
- One feature = one folder, view and logic in sibling files named after the feature (Kotlin: `XFeature.kt` + `XViewModel.kt`).
- Shared UI splits into two layers: framework-generic primitives, and app-branded components built on them.
- Platform differences sit behind one common API with per-platform implementations (Kotlin: `expect`/`actual`, `.android.kt` / `.ios.kt`).

## Concurrency and lifecycle

- Concurrency context is injected, never referenced statically (Kotlin: `DispatchersProvider`, never `Dispatchers.IO`).
- Long-lived and UI-lifetime work get separate scopes, and the long-lived one is cancelled explicitly on teardown.
- Cross-cutting infrastructure — logging tag, error boundary, scope wiring — lives in a base class configured once, never repeated per call site.

## Error handling

- "Something went wrong" is never an acceptable error state. Every failure surfaces what happened and, where possible, what the user can do about it.
- Every failure mode is a **typed** error. Not a string, not a bool, not a null.
- Repository- and infrastructure-specific errors (SQL, HTTP status, BLE stack, socket, filesystem) are mapped to the project's own error model **at the boundary that produced them**. Domain and UI never see a library exception.
- The pipeline is: typed exception → boundary converter → central localizer → error view. Never ad-hoc strings.
- The error model is exhaustive where the language allows it (sealed hierarchy / sum type), so a new failure mode forces every handler to be revisited.
- Never swallow: no empty catch, no catch-and-log-and-continue without an explicit decision about the failed operation.
- One central mapping from error type → user-facing text, domain-agnostic and unit-testable without a UI framework.
- Technical detail stays available for support as a separate copyable block, distinct from the human-readable message.

## Boundaries and contracts

- A response field that is absent on success is a contract, not an omission. Document the default at the parse site.
- Entities are keyed by the identifier their consumers already hold, not by whatever the producing system finds convenient.
- Domain and UI may use different scales for the same quantity, but the conversion lives in exactly one place.
- A capability only one transport can report must be advertised on every transport a flow can use — otherwise that flow cannot gate on it.
- Where no remote-config service exists, a per-instance capability flag is the rollout mechanism. Design features to degrade, not to require a flag service.
- Product/variant differences live in one registry that owns them (identifiers, handlers, routes), never in scattered `if (type == …)` checks.

## Data flows

- Sampled time series: read the interval from the response, never assume it. Treat the newest point as up to one interval stale. Fetch append-only, never upsert.
- Telemetry payloads are PII-safe by construction, enforced by a sanitizer at the transport edge — never trust the call site.
