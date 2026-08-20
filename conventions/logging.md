# Logging

## Never log

- Secrets, tokens, PATs, passwords, bonding/secret keys, private keys.
- Full BLE payloads containing key material.
- Customer-identifying data beyond an opaque device id.

## Rules

- The logging tag comes from the owning class, configured once in a base class — never hand-passed per call site.

## TODO — answer on first hit

- Level semantics: what belongs in debug vs info vs warn vs error for app, device platform, and CI scripts.
- Format: plain string, structured key=value, or JSON per surface.
- Which logger per stack (Dart, Kotlin, Python/shell on Pi).
- Retention/verbosity in release builds.
