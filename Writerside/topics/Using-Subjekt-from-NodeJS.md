# Using Subjekt from NodeJS

Subjekt is also available on `npm`, you just need to run:

```bash
npm i @mini-roostico/subjekt
```

to install it. Since it is compiled using Kotlin multiplatform, the import process is a bit verbose:

```javascript
const subjekt = require("@mini-roostico/subjekt").io.github.subjekt.Subjekt
```

From then, you can use all the functionalities available on the main `Subjekt` class. Here's an example on how to run
a simple Subjekt configuration quickly:

```javascript
const subjekt = require("@mini-roostico/subjekt").io.github.subjekt.Subjekt

const result = JSON.parse(subjekt.fromJson(
    JSON.stringify({
        name: "Suite",
        subject: {
            person: "\${{ name }}",
            body: `My name is \${{ name }}`
        }
    })
).customParameter("name", ["Foo", "Bar"]).resolveSubjectsAsJson().asString())

console.log(result)
```

