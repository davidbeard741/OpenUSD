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

This foundational module builds your core vocabulary for OpenUSD. Master stages, scenegraphs, prim hierarchies, and attributes now, and every later topic—composition, instancing, pipelines—becomes clearer and easier. We move slowly, explain precisely, and include practical Python examples you can run. Pause after each section to type the code or visualize the hierarchy.

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

# Module 2: Scene Description Blueprints – In-Depth Audio Lesson

Welcome to the deep dive on Module 2: Scene Description Blueprints. This module teaches you how USD schemas act as blueprints that define and structure every element in your 3D scenes. Schemas bring meaning, consistency, and interoperability to prims and their properties. Mastering them makes authoring correct, readable, and interchangeable USD data much easier.

We will cover what schemas are, the two main categories (IsA and API), concrete vs abstract schemas, how to use them in Python, major built-in domains like UsdGeom and UsdShade, and practical authoring patterns. Expect code examples you can run immediately.

### What Are Schemas?
Schemas are data models and optional APIs that define what a prim *is* and what properties it should have. They answer: “What kind of thing is this prim? What attributes and relationships does it support? What are the rules and defaults?”

Instead of authoring raw attributes with arbitrary names, you use schema classes. This gives you:
- Typed, validated properties
- Consistent naming and value types
- Default values where defined
- Better interchange between tools and pipelines
- Clear documentation of intent

Schemas live in the USD core and in extension modules. They are primarily data models. Behavior (rendering, simulation, etc.) is often provided by the consuming application or other subsystems.

There are two fundamental kinds of schemas in OpenUSD: **IsA schemas** (also called Typed schemas) and **API schemas**.

### IsA Schemas (Typed Schemas)
An IsA schema tells a prim exactly what it *is*. Every prim can have only one IsA schema because it sets the prim’s `typeName` metadata.

IsA schemas derive from `UsdTyped`. They come in two flavors:

**Concrete (instantiable) schemas** — You can create prims of this exact type. Examples:
- `UsdGeomSphere`
- `UsdGeomMesh`
- `UsdGeomXform`
- `UsdLuxDiskLight`
- `UsdShadeMaterial`

**Abstract (non-concrete) schemas** — These serve as base classes. They define common properties but cannot be instantiated directly. Examples:
- `UsdGeomImageable` (base for anything visible)
- `UsdGeomPointBased` (base for meshes, curves, points)
- `UsdGeomXformable` (base for anything transformable)

When you define a concrete prim, it inherits all properties from its abstract base schemas.

**Python example – UsdGeomSphere**:

```python
from pxr import Usd, UsdGeom

stage = Usd.Stage.CreateNew("blueprints.usda")

# Define a sphere prim using the schema
sphere = UsdGeom.Sphere.Define(stage, "/World/MySphere")

# The schema gives you typed access to attributes
sphere.GetRadiusAttr().Set(2.5)           # double with default in schema
sphere.GetDisplayColorAttr().Set([(1, 0, 0)])  # from Imageable base
```

The `Define()` method ensures the prim exists with the correct typeName and returns the schema object for convenient authoring.

### API Schemas
API schemas do **not** set a prim’s typeName. They add extra properties, metadata, or behavior annotations to an already-typed prim. You apply them via the `apiSchemas` metadata list on the prim.

API schemas end with “API” in their class name (e.g., `UsdPhysicsRigidBodyAPI`, `UsdGeomPrimvarsAPI`).

They fall into categories:

**Applied vs Non-applied**:
- Applied API schemas are recorded in `apiSchemas` metadata and can be queried with `prim.HasAPI()`.
- Non-applied schemas mainly provide an interface to metadata or core functionality (examples: `UsdModelAPI`, `UsdClipsAPI`).

**Single-apply vs Multiple-apply**:
- Single-apply: Can be applied only once per prim.
- Multiple-apply: Can be applied many times with different *instance names* (e.g., multiple collections or material bindings). Properties are namespaced with the instance name.

**Python example – Applying an API schema**:

```python
from pxr import Usd, UsdPhysics, UsdGeom

cube = UsdGeom.Cube.Define(stage, "/World/Cube")

# Apply the RigidBodyAPI (single-apply applied API schema)
rb_api = UsdPhysics.RigidBodyAPI.Apply(cube.GetPrim())

# Now use the API to author physics-specific attributes
rb_api.CreateVelocityAttr().Set((0, 10, 0))
rb_api.GetKinematicEnabledAttr().Set(True)
```

You can check application later:

```python
if cube.GetPrim().HasAPI(UsdPhysics.RigidBodyAPI):
    print("Rigid body API is applied")
```

### Major Built-in Schema Domains
**UsdGeom** — The most used domain for geometry and transforms.
- Core: `UsdGeomXformable`, `UsdGeomImageable`, `UsdGeomPointBased`
- Concrete: `UsdGeomMesh`, `UsdGeomSphere`, `UsdGeomCube`, `UsdGeomCylinder`, `UsdGeomBasisCurves`, `UsdGeomPoints`, `UsdGeomCamera`, `UsdGeomXform`, `UsdGeomScope`
- Also defines primvars via `UsdGeomPrimvarsAPI`

**UsdShade** — Materials and shading networks.
- `UsdShadeMaterial`, `UsdShadeShader`, `UsdShadeNodeGraph`
- Connectable API for shading graphs
- Material binding via `UsdShadeMaterialBindingAPI` (multiple-apply)

**UsdLux** — Lights.
- `UsdLuxSphereLight`, `UsdLuxDiskLight`, `UsdLuxRectLight`, `UsdLuxDistantLight`, etc.
- Combined with `UsdLuxLightAPI` for common light properties (intensity, exposure, color, etc.)

**Other important domains**:
- `UsdPhysics` — Rigid bodies, colliders, joints
- `UsdSkel` — Skeletal animation and skinning
- `UsdVol` — Volumes
- `UsdRender` — Render settings and products

These domains work together. A mesh prim can have UsdGeomMesh (IsA), UsdShadeMaterialBindingAPI applied, and UsdGeomPrimvarsAPI for extra data.

### Authoring Best Practices with Schemas
1. **Prefer schema Define() and typed methods** over raw `CreateAttribute()`. You get validation, correct value types, and future-proofing.
2. Use schema objects for both creation and later editing.
3. For API schemas, always use the `Apply()` method on the prim.
4. Query schema attribute names with `GetSchemaAttributeNames()` when needed.
5. Combine schemas: a prim has one IsA type + zero or more applied API schemas.
6. Respect value types and defaults defined in the schema.

**Example – Building a simple lit mesh**:

```python
from pxr import Usd, UsdGeom, UsdShade, UsdLux

mesh = UsdGeom.Mesh.Define(stage, "/World/Geom/MyMesh")
# ... author points, topology via mesh.CreatePointsAttr() etc.

# Apply material binding (multiple-apply)
binding_api = UsdShade.MaterialBindingAPI.Apply(mesh.GetPrim())
binding_api.Bind(material_prim)

# Add a light
light = UsdLux.SphereLight.Define(stage, "/World/Lights/KeyLight")
light.GetIntensityAttr().Set(50000)
```

### Introspection with SchemaRegistry
You can discover registered schemas at runtime:

```python
from pxr import Usd

info = Usd.SchemaRegistry.FindSchemaInfo("UsdGeomSphere")
print(info)
type_name = Usd.SchemaRegistry.GetSchemaTypeName(UsdGeom.Sphere)
```

This is useful for tools, validators, and dynamic pipelines.

### Connection to Exam Blueprint and Later Modules
Module 2 directly supports:
- **Visualization (8%)** — Heavy use of UsdGeom, UsdShade, UsdLux
- **Data Modeling (13%)** — Understanding typed prims, properties, and value types
- **Customizing USD (6%)** — Creating your own schemas builds on this foundation

Schemas make composition, instancing, and data exchange reliable because everyone agrees on the meaning of a “Mesh” or a “Material”.

### Practical Exercises
1. Create a stage and define several UsdGeom prims (Sphere, Cube, Mesh). Author their core attributes using schema methods.
2. Apply `UsdGeomPrimvarsAPI` or `UsdShadeMaterialBindingAPI` to a prim and author the corresponding properties.
3. Define a light using UsdLux and combine it with LightAPI properties.
4. Write code that checks `HasAPI` on several prims and prints their applied schemas.
5. Open a complex asset in usdview and inspect the typeName and apiSchemas metadata on various prims.

Run these until schema usage feels natural.

### Common Pitfalls
- Trying to apply more than one IsA schema to a prim (impossible — only one typeName).
- Forgetting to call `Apply()` on API schemas and authoring attributes manually (loses the apiSchemas record).
- Using raw attribute names instead of schema methods (fragile and less readable).
- Assuming all properties on a prim come from its IsA schema (many come from applied API schemas).
- Ignoring abstract base schemas when exploring inheritance.

### Key Takeaways – Module 2
- Schemas are blueprints that give prims meaning and structure.
- **IsA (Typed) schemas** set the prim’s type via `typeName` and come in concrete and abstract forms.
- **API schemas** add capabilities without changing the prim type; use `Apply()` and check with `HasAPI()`.
- Major domains: UsdGeom (geometry & transforms), UsdShade (materials), UsdLux (lights), plus Physics, Skel, etc.
- Always prefer schema classes for authoring — they enforce correctness and improve interchange.
- Schemas are the foundation for clean, professional USD pipelines.

You now understand how to structure scenes properly using blueprints. This knowledge will make composition (next major topic) and visualization far more intuitive.

Review the examples, complete the exercises, and practice mixing IsA and API schemas on the same prims. When you feel solid, reply and we will tackle the next module or a specific deep-dive topic.

# Modules 3 & 5: Composition Basics and Creating Composition Arcs

Welcome to the combined deep dive on Modules 3 and 5. These modules cover **Composition** — the single most important topic on the exam at **23%**. Mastering layers, composition arcs, and especially **LIVERPS** strength ordering will determine a large part of your score. Composition is how USD assembles rich, modular, collaborative scenes from many pieces without destructive edits.

We will move carefully: first the fundamentals of layers and composition, then every major arc, the strength ordering rules, value resolution, practical authoring, debugging, and when to choose each technique. Code examples are included. Pause often and visualize or test the concepts.

### Why Composition Matters
USD’s power comes from its ability to compose many layers into one coherent stage. Instead of one giant monolithic file, you build reusable assets and combine them with precise control over overrides. This enables:
- Non-destructive editing
- Parallel work by many artists
- Efficient large scenes via instancing and payloads
- Variant choices and overrides at multiple levels

Composition happens when you open a stage. The stage follows the composition recipe in the root layer and recursively pulls in other data according to the arcs you authored.

### Layers and the Layer Stack (Composition Basics)
A **layer** is a container of scene description (a `.usd`, `.usda`, or `.usdc` file, or an in-memory layer).

A **layer stack** is the ordered list of layers that contribute to a particular composition context. It starts with a root layer and includes all its recursive sublayers.

When you open a stage with `Usd.Stage.Open("root.usd")`, the stage builds the composed scenegraph by following all composition arcs and resolving opinions according to strength rules.

**Sublayers** are the simplest arc. They include another layer’s opinions directly into the current layer stack. Sublayer opinions are considered **local** (strong).

```python
from pxr import Usd, Sdf

stage = Usd.Stage.CreateNew("root.usda")
root_layer = stage.GetRootLayer()
root_layer.subLayerPaths.append("./base.usd")
root_layer.subLayerPaths.append("./overrides.usd")
```

Sublayers are processed in order; later sublayers can override earlier ones within the same stack.

### The Composition Arcs
Composition arcs are the “operators” that connect layers and prims. The main arcs are:

1. **Sublayers** — Include entire layers into the current stack (local/strong).
2. **References** — Pull in an external prim (and its subtree) from another USD file or layer. Creates a new composition context.
3. **Payloads** — Like references, but **lazy-loaded**. The target is not loaded until explicitly requested. Essential for performance with heavy assets.
4. **Inherits** — A prim inherits opinions from a prototype prim (similar to class inheritance). Stronger than references.
5. **Specializes** — Similar to inherits but **weaker**. Useful for specializing a base without strong overrides.
6. **Variants** — Define named alternatives (e.g., LOD levels, material variants). You select which variant to use; selections also compose.

Each arc (except sublayers) can remap the target prim name under the source prim.

### LIVERPS Strength Ordering (The Core Rule)
When multiple opinions exist for the same property on a prim, USD must decide which one wins. This is governed by **LIVERPS** (sometimes still called LIVRPS; recent versions added Relocates as “E”).

**Strength order (strongest to weakest):**
- **L**ocal opinions (including all sublayers in the current stack)
- **I**nherits
- **V**ariants (variant sets)
- **R**eferences
- **P**ayloads
- **S**pecializes (weakest)

**Local** means any opinion authored directly in the current layer stack (root layer + its sublayers). These are the strongest.

The ordering is recursive. Each new reference or payload creates its own layer stack and applies LIVERPS inside that context.

**Value resolution** = the process of walking the ordered opinions and taking the strongest one that exists for a given property (or metadata).

**Key mental model**: Stronger arcs win. Local edits override almost everything. Specializes are the weakest and are easily overridden.

### When and Why to Use Each Arc

**Sublayers** — Use for strong, local overrides or to organize your own work into multiple files while keeping everything in one composition context. Good for shot-level overrides.

**References** — Use to bring in shared assets (models, props). The referenced content becomes part of the scenegraph under the referencing prim. Good balance of reuse and control.

**Payloads** — Use for heavy geometry or large assets you don’t always need loaded. The stage can open quickly; you load payloads on demand with `stage.Load()` / `Unload()`. Critical for performance in large worlds or shots.

**Inherits** — Use when you want prototype-like behavior. Changes to the prototype flow to all inheriting prims unless locally overridden. Stronger than references, so good for shared templates.

**Specializes** — Use when you want a weaker “specialization” relationship. The specialized prim can be overridden more easily by stronger arcs. Often used in asset pipelines for variations that should not fight stronger references.

**Variants** — Use for discrete choices (high/medium/low LOD, red/blue/green materials, different configurations). Define a variant set on a prim, then select the active variant. Variants are very powerful for flexible assets.

### Practical Authoring Examples

**Reference an asset:**

```python
asset = stage.DefinePrim("/World/Props/Chair")
asset.GetReferences().AddReference("./assets/chair.usd")
```

**Payload a heavy model:**

```python
model = stage.DefinePrim("/World/Environment/Building")
model.GetPayloads().AddPayload("./heavy_building.usd")
# Later: stage.Load(model.GetPath())
```

**Inherit from a prototype:**

```python
proto = stage.DefinePrim("/Prototypes/StandardChair", "Xform")
# ... author the prototype ...

instance = stage.DefinePrim("/World/Chairs/Chair_01")
instance.GetInherits().AddInherit("/Prototypes/StandardChair")
```

**Add and select a variant:**

```python
vset = prim.GetVariantSets().AddVariantSet("lod")
vset.AddVariant("high")
vset.AddVariant("low")

with vset.GetVariantEditContext("high"):
    # author high-LOD data here
```

### Debugging Composition (Essential Skill)
Use **usdview** constantly:
- Layer Stack tab — see the full ordered layers
- Prim Stack / Composition Arcs — see exactly which arcs affect a prim
- Attribute values — see the resolved (strongest) value and where it came from

Python inspection:
```python
prim = stage.GetPrimAtPath("/World/SomePrim")
print(prim.GetPrimStack())           # composition sources
print(prim.GetAttribute("radius").Get())  # resolved value
```

Tools: `usdcat`, `usddiff`, `usdtree`.

Common issues:
- Unexpected override because a weaker arc was used
- Payload not loaded
- Variant selection not propagating
- Local opinion hidden by stronger sublayers

### Exam Strategy for Composition (23%)
You must be able to:
- Predict the winning opinion using LIVERPS
- Choose the correct arc for a production scenario (payload vs reference vs inherit)
- Debug complex compositions in usdview
- Author arcs correctly in code or text
- Understand layer stack vs prim stack

Practice building small examples with conflicting opinions on the same attribute and tracing which value wins.

### Key Takeaways – Modules 3 & 5
- Composition assembles layers into a stage using arcs.
- **Sublayers** are local and strong.
- **References** and **Payloads** bring in external content (payloads are lazy).
- **Inherits** are strong prototype-like arcs.
- **Specializes** are weaker specialization arcs.
- **Variants** provide named alternatives.
- **LIVERPS** decides which opinion wins: Local > Inherits > Variants > References > Payloads > Specializes.
- Local opinions (including sublayers) are the strongest.
- Master debugging with usdview and prim stack inspection.
- Composition enables the scalable, collaborative pipelines that make USD powerful.

This topic is dense but extremely high-value. Work through small examples until LIVERPS becomes intuitive. Visualize the strength arrows pointing from weak to strong.

Complete the official NVIDIA exercises if available, build your own test scenes with conflicting attributes, and inspect everything in usdview.

# Module 4: Beyond the Basics

Welcome to the deep dive on Module 4: Beyond the Basics. This module takes the foundations from Modules 1–3/5 and adds critical production concepts: **primvars**, deeper **value resolution**, **custom properties**, and **model kinds**. These topics directly support Data Modeling, Visualization, Pipeline Development, and Content Aggregation on the exam.

We will go into detail on each area with explanations, code examples, production use cases, and connections to composition.

### Primvars (Geometry-Varying Data)
Primvars are special attributes designed for data that varies across geometry. They are the standard way to attach per-vertex, per-face, or interpolated data such as:

- `displayColor` and `displayOpacity`
- Texture coordinates (UVs)
- Normals
- Arbitrary custom data for shading (e.g., weights, IDs, custom attributes)

Primvars live under the `UsdGeomPrimvarsAPI`. They support **interpolation modes** that tell the renderer or shader how to interpolate the data across the surface:

- **constant** — One value for the entire prim
- **uniform** — One value per face (or element)
- **varying** — One value per point/vertex (linear interpolation)
- **vertex** — One value per vertex (smooth interpolation)
- **faceVarying** — One value per face vertex (for discontinuous data like UV seams)

**Authoring primvars**:

```python
from pxr import Usd, UsdGeom

mesh = UsdGeom.Mesh.Define(stage, "/World/Geom/MyMesh")

# Get the primvars API
primvars_api = UsdGeom.PrimvarsAPI(mesh)

# Create a primvar
color_primvar = primvars_api.CreatePrimvar(
    "displayColor", 
    Sdf.ValueTypeNames.Color3fArray, 
    UsdGeom.Tokens.vertex   # interpolation mode
)

color_primvar.Set([(1, 0, 0), (0, 1, 0), (0, 0, 1)])  # per-vertex colors
```

You can also query existing primvars and check their interpolation:

```python
for pv in primvars_api.GetPrimvars():
    print(pv.GetName(), pv.GetInterpolation())
```

**Why primvars matter**: They are the efficient, standard way to pass varying data to Hydra renderers and shaders. Using raw attributes for this data is discouraged.

**Exam / production tip**: Know the interpolation modes and when to use `faceVarying` vs `vertex`. Primvars appear heavily in Visualization topics.

### Value Resolution (Deeper Dive)
Value resolution is how USD determines the final value of any property or piece of metadata when multiple opinions exist across the composition.

It combines two things:
1. The **LIVERPS** strength ordering from composition (Local → Inherits → Variants → References → Payloads → Specializes)
2. Additional rules for the specific data type (time samples, defaults, fallbacks, list editing, etc.)

**Process**:
- USD builds an ordered list of opinions from strongest to weakest using LIVERPS.
- It then walks that list and takes the first (strongest) opinion that provides a value.
- For time-sampled attributes, it resolves the value at the requested time.
- Metadata often follows a simple “strongest wins” rule, with a few exceptions (e.g., `typeName`, `apiSchemas`).

**Key behaviors**:
- Stronger arcs always win over weaker ones.
- Within the same strength level, later opinions in list-edited fields can win.
- Default values defined in schemas act as the weakest fallback.
- Time samples on a stronger arc override time samples on weaker arcs.

**Example scenario**:
You have a radius attribute authored in a referenced asset (weaker) and then overridden locally in the shot layer (stronger). The local value always wins.

**Practical inspection**:
```python
attr = prim.GetAttribute("radius")
print(attr.Get())                    # resolved value at default time
print(attr.GetTimeSamples())         # list of time samples
```

In usdview you can see the full resolution stack for any attribute.

**Why it matters**: Understanding value resolution lets you predict and control overrides in complex pipelines. It is essential for debugging unexpected results.

### Custom Properties
Custom properties are any attributes or relationships you author that are **not** defined by a schema.

You create them the same way as schema properties:

```python
prim = stage.DefinePrim("/World/MyPrim", "Xform")
custom_attr = prim.CreateAttribute("myPipeline:version", Sdf.ValueTypeNames.String)
custom_attr.Set("v2.3")
```

**Best practices**:
- Namespace custom properties (e.g., `myPipeline:`, `studio:`, `asset:`) to avoid collisions.
- Prefer extending or creating proper schemas when the property has broader reuse.
- Document custom properties clearly.
- Be careful with custom properties during interchange — receiving applications may ignore them.

Custom properties are useful for pipeline-specific metadata (version, artist, approval status, etc.) that doesn’t belong in core schemas.

### Model Kinds (Asset Classification)
Model kinds are a powerful organizational and behavioral tool provided by `UsdModelAPI`.

You apply a kind to a prim to classify its role in the asset hierarchy:

Common kinds:
- **component** — A leaf-level, self-contained asset (most common for models)
- **group** — A container that holds other models
- **assembly** — A higher-level collection (e.g., a full set or environment)
- **subcomponent** — A part of a component

**Applying a model kind**:

```python
from pxr import Usd, UsdModelAPI

model_prim = stage.DefinePrim("/World/Props/Chair", "Xform")
model_api = UsdModelAPI(model_prim)
model_api.SetKind(Usd.ModelAPI.KindComponent)
```

**Why model kinds are important in production**:
- They help traversal tools and pipelines identify asset roots.
- They influence bounding box computation and instancing behavior.
- They support asset validation and discovery.
- They work with composition arcs and references to maintain clear asset boundaries.
- Many pipelines enforce rules based on kind (e.g., only `component` prims should be referenced or instanced).

You can query the kind:

```python
kind = UsdModelAPI(prim).GetKind()
```

Model kinds are part of the broader asset structure principles you will use in Modules 6 and 8.

### How These Topics Connect
- **Primvars** build on UsdGeom schemas (Module 2) and are resolved via value resolution.
- **Value resolution** is the practical application of LIVERPS from composition modules.
- **Custom properties** extend the data modeling you learned in Modules 1–2.
- **Model kinds** prepare you for scalable asset aggregation and instancing in later modules.

Together they move you from basic authoring to production-ready scene description.

### Practical Exercises for Module 4
1. Create a mesh and author several primvars with different interpolation modes. Inspect them in usdview.
2. Build a small composition with a referenced asset that has a primvar. Override the primvar locally and verify value resolution.
3. Add custom namespaced attributes to several prims and query them.
4. Apply model kinds to a hierarchy (`component` under `group`) and explore how tools or traversal might use them.
5. In usdview, inspect the resolved value and source stack for both regular attributes and primvars.

### Exam Relevance
Module 4 supports:
- Data Modeling (13%)
- Visualization (8%)
- Pipeline Development and Content Aggregation

Expect questions on primvar interpolation, how value resolution interacts with composition arcs, appropriate use of custom properties, and the purpose of model kinds in asset pipelines.

### Key Takeaways – Module 4
- **Primvars** are the correct way to attach interpolating geometry data. Master the interpolation modes.
- **Value resolution** combines LIVERPS strength with data-type rules to produce final values. Stronger opinions always win.
- **Custom properties** are useful for pipeline data — namespace them and prefer schemas when possible.
- **Model kinds** classify prims for asset pipelines, traversal, and instancing behavior. Use `component`, `group`, and `assembly` appropriately.
- These concepts turn basic USD scenes into structured, production-grade assets.

You now have a deeper, more production-oriented understanding of USD data modeling and scene description.

Review the primvar interpolation modes and value resolution rules carefully — they appear in both authoring and debugging scenarios. Practice the exercises until the concepts feel natural.

# Modules 6 & 8: Asset Structure Principles, Content Aggregation, Asset Modularity and Instancing

Welcome to the combined deep dive on Modules 6 and 8. These modules focus on building **scalable, modular, and efficient** USD scenes for real production use. They directly support the **Content Aggregation (10%)** and **Pipeline Development (14%)** sections of the exam, while reinforcing composition and model kinds.

You will learn how to structure assets properly, organize layers for collaboration, aggregate many assets into large scenes, and use instancing for performance. These patterns are used daily in film, games, and real-time pipelines.

### Asset Structure Principles (The Foundation)
Good asset structure makes everything else easier: referencing, instancing, traversal, validation, and interchange.

**Recommended conventions**:
- Every asset has a clear **root prim** (usually an Xform or Scope).
- Apply a **model kind** (`component` for leaf assets, `group` or `assembly` for containers).
- Use **payloads** to the heavy geometry so the asset can be opened quickly.
- Expose a clean **interface** at the root (transforms, variants, material bindings, metadata).
- Keep the asset self-contained but referenceable.

**Example asset structure**:

```
/MyAsset.usd
  /MyAsset (Xform, kind=component)
    payload → geometry.usd
    variants (lod, material)
    /Geom
    /Materials
    /Rig (if applicable)
```

Apply the model kind:

```python
from pxr import Usd, UsdModelAPI

root = stage.DefinePrim("/MyAsset", "Xform")
UsdModelAPI(root).SetKind(Usd.ModelAPI.KindComponent)
```

This structure allows tools to identify the asset root, compute bounding boxes correctly, and decide how to instance or traverse it.

### Parallel Layer Organization
Instead of putting everything in one layer, split concerns across multiple layers for better collaboration and versioning.

Common parallel layers:
- `geom.usd` or `geometry.usd` — topology and points
- `shading.usd` or `materials.usd` — materials and bindings
- `animation.usd` — animation and time samples
- `rig.usd` or `deformation.usd`
- `metadata.usd` or `pipeline.usd` — custom properties and asset info

The root layer (or a shot/assembly layer) then references or sublayers these pieces. This allows different teams to work on geometry vs shading without conflicts and makes partial updates easier.

### Content Aggregation
Aggregation means combining many modular assets into a larger scene (a shot, environment, level, or digital twin).

**Core techniques**:
- Use **references** or **payloads** to bring assets into the scene.
- Payload heavy assets for faster initial load.
- Organize the aggregate scene with clear hierarchy (`/World/Props/`, `/World/Characters/`, etc.).
- Use **collections** (`UsdCollectionAPI`) to group assets for visibility, materials, or rendering without changing hierarchy.
- Apply overrides at the aggregate level (strong local opinions win via LIVERPS).

**Example aggregation**:

```python
world = stage.DefinePrim("/World", "Xform")

prop = stage.DefinePrim("/World/Props/Chair_01")
prop.GetPayloads().AddPayload("./assets/chair.usd")

# Another asset
building = stage.DefinePrim("/World/Environment/Building")
building.GetReferences().AddReference("./assets/building.usd")
```

For very large scenes, combine with instancing and careful payload management.

### Asset Modularity and Composition Arcs
Modularity comes from reusing assets via composition arcs while maintaining clean interfaces.

Best practices:
- Reference or payload assets rather than copying data.
- Use **variants** inside assets for LOD, materials, or configurations.
- Use **inherits** for shared prototypes across many instances.
- Keep asset interfaces stable (root prim name, key attributes, variant sets).
- Document the interface with metadata or custom properties.

This approach lets you update a single asset and have changes propagate everywhere it is referenced (subject to strength ordering).

### Instancing – Two Main Approaches

**1. Native Instancing** (Recommended for most cases)
Native instancing shares the composed prim subtree efficiently. It is activated by setting the `instanceable` metadata to true on a referenced or payloaded prim, or by using certain reference patterns.

Benefits:
- Excellent memory and performance sharing
- Full prim hierarchy is still traversable per instance when needed
- Works well with model kinds and composition

Basic pattern:

```python
instance = stage.DefinePrim("/World/Instances/Tree_01")
instance.GetReferences().AddReference("./assets/tree.usd")
instance.SetInstanceable(True)
```

You can still override individual instances with stronger local opinions or by using `instance proxy` concepts.

**2. Point Instancing** (For massive numbers of instances)
Use `UsdGeomPointInstancer` when you need thousands or millions of instances (grass, trees, crowds, debris).

It works by:
- Defining prototype prims
- Storing positions, orientations, scales, and prototype indices as attributes on the PointInstancer prim

This is extremely efficient because it avoids creating full prim hierarchies for every instance.

```python
from pxr import UsdGeom

instancer = UsdGeom.PointInstancer.Define(stage, "/World/Foliage/Grass")

# Set positions, prototype indices, etc.
instancer.CreatePositionsAttr().Set([...])
instancer.CreateProtoIndicesAttr().Set([...])
```

**When to choose which**:
- Use **native instancing** for reusable assets where you may need to inspect or override individual instances.
- Use **point instancing** for very high counts where individual prim access is not required.

### Overriding Instanced Assets
One of the most important production skills is overriding parts of an instanced asset without breaking sharing.

Techniques:
- Author stronger opinions in a higher layer (local opinions win).
- Use **specializes** or additional references on specific instances.
- Target specific prims inside the instance using paths (instance proxies allow this in many tools).
- For native instances, you can still apply per-instance overrides on attributes and relationships.

Example of overriding one instance:

```python
specific_instance = stage.OverridePrim("/World/Instances/Tree_042")
specific_instance.GetAttribute("xformOp:translate").Set((10, 0, 5))
```

 usdview and the composition system help you see which opinions are coming from the instance vs your overrides.

### Performance and Scalability Considerations
- Payload heavy data so scenes open quickly.
- Use instancing (native or point) to reduce memory duplication.
- Keep asset interfaces clean so aggregation remains manageable.
- Use collections instead of deep hierarchy changes for grouping.
- Leverage model kinds so tools can optimize traversal and bounding boxes.
- Parallel layer organization reduces merge conflicts in version control.

### Connection to Previous Modules
- Builds directly on **model kinds** (Module 4)
- Relies heavily on **composition arcs** and **LIVERPS** (Modules 3 & 5)
- Uses **primvars** and schemas for data inside assets (Modules 2 & 4)
- Prepares you for **Pipeline Development** and large-scene workflows

### Exam Focus for These Modules
- Know when to use **native instancing** vs **point instancing**
- Understand how to structure assets with model kinds and payloads
- Be able to describe strategies for overriding instanced assets
- Explain benefits of parallel layers and modular referencing
- Recognize how these patterns enable scalable aggregation

### Practical Exercises
1. Build a simple asset with a clear root prim, model kind `component`, and a payload to geometry.
2. Create an aggregate scene that references or payloads several copies of the asset.
3. Turn some of those into native instances and verify sharing in usdview or memory usage.
4. Create a PointInstancer with a few prototypes and many positions.
5. Override attributes on one or two instances and confirm the overrides win via value resolution.
6. Organize an asset across parallel layers (geom + shading) and aggregate it.

### Key Takeaways – Modules 6 & 8
- Good **asset structure** uses clear root prims, model kinds (`component`), and payloads.
- **Parallel layer organization** improves collaboration and maintainability.
- **Content aggregation** is done primarily with references and payloads, organized with clear hierarchy and collections.
- **Native instancing** provides efficient sharing with full prim access when needed.
- **Point instancing** excels at very high instance counts.
- You can still apply targeted overrides on instances using stronger composition opinions.
- These patterns together enable modular, scalable, high-performance USD pipelines.

Mastering asset structure and instancing turns you from someone who can author USD into someone who can design efficient production pipelines.

Practice building modular assets and aggregating them with instancing. Inspect everything in usdview so you can see sharing and overrides clearly.

You now have strong coverage of the practical, production-oriented side of OpenUSD.

# Module 7: Developing Data Exchange Pipelines

Welcome to the deep dive on Module 7: Developing Data Exchange Pipelines. This module carries significant weight on the exam (**Data Exchange at 15%**) and is highly practical for real-world work.

You will learn how to treat **OpenUSD as a central interchange hub**, create conceptual mappings, build importers and exporters, transform data cleanly, and remove proprietary dependencies. These skills turn USD from a file format into a true pipeline backbone.

### Why Data Exchange Pipelines Matter
Most production environments contain many tools and formats: DCCs (Maya, Houdini, Blender, 3ds Max), game engines, simulation tools, CAD systems, and renderers. USD excels as a neutral, rich interchange format because of its strong schemas, composition, and extensibility.

A good data exchange pipeline:
- Extracts data from source formats
- Transforms it into well-structured USD (using schemas correctly)
- Integrates it into larger USD workflows
- Allows round-tripping or export when needed
- Reduces lock-in by minimizing proprietary dependencies

### Core Principles of USD Data Exchange

**1. USD as the Hub**
Position USD in the middle of your pipeline rather than as just another output format. Import into USD → work in USD (composition, instancing, variants, collaboration) → export only when required.

**2. Conceptual Data Mapping (Most Important Step)**
Before writing code, create a clear mapping document:
- Source concept → USD schema / prim type
- Source attributes → USD attributes or primvars (with correct value types and interpolation)
- Relationships and hierarchies → USD prim hierarchy + relationships
- Materials and shading → UsdShade
- Animation → time samples or clips
- Metadata → custom properties or assetInfo

Example mapping (simplified):
- Source Mesh → `UsdGeomMesh`
- Source Vertex Colors → primvar with `vertex` interpolation
- Source Material → `UsdShadeMaterial` + shaders
- Source Transform → `UsdGeomXform` + xformOps

Good mapping prevents data loss and ensures the resulting USD is usable by other tools.

**3. Author to Schemas**
Always prefer schema APIs (`UsdGeom.Mesh`, `UsdShade.Material`, etc.) over raw attributes. This produces correct, validated, and interchangeable data.

### Building Importers
An importer reads a source format and authors USD data.

**High-level steps**:
1. Read the source file using its SDK or library.
2. Create or open a USD stage.
3. Traverse or query the source data.
4. Create corresponding prims using schema `Define()` methods.
5. Author attributes, primvars, relationships, and metadata according to your mapping.
6. Handle hierarchy, transforms, and time samples.
7. Save the stage (or layers).

**Python example skeleton** (conceptual):

```python
from pxr import Usd, UsdGeom, Sdf

def import_mesh(source_path, usd_path):
    stage = Usd.Stage.CreateNew(usd_path)
    
    # Create root and geometry prims
    root = stage.DefinePrim("/World", "Xform")
    mesh_prim = UsdGeom.Mesh.Define(stage, "/World/ImportedMesh")
    
    # Read source data (pseudo-code)
    points, faces, colors = read_source_mesh(source_path)
    
    # Author using schema
    mesh_prim.CreatePointsAttr().Set(points)
    mesh_prim.CreateFaceVertexCountsAttr().Set(...)  # topology
    mesh_prim.CreateFaceVertexIndicesAttr().Set(...)
    
    # Primvar for colors
    pv_api = UsdGeom.PrimvarsAPI(mesh_prim)
    color_pv = pv_api.CreatePrimvar("displayColor", Sdf.ValueTypeNames.Color3fArray, UsdGeom.Tokens.vertex)
    color_pv.Set(colors)
    
    stage.Save()
```

Handle:
- Units and up-axis conversion
- Coordinate system handedness
- Time-sampled data
- Material assignment
- Error cases and validation

### Building Exporters
An exporter traverses a USD stage and writes to a target format.

**High-level steps**:
1. Open the USD stage.
2. Traverse prims (use `stage.Traverse()` or filtered traversal).
3. For each relevant prim, read resolved values (use schema APIs to get clean data).
4. Write to the target format.
5. Handle composition (you usually want the composed result).

**Key tip**: Read *resolved* values using schema methods so you export the final state after composition, not raw authored opinions.

Example pattern:

```python
stage = Usd.Stage.Open("scene.usd")

for prim in stage.Traverse():
    if prim.IsA(UsdGeom.Mesh):
        mesh = UsdGeom.Mesh(prim)
        points = mesh.GetPointsAttr().Get()  # resolved
        # write to target format...
```

### Data Transformation Techniques
Common transformations during exchange:
- Remapping attribute names and types
- Converting between primvar interpolation modes
- Flattening or baking animation
- Converting materials (e.g., source shader → UsdPreviewSurface or custom)
- Normalizing hierarchies or applying model kinds
- Handling units, time codes, and coordinate systems

Use helper functions from `UsdUtils` where available, and write clear transformation functions.

### Removing Proprietary Dependencies
One major goal of USD pipelines is reducing lock-in.

Techniques:
- **Flattening**: Use `UsdUtils.FlattenLayerStack` or command-line `usdcat --flatten` to bake composition into a single layer. This removes references/payloads/sublayers but increases file size.
- **Baking**: Author final resolved values instead of keeping live composition arcs.
- Avoid writing proprietary metadata or using closed extensions when possible.
- Document any custom properties or schemas clearly.

Flattening is useful for delivery or when sending assets to tools that don’t support full composition.

### Custom Plugins and Extensibility
For seamless exchange, you can create:
- Custom file format plugins (so USD can natively open/save your format)
- Custom schemas (for domain-specific data)

These are covered more deeply in the Customizing USD module, but for data exchange you mainly need to understand when a plugin is worth the effort versus a standalone script.

### Validation and Robustness
Good pipelines include validation:
- Check that imported data matches expected schemas
- Verify topology and attribute presence
- Test round-tripping where possible
- Use usdview and Python inspection during development

### Exam Focus for Data Exchange (15%)
You should be able to:
- Explain the value of conceptual data mapping
- Describe the flow of a typical importer and exporter
- Discuss strategies for removing proprietary dependencies (flattening, baking)
- Understand why authoring to schemas is critical for interchange
- Know common challenges (units, time, materials, hierarchy)

The exam emphasizes conceptual understanding and pipeline thinking more than writing full plugin code.

### Practical Exercises
1. Create a simple importer that reads a basic mesh description (even from a text file or hardcoded data) and authors a correct `UsdGeomMesh` with primvars.
2. Build a small exporter that traverses a stage and prints or writes key geometry and material information.
3. Practice creating a conceptual mapping document for a common format (e.g., Alembic or FBX concepts → USD).
4. Flatten a composed stage and compare the result.
5. Add model kinds and clean hierarchy during an import process.

### Key Takeaways – Module 7
- Treat **USD as the central hub** for data exchange.
- Start with a clear **conceptual data mapping** before coding.
- Always author using **schemas** for correctness and interoperability.
- Importers extract and transform source data into structured USD.
- Exporters read *resolved* composed data from USD.
- Use **flattening and baking** to reduce proprietary dependencies.
- Build robust pipelines with validation and clear transformation logic.
- These skills directly support scalable, tool-agnostic 3D pipelines.

Data exchange is where USD’s strengths in schemas, composition, and extensibility shine. Good mapping and schema usage prevent most common problems.

You now have solid coverage of how to bring existing data into USD workflows and move data out when needed.

