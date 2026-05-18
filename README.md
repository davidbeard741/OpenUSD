# OpenUSD Development Certification Overview

OpenUSD is Pixar’s open-source Universal Scene Description framework for collaborative, scalable 3D scene description and interchange. It composes data from layers at read time without duplication.

### Module 1: Setting the Stage
A UsdStage is the entry point. It represents your fully composed scene. Create or open one with `Usd.Stage.CreateNew` or `Usd.Stage.Open`. The stage manages the scenegraph.

The scenegraph is a hierarchical tree of prims. A prim (UsdPrim) is the core container. It lives at a path such as `/World/Asset/Geom/Mesh`. Prims contain other prims and properties. Traverse with `GetChildren` or `GetAllDescendants`.

Attributes are named, strongly typed properties on prims. Examples include radius (float) on a sphere or points (VtArrayVec3f) on a mesh. They support `timeSamples` for animation. Set values with `Set` or `SetTimeSample`. Relationships point to other prims or properties, such as material bindings.

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

---

# Module 1: Setting the Stage

Welcome to the deep dive on Module 1: Setting the Stage. This foundational module builds your core vocabulary for OpenUSD. Master stages, scenegraphs, prim hierarchies, and attributes now, and every later topic—composition, instancing, pipelines—becomes clearer and easier. We move slowly, explain precisely, and include practical Python examples you can run. Pause after each section to type the code or visualize the hierarchy.

### What Is a Stage?
A UsdStage is the top-level container and the composed view of your 3D scene. It presents the scenegraph—the dynamic hierarchy of objects assembled from one or more layers according to USD’s composition rules.

Think of the stage as the live, resolved result you see in usdview or your DCC. The underlying data lives in layers (files or in-memory). The stage composes them on demand. This design enables non-destructive editing, modularity, and massive scalability because changes in one layer affect the composed result without copying data.

Every stage has a root layer—the anchor file or layer you opened or created. The stage owns and presents composed prims. You can have multiple stages open at once; each maintains its own composed scenegraph while sharing underlying layer data where possible.

**Why it matters**: In production you rarely work with a single file. A stage lets you aggregate geometry, materials, lights, animation, and environment from many reusable assets. It supports collaborative pipelines where different teams edit different layers.

### Creating and Working with Stages (Python)
Import the core module:

```python
from pxr import Usd, Sdf
```

**Create a new stage on disk** (becomes the root layer):

```python
stage: Usd.Stage = Usd.Stage.CreateNew("_assets/my_scene.usda")
print(stage.ExportToString(addSourceFileComment=False))
```

This produces an empty `#usda 1.0` file.

**Open an existing stage**:

```python
stage: Usd.Stage = Usd.Stage.Open("_assets/my_scene.usda")
```

**Create entirely in memory** (great for tests or procedural work):

```python
stage: Usd.Stage = Usd.Stage.CreateInMemory()
stage.DefinePrim("/World", "Xform")
print(stage.ExportToString())
stage.Export("_assets/exported.usda")  # write to disk later
```

**Save changes**:

```python
stage.Save()  # writes edits back to the root layer and any edited sublayers
```

**Access the root layer**:

```python
root_layer: Sdf.Layer = stage.GetRootLayer()
print(root_layer.identifier)
root_layer.subLayerPaths.append("./extra.usdc")  # add sublayers
```

Common pattern: CreateNew for new work, Open for existing assets, CreateInMemory for temporary or generated data, then Export when ready.

**Exam tip**: Know the difference between CreateNew, Open, CreateInMemory, and when to call Save vs Export. Expect questions on root layer behavior.

### The Scenegraph and Prim Hierarchies
The scenegraph is the tree of prims presented by the stage. It is the composed result of all contributing layers and arcs.

Prims are the nodes. They organize everything: geometry, materials, lights, transforms (Xform), cameras, custom data, or pure organizational containers.

Hierarchy uses forward-slash paths, exactly like a filesystem:

- `/World`
- `/World/Environment`
- `/World/Environment/Ground`
- `/World/Characters/Hero/Mesh`

The root prim is usually an Xform or Scope at `/World` or similar. Child prims inherit transforms from parents unless they override.

You traverse the hierarchy with:

- `stage.GetPrimAtPath("/World")`
- `prim.GetChildren()`
- `prim.GetAllChildren()` or filtered traversal with predicates

Prims can be valid or invalid (expired or not found). Always check `prim.IsValid()` or `prim` in boolean context.

**Why hierarchy matters**: Clear naming and structure enable efficient traversal, instancing, collections, and overrides. Poor hierarchy leads to hard-to-debug composition and performance issues later.

### Prims in Depth (UsdPrim)
A prim is the fundamental persistent object on a stage. It has:

- A path (unique within the stage)
- A type name (schema, e.g., “Xform”, “Mesh”, “Material”)
- Properties (attributes and relationships)
- Metadata (documentation, assetInfo, custom data, model kind, etc.)
- Children

Create or define a prim:

```python
world = stage.DefinePrim("/World", "Xform")
print(world.GetTypeName())  # "Xform"
print(world.GetPath())      # /World
```

`DefinePrim` creates the prim if it does not exist and returns it. It also ensures the type is set.

You can also get an existing prim without defining:

```python
prim = stage.GetPrimAtPath("/World")
if prim:
    print(prim)
```

Key prim methods and properties:
- `GetTypeName()`, `SetTypeName()`
- `GetChildren()`, `GetAllChildren()`
- `GetProperties()`, `GetAttributes()`, `GetRelationships()`
- `GetMetadata()`, `SetMetadata()`
- `IsValid()`, `IsActive()`, `IsDefined()`

Prims support metadata such as `documentation`, `assetInfo`, `customData`, and `kind` (model kind).

**Common pattern**: Define an Xform at the root, then define child prims for geometry or groups. Use meaningful paths and types.

### Attributes and Relationships (Properties)
Properties live on prims. There are two kinds:

**Attributes** hold typed data values. Examples: `radius` (float), `points` (VtArrayVec3f), `displayColor` (VtArrayVec3f), `xformOp:transform` (matrix).

Create and set an attribute:

```python
radius_attr = world.CreateAttribute("radius", Sdf.ValueTypeNames.Float)
radius_attr.Set(5.0)
```

Or use schema APIs for safety:

```python
from pxr import UsdGeom
mesh = UsdGeom.Mesh.Define(stage, "/World/MyMesh")
mesh.CreateRadiusAttr(1.0)  # typed and validated
```

Attributes support:
- Single values
- Time samples for animation: `attr.Set(value, time)`
- Value resolution (strongest opinion wins — covered deeply in composition modules)
- Interpolation modes (for some types)

**Relationships** connect prims or properties. Example: material binding or skeleton joints.

```python
rel = prim.CreateRelationship("material:binding")
rel.AddTarget("/World/Materials/Red")
```

**Inspecting properties**:

```python
for attr in prim.GetAttributes():
    print(attr.GetName(), attr.GetTypeName())
```

Value types are strict (float, double, token, string, matrix4d, VtArray<...>, etc.). Use `Sdf.ValueTypeNames` constants.

**Time-varying data intro**: Many attributes support time samples. Set different values at different times; USD handles interpolation. This is foundational for animation.

### File Formats and Layers (Brief but Important)
USD supports:
- `.usda` — human-readable ASCII (great for inspection and source control)
- `.usdc` — binary Crate format (compact, fast)
- `.usd` — crate by default, can be either

Choose based on workflow: `.usda` for readability and diffing, `.usdc` for large binary data or performance.

Layers are the persistent storage. The stage composes a stack of layers. You will learn sublayers, references, and payloads in later modules, but remember every stage starts with its root layer.

### Introspection and Tools
Use Python to explore:

```python
stage = Usd.Stage.Open("scene.usd")
print(stage.GetRootLayer().identifier)
for prim in stage.Traverse():
    print(prim.GetPath(), prim.GetTypeName())
```

**usdview** is your best friend for visual debugging. Open any USD file or stage in usdview. It shows the scenegraph tree, layer stack, prim stack, resolved attributes, and composition arcs. Use it constantly while learning.

Other CLI tools: usdcat (print layer), usddiff, usdtree.

### Practical Exercises for Module 1
1. Create a new stage, define `/World` as Xform, add a child Mesh with points and a radius attribute. Save and open in usdview.
2. Create an in-memory stage, build a small hierarchy, export it.
3. Open an existing asset, inspect its prims and attributes with Python, then add a custom attribute and save.
4. Traverse the entire scenegraph and print every prim path and type.
5. Experiment with time samples on a simple attribute.

Run these repeatedly until the API feels natural.

### Common Pitfalls
- Forgetting to check `prim.IsValid()`
- Using wrong value types or mixing attribute/relationship APIs
- Assuming paths are case-sensitive in certain contexts or forgetting namespace rules
- Editing the wrong layer (always understand which layer receives your opinions)
- Creating deep hierarchies without considering traversal cost later

### Exam Relevance for Module 1
Module 1 concepts appear across the blueprint, especially Data Modeling (13%) and Debugging (11%). You must:
- Distinguish stage vs layer vs prim
- Know how to create/open/save stages
- Navigate prim hierarchies and inspect attributes
- Understand basic property authoring and value types

Later modules build directly on this: composition operates on prims and attributes, visualization uses UsdGeom schemas on prims, etc.

### Key Takeaways – Module 1
- A stage presents the composed scenegraph.
- Prims form the hierarchy; use clear paths and types.
- Attributes store typed data; relationships connect elements.
- CreateNew / Open / CreateInMemory cover most creation needs.
- usdview + Python introspection are essential daily tools.
- Everything is data and non-destructive by design.

You now have a rock-solid foundation in stages, scenegraphs, prims, and attributes. Practice the code examples until they are second nature.

Next we will move to Module 2: Scene Description Blueprints (schemas). Review this module, run the exercises, then reply when you are ready for the next deep dive.

You are building real expertise. Keep going.

End of audio course. Good luck.
