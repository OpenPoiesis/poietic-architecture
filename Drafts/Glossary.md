# Glossary

Design
: The core data structure of the Modelling Domain, representing a versioned property graph of
the model.

Design Frame
: An immutable snapshot of the entire Design at a given point in time, created by a transaction.
Also referred to as *Frame*.

Object
: A user-facing entity within the Design, identified by an *Object ID* and having attributes,
traits, and a structural role.

Object ID
: Persistent logical identity of an Object across frames. Unique within each frame,
used in user-editable references.

Object Snapshot
: A specific version of an Object within a particular Design Frame, identified by a unique
*Snapshot ID*.

Snapshot ID
: Unique identifier for an Object Snapshot across the entire Design (not just within a frame).

Frame ID
: Unique identifier for a Design Frame across the entire Design.

Bound Frame
: The Design Frame currently associated with a World. Also called *World’s Frame*.

Current Frame
: The Design Frame presented to the user as the “current reality” of the model.

Metamodel
: Defines the expected structure of the Design: Object Types, Traits, Constraints, and Edge Rules.

Object Type
: Defines the traits and structural role (standalone, node, edge) of an Object.

Trait
: A named collection of related attributes that can be applied to Object Types
(e.g., “arithmetic formula”, “diagram block”).

Attribute
: A named property of an Object, defined by a Trait, with a value stored as a Variant.

Variant
: A flat union type storing atomic values or homogeneous lists of atoms
(bool, int, float, string, point).

Structural Role
: Defines how an Object participates in the graph: *standalone*, *node*, or *edge*.

Standalone
: An Object with no graph-related structure (e.g., a comment or configuration).

Node
: An Object that acts as a vertex in the property graph and can be connected by edges.

Edge
: An Object that represents a connection between two nodes in the graph.

Constraint
: A predicate that Objects must satisfy to be valid according to the Metamodel.

Edge Rule
: A rule defining valid connections between nodes based on object types, predicates,
and cardinality.

Transaction
: An atomic group of changes to the Design that, when committed, creates a new Design Frame.

Constraint Validity
: Conformance of the Design to the Metamodel’s rules. Non-conformance is a structural error.

Semantic Validity
: Domain-specific correctness (e.g., formula references) determined by simulation systems.

World
: An operational, transient runtime instance of a Design Frame, structured as an ECS
(Entity-Component-System).

Runtime ID
: Transient unique identifier for an Entity in the World, distinguishing it from persistent
Design identities.

Entity
: A runtime instance in the World, corresponding to a Design Object.

Component
: Data attached to an Entity in the World (e.g., geometry, compiled expression, simulation results).

System
: Logic that operates on World Entities and Components (e.g., formula parser, diagram geometry).

Schedule
: A named, ordered collection of Systems stored in the World, executed by the application in
a specific context.

Frame Changed Schedule
: A schedule that runs when a new Design Frame is bound to the World.

Interactive Preview Schedule
: A schedule that runs during user interaction to update visuals without creating a transaction.



## Groups of Related Terms

| Group | Related Terms |
|-------|---------------|
| Design Identities | Object ID, Snapshot ID, Frame ID |
| Structural Roles |  Standalone, Node, Edge |
| Metamodel Components | Object Type, Trait, Constraint, Edge Rule |
| World Runtime | Runtime ID, Entity, Component, System, Schedule |
| Validation |  Constraint Validity, Semantic Validity |
