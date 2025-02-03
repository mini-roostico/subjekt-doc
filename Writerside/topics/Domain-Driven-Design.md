# Domain Driven Design

Starting from the glossary, the Domain Driven Design phase started identifying the various roles to assign to the
different concepts involved in the project. Mapping each element to the glossary, we identified three main contexts:

- Subjekt Library
- Subjekt API
- Subjekt Frontend

## Subjekt Library

In the context of the Subjekt library, we identified the following terms, expresses in ubiquitous language
(note: same as the glossary):

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

And subsequently, we drew their context map:

```plantuml
@startuml
hide empty members
hide <<Entity>> circle
hide <<Value Object>> circle
hide <<Factory>> circle
hide <<Service>> circle
hide <<Domain Event>> circle
hide <<Aggregate Root>> circle
hide <<Repository>> circle

package SuiteAggregate  {
    class Suite <<Aggregate Root>>

    Suite "1" o-- "n" Subject
    Suite "1" *-- "1" Configuration

    class Subject <<Entity>>
    class Configuration <<Value Object>>
}

class sourceSubjekt as "Source" <<Value Object>>
class SuiteFactory <<Factory>>

package ContextAggregate {
    class Context <<Aggregate Root>>

    Context "1" o-- "n" DefinedParameter
    Context "1" o-- "n" DefinedMacro

    class DefinedParameter <<Value Object>>
    class DefinedMacro <<Value Object>>
}

class Module <<Entity>>

package SymbolTableAggregate {

    class SymbolTable <<Aggregate Root>>

    SymbolTable "1" o-- "n" Parameter
    SymbolTable "1" o-- "n" Macro

    class Parameter <<Entity>>
    class Macro <<Entity>>
}

package ResolvedSuiteAggregate {
    class ResolvedSuite <<Aggregate Root>>

    ResolvedSuite "1" *-- "n" ResolvedSubject
    class ResolvedSubject <<Value Object>>
}

class Resolvable <<Entity>>
class Instance <<Value Object>>

class Exporter <<Service>>
class resultSubjekt as "Result" <<Value Object>>

class Mapper <<Service>>
Mapper ..> ResolvedSuite : "converts"
Exporter ..> ResolvedSuite : "exports"
Exporter ..> resultSubjekt : "produces"

Subject "1" *-- "n" Resolvable
Resolvable "1" *-- "n" Instance
ResolvedSubject "1" *-- "n" Instance

SymbolTable "1" --o "1" Suite
SymbolTable "1" -o "1" Module

SuiteFactory ..> Suite : "produces"
SuiteFactory ..> sourceSubjekt : "handles"
@enduml
```

## Subjekt API

In the context of the Subjekt API, we identified the following terms, expresses in ubiquitous language
(note: same as the glossary):

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

The `Source` is homonym with the one in the Subjekt library context, but its definition refers to the fact of being
*editable* and *"saveable"*.

The `Result` too is homonym with the one in the Subjekt library context, but its definition refers more to the fact of 
being the *final product* of the elaboration, rather than an object produced by an `Exporter` (which is not present in
this context).

The following is the context map for the Subjekt API:

```plantuml
@startuml
hide empty members
hide <<Entity>> circle
hide <<Value Object>> circle
hide <<Factory>> circle
hide <<Service>> circle
hide <<Domain Event>> circle
hide <<Aggregate Root>> circle
hide <<Repository>> circle

class User <<Entity>>
class sourceApi as "Source" <<Value Object>>

class resultApi as "Result" <<Value Object>>
class UserRegistry <<Repository>>

UserRegistry "1" o-- "n" User : "contains"

class SourceRegistry <<Repository>>

SourceRegistry "1" o-- "n" sourceApi : "contains"

class AuthenticationService <<Service>>

AuthenticationService ..> AuthenticationEvent : "handles"
AuthenticationEvent ..> User : "uses"
AuthenticationService ..> UserRegistry : "updates"
UserEvent ..> User : "uses"
AuthenticationService ..> UserEvent : "handles"

class SourceService <<Service>>

SourceService ..> SourceEvent : "handles"
SourceService ..> SourceRegistry : "updates"

class GenerationService <<Service>>

GenerationService ..> GenerationEvent : "handles"
GenerationEvent ..> sourceApi : "uses"
GenerationService ...> resultApi: "presents"

class AuthenticationEvent <<Domain Event>>
class UserEvent <<Domain Event>>
class SourceEvent <<Domain Event>>
class GenerationEvent <<Domain Event>>

User ..> sourceApi : "accesses"
@enduml
```

## Subjekt Frontend

The Subjekt Frontend is the context which has more in common with the previous ones, but all the terms are expressed 
in the scope of "customization" and "presentation". 

| Glossary term           | Definition                                                                                                                 |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------|
| **User**                | A person who can create/edit/open Sources.                                                                                 |
| **Suite**               | A container of values that can used to generate results.                                                                   |
| **Source**              | A configuration object that can be saved and edited.                                                                       |
| **Subject**             | An entity that contains key-value pairs that define the generation results.                                                |
| **Configuration**       | An object that contains key-value pairs that customize some global generation preferences.                                 |
| **Parameter**           | An entity that can have multiple values and that can be used inside Subjects                                               |
| **Macro**               | An entity that can accept multiple arguments and values that depends on the arguments and that can be used inside Subjects |
| **GenerationResult**    | The result produced after running a generation.                                                                            |
| **ResolvedSubject**     | An object that contains key-value pairs that represent results of the generation.                                          |
| **GenerationTree**      | An object that contains the tree of the generation process.                                                                |
| **AuthenticationEvent** | An event that is triggered by a User that wants to be authenticated (e.g., login).                                         |
| **SourceEvent**         | An event that is triggered when a User interacts with a Source (e.g. creation, deletion etc.)                              |
| **GenerationEvent**     | An event that is triggered when a User runs a generation.                                                                  |

The context map for the Subjekt Frontend is the following:

```plantuml
@startuml
hide empty members
hide <<Entity>> circle
hide <<Value Object>> circle
hide <<Factory>> circle
hide <<Service>> circle
hide <<Domain Event>> circle
hide <<Aggregate Root>> circle
hide <<Repository>> circle

package SuiteAggregate  {
    class Suite <<Aggregate Root>>

    Suite "1" o-- "n" Subject
    Suite "1" *-- "1" Configuration
    Suite "1" o-- "n" Parameter
    Suite "1" o-- "n" Macro

    class Subject <<Entity>>
    class Configuration <<Value Object>>
    class Parameter <<Entity>>
    class Macro <<Entity>>
}

class sourceSubjekt as "Source" <<Entity>>
class User <<Entity>>

sourceSubjekt ..> Suite : "refers to"
Suite ..> GenerationResultAggregate : "produces"

User ..> sourceSubjekt : "accesses"

package GenerationResultAggregate {
    class GenerationResult <<Aggregate Root>>
    class ResolvedSubject <<Value Object>>
    class GenerationTree <<Value Object>>
    
    GenerationResult "1" o-- "n" ResolvedSubject
    GenerationResult "1" o-- "1" GenerationTree
}
@enduml
```

## Bounded Contexts

After analyzing the three contexts, we identified the following bounded contexts, focusing on the common terms and 
identifying the related differences:

```mermaid
graph TD
    subgraph Library
        L1[Suite]
        L2[Subject]
        L3[Configuration]
        L4[Parameter]
        L5[Macro]
        L6[ResolvedSubject]
        L7[Source]
        L8[Result]
    end

    subgraph API
        A1[User]
        A2[Source]
        A3[Result]
        A4[UserRegistry]
        A5[SourceRegistry]
        A6[AuthenticationService]
        A7[GenerationService]
    end

    subgraph Frontend
        F1[User]
        F2[Suite]
        F3[Source]
        F4[Subject]
        F5[Configuration]
        F6[Parameter]
        F7[Macro]
        F8[GenerationResult]
        F9[ResolvedSubject]
    end

    L7 --- A2
    L8 --- A3
    A1 --- F1
    L1 --- F2
    L2 --- F4
    L3 --- F5
    L4 --- F6
    L5 --- F7
    L6 --- F9

```

The common terms between contexts have been highlighted. 

## Architecture

The global architecture adopted **customer-supplier** as model integrity pattern, with the Subjekt Library as the 
supplier and the Subjekt API as customer. The Subjekt Frontend is a customer of both the Subjekt Library and the Subjekt
API. 

Inside the Subjekt API context, the **shared kernel** pattern has been adopted to share the model for two different
sub-contexts, `auth` and `api`, sharing a `common` model.

The following is the architecture diagram:

```plantuml
@startuml
[Subjekt Library]
[Subjekt Frontend]

[Subjekt Library] --> [Subjekt API] : "Customer-Supplier"
[Subjekt API] --> [Subjekt Frontend] : "Customer-Supplier"

package "Subjekt API" {
    [auth] - [common]
    [common] - [api]
}
@enduml
```

