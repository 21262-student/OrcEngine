# Remove drawQuad API in Renderer

Status: resolved
Role: spec

## Problem Statement

Following the consolidation of 2D rendering into a unified dynamic quad batcher (ADR 0001) and canonical vertex layout (ADR 0002), 2D rendering in OrcEngine is standardized around high-level `Texturable` primitives (`Rectangle`, `Sprite`, `Circle`), `Text`, and line rendering. However, `Renderer` still retains four legacy `drawQuad` overloads that take raw coordinates, sizes, transforms, textures, and tint colors directly.

This creates several architectural problems:
1. **Dual Submission Abstractions**: Callers face two divergent approaches to submit 2D quad geometry—either instantiate a `Texturable` shape (`Rectangle` or `Sprite`) or invoke a raw immediate `drawQuad` method.
2. **Incomplete Transform & Geometry Pipeline**: The `drawQuad` implementation bypasses `Transformable`'s origin, rotation, and scale pipeline, ignores secondary attributes and border parameters, and performs ad-hoc vertex generation directly inside `Renderer`.
3. **Dead API Clutter**: Zero call sites exist across `OrcEngine` and `TestProject`, yet `drawQuad` continues to clutter the public header and implementation of `Renderer`.

## Solution

Eliminate the obsolete `drawQuad` API completely from `Renderer`. Establish high-level `Texturable` primitives (`Rectangle`, `Sprite`, `Circle`) and `Text` as the sole public mechanisms for submitting quad geometry to the dynamic quad batcher. Direct callers requiring transient or immediate quads to use `Rectangle` or `Sprite` constructors, preserving consistent transform hierarchies, UV coordinate calculation, and vertex caching. Ensure low-level quad submission (`submitQuad`) remains private within `Renderer`.

## User Stories

1. As an engine developer, I want `Renderer` to present a single, cohesive 2D drawing interface centered on high-level `Texturable` primitives, so that I do not have to choose between dual abstractions for quad submission.
2. As an engine developer, I want all obsolete `drawQuad` overloads removed from `Renderer`, so that dead code is eliminated from the public engine API.
3. As a graphics programmer, I want all quad geometry submitted through `Rectangle` or `Sprite`, so that transformations, origins, rotation, and texture coordinates are consistently calculated via `Transformable` and `Texturable`.
4. As an engine user, I want to draw transient or immediate quads by constructing lightweight `Rectangle` or `Sprite` instances on the stack, so that I retain full immediate-drawing ergonomics without bypassing the engine's transform and vertex pipeline.
5. As an engine maintainer, I want `drawQuad` deleted immediately without deprecated stubs or aliases, so that downstream code and new contributors are not misled by transitional clutter.
6. As a rendering system maintainer, I want low-level quad submission (`submitQuad`) to remain private to `Renderer`, so that external callers cannot bypass batch boundaries or texture slot allocation invariants.
7. As an engine architect, I want `GLOSSARY.md` to remain free of implementation-level method names, so that the domain model stays focused on ubiquitous language.
8. As an engine maintainer, I want `AGENTS.md` subsystem documentation kept strictly in sync with the clean `Renderer` interface, so that future agents and developers have an accurate reference for rendering architecture.
9. As a demo user, I want existing `TestProject` demo layers (`RectanglesTest`, `SpritesTest`, `CirclesTest`, `CameraTest`) to continue rendering identically without code modifications, so that 0 regressions are introduced.
10. As a QA / build engineer, I want the engine and demo projects to compile cleanly with zero warnings or errors after removing `drawQuad`, so that build health is verified across configurations.
11. As a repository maintainer, I want code formatting and naming convention checks to pass with zero errors, ensuring full compliance with the repository's coding standards.

## Implementation Decisions

- **Renderer Public Interface Cleanup**: Remove all four overloaded `drawQuad` functions (taking position/size/color, position/size/texture/tint, transform/size/color, and transform/size/texture/tint) from the public interface of the `Renderer` module. No stubs, aliases, or deprecation warnings are retained.
- **Sole Quad Submission Mechanism via Texturable**: Callers must submit quad geometry exclusively through concrete `Texturable` primitive subclasses (`Rectangle` and `Sprite`). For immediate or unmanaged quad rendering, callers instantiate temporary `Rectangle` or `Sprite` objects directly at the draw call site.
- **Encapsulation of Batch Buffer Operations**: Keep low-level vertex array submission (`submitQuad`) strictly private within `Renderer`. External modules must not have direct access to stream raw vertices into the dynamic batch buffer.
- **Preservation of Existing Primitives**: Maintain all existing shape drawing overloads on `Renderer` (`draw(const Sprite&)`, `draw(const Rectangle&)`, `draw(const Circle&)`, `draw(const Text&)`, and `drawLine`) without alterations to their signatures or behaviors.
- **Zero Call Site Migrations**: Because zero call sites of `drawQuad` exist across the codebase, no call site refactoring or migration code is needed.
- **Documentation and Ticket Synchronization**: Synchronize the graphics subsystem documentation in the repository's core guidance (`AGENTS.md`) and resolve the originating issue ticket in the local tracker.

## Testing Decisions

- **Test Seam**: The highest and only required seam is the `TestProject` demo integration harness together with the project's build and compilation pipeline.
- **Good Test Criteria**: Tests must verify observable system behavior (clean compilation of the entire engine and demo suite, clean execution of the demo suite without rendering anomalies or errors in logs) rather than checking whether private methods exist.
- **Modules Tested**: `OrcEngine` (graphics module, specifically `Renderer`, `Rectangle`, `Sprite`) and `TestProject` (all demo layers: `RectanglesTest`, `SpritesTest`, `CirclesTest`, `CameraTest`).
- **Prior Art**: The demo execution smoke test (`.\scripts\run-demo.ps1 -TimeoutSeconds 5 -FailOnErrorLog`) and compiler suite (`.\scripts\build.ps1 -Preset windows-debug`) established in ADR 0001 and ADR 0002.
- **Conventions Enforcement**: `.\scripts\lint-conventions.ps1 -ChangedOnly` to verify that modified files adhere to repository styling rules (tabs, `#pragma once`, naming conventions).

## Out of Scope

- Introducing a polymorphic `draw(const Texturable&)` interface.
- Adding custom immediate-mode helper functions or macros for quad rendering.
- Modifying `submitQuad`, dynamic batch limits, or composite shader stage code.
- Adding new demo layers or GUI tools to `TestProject`.

## Further Notes

- Originates from `.scratch/ideas/issues/01-remove-drawquad-api-in-renderer.md`.
- Relies on architecture established in `docs/adr/0001-collapse-2d-render-submission-pipeline.md` and `docs/adr/0002-unify-vertex-and-untemplate-texturable.md`.
