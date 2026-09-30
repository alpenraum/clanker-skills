# Dart / Flutter

## Stack facts

- Flutter via `fvm`. Never invoke bare `flutter` when `.fvm/` exists — use `fvm flutter`.
- `riverpod` + `riverpod_generator`, `json_serializable`, `build_runner` codegen. Generated files are never hand-edited.
- `l10n.yaml` present — localisation options come from there, not from CLI flags.

## Answered

- **Own feature folder vs joining an existing screen.** A feature gets its own folder and route
  when it carries state of its own beyond a single value — a result list, an error with a retry,
  a multi-step flow. A settings screen then holds only the entry row. A feature that is exactly
  one value and one label stays an inline card on the screen that owns it.
  (21energy-app, offline mining, 2026-09-25.)
- **Provider naming and file placement.** Feature state lives in
  `lib/components/<feature>/<feature>_model.dart`: the sealed Freezed state and the `@riverpod`
  `<Feature>Model` notifier in one file, screen beside it as `<feature>_screen.dart`. A value
  that is optimistically written gets a separate `<feature>_toggle_notifier.dart` holding a
  `ConsistentValue<T>`. (21energy-app, 2026-09-25.)

- **Toggles are optimistic and eventually consistent, even over a long device write.** The switch
  flips on tap (`ConsistentValue.action`). The command goes out fire-and-forget (`sendCommand`), its
  reply arrives on `commandResponseProvider`, and the confirmed value comes from an async query
  that lands in `DeviceState`. Never `sendCommandSync` for this. When the write may have landed
  anyway, re-read before reverting. A tap during an in-flight write queues latest-wins: one
  follow-up write after the reply, only if the desired value differs from the confirmed one.
  (21energy-app, offline mining, 2026-09-25.)

## TODO — answer on first hit

- Navigation/pop conventions — provider invalidation on pop is a recurring bug source; define the rule.
