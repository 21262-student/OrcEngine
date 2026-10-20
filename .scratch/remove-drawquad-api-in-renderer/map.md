# Remove drawQuad API in Renderer

## Notes

Synthesized from issue `.scratch/ideas/issues/01-remove-drawquad-api-in-renderer.md` and settled grilling interview.
Removes legacy `drawQuad` API from `Renderer`, confirming `Rectangle` and `Sprite` as the canonical mechanisms for 2D quad geometry submission.

## Decisions so far
- [02-synchronize-graphics-architecture-documentation](issues/02-synchronize-graphics-architecture-documentation.md): Updated AGENTS.md section 4.4 to document quad geometry submission without drawQuad; reviewed GLOSSARY.md.
- [01-remove-drawquad-api-in-renderer](issues/01-remove-drawquad-api-in-renderer.md): Removed all 4 drawQuad overloads from Renderer.hpp and Renderer.cpp. Verified clean compilation and demo smoke test.

- Settled in grilling round: immediate deletion without deprecation, purely high-level primitive submission interface, no-op migration for `TestProject`, update `AGENTS.md` and tickets.

## Issues

- [01-remove-drawquad-api-in-renderer](issues/01-remove-drawquad-api-in-renderer.md): Remove drawQuad API from Renderer and Verify Pipeline
- [02-synchronize-graphics-architecture-documentation](issues/02-synchronize-graphics-architecture-documentation.md): Synchronize Graphics Architecture Documentation (Blocked by: 01)
