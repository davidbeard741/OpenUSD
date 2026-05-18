OpenUSD is Pixar’s open-source Universal Scene Description framework for collaborative, scalable 3D scene description and interchange. It composes data from layers at read time without duplication.

### Module 1: Setting the Stage
A UsdStage is the entry point. It represents your fully composed scene. Create or open one with Usd.Stage.CreateNew or Usd.Stage.Open. The stage manages the scenegraph.

The scenegraph is a hierarchical tree of prims. A prim (UsdPrim) is the core container. It lives at a path such as /World/Asset/Geom/Mesh. Prims contain other prims and properties. Traverse with GetChildren or GetAllDescendants.

Attributes are named, strongly typed properties on prims. Examples include radius (float) on a sphere or points (VtArrayVec3f) on a mesh. They support timeSamples for animation. Set values with Set or SetTimeSample. Relationships point to other prims or properties, such as material bindings.

Core principle: USD describes data only. No runtime execution. Composition assembles the final view. Metadata lives on prims, layers, and stages for extra info like documentation or asset identifiers.

**Exam note**: Know prim paths, attribute value types (float, token, matrix4d, arrays), and basic stage/prim navigation. Practice creating a simple stage, adding a typed prim and attribute, then saving as .usda.

### Module 2: Scene Description Blueprints
Schemas act as blueprints. They define prim types and their properties. Built-in examples: UsdGeomMesh, UsdShadeMaterial, UsdLuxLight. Apply a schema API for typed, validated authoring—UsdGeom.Mesh(prim) gives safe access to points, normals, and topology.

Multiple-apply schemas (for example UsdShadeMaterialBindingAPI) support several instances on one prim via instance names that extend the namespace.

Value types are precise: scalars, tokens, strings, matrices, VtArrays, and time-sampled data. Clips handle large animation efficiently.

Understand inheritance of schema properties and how to extend or validate data against schemas.

**Exam note**: Link schemas to Visualization (8%). Know common domains like UsdGeom for meshes/cameras and UsdShade for materials.

### Modules 3 & 5: Composition Basics and Creating Composition Arcs (Critical – 23% of exam)
Layers are the files (.usd, .usda, .usdc) or in-memory data containers. A root layer plus its recursive sublayers forms a layer stack. Composition arcs connect and combine data across layers and prims.

Key arcs:
- **Sublayers**: Include opinions from another layer directly into the current stack. Strong and local.
- **References**: Pull an entire prim tree from an external USD file. Weaker than local or inherits.
- **Payloads**: Like references but lazy-loaded. Ideal for heavy geometry. Load on demand for performance.
- **Inherits**: Prim inherits opinions from a prototype (like class inheritance). Stronger than references.
- **Specializes**: Similar to inherits but weaker. Useful for specializing bases without strong overrides.
- **Variants**: Define alternative sets (LOD, materials, configurations). Variant selections also compose.

**LIVERPS strength ordering** (pronounced “liver peas”; note: sometimes shown as LIVRPS; recent updates include relocates as “E”): Local opinions (strongest, including all sublayers in the stack) → Inherits → Variants → References → Payloads → Specializes (weakest).

Local opinions are those authored directly in the current layer stack. When multiple opinions exist for the same property, the strongest wins—this is value resolution. It applies recursively within each composition context.

Use cases: Payload heavy assets, reference shared models, inherit for prototypes/templates, variants for choices, sublayers for local strong overrides.

**Debugging**: Use usdview to inspect layer stack, prim stack, composition arcs, and resolved values. Tools like usddiff, usdcat, and Python queries (stage.GetPrimAtPath, prim.GetAttributes) reveal issues. Common problems: unexpected overrides from weak arcs or missing payloads.

**Exam focus**: Author arcs correctly, predict results, and debug complex LIVERPS scenarios. Know when to choose payload vs reference vs inherit. Practice building layered scenes and tracing value resolution.

### Module 4: Beyond the Basics
Primvars carry per-geometry data (displayColor, UVs, normals) that interpolate across faces or vertices. Author via UsdGeom.Primvar API.

Value resolution handles defaults, fallbacks, and time samples alongside strength order.

Custom properties are any attributes beyond schema definitions—use them carefully for pipeline data.

Model kinds (via UsdModelAPI) classify prims: component (leaf asset), group, assembly. They influence instancing, bounding boxes, and hierarchy traversal. Apply at asset roots.

### Modules 6 & 8: Asset Structure Principles, Content Aggregation, Asset Modularity and Instancing
Design scalable assets with clear interfaces. Root prim carries model kind “component”. Payload geometry and materials. Use variants for LOD or configurations. Separate concerns into parallel layers (geom, materials, animation).

Instancing optimizes duplicates. Native instancing shares prim subtrees efficiently. Point instancing handles massive counts via points + prototypes.

Override instances selectively with references, specializes, or edit targets. Aggregate by referencing many assets into a world or shot layer. Use collections for grouping.

Best practices: consistent asset structure, payload heavy data, instance where possible, clear variant sets. This enables collaborative, performant large scenes.

**Exam note**: Focus on modularity, native vs point instancing, and overriding instanced assets efficiently.

### Module 7: Developing Data Exchange Pipelines
Treat USD as the central interchange format. Build conceptual mappings from source data (FBX, Alembic, etc.) to USD schemas. Write importers, exporters, and transformation scripts.

Create custom importers that read source files and author prims/attributes/relationships. Exporters flatten or convert USD data outward.

Handle proprietary dependencies by flattening (UsdUtils or usdcat) or baking data. Document mappings clearly.

Pipeline tasks include versioning strategies, asset management, and hooks that restructure data on export.

### Blueprint Topic: Pipeline Development (14%)
Design layer structures for artist collaboration (strong edit layers). Manage versioning and documentation. Create exporter hooks that enforce your pipeline’s preferred structure. Handle build configurations and remove proprietary dependencies via flattening. Consider UI/UX integration and Hydra viewports.

### Blueprint Topic: Debugging and Troubleshooting (11%)
Introspect stages and prims to find composition surprises or bad authoring. Spot weak arcs causing overrides or performance issues. Optimize load/render times with payloads, instancing, clips, and proper model kinds. Master usdview and Python inspection.

### Blueprint Topic: Visualization (8%)
Work with UsdGeom (meshes, points, curves, cameras, transforms), UsdShade (materials, shaders, bindings—use PreviewSurface), and UsdLux (lights). Author a complete lit and shaded mesh. These domains appear in nearly every USD workflow.

### Blueprint Topic: Data Modeling (13%)
Understand Sdf (low-level) vs Usd (high-level) structures. Prims, properties (attributes vs relationships), primvars, value types, timeSamples, and built-in schemas. Author correct data types and resolve values properly.

### Blueprint Topic: Customizing USD (6%)
Extend functionality with plugins. Generate custom schemas using usdGenSchema. Create file format plugins. Define custom model kinds and variant fallbacks.

### Blueprint Topic: Content Aggregation (10%)
Build modular reusable components. Leverage native and point instancing. Apply strategies to override instanced assets. Aggregate efficiently for large collaborative scenes.

### Exam Strategy and Final Review
Composition (23%) is the biggest lever—master LIVERPS, arc selection, value resolution, and debugging. Data exchange and pipeline topics reward practical pipeline thinking. Hands-on beats passive reading.

Practice workflow:
1. Create stages and prims with attributes.
2. Build multi-layer compositions with references, payloads, inherits, and variants.
3. Inspect and debug in usdview.
4. Author geometry/materials/lights.
5. Instance assets and aggregate.
6. Write simple importers/exporters or flattening scripts.

Key terms to internalize: prim, attribute, relationship, primvar, layer stack, composition arc, opinion, value resolution, LIVERPS, payload, reference, inherit, specialize, variant, model kind, native instancing, point instancing.

Resources: openusd.org documentation and tutorials, NVIDIA Learn OpenUSD (free, matches your modules exactly), GitHub examples, usdview, and Python/C++ API docs. Practice until you can predict composition results quickly.

You now possess a solid, exam-focused understanding of the full course. Review weak sections, especially composition scenarios. Practice actively. You are prepared to pass the NCP-OpenUSD Development certification.

End of audio course. Good luck.
