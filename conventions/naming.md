# Naming

## Rules

- Full words. No abbreviations except established ones: `id`, `url`, `ui`, `api`, `db`.
- Names say what the thing does, not what it is technically. "handler", "manager", "util", "data", "info" are rejected unless the domain genuinely uses the word.
- A function named "get" must not also write.
- Constants carry their unit in the name: `STABILITY_THRESHOLD_DEG`, `MIN_REFRESH_GAP_MS`.
- Test names describe the behaviour under test.

## TODO — answer on first hit

- File naming per language (snake_case Dart files vs PascalCase Kotlin files — confirm project-by-project).
- Test naming pattern: `should_x_when_y` vs `x returns y when z` vs backticked sentences.
- Branch naming: `feat/<slug>`, `<ticket>-<slug>`, or other.
- Domain vocabulary: "Ofen" vs "heater" vs "device" in code — which term is canonical in which layer?
