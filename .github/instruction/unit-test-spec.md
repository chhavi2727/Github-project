# Unit Testing Instructions

## Application context

This project is a server-rendered web application built with Java 17, Spring Boot 3, Spring MVC, and Thymeleaf. Keep tests aligned with the existing application and dependencies. Use JUnit 5, Mockito, and AssertJ; use Spring MVC test support for controller behavior.

## General rules

- Inspect the implementation and existing tests before writing or changing tests.
- Put tests under `src/test/java` in packages that mirror `src/main/java`.
- Name test classes after the production class with a `Test` suffix.
- Use descriptive test names that state the behavior, for example `shouldReturnRepositoriesWhenGithubApiSucceeds()`.
- Structure tests as Arrange → Act → Assert.
- Test observable behavior and meaningful edge cases; avoid asserting private implementation details.
- Keep each test focused on one behavior and independent of execution order.
- Use fixed, representative test data. Avoid sleeps, current-time assumptions, and random values.
- Do not call the real GitHub API, depend on external services, or require credentials in tests.
- Do not add test dependencies or change production visibility solely to make a test possible. Reuse the project's current test libraries and test seams.

## Test by layer

### Service unit tests

- Instantiate the service directly and mock its collaborators with Mockito.
- Verify successful results, empty results, and expected failure handling.
- Assert returned data and externally visible interactions only when those interactions are part of the service contract.
- Keep business-rule tests independent of Spring application-context startup.

### GitHub client tests

- Replace the HTTP boundary with the project's existing mock HTTP facility (for example, `MockRestServiceServer` when the client uses `RestTemplate`).
- Cover successful responses, empty response bodies or lists where supported, and relevant HTTP/API failures.
- Assert request details when they are part of the client contract, such as the configured endpoint or owner.
- Never make a live network request. Do not introduce an HTTP mocking library unless the project needs it and the change is approved.

### MVC controller and dashboard tests

- Prefer `@WebMvcTest` with `MockMvc` to test Spring MVC behavior without starting the full application.
- Mock the controller's service dependency; do not involve the real GitHub client.
- For `GET /`, verify the HTTP status, expected view name, and model attributes used by the dashboard template.
- Verify relevant error or empty-state behavior when the controller exposes it.
- When checking rendered Thymeleaf output, assert stable, user-visible content or key elements; do not snapshot the entire HTML or couple tests to incidental markup and styling.
- Keep template rendering checks in the MVC/web test layer rather than service unit tests.

### Full application tests

- Use `@SpringBootTest` only when a behavior genuinely requires application wiring across multiple layers.
- Keep full-context tests separate from fast unit and MVC slice tests.
- Stub all external boundaries so these tests remain deterministic and offline.

## What to cover

For behavior that exists in the application, include tests for:

- A successful GitHub repository response and mapping of displayed repository fields.
- An empty repository result.
- GitHub API errors and the application's expected handling of them.
- Service behavior and its relevant success and failure cases.
- Dashboard routing, view selection, and model data.
- Any changed behavior or regression introduced by a code change.

Do not add tests for out-of-scope features such as authentication, persistence, or repository management unless those features are explicitly introduced.

## Running and maintaining tests

- Run the narrowest relevant test first, then the project's full test suite when practical.
- For this Maven project, use `mvn test` from the project root unless the repository defines a more specific test command.
- Keep tests repeatable and readable. Update or remove obsolete assertions when application behavior intentionally changes; do not weaken tests just to make them pass.
- Do not modify production behavior merely to satisfy a test.