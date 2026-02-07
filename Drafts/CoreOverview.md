# Overview of The Poietic Core

Poietic is a collection of libraries for a systems modelling and simulation virtual laboratory.

- Poietic is a set of libraries for CAD/CAM like application for systems modelling and simulation.
- There are two inter-connected content domains with two modelling approaches:
  _Modelling Domain_ and _Simulation Domain_:
    - The _Modelling Domain_ containing the model - to which we will refer to as "Design".
    - The _Simulation Domain_, contains instantiated world with derived data.
- The third domain is application domain, which data structures and architecture is not covered
  by this document.


## Modelling Domain: The Design

- The core data structure of the modelling domain is _Design_.
- _Design_ is a versioned container structure that maintains a traversable history through transactions.
- _Design_ (of a model) is described as an oriented property graph.
- _Design_ contains _objects_.


### Objects

- _Objects_ have attributes which are defined by their traits.
    - _Attribute_ names are strings and values are _Variants_
- _Object Type_ describes instances of an object – what are their traits (and therefore their
  attributes) and what is their structural role within the design graph.
    - Examples of object types: stock, flow rate, note, parameter (edge).
- _Object_ has exactly one of the following structural components, which define their structural role
  in the design:
    - _standalone_: the object does not have a graph-related structure, it exists in the design
      independently of other objects. Example: a comment, a configuration.
    - _node_: object is a graph node and can be referred to by an _edge_.
    - _edge_: object is a connection between two _nodes_.
- Structural references are object IDs.
- Any Object can be part of a hierarchy through a parent-child relationship.
    - An Object can have children – references to other objects.
    - Hierarchy is orthogonal to the graph structure.
    - When a parent is removed from the design, all its children are removed.
    - Parent-child references must not contain cycles.

**Note:** Current implementation is using the term _unstructured_ instead of _standalone_.
      It is a historical left-over that will be changed.


### Design Entities and their Identity Model

- The _Design_ contains three types of uniquely identified entities: _design frames_, _objects_ and
  _object snapshots_.
- **Objects** are user-facing entities, identified by _Object ID_.
- **Snapshots** are specific object versions, identified by _Snapshot ID_.
- **Frames** are snapshots of design, identified by _Frame ID_.

- **Object ID**: Logical, user-facing identity that persists across frames.
    - Unique within each frame.
    - Used in user-editable references between objects.
    - Same Object ID may appear in multiple frames (representing the object's evolution).
- **Snapshot ID**: Physical identity of a specific object version.
    - Unique across the entire design, not just within a frame.
    - Shared between frames when an object hasn't changed.
- **Frame ID**: Physical identity of a design frame.
    - Unique across the entire design.
- Relationships between entities and their identities:
    - One Object can have multiple Snapshots: One Object ID -> Many Snapshot IDs.
    - One Snapshot represents exactly one object: One Snapshot ID -> Exactly one Object ID.
    - One Frame contains many snapshots of distinct objects: Frame ID -> many unique Snapshot IDs,
      many unique Object IDs. 
    - **Important:** Frame can not contain multiple snapshots of the same object.


### Metamodel

- How the _Design_ is expected to be structured is defined by the _Metamodel_.
- _Metamodel_ is a collection of object type definitions, object and edge rules.
- _Trait_ is a named collection of related attributes that co-exist together.
    - Example of traits: arithmetic formula (formula text), diagram block (position, pictogram), style (colour), numeric indicator (min, max, baseline).
    - Traits can be empty (no attributes) and serve as tags, for example:
      simulation object.
    - All object attributes come from traits; objects cannot have stand-alone attributes outside
      of traits.
- _Constraints_ define predicates for objects that must match certain requirements.
- _Edge Rules_ is a collection of rules that the edges in the graph must match to be valid.
    - Edge rules are defined by edge object type, predicate matching origin (optional),
      predicate matching target (optional), cardinality at the origin and at the target.


### Validation

- We distinguish between constraint validity and semantic validity.
- **Constraint Validity**: The Design must always conform to the design _Metamodel_ – its
  constraints, edge rules and object type requirements.
- A Design that does not conform to the Metamodel is considered structurally invalid and should not
  be loaded or used without repair.
  - Application might offer to repair, convert or suggest a different metamodel for a non-conforming
    design.
- **Semantic Validity**: Decided by the systems of particular problem domain.
- For example a system that creates a simulation plan for stock-and-flow might find some formulas containing invalid
  variable references. 
- The Design may be semantically incorrect (preventing simulation, causing validation 
  errors displayed in UI), but remains fully under user control.
  

### Changes, Transactions and History

- Each change or group of changes is represented by a transaction.
- _Transaction_: An atomic group of changes to the design
    - Creates a new Design Frame when committed
    - Changes within a transaction are all-or-nothing
    - Provides the unit of undo/redo operations
- Transaction creates a new design snapshot, which is called _Design Frame_.
- _Design Frame_ is immutable.
- _Design_ can have multiple _Design Frames_.
- Current state of the design is presented in a _current frame_.
    - _Current frame_ is the frame that is presented to the user by default.
      It represents "current reality" of the design.
- Main history timeline is represented by a list of _undoable frames_ and _redoable frames_.
- All user-editable object references are _Object IDs_, therefore they must point to objects
  that must exist within the same _Design Frame_ (the same design version snapshot).
- On a frame-change transaction:
    - When an object is modified, transaction creates a new Snapshot ID for that Object ID in the
      new frame.
    - When an object is unchanged, the new frame reuses the same Snapshot ID.
    
    
### Data Types

- _Design_ attribute values are stored as _Variants_ for the following reasons:
    - Limited data types for easier inter-operability of applications using the design.
    - Simplification of persistence of the design.
    - Scriptability.
    - Simplification bridging the user interface with design. For example implementation of
      inspectors.
- Validated design frame is guaranteed to have attributes with data types convertible to
  the types specified in the metamodel.
- _Variant_ is a union (sum) data type which can represent an atom or a homogenous list of atoms.
  - The variant atoms can be of one of the following types: bool, integer,
    floating point number (double precision), string, point (2D vector).
  - Given that the lists are homogenous and only of atoms, therefore variants are flat.


## The Simulation Domain: The World

- World is an operational representation of the design, it is user's or application's focus.
- World is a runtime object (in-memory), it is not expected to be persisted.
- World is architectonically structured as an ECS (Entity-Component-System) World:
    - World has entities, entities have components.
- Entities are identified by unique ID called _Runtime ID_.
    - _Runtime ID_ is used instead of _Entity ID_ to clearly denote transient nature of the world
    and to distinguish from persistent identities of design entities (such as objects, snapshots and frames).
- World typically contains:
    - data derived from the design
    - simulation or computational state
    - simulation results
    - design or simulation related session (or application) data
- World is typically bound to one design frame, referred to as _bound frame_ or just _world's frame_
  when there is no confusion.
    - Typically the world's bound frame is the current frame of the design.
    - To have different simulation (operational) focus, for example for simultaneous variation of
      parameters or to compare different frames, multiple worlds are used.
- When a world is bound to a design frame, it contains runtime entities corresponding to the design
  objects in that frame.
- A World may be created without a frame for preview or diagnostic purposes.


### Systems and World Updates

- Systems operate on the world, entities and their components.
- Systems do not operate on the Design.
    - **Rationale**: Separation of concerns. The Design represents the user's creation and should
      only be modified through explicit user actions. The Design is "owned" by the user and must
      not be altered by automated simulation or computation processes
- Systems can create, read, directly mutate or delete entities and their components.
- Convention is, that systems do not change the _Design_.
    - They are not directly dis-allowed by the interface, just by recommendation and convention.
    - Inability to change the design might be enforced by interface in the future.
- Systems are grouped in _Schedules_ and world has multiple _Schedules_.
- Systems can define dependencies within a schedule: run before/after other systems.

- Schedule: An ordered execution plan for a collection of systems.
    - Systems within a schedule run according to their dependencies.
- Different schedules execute in different contexts (e.g., on frame change vs. 
  during interaction)


### Examples

- Examples of runtime components:
    - Diagram block with pictogram geometry
    - Diagram connector with line geometry
    - Compiler arithmetic expression
    - Information about simulation objects
    - Simulation result time series for given object

- Examples of singleton runtime components:
    - Simulation plan
    - Order of simulation object dependencies
    - Graphical notation (pictograms, connector glyphs specification, styles)
    - Simulation results

- Examples of systems:
    - Formula parser system
    - Object dependency order resolution system
    - Simulation planning system
    - Diagram geometry system

- Examples of schedules:
    - Frame changed schedule – run when a new design frame has been accepted, typically after
      committing a transaction.
    - Interactive preview schedule – run on interactive change, such as mouse event, to update
      visuals and geometries.
