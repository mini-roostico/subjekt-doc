# Glossary

During the initial phases of the project's development, we identified the main terms that defined the entities and
objects of the Subjekt domain. Since we already identified the main **contexts** of the domain, we separated right at the
start the two main *spheres* of the model.

Here the terms are described in a common language used by all the stakeholders taking part of the project.

## Subjekt - library

Since the main sphere of the project concerns the Multi-platform core library, we identified this as one of the main
contexts.

| Glossary term        | Definition                                                                                                                                                |
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Suite**            | A global object containing Parameters, Macros, Subjects, and a Configuration.                                                                             |
| **Parameter**        | A set of values identified by a unique name.                                                                                                              |
| **Macro**            | A function that takes a set of arguments and returns a set of possible values.                                                                            |
| **DefinedParameter** | A Parameter fixed to one of its possible values.                                                                                                          |
| **DefinedMacro**     | A Macro fixed to one of its possible values.                                                                                                              |
| **Resolvable**       | An object that, fixed to a Context, can be resolved to a single value.                                                                                    |
| **Context**          | One of the fixed sets of DefinedParameters and DefinedMacros from the possible permutations of Parameters and Macros values.                              |
| **Instance**         | A Resolvable that has been resolved to a single value by fixing the Context.                                                                              |
| **Subject**          | A conceptual unit that contains Resolvables. It encapsulates the configuration and behavior required to generate results in the form of ResolvedSubjects. |
| **ResolvedSubject**  | A result obtained from a Subject by transforming its Resolvables into Instances (i.e., by fixing a Context).                                              |
| **ResolvedSuite**    | A collection of ResolvedSubjects obtained by resolving all Subjects in a Suite.                                                                           |
| **Configuration**    | A set of values that can be used to configure the generation.                                                                                             |
| **Exporter**         | An entity that converts a ResolvedSuite into a given format.                                                                                              |
| **Result**           | An object that represents the result produced by an Exporter.                                                                                             |
| **Source**           | An object that provides all the Suite's data.                                                                                                             |
| **SuiteFactory**     | An entity that creates a Suite from a Source.                                                                                                             |
| **Module**           | A library providing a set of Parameters and DefinedMacros.                                                                                                |
| **Mapper**           | An entity that applies a conversion to a ResolvedSuite (e.g., code linter).                                                                               |

## Subjekt - API

The API refers to the context in which Subjekt is used as a library from the outside, in a user-ready service. Here,
only some of the terms are also present in the library context, because the API doesn't need a notion of those.
These are the *Source* and the *Result*. Note: from the point of view of the API, their definition appears to be
slightly different.

| Glossary term             | Definition                                                                                              |
|---------------------------|---------------------------------------------------------------------------------------------------------|
| **User**                  | A person who interacts with the API.                                                                    |
| **Source**                | A configuration object that can be saved and edited.                                                    |
| **Result**                | An entity that represents the result of the Source elaboration.                                         |
| **UserRegistry**          | An entity that manages the users' repository.                                                           |
| **SourceRegistry**        | An entity that manages the sources' repository.                                                         |
| **AuthenticationService** | An entity that manages users' authentication.                                                           |
| **GenerationService**     | An entity that manages the Source elaboration.                                                          |
| **UserEvent**             | An event that is triggered by an action involving the User in UserRegistry (e.g., creation, etc.).      |
| **AuthenticationEvent**   | An event that is triggered by an action involving Authentication in UserRegistry (e.g., login, logout). |
| **SourceEvent**           | An event that is triggered by an action involving the SourceRegistry (e.g., creation, update, etc.).    |
| **GenerationEvent**       | An event that is triggered by a request for a Source elaboration.                                       |
