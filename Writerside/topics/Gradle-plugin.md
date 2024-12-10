# Gradle plugin

Subjekt is available as a very simple Gradle plugin with *id*: `io.github.freshmag.subjekt`, currently at version `1.0.0.`

This plugin provides a single task named `generateSubjektFiles`, that generates the files starting from the provided 
YAML configuration files.

To provide these configuration files, you can edit the section `subjekt` in the following way:

```kotlin
plugins {
    id("io.github.freshmag.subjekt") version "1.0.0"
}

subjekt {
    inputPaths = ... // list of files or directories that contains 
                     // YAML configuration files
    outputDir = ...  // directory where generated files will be 
                     // outputted to
}
```

For example, a valid configuration can be the following:

```kotlin
subjekt {
    inputPaths = listOf(
        file("src/main/resources/subjekt")
    )
    outputDir = file("src/main/resources/subjekt/generated")
}
```

After this, you can simply run the task manually:

```bash
./gradlew generateSubjektFiles
```

Or configure it to run automatically before some other task, for example:

```kotlin
tasks.processResources {
    dependsOn("generateSubjektFiles")
}
```
