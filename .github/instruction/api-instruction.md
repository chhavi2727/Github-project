# REST API Instructions

## Technology and scope

- Build REST endpoints with the existing Java 17, Spring Boot 3, and Spring Web stack.
- Use Spring MVC annotations such as `@RestController`, `@RequestMapping`, and the HTTP method mappings. Do not add a separate REST framework or switch to a different language/framework.
- Keep the existing Thymeleaf dashboard (`GET /`) working. Add API routes only when the requested feature calls for them; do not replace the dashboard or expand the MVP scope by assumption.
- Keep the architecture simple: controller → service → GitHub client. Controllers handle HTTP concerns, services own application logic, and clients handle external GitHub API calls.
- Follow [unit-test-spec.md](unit-test-spec.md) for test structure and web-layer testing.

## Endpoint design

- Use resource-oriented, plural nouns for collection paths, for example `/api/v1/repositories`.
- Prefix new public API routes with `/api/v1` unless the existing application already has an established API versioning convention.
- Use HTTP methods according to their semantics:
  - `GET` reads resources and must not change server state.
  - `POST` creates a resource only when creation is in scope.
  - `PUT` replaces a resource; `PATCH` partially updates one only when updates are in scope.
  - `DELETE` removes a resource only when deletion is in scope.
- Use path segments to identify resources and query parameters for filtering, sorting, or pagination. Do not encode request data in ad hoc path formats.
- Keep endpoint contracts consistent and avoid exposing internal class or database details.
- Do not introduce authentication, persistence, write operations, or other out-of-scope features unless explicitly requested.

## Request and response contracts

- Use dedicated request and response DTOs for API contracts. Do not expose framework, persistence, or GitHub client implementation objects directly.
- Return JSON for API responses and set the correct content type through Spring MVC.
- Keep response fields focused on the feature. For repository data, reuse only the required fields: name, description, language, stars, forks, URL, and last-updated date.
- Validate incoming request data at the boundary with Jakarta Bean Validation annotations and `@Valid` where applicable. Return clear validation errors; do not silently coerce invalid input.
- Represent dates and times with appropriate Java time types and serialize them consistently using the project's Jackson/Spring defaults.
- For collection endpoints, support pagination only when needed by the feature or the upstream API. Define and validate parameters consistently, and do not silently return misleading partial results.
- Avoid returning stack traces, secrets, upstream credentials, or internal implementation details in response bodies.

## HTTP status and errors

- Return status codes that reflect the outcome:
  - `200 OK` for a successful read or update with a response body.
  - `201 Created` for successful creation, with a `Location` header when a resource URL exists.
  - `204 No Content` for a successful operation with no response body.
  - `400 Bad Request` for malformed or invalid client input.
  - `404 Not Found` when the requested resource does not exist.
  - `502 Bad Gateway` or `503 Service Unavailable` when an upstream GitHub failure makes the requested operation unavailable, choosing consistently with the failure.
- Use Spring's existing exception-handling facilities, such as `@ControllerAdvice`, where shared mapping is needed. Keep error handling explicit and avoid broad catches that turn unexpected failures into success-shaped responses.
- Return a consistent, useful error representation. Use Spring `ProblemDetail` when supported by the project's Spring version and conventions; otherwise use a small explicit error DTO.
- Do not expose raw upstream error bodies or GitHub tokens. Log operational details safely, without secrets.

## Implementation conventions

- Keep controllers thin: bind and validate input, call the service, and map the result to an HTTP response.
- Use constructor injection and the existing application packages and naming conventions.
- Prefer explicit response DTO mapping over leaking internal models. Keep mapping straightforward and avoid adding abstraction layers without a demonstrated need.
- Externalize configurable URLs and settings. Never hardcode credentials or log them.
- Do not add dependencies for API documentation, mapping, or serialization unless the feature requires them and the change is approved.

## Testing

- Test REST endpoints with Spring MVC test support (`MockMvc`) and mock the service boundary.
- Verify route and method behavior, status codes, response JSON fields, request validation, and expected error responses.
- Test service behavior separately, and mock the GitHub HTTP boundary in client tests.
- Never call the real GitHub API or require network access or credentials in tests.
- Add or update tests whenever an API contract or behavior changes. Avoid tests that depend on incidental JSON formatting or private implementation details.