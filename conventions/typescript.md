# TypeScript

## Stack facts

- NestJS monorepos, RxJS end to end, Jest + ts-jest.

## Async

- **Every asynchronous boundary returns `Observable<T>`. Never `Promise<T>`.** Applies to service
  methods, transports, repositories, injected clocks — not only to code that already uses RxJS.
  Load-bearing: a promise cannot be cancelled by unsubscribe and cannot be composed with
  `concatMap`, so one promise-returning method forces conversion on every caller above it.
- Commands are **cold**. Wrap side effects in `defer` so the work starts on subscribe, not on
  construction. A hot command puts traffic on a shared resource for a caller that already walked
  away.
- `firstValueFrom` / `lastValueFrom` appear **only at a framework boundary that demands a promise**
  — a Nest lifecycle hook, an Express handler that cannot take an Observable. Put the Observable
  version beside it and call that from everywhere else.
- Serialise access to a shared resource with a `Subject` + `concatMap`. Not a promise chain.
- Retry with `retry({ count, delay })`; `delay` returns `throwError` for a failure that must not be
  retried, rather than the retry being wrapped in a conditional outside.
- Inject time as `{ now(): number; wait(ms): Observable<void> }` backed by `timer()`. Never
  `setTimeout` inside a `new Promise`.
- Suffix `$` marks a long-lived stream (`stop$`, `stream$`). One-shot commands keep plain names even
  though they return Observables.

## Types

- A **closed set of values that crosses a wire or is compared in more than one place is an `enum`**, not a union of string literals. Renaming a value then fails to compile instead of silently failing a comparison. Members are `UPPER_SNAKE`, values are the wire strings.
- A union of string literals is still right for a set that is only ever produced and rendered, never compared.
- At a layer boundary that deliberately does not depend on the enum's owner, the field stays `string` and carries the enum's wire value. Do not import a package just to type a field you never branch on.

## Testing

- Specs use `firstValueFrom` at the assertion. That is the only place awaiting is idiomatic.
