# wgpui-component adapter status

**GitHub:** private [Muktidaya/wgpui-component](https://github.com/Muktidaya/wgpui-component) (`root`). Longbridge remains `upstream` (`https://github.com/longbridge/gpui-component.git`).

**Upstream pin:** `5cb0946` (gpui-component v0.5.2) — see `vendor/UPSTREAM_SHA`

**Stack:** vendored gpui-component → **wgpui 0.3.4** via Cargo dependency aliasing (`gpui` → `wgpui`, `gpui_platform` → `wgpui-platform`).

## Phase gates

| Phase | Gate | Status |
|-------|------|--------|
| 0 Scaffold | workspace + platform shim | ✅ |
| 1 wgpui-base | `cargo check -p wgpui-base --lib` | ✅ |
| 2 wgpui-component | `cargo check -p wgpui-component --lib` | ✅ |
| 3 hello_world | `cargo run -p hello_world` | ✅ (manual smoke) |
| 4 assets/macros/i18n | `icon_named!` via build.rs + `rust_i18n` locales | ✅ |
| 5 integration harness | `cargo test -p wgpui-component-harness` | ✅ |

## Architecture

- **Not a thin wrapper.** Upstream expects Zed GPUI 0.2.2 APIs; wgpui 0.3.4 required substantial compatibility extensions in `~/Developer/wgpui` (a11y, `FollowMode`/`ListState`, `BoxShadow::new`, `container_query`, system notifications stubs, focus API, etc.).
- **Source aliasing:** vendored `.rs` files keep `use gpui::` / `gpui_component::` import names; workspace `Cargo.toml` maps packages to `wgpui-*`.
- **Platform:** `wgpui-platform` shim exposes `application() → wgpui::Application::new()`.

## Verification commands

```bash
cd ~/Developer/wgpui-component
cargo check -p wgpui-base --lib
cargo check -p wgpui-component --lib
cargo run -p hello_world
cargo test -p wgpui-component-harness
```

## Out of scope (this pass)

- WASM / `gpui_web`
- Full upstream `story` gallery (`gpui-shell`, `reqwest_client`, `gpui_web` deps deferred)
- crates.io publish
- Braid app migration

## API delta log (high-signal)

Compatibility shims live primarily in **wgpui** (`~/Developer/wgpui`), not in vendored component sources. Notable gaps patched:

- `FocusHandle::focus(window, cx)`, `Window::focus(handle, cx)`, `focus_next(cx)`
- `StatefulInteractiveElement` aria helpers (`aria_numeric_value`, `aria_row_count`, …)
- `ShapedLine::paint` with align/width; `TouchClickEvent`
- `ListState`: `set_follow_mode`, `scroll_to_end`, `remeasure*`, `is_following_tail`
- `BoxShadow::new`, `container_query`, `SystemNotification*` stubs
- `TextSelectionScopeExt` split from `ElementExt` (for `AnimationElement` bounds)

## Next actions

1. Re-enable `crates/story` when `gpui-shell` + HTTP client are wired for wgpui.
2. Trim wgpui profile `[profile.dev.package]` keys that still reference `gpui`/`reqwest_client`.
