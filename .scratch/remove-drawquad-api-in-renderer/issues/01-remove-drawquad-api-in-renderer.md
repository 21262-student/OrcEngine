# 01: Remove drawQuad API from Renderer and Verify Pipeline

Status: resolved
Type: task
Blocked by: None

**What to build:**
Remove all four `drawQuad` method overloads from the public interface and implementation of `Renderer`, ensuring that all 2D quad geometry submission goes through high-level `Texturable` shapes (`Rectangle` and `Sprite`). Keep low-level batch vertex submission (`submitQuad`) private inside `Renderer`. Verify that the engine and demo harness compile cleanly without errors or warnings and that demo smoke tests pass.

## Acceptance criteria

- [ ] All four `drawQuad` method overloads are removed from `Renderer`'s public header.
- [ ] All four `drawQuad` method definitions are removed from `Renderer`'s source implementation.
- [ ] `submitQuad` remains private in `Renderer`.
- [ ] `OrcEngine` and `TestProject` compile cleanly with zero errors via `.\scripts\build.ps1 -Preset windows-debug`.
- [ ] Demo smoke test passes with zero errors via `.\scripts\run-demo.ps1 -TimeoutSeconds 5 -FailOnErrorLog`.
- [ ] Code formatting and conventions pass with zero errors via `.\scripts\lint-conventions.ps1 -ChangedOnly`.

## Comments

## Answer

Removed all 4 drawQuad overloads from Renderer.hpp and Renderer.cpp. Verified clean compilation and demo smoke test.
