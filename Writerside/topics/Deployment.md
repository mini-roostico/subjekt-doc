# Deployment and Publishing

All the Subjekts services can be deployed using `docker-compose` inside
the [Bootstrap](https://github.com/mini-roostico/bootstrap) repository. Follow the instructions in the README file to
deploy the services.

`auth`, `api` and `frontend` services are docker images automatically published on GitHub Container Registry through
GitHub Actions every release. 

`auth` and `api` share the same `common` library, which contains the domain models and utility code for both services.
This library is available on `npm` and can be installed using:
```bash
npm install @mini-roostico/api-common
```
The publishing takes place on every release through GitHub Actions.

The `subjekt` library is used by the `api` service and contains the actual business logic of the application.
This library is available on `npm`, Maven and GitHub Packages. The publishing takes place on every release through 
GitHub Actions on all three platforms.

The deployment of the whole project is summarized in the following diagram:

```plantuml
@startuml
node "Frontend" << container >> {
    [nginx] << Web Server >>
    [vue-app] << Frontend web app >>
}

node "API service" << container >> {
    [api-service] << Service >>
}

node "Auth service" << container >> {
    [auth-service] << Service >>
}

node "Database" << container >> {
    [mongo] << Database >>
}

node "Database rate limiter" << container >> {
    [redis] << Database >>
}

[nginx] ..> [api-service] : <<serves>>
[nginx] ..> [auth-service] : <<serves>>
[nginx] ..> [vue-app] : <<serves>>

[Subjekt Library]
[api-service] ..> [api-common] : <<uses>>
[auth-service] ..> [api-common] : <<uses>>
[api-service] ..> [mongo] : <<uses>>
[auth-service] ..> [mongo] : <<uses>>
[api-service] ..> [redis] : <<uses>>
[auth-service] ..> [redis] : <<uses>>

package "Repositories" {
    [Maven]
    [Npmjs]
    [GitHub Packages]
}

[Subjekt Library] ..> [Maven] : <<publishes>>
[Subjekt Library] ..> [Npmjs] : <<publishes>>
[Subjekt Library] ..> [GitHub Packages] : <<publishes>>
[api-service] .> [Npmjs] : <<uses version from>>

[api-common] ..> [Npmjs] : <<publishes>>
@enduml
```