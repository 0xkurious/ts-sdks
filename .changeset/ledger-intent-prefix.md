---
"@mysten/ledgerjs-hw-app-sui": patch
---

Fix `signTransaction` to prepend the Sui intent prefix automatically.

The Ledger Sui app signs `Blake2b(rawInput)` directly — it does not add
the intent prefix internally. The firmware uses the prefix bytes to identify
the data type and render clear-signing details on screen; without it the
device falls back to a "blind signing" warning. This means the firmware
contract expects to receive `[0x00, 0x00, 0x00] || txBytes` (intent scope +
version + app_id, followed by the raw transaction bytes).

Previously callers had to prepend this prefix manually before calling
`signTransaction`, which was undocumented and caused signatures to be
rejected by the Sui network when callers passed raw `txBytes` directly.

`signTransaction` now prepends the `TransactionData` intent prefix
internally, matching both the method name semantics and the expected
firmware input format.
