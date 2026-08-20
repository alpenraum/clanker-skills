# Dart / Flutter

## Stack facts

- Flutter via `fvm`. Never invoke bare `flutter` when `.fvm/` exists — use `fvm flutter`.
- `riverpod` + `riverpod_generator`, `json_serializable`, `build_runner` codegen. Generated files are never hand-edited.
- `l10n.yaml` present — localisation options come from there, not from CLI flags.

## TODO — answer on first hit

- Provider naming and file placement for generated providers.
- When a new screen gets its own feature folder vs joins an existing one.
- Navigation/pop conventions — provider invalidation on pop is a recurring bug source; define the rule.
