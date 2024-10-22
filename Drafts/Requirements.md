# Requirements

Date: 2024-10-20

_System to be designed:_

Building blocks of a virtual laboratory
for computer aided modelling, simulation and reasoning about systems,
their structure, control, communication and evolution.


## System Concerns

### Purpose

Main purpose: Building blocks for a virtual laboratory.

1. Aid in visual modelling of systems structure and systems dynamics. [^1]
2. Running and controlling simulations with potential for interactivity.
3. Composition of models from smaller, potentially reusable units.
4. Comparison of models and their versions.
5. Aid in reasoning about a model by deriving/computing additional
   information from the model.

[^1]: "Aid in visual modelling" does not necessarily mean providing a graphical
  application. It means that the libraries provide shared data structures
  and functionality to support diverse graphical applications.


### Needs

**User-oriented Needs**

1. User entered content is sacred.
    - The content should be preserved as-is and provided to the user in an
      understandable and processable form when asked for.
2. Ability to design a model interactively and iteratively.
    - User must be able to explore different model versions easily.
    - User must be able to revert their actions without losing any of the
      entered content.
3. User must be allowed to make mistakes without harm to the user and to the
   design.
    - Alternative wording: User must be allowed to do experiments and to be playful.
    - User must know with sufficient precision where the mistake was made.
    - User should receive a hint what to do to prevent the mistake,
      if possible.

**Design Needs**

4. The design storage format should have assured durability and should not be
   locked-in by a particular version of the system.
    - The persisted artefacts of user's creation must be stored in a format
      that is open and documented.
    - Inspection of the structure of stored archive should be reasonably
      possible with third-party tools that are not based on the Open Poiesis
      components. [^2]
5. System must support designs from different problem domains.
6. The metamodel defining the problem domain and the constraints must be
   explicit and its description must be available during runtime.
7. The problem domains might evolve and the system should provide a way how
   to migrate designs from older domain definitions to newer ones.

[^2]: For example a relational database storage or a directory with a collection
      of CSV files satisfies this requirement.


### Maintainability and Evolvability

1. Continuation of the system is an important feature.
2. It must be easy to add new object types and to extend objects with new
   components.
3. It must be possible to inspect raw representation of the design during
   runtime.

**Principles governing the evolution of the system:**
b
4. The system should be implemented in a transparent way and it should be
   reasonably easy to explain its functioning.
5. Functioning of the system should not be tightly coupled with features of
   a programming language the system is implemented in.
    - It should be reasonably straightforward to rewrite the system or its
      subsystems in another programming language.
