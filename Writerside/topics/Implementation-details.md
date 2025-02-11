# Implementation details

In this section we will shortly present the main implementation details of the project, focusing on the main libraries
and particular solutions adopted during the development of this project.

## Subjekt library

The library was developed using Kotlin Multiplatform, allowing the code to target both the JVM and JavaScript.
To achieve this, in order to minimize the amount of code that is platform-specific, the library was developed using
as many already existing multiplatform libraries as possible.

- **Kotlin serialization**: multiplatform library that allows to serialize and deserialize objects in a
  platform-independent way. The JSON module was used to parse and serialize JSON files.
- **YamlKt**: utility multiplatform library that allows to parse and serialize YAML files. It must be used in
  conjunction with Kotlin serialization.
- **ANTLR**: this library is JVM-based, and therefore its usage is limited to the JVM target. Instead of using it,
  a fork by [Strumenta](https://github.com/Strumenta) was used, which is a multiplatform version of the library.

Particular attention was given to the development of the **public API** of the library. The Javascript exporting
system required to invest time in the definition of the public side of the library, sometimes using `dynamic` types
to bypass un-exportable types.

In addition to the main entities of the domain, the library entry point is the `Subjekt` class, which generates a
Subjekt instance from a YAML or JSON string. **Note**: files are not supported to avoid platform-specific code.

## Auth and API services

Node.js and Express were used to develop both the RESTful services (i.e. `auth` and `api`). TypeScript was employed to
enhance code maintainability.

In details, the authentication service was implemented using JWT (JSON Web Tokens) to ensure secure and stateless
user authentication.

MongoDB was used as the primary database for storing application data, while Redis was employed for rate limiting
storage. Additionally, a validation handler has been applied to specific routes to ensure data integrity and
enforce input validation.

To enhance API usability and maintainability, route documentation has been provided using Swagger, enabling
interactive exploration and testing of endpoints.

Jest was used as the testing framework.

## Frontend application

The frontend service was developed using Vue.js, a popular JavaScript framework, with TypeScript. The application
is a simple web application that allows to authenticate users and manage sources through which generate Subjekt results.

The application uses the [axios](https://axios-http.com/) library to perform HTTP requests to the backend services (auth
and API). To manage authentication and authorization, we used `interceptors` to intercept requests and responses in
order to perform checks on the JWT token of the user's session.

To handle the state of the application and encapsulate the API calls, we used the [Pinia](https://pinia.vuejs.org/) 
library, which is a Vue.js state management library that allows to create stores and actions in a simple way.

