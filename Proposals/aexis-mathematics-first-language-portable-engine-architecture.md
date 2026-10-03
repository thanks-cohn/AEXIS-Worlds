# ÆXIS — Mathematics-First, Language-Portable Engine Architecture

**Status:** Strategic architecture proposal  
**Initial implementation home:** JavaScript + TypeScript in the browser  
**Long-term portability targets:** C, C++, WebAssembly, and other future runtimes/languages  
**Primary goal:** Make ÆXIS portable by preserving its mathematical and semantic meaning above any one implementation language, renderer, framework, or platform.

## 1. Vision

ÆXIS should begin where it is strongest today: in the browser, using JavaScript and TypeScript, with direct access to the web platform and a fast path for iteration.

But JavaScript, TypeScript, Three.js, Vite, browser DOM APIs, and today's package ecosystem should not become the permanent definition of ÆXIS.

The long-term goal is to design the engine so that its essential world logic can be transported into a native implementation such as C or C++ without having to rediscover what the engine means.

ÆXIS should become increasingly **mathematics-first**.

The authoritative parts of the engine should be expressible as:

- numbers,
- vectors,
- matrices,
- transforms,
- coordinate spaces,
- topology rules,
- geometric operations,
- deterministic state transitions,
- stable identifiers,
- compact data structures,
- versioned schemas,
- explicit algorithms,
- and testable input/output contracts.

The browser implementation should be the first implementation of those ideas, not the only place those ideas can exist.

The architectural principle is:

> **Preserve the mathematics and world semantics. Replace the implementation language when necessary.**

A future native ÆXIS should not have to imitate JavaScript line by line. It should be able to implement the same mathematical contracts in C, C++, Rust, WebAssembly, or another future language and still be ÆXIS.

## 2. Why this matters

A world engine intended to survive for decades cannot assume that today's preferred language, framework, renderer, browser API, or dependency ecosystem will remain the best execution environment forever.

JavaScript and TypeScript are excellent initial homes because they provide:

- immediate browser reach,
- low deployment friction,
- fast iteration,
- strong tooling,
- easy agent-driven editing,
- and an enormous ecosystem.

They are therefore not temporary mistakes to be escaped. They are the correct place to prove the architecture.

However, future versions of ÆXIS may need:

- tighter memory control,
- deterministic performance,
- lower-level threading,
- native filesystem and device integration,
- console or embedded targets,
- long-running simulation,
- custom allocators,
- optimized geometry kernels,
- native rendering APIs,
- specialized hardware,
- or execution environments that do not host a browser.

The engine should be ready for that future before it arrives.

## 3. Portability does not mean rewriting everything today

This proposal does **not** introduce a C, C++, or WebAssembly requirement for the current browser version.

The existing browser-first product contract remains valid.

The current implementation should continue to optimize for:

- JavaScript and TypeScript,
- ordinary browser CPU/GPU execution,
- low-end hardware,
- framework-light operation,
- and fast creator iteration.

The portability goal affects **how new core systems are designed**, not which language must be used today.

In particular, this proposal does not require:

- replacing Three.js now,
- adding a native build dependency now,
- compiling the current project to WebAssembly now,
- rewriting the browser viewer in C++,
- or forcing browser contributors to understand native toolchains.

Instead:

> **We make today's JavaScript/TypeScript architecture easy to reproduce faithfully elsewhere tomorrow.**

## 4. The architectural split

ÆXIS should gradually separate into conceptual layers.

```text
                  ÆXIS WORLD MEANING
                         │
              mathematics + schemas
                         │
          deterministic engine contracts
                         │
        ┌────────────────┼────────────────┐
        │                │                │
     Browser          Native C/C++      Future
     JS / TS             runtime         runtime
        │                │                │
     Three.js        native renderer     other
     Web APIs        native platform     adapters
```

The upper layers should change much less frequently than the lower layers.

### Layer A — Mathematical core

This is the most portable layer.

Examples include:

- vector and matrix conventions,
- units,
- axis conventions,
- coordinate transformations,
- wrapping,
- spatial indexing,
- terrain sampling,
- elevation math,
- interpolation,
- deterministic procedural generation,
- collision primitives,
- bounds,
- intersections,
- projection math,
- camera transforms,
- topology,
- world-space relationships,
- and numeric tolerances.

These algorithms should be describable independently of JavaScript objects or browser APIs.

### Layer B — Canonical semantic world model

This describes what the world *is*.

Examples include:

- spaces,
- entities,
- components,
- terrain,
- objects,
- cameras,
- lights,
- actions,
- events,
- relationships,
- identities,
- assets,
- constraints,
- revisions,
- provenance,
- and compatibility information.

The canonical model should be versioned and serializable.

A C++ runtime and a browser runtime should be able to load equivalent world meaning even if they use different internal memory layouts.

### Layer C — Deterministic engine operations

These are language-neutral behavioral contracts.

Examples:

- query terrain,
- sample height,
- place entity,
- transform object,
- test collision,
- enter region,
- apply patch,
- validate world,
- advance deterministic simulation,
- generate procedural geometry,
- resolve camera state,
- serialize state,
- and verify compatibility.

Each operation should have explicit inputs, outputs, failure conditions, and tests.

### Layer D — Runtime adapters

This layer handles platform differences.

Browser examples:

- DOM,
- IndexedDB,
- File APIs,
- pointer/keyboard events,
- WebGL/WebGPU,
- Three.js,
- browser audio.

Native examples:

- filesystem APIs,
- SDL or native window/input,
- OpenGL/Vulkan/Direct3D/Metal,
- native audio,
- threads,
- memory-mapped assets,
- operating-system integration.

Runtime adapters may differ radically without changing the meaning of the world.

### Layer E — Creator interfaces

Editors, agent tools, command surfaces, debug UIs, browser pages, and native desktop applications sit above the same underlying contracts.

They should not become the only definition of engine behavior.

## 5. Mathematics should replace accidental implementation truth

Whenever practical, authoritative behavior should be represented by explicit mathematics rather than by side effects of the renderer.

Examples:

Bad architectural truth:

> The player is standing on the mountain because the rendered mesh appears below the ship.

Better architectural truth:

> The player's world-space position is tested against a canonical terrain/elevation function and collision contract; the renderer visualizes the result.

Bad architectural truth:

> This island wraps because the Three.js scene repositions meshes.

Better architectural truth:

> The world topology defines a wrap function. The renderer chooses how to visualize that topology.

Bad architectural truth:

> This camera angle exists because a browser object happens to contain these internal fields.

Better architectural truth:

> Camera pose is represented by explicit position, orientation/projection parameters, conventions, and deterministic transform math.

The engine should increasingly make the latter form canonical.

## 6. Cross-language equivalence

The goal is not source-code similarity.

A JavaScript implementation and C++ implementation may look entirely different internally.

What should match is observable engine meaning.

For portable engine functions, we should be able to define shared cases such as:

```text
input:
  world position = (-1, 20)
  map size = 500

operation:
  wrap

expected:
  (499, 20)
```

or:

```text
input:
  local transform
  parent transform

operation:
  compose world transform

expected:
  canonical matrix / position / orientation
```

or:

```text
input:
  seed
  generator parameters

operation:
  procedural terrain generation

expected:
  canonical result hash / sampled reference values
```

A browser runtime and a future native runtime should both pass the same semantic fixtures within documented numeric tolerances.

This is stronger than merely having similar-looking implementations.

## 7. Portable numeric contracts

To make future translation realistic, ÆXIS should become increasingly explicit about numeric behavior.

Document:

- coordinate handedness,
- axis meanings,
- world units,
- angle units,
- matrix layout when serialized,
- transform order,
- floating-point assumptions,
- precision requirements,
- tolerances,
- integer ranges,
- seed behavior,
- overflow behavior,
- serialization precision,
- and whether operations require exact determinism or tolerance-based equivalence.

Do not rely on JavaScript's `Number` behavior as an undocumented engine specification.

Where numeric differences between languages or hardware are unavoidable, define acceptable tolerances and test them.

## 8. Data should cross language boundaries cleanly

Portable state should favor explicit, versioned representations.

Good long-lived interchange candidates include:

- UTF-8 JSON for human-readable project/state interchange,
- compact binary formats later for high-volume runtime data,
- documented arrays/structures,
- explicit version fields,
- stable IDs,
- little reliance on language-specific object graphs,
- and forward-compatible extension fields.

Avoid making critical persistent data depend on:

- JavaScript prototypes,
- closures,
- DOM nodes,
- Three.js class instances,
- engine-internal pointer identities,
- or unserializable callbacks.

Those may exist inside an adapter, but they should not be the canonical world format.

## 9. Renderer independence

Three.js is currently useful and should remain useful.

But ÆXIS should avoid allowing Three.js to become synonymous with ÆXIS.

The engine should distinguish:

```text
canonical world state
        ↓
render description / render-facing data
        ↓
renderer adapter
        ↓
Three.js today
native renderer tomorrow
```

Geometry generation, object identity, world transforms, semantic layers, collision meaning, camera meaning, LOD policy, and visibility decisions should be separable from Three.js classes where practical.

A future C++ renderer may use completely different GPU abstractions while consuming equivalent engine state.

## 10. Browser-first, not browser-bound

The browser remains the reference environment for early ÆXIS development.

That gives us an important advantage: we can refine the model rapidly, test it cheaply, expose it to agents, and make the public APIs understandable before committing to native complexity.

The intended progression is:

```text
JavaScript / TypeScript browser implementation
                ↓
explicit mathematical contracts
                ↓
portable schemas + tests
                ↓
language-neutral engine interfaces
                ↓
selected native kernels where valuable
                ↓
C / C++ reference or production runtime
                ↓
other runtimes without architectural reinvention
```

Native work should emerge because a capability benefits from it, not because native code is assumed to be inherently superior.

## 11. A future native core

If and when native ÆXIS is justified, a likely architecture is a small portable engine core with a deliberately narrow boundary.

For example:

```text
aexis_world_load(...)
aexis_world_query(...)
aexis_world_step(...)
aexis_world_apply(...)
aexis_world_serialize(...)
```

This is illustrative, not a proposed ABI today.

The important idea is that a future C-compatible boundary could expose stable engine semantics to:

- C,
- C++,
- WebAssembly,
- Rust,
- Python,
- JavaScript,
- desktop applications,
- and other languages through bindings.

If an ABI is eventually introduced, it should be small, versioned, data-oriented, and much more stable than internal C++ class layouts.

## 12. Data-oriented design where it helps portability

Portable architecture should prefer data and pure functions for core logic where reasonable.

Examples:

- arrays of terrain values,
- stable component records,
- explicit transforms,
- immutable or transaction-based operation descriptions,
- deterministic query functions,
- and bounded state transitions.

This does not forbid classes, objects, closures, ECS designs, or idiomatic TypeScript.

It means the **meaning underneath them** should remain recoverable as data and mathematics.

This makes native implementations easier, testing clearer, serialization safer, agents more capable, and long-term migration less fragile.

## 13. Procedural systems should be reconstructible

A mathematics-first engine is especially valuable for procedural content.

A procedural world feature should ideally be reconstructible from:

- algorithm/version,
- parameters,
- seed,
- referenced source assets,
- coordinate conventions,
- and explicit generation rules.

Do not make the only surviving representation a giant baked result when a compact source description can preserve how it was made.

Baking remains useful for performance and compatibility. Preserve the procedural source separately when practical.

## 14. APIs should describe engine concepts, not JavaScript conveniences

Public engine interfaces should avoid accidentally freezing JavaScript-specific patterns into the conceptual architecture.

For example, the durable concept may be:

> query region by bounded coordinates and revision

rather than:

> invoke this particular object method with a browser callback.

Bindings may remain idiomatic:

```js
const result = world.inspectRegion(bounds);
```

A future C API might instead be:

```c
aexis_result result = aexis_world_inspect_region(world, &bounds, &out);
```

They can still implement the same underlying operation.

## 15. Conformance should eventually matter more than implementation lineage

As the engine matures, portable core behavior should acquire reference fixtures and golden results.

Potential conformance areas:

- coordinate conversion,
- wrap/topology behavior,
- elevation sampling,
- geometry primitives,
- transform composition,
- procedural generation,
- collision primitives,
- world serialization,
- revision handling,
- deterministic plan/apply operations,
- stable error categories,
- and canonical identity preservation.

The question should become:

> Does this implementation behave like ÆXIS?

not:

> Was this implementation translated from the JavaScript source?

That allows future engines to be rewritten cleanly without losing compatibility.

## 16. What should remain platform-specific

Not everything should be forced into a fake universal abstraction.

Some systems are naturally platform adapters:

- browser UI,
- native windows,
- GPU command APIs,
- browser security permissions,
- filesystem dialogs,
- extension messaging,
- OS input devices,
- video/audio backends,
- network stacks,
- and deployment packaging.

The portability goal is to make those replaceable shells around a durable engine core.

Portability should not create a giant lowest-common-denominator platform API.

## 17. Relationship to current ÆXIS work

This proposal strengthens principles already present in the repository:

- authoritative world data remains independent of rendering,
- coordinate and topology semantics are explicit,
- numeric elevation is canonical,
- world IDs and semantic object identity survive render changes,
- procedural geometry is increasingly mathematical,
- APIs are moving toward stable structured operations,
- Tiled, Blender/glTF, browser rendering, and future runtimes are adapters over shared meaning.

The semantic geometry proposal's mathematical/component model is especially aligned with this direction.

The optional Qt/C++ desktop shell is also compatible with this vision, but it should remain an adapter/host experiment rather than silently becoming the canonical engine.

## 18. Near-term design rules

Without requiring a native implementation now, future ÆXIS changes should increasingly follow these rules:

1. Keep authoritative world and simulation state outside renderer objects.
2. Prefer pure mathematical helpers for reusable engine logic.
3. Document coordinate spaces, units, precision, and topology.
4. Keep serialization versioned and language-neutral.
5. Separate renderer/platform adapters from canonical engine state.
6. Avoid persistent formats that depend on JavaScript-specific runtime behavior.
7. Give procedural systems explicit parameters and seeds.
8. Add deterministic or tolerance-based tests for important math.
9. Preserve stable IDs across load/save/import/export.
10. Define operations by semantic input/output contracts.
11. Keep browser-first performance and simplicity.
12. Do not require C/C++/WASM until a real implementation phase justifies them.

## 19. Suggested future milestones

### Phase 1 — Math inventory

Identify current engine logic that is already portable:

- wrap functions,
- coordinate transforms,
- height sampling,
- bounds,
- collision primitives,
- terrain semantics,
- camera math,
- world transforms,
- deterministic generation.

Document their conventions and add focused tests.

### Phase 2 — Portable core boundaries

Move reusable engine math and canonical world operations away from browser/renderer dependencies where practical.

Do this incrementally, not through a risky rewrite.

### Phase 3 — Cross-language fixtures

Create a small language-neutral fixture set for critical engine behavior.

JavaScript/TypeScript remains the reference implementation.

### Phase 4 — Native feasibility proof

Implement a tiny selected subset in C or C++, such as:

- world coordinate/wrap math,
- transform math,
- terrain/elevation query,
- or one procedural geometry primitive.

Run it against the same fixtures.

Do not attempt to port the whole renderer.

### Phase 5 — Stable native boundary

Only after semantics are proven, evaluate a small C-compatible API/ABI suitable for C++, WebAssembly, and foreign-language bindings.

### Phase 6 — Native runtime

If future product needs justify it, build a native execution/runtime layer while preserving browser compatibility and shared world formats.

The browser and native runtimes should then be peers over one engine meaning, not unrelated forks.

## 20. Acceptance test for portability

A future implementation should be able to answer:

> Can a team or capable agent rebuild the ÆXIS core in another language using the specifications, schemas, tests, mathematical contracts, and project data without reverse-engineering JavaScript implementation accidents?

If the answer is yes, portability is working.

A stronger future proof would be:

> Load the same canonical world in the browser reference runtime and a native runtime, run equivalent portable operations, and produce equivalent semantic results within documented numeric tolerances.

## 21. Long-term goal

The deepest goal is not merely to "port ÆXIS to C++."

The goal is to make ÆXIS **portable by design**.

JavaScript and TypeScript should be the first home of ÆXIS because they let us create quickly, expose the engine to the browser, and iterate with agents and creators.

But the design should become strong enough that decades from now we can move performance-critical or entire runtime layers into C, C++, WebAssembly, or technologies that do not yet exist without losing the engine's identity.

The project should carry its own mathematics, contracts, data, and tests strongly enough that implementation language becomes replaceable infrastructure.

> **The language is where ÆXIS runs. The mathematics and world model are what ÆXIS is.**
