# GitHub Repository Dashboard - Copilot Instructions

## Project

Create a simple Java 17 + Spring Boot MVP that fetches repository data from GitHub and displays it on a web dashboard.

### Technology

* Java 17
* Spring Boot 3.x
* Maven
* Spring Web
* Thymeleaf
* JUnit 5
* Mockito
* AssertJ

Do not use React, Angular, microservices, database, Kafka, Redis, or other unnecessary technologies.

## Features

Implement only:

* Dashboard page: `GET /`
* Configure GitHub owner/organization
* Fetch repositories using GitHub REST API
* Display:

  * Repository name
  * Description
  * Language
  * Stars
  * Forks
  * Repository URL
  * Last updated date
* Basic GitHub API error handling

## Architecture

Use a simple layered structure:

```text
Controller
    ↓
Service
    ↓
GitHub Client
    ↓
GitHub REST API
```

Suggested packages:

```text
controller
service
client
model
```

### Rules

* Controller handles HTTP requests only.
* Service contains application/business logic.
* GitHub client handles GitHub API communication.
* Model contains only required GitHub repository fields.
* Use constructor injection.
* Do not put business logic in controllers.
* Keep classes and methods small and focused.
* Avoid unnecessary design patterns and abstractions.

## Configuration

Externalize GitHub configuration:

```properties
github.api-url=https://api.github.com
github.owner=example-owner
github.token=${GITHUB_TOKEN:}
```

Never hardcode tokens, passwords, API keys, or other secrets.

Never log secrets.

## Testing

Use JUnit 5, Mockito, and AssertJ.

Follow [unit-test-spec.md](unit-test-spec.md) for the detailed unit and web-layer testing conventions. In particular, test the Spring MVC/Thymeleaf dashboard with `MockMvc`, keep service tests isolated from Spring where possible, and mock the GitHub HTTP boundary so tests never call the real API.

When adding REST endpoints, follow [api-instruction.md](api-instruction.md) and preserve the existing dashboard behavior and MVP scope.

## Refactoring Rules

When refactoring:

* Preserve existing behavior.
* Do not change API contracts unless requested.
* Remove duplicate code.
* Extract long methods.
* Improve variable/method/class names.
* Replace magic values with constants where appropriate.
* Reduce nested conditions.
* Improve exception handling.
* Keep responsibilities separated.
* Do not introduce unnecessary complexity.

## Copilot Rules

Before changing code:

1. Inspect existing code and tests.
2. Follow these instructions.
3. Reuse existing classes and utilities.
4. Make the smallest reasonable change.
5. Add/update tests for changed behavior.
6. Do not modify production behavior just to make tests pass.
7. Do not introduce unnecessary dependencies.

## MVP Scope

Do NOT implement:

* Authentication
* User management
* Database persistence
* Pull requests
* Issues
* Commits analytics
* GitHub webhooks
* Repository creation/deletion
* Docker/Kubernetes
* Microservices
* Advanced dashboard features

Keep the project **simple, readable, and suitable for demonstrating GitHub Copilot code generation, testing, and refactoring**.
