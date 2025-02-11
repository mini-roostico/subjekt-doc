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

| Glossary term             | Definition                                                         |
|---------------------------|--------------------------------------------------------------------|
| **User**                  | A person who interacts with the API.                               |
| **Source**                | A configuration object that can be saved and edited.               |
| **Parameter**             | A set of values identified by a unique name.                       |
| **Macro**                 | A map from a name definition to a set of values                    |
| **Subject**               | A map of keys and values that defines the output of the generation |
| **Configuration**         | A set of values that can be used to configure the generation.      |
| **Result**                | An entity that represents the result of the Source elaboration.    |
| **UserRegistry**          | An entity that manages the users' repository.                      |
| **SourceRegistry**        | An entity that manages the sources' repository.                    |
| **AuthenticationService** | An entity that manages users' authentication.                      |                                                       |

There are some homonym terms with the Subjekt Library context, but their definition is slightly different. For example:
- `Source`: its definition refers to the fact of being *editable* and *"saveable"*.
- `Result`: its definition refers more to the fact of being the *final product* of the elaboration, rather than an 
object produced by an `Exporter` (which is not present in this context).
- `Parameter`, `Macro`, `Subject`, `Configuration`: here they represent simple data structures that are used to 
generate the final result. The `Suite` is not present, in fact these objects represent only the data that will be
saved and edited as `Source`, the one fed to the Subjekt Library.

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

package SourceAggregate {
    class Source <<Aggregate Root>>
    class Parameter <<Value Object>>
    class Macro <<Value Object>>
    class Subject <<Value Object>>
    class Configuration <<Value Object>>
    
    Source "1" o-- "n" Parameter
    Source "1" o-- "n" Macro
    Source "1" o-- "n" Subject
    Source "1" o-- "1" Configuration
}
class resultApi as "Result" <<Value Object>>
class UserRegistry <<Repository>>

UserRegistry "1" o-- "n" User : "contains"

class SourceRegistry <<Repository>>

SourceRegistry "1" o-- "n" Source : "contains"

class AuthenticationService <<Service>>

AuthenticationService ..> UserRegistry : "updates"

class SourceService <<Service>>
SourceService ..> SourceRegistry : "updates"
SourceService ...> resultApi: "presents"


User ..> Source : "accesses"
@enduml
```

## Subjekt Frontend

The Subjekt Frontend is the context which has more in common with the previous ones, in fact is not presented in the 
glossary section. All the terms are expressed, contrary to previous contexts, in the scope of "customization" and 
"presentation". 

| Glossary term       | Definition                                                                                                                 |
|---------------------|----------------------------------------------------------------------------------------------------------------------------|
| **User**            | A person who can create/edit/open Sources.                                                                                 |
| **Suite**           | A container of values that can used to generate results.                                                                   |
| **Source**          | A configuration object that can be saved and edited.                                                                       |
| **Subject**         | An entity that contains key-value pairs that define the generation results.                                                |
| **Configuration**   | An object that contains key-value pairs that customize some global generation preferences.                                 |
| **Parameter**       | An entity that can have multiple values and that can be used inside Subjects                                               |
| **Macro**           | An entity that can accept multiple arguments and values that depends on the arguments and that can be used inside Subjects |
| **Result**          | The result produced after running a generation.                                                                            |
| **ResolvedSubject** | An object that contains key-value pairs that represent results of the generation.                                          |
| **GenerationTree**  | An object that contains the tree of the generation process.                                                                |

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
Suite ..> ResultAggregate : "produces"

User ..> sourceSubjekt : "accesses"

package ResultAggregate {
    class Result <<Aggregate Root>>
    class ResolvedSubject <<Value Object>>
    class GenerationGraph <<Value Object>>
    
    Result "1" o-- "n" ResolvedSubject
    Result "1" o-- "1" GenerationGraph
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
    end

    subgraph Frontend
        F1[User]
        F2[Suite]
        F3[Source]
        F4[Subject]
        F5[Configuration]
        F6[Parameter]
        F7[Macro]
        F8[Result]
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

## Design in detail

After the initial analysis of the contexts and the architecture, we started designing a more detailed version of the 
model. Following DDD principles, we translated the main entities and value objects into a class diagram.

### Subjekt Library class diagram
```plantuml

@startuml
@startuml
hide empty members
interface Suite {
    + suiteId: String
    + subjects: Collection<Subject>
    + symbolTable: SymbolTable
    + configuration: Configuration
}
interface Subject {
    + subjectId: String
    + resolvables: Map<String, Resolvable>
}
interface Parameter {
    + parameterId: String
    + values: Collection<String>
}
interface Macro {
    + macroId: String
    + arguments: Collection<String>
    + values: Collection<Resolvable>
}
interface Configuration {
    + settings: Map<String, Any>
}
interface SuiteFactory {
    + parseSource(source: Source): Suite
}

Suite "1" *-- "n" Subject
Suite *-- Configuration
Suite o-- SymbolTable

SuiteFactory ..> Source : uses
SuiteFactory ..> Suite : creates

interface Context {
    + parameters: Collection<DefinedParameter>
    + macros: Collection<DefinedMacro>
}

Context "1" o-- "n" DefinedParameter
Context "1" o-- "n" DefinedMacro

interface DefinedParameter {
    + parameterId: String
    + value: String
}

interface DefinedMacro {
    + macroId: String
    + value: Resolvable
}

interface SymbolTable {
    + macros: Collection<Macro>
    + parameters: Collection<Parameter>
}

interface Module {
    + moduleId: String
    + symbolTable: SymbolTable
}

Module o-- SymbolTable

SymbolTable "1" o-- "n" Parameter
SymbolTable "1" o-- "n" Macro

interface ResolvedSuite {
    + suiteId: String
    + resolvedSubjects: Collection<ResolvedSubject>
}

ResolvedSuite "1" *-- "n" ResolvedSubject

interface ResolvedSubject {
    + parentSubjectId: String
    + instances: Map<String, Instance>
}

ResolvedSubject "1" *-- "n" Instance
Subject "1" *-- "n" Resolvable
Macro "1" *-- "n" Resolvable
DefinedMacro "1" *-- "n" Resolvable

interface Resolvable
interface Instance<T> {
    + value: T
}

interface Exporter {
    + exportAsJson(resolvedSuite: ResolvedSuite): JsonResult
    + exportAsText(resolvedSuite: ResolvedSuite): TextResult
}

Exporter ..> JsonResult : "uses"
Exporter ..> TextResult : "uses"
Exporter ..> ResolvedSuite : "exports"
Mapper ..> ResolvedSuite : "converts"

interface Result {
    + asString(): String
    + toFiles(path: String): Collection<File>
}
class JsonResult
class TextResult

Result <|-- JsonResult
Result <|-- TextResult

interface Source
Source <|-- JsonSource
Source <|-- YamlSource

interface Mapper {
    + map(resolvedSuite: ResolvedSuite): ResolvedSuite
}
@enduml
```

As we can see, additional relationships have been added to the diagram, such as the one between `Resolvable` and 
`DefinedMacro`. This relationship arose when we realized that a `DefinedMacro` can be seen as a `Resolvable` too, but 
only with a single value. This less abstract connection together with others helped us to better understand the model 
and to design a more cohesive and consistent system.

### Subjekt API class diagram
```plantuml
@startuml
hide empty members
interface User {
    + email: String
    + password: String
    + firstName: String
    + lastName: String
}
interface UserRegistry {
    + createUser(email: String, password: String, firstName: String, lastName: String): User
    + readUser(email: String): User
    + updateUser(email: String, newUserData: User): User
    + deleteUser(email: String): User
}

interface SourceRegistry {
    + createSource(source: Source): Source
    + readSource(sourceId: String): Source
    + updateSource(sourceId: String, newSource: Source): Source
    + deleteSource(sourceId: String): Source
}

interface AuthenticationService {
}

interface SourceService {
}

SourceService ..> SourceRegistry : "uses"
SourceService ..> Result : "uses"
SourceRegistry ..> Source : "contains"

UserRegistry ..> User : "contains"
SourceService ..> AuthenticationService : "contacts"


interface Result {
    + asString(): String
    + toFiles(path: String): Collection<File>
}

interface Source {
    + name: String
    + userEmail: String
    + lastUpdate: Date
    + configuration: Configuration
    + subjects: List<Subject>;
    + parameters: List<Parameter>;
    + macros: List<Macro>;
}

class Configuration << (T,#FF7700) Map<string, string> >>
class Subject << (T,#FF7700) Map<string, string> >>
class Parameter << (T,#FF7700) Map<string, List<string>> >>
class Macros << (T,#FF7700) Map<string, List<string>> >>

Source *-- Configuration
Source *-- Subject
Source *-- Parameter
Source *-- Macros

AuthenticationService ..> UserRegistry : "updates"
@enduml
```

The `Source` class acts as a container for all the data that will be used by the Subjekt Library to generate the final
result. The `Result` class is used to represent the final product of the elaboration, and it is used by the 
`SourceService`. 

The entries of the `Source` class are all **type aliases**: this choice has been made since they don't need any extra 
logic, and they are just data containers (in fact they are all *Value Objects*).

### Subjekt Frontend class diagram
```plantuml
@startuml
hide empty members
interface User {
    + email: String
    + password: String
    + firstName: String
    + lastName: String
}
interface Suite {
    + name: String
    + subjects: List<Subject>
    + macros: List<Macro>
    + parameters: List<Parameter>
    + configuration: Configuration
}
interface Source {
    + id: String
    + name: String
    + lastUpdate: Date
    + yaml: String
}

interface Subject {
    + name: String
    + pairs: List<Pair<String, String>>
}

interface Parameter {
    + name: String
    + values: List<String>
}
interface Macro {
    + definition: String
    + values: List<String>
}
interface Result {
    + resolvedSubjects: List<ResolvedSubject>
    + generationGraph: GenerationGraph
}
interface ResolvedSubject {
    + name: String
    + values: List<Map<String, String>>
}
class GenerationGraph << (T,#FF7700) Graph<String> >>
class Configuration << (T,#FF7700) Map<String, String> >>

User *-- Source : "possesses"
Source ..> Suite : "gets processed into"
Suite *-- Subject
Suite *-- Parameter
Suite *-- Macro
Suite *-- Configuration
Result *-- ResolvedSubject
Result *-- GenerationGraph

Suite --> Result : "produces"
@enduml
```

Main entities of the Subjekt Library are used also in the Subjekt Frontend. Here the `Suite` class is used to represent
the `Source` from the point of view of the *generation*, while the `Source` is used to represent the management of the
`User` saved data. The `Source` is saved containing a YAML, meaning that the `Suite` gets processed inside the 
application logic. The `Result` is the final product of the generation process, and it contains the 
`ResolvedSubject`s and the `GenerationGraph` summarizing the process.
