# Introduction

**Subjekt** is a simple tool to easily create files based on some template, specified in a YAML configuration file.

Internally, it uses a custom compiler to process _expressions_ inside the YAML file in order to
create _permutations_ of custom parameters' values.

## Getting started

~~To start using Subjekt, you can use the <a href="Gradle-plugin.md"/> at the last version available as a Gradle
dependency (`io.github.mini-roostico:io.github.subjekt:1.1.1`)~~

## Usage

Subjekt works using YAML or JSON **configuration files**.
This explanation assumes you have a basic understanding of the [YAML format](https://yaml.org/).

All Subjekt YAML must follow a structure similar to this one:

```yaml
name: "<SUITE_NAME>"
subjects:
  - name: "<SUBJECT1_NAME>"
    code: "..."
    outcomes: [ ]
  - name: "<SUBJECT2_NAME>"
    code: "..."
    outcomes: [ ]
...
```

Each configuration file defines one **Subjekt Suite**, each Suite can have multiple **Subjects**.

When compiling a Subjekt YAML file, each Subject will be resolved into multiple `ResolvedSubject`s, each one can
be rendered into a separate file.

The simplest configuration you can have is something like this:

```yaml
name: "Simple suite"
subject: "simple content"
```

This generates only one `ResolvedSubject` whose content is "simple content", and though will be rendered to only one
file.

### Parameters

Subjekt can be more useful when using **parameters**, like this:

```yaml
name: "Suite with parameters"
parameters:
  - name: "label"
    values: [ "v1", "v2" ]
  - name: "punctuation"
    values: [ "o", "x" ]
subjects:
  - name: "Subject ${{ label }}${{ punctuation }}"
    body: |-
      ${{ punctuation }} ${{ label }}
```

This configuration will produce **four** `ResolvedSubject`s, with the following body:

```Generic
o v1
o v2
x v1
x v2
```

As you can see, parameters' values are permuted to obtain all the possible combinations.

Parameters can be declared both at the **top level** and the **subject level** (together with the mandatory fields
`name`, `code` and `outcomes`).

### Expressions

You probably have noticed the `${{ }}` blocks: these are the **expressions** compiled by Subjekt. Actually, it is
possible to customize the delimiters used to insert expressions in the code, simply adding a `config` block at the top
level
of the YAML file and specifying the `expressionPrefix` and `expressionSuffix` fields, like this:

```yaml
name: "..."
config:
  expressionPrefix: "##{"
  expressionSuffix: "}"
```

The `config` block of the configuration will be covered later.

**Expressions** are special part inside YAML strings that can be evaluated to generate permutations. Subjekt does not
generate **true** permutations, but uses a system that _fixes the values' instances_ instead. To better understand this
concept, you can explore the [Template resolution section](Template-resolution.md).

### Macros

Another useful tool included in Subjekt are **Macros**. Macros are like functions that return multiple values and that
can accept multiple arguments.

You can declare macros both at the **top level** and the **subject level**.

```yaml
name: "..."
macros:
  def: "macroName(a)"
  values:
    - "(${{ a }})"
    - "{${{ a }}}"
parameters:
  name: "Par1"
  values: [ "cool", "bad" ]
subjects:
  name: "Subject"
  macros:
    - def: "innerMacro()"
      values: "This is very ${{ Par1 }}:"
  body: |-
    ${{ innerMacro() }} ${{ macroName("hello") }} !!!
```

This configuration will produce the following subjects:

- `This is very cool: (hello) !!!`
- `This is very cool: {hello} !!!`
- `This is very bad: (hello) !!!`
- `This is very bad: {hello} !!!`

The outer macro accepts an argument `a` that will be outputted between parentheses.

The inner macro does not accept arguments, but instead **can use the global parameter** `Par1` **inside its body**.

Macros, like arguments, are resolved into instances only **one time per subject**, like parameters. For this reason,
if you use

```
${{ innerMacro() }} - ${{ innerMacro() }}
``` 

as _code_ for the subject, you will only obtain these two results:

- `cool - cool`
- `bad - bad`

And not all the possible permutations. If you are curious, again, see
the [Template resolution section](Template-resolution.md)
for more details.

### Subjekt syntax

Subjekt uses a flexible YAML syntax: as you can see in previous examples, you can omit writing YAML or JSON arrays
when there is only one element. You can write many synonyms for the keys, for example `id`, `identifier` and `name`
are all resolved into the same thing inside parameters. You can use names as singulars (e.g. `parameter`) and use
any key you like inside the `subjects` block.
