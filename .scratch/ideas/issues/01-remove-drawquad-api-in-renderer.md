# Remove drawQuad API in Renderer

Status: resolved
Type: grilling

## Raw Proposal

In Rederer.cpp remove drawQuad API

## Context

- **Source:** Standalone (`OrcEngine/source/Orc/Graphics/Renderer.cpp`)
- **Captured:** 2026-10-15

## Frontier Seeds

- ❓ What primitive submission mechanism replaces `drawQuad` for callers (e.g., shapes, text, sprites)?
- ❓ How should existing call sites across `TestProject` demo layers be migrated?

## Answer

Grilling interview completed and synthesized into full spec at `.scratch/remove-drawquad-api-in-renderer/spec.md`.
All four `drawQuad` overloads will be deleted; callers use `Rectangle` or `Sprite` for quad rendering; zero call sites exist to migrate. Implementation tracked in `.scratch/remove-drawquad-api-in-renderer/issues/01-remove-drawquad-api-in-renderer.md`.

