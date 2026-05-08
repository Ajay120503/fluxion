# Fluxion Bug Fixes & Feature Updates

## Bug Fixes

### `lib/InterfaceUtils.sh`
- **Critical:** Fixed `default)` → `*)`in `interface_hardware()` and `interface_chipset()` case statements. In bash, `default` is a literal match, not a catch-all — only `*` works as a catch-all. This caused unknown hardware bus types to always fall through silently instead of being handled.
- **Fix:** Re-enabled the double-source guard (`InterfaceUtilsVersion`) that was commented out, preventing the file from being sourced multiple times on re-use.

### `fluxion.sh`
- **Critical:** Fixed `options+=("$FLUXIONGeneralBackOption")` → `choices+=` in `fluxion_hash_verify()`. The array was named `choices`, but the Back option was being appended to an undefined `options` array and never displayed. Back navigation was broken in the hash verification menu.
- **Critical:** Fixed `fluxion_set_language()` subshell `return` bug. The language-file check used `find ... | while read ...` (pipe), which runs in a subshell, making `return 1` inside affect only the subshell — not the outer function. Switched to process substitution (`< <(find ...)`) and a `_langMissing` flag, so the fallback to English actually triggers.
- **Critical:** Fixed `more $FLUXIONPreferencesFile | grep -q` — `more` is an interactive pager and its pipe loses the `grep` exit code. Replaced with `grep -q ... "$FLUXIONPreferencesFile"` directly.
- **Bug:** Fixed `let __fluxion_do_sequence__instructionIndex++` → `(( ... )) || true`. The `let` builtin returns exit code 1 when the result is 0 (e.g., first iteration), which would abort the sequence under strict error handling. Using `(( ... )) || true` is safe.
- **Bug:** Fixed `kill -s SIGKILL $authService` — `$authService` may be undefined, causing a fatal kill invocation. Now guarded with `[ -n "${authService:-}" ]`.
- **Feature:** Added 6GHz / Wi-Fi 6E band support to the interactive scan channel menu. If the interface reports 6GHz capability, options for "6GHz" and "All Bands" now appear.
- **Feature:** 6GHz channels (≥178) now get a `₆` suffix in the target network list, similar to how DFS channels get `!`.
- **Feature:** Channel-to-band auto-inference now correctly maps channels ≥178 to `ax` (6GHz) instead of incorrectly treating them as 5GHz.

### `lib/WindowUtils.sh`
- **Bug:** Fixed `fluxion_window_close()` in tmux mode — previously only the process PID was killed, leaving orphaned tmux windows. Now the corresponding tmux window is also killed by matching the stored pane PID.
