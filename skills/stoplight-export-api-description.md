---
name: stoplight-export-api-description
description: >-
  Export an API description held in a Stoplight version to OpenAPI 3, Swagger 2, RAML
  0.8/1.0 or the Stoplight annotated format, using the Stoplight v1 Platform API.
api: Stoplight v1 Platform API
api_base: https://api.stoplight.io/v1
spec: openapi/stoplight-platform-v1-openapi.yml
operations:
  - GET_versions-versionId-export-format
generated: '2026-08-27'
method: generated
source: openapi/stoplight-platform-v1-openapi.yml
---

# Export an API description from Stoplight

> **Read this first.** This skill is grounded in the only machine-readable contract
> Stoplight has ever published for its own API: the **v1 (Classic)** Platform API.
> Stoplight publishes migration guides away from V1, and the export URL Stoplight
> itself referenced from that contract returned **HTTP 404** when probed on
> 2026-08-27. Treat every call below as unverified against a live service. If it
> fails, the correct fallback is the **Export API Files** feature in the Stoplight
> UI (https://docs.stoplight.io/docs/platform/37d160068e33c-export-files) or the
> Stoplight CLI — not a different endpoint you guessed at.

## When to use this

You hold a Stoplight `versionId` and you need the API description as a file — to lint
it with Spectral, to mock it with Prism, or to commit it to a repository.

## Steps

1. **Authenticate.** The contract declares a single security scheme: an API key sent
   in the `Authorization` request header.

   ```
   Authorization: <token>
   ```

   The scheme is declared in `components.securitySchemes` but is **not applied** to
   any operation in the published document, so the contract does not tell you which
   calls require it. Every operation declares a `401` response, which is the only
   signal that it is needed. Send it.

2. **Choose a format.** `format` is a required path parameter, and Stoplight
   enumerates the allowed values in the operation description:

   | `format` | What you get |
   |---|---|
   | `oas.json` | OpenAPI/Swagger, JSON |
   | `oas.yaml` | OpenAPI/Swagger, YAML |
   | `raml08.yaml` | RAML 0.8 |
   | `raml10.yaml` | RAML 1.0 |
   | `stoplight.json` | Swagger 2 with `x-stoplight` annotations, JSON |
   | `stoplight.yaml` | Swagger 2 with `x-stoplight` annotations, YAML |

   Use `stoplight.json` / `stoplight.yaml` **only** when you intend to import the
   result back into Stoplight — Stoplight states that these preserve the most
   information on a round trip.

3. **Call `GET_versions-versionId-export-format`.**

   ```
   GET https://api.stoplight.io/v1/versions/{versionId}/export/{format}
   ```

   `versionId` is described by Stoplight as "the unique identifier for the version".

4. **Handle the response shape.** A `200` returns the specification as a **JSON
   object** for the `.json` formats and as a **string** for the `.yaml` formats. The
   contract declares both `application/json` and `text/yaml` content. Do not assume
   JSON.

## Errors

Only two error responses are described on this operation, both referencing the
(empty) `standarderror` schema:

- `401` — credentials missing or rejected. Re-check step 1.
- `404` — the `versionId` does not exist, or the resource has been retired.

The live envelope observed on `api.stoplight.io` is `{"message": ..., "code": ...,
"type": ...}`. Stoplight does **not** publish that shape — see
`errors/stoplight-problem-types.yml`. There is no `application/problem+json` here.

## What you must not do

- **Do not retry on a `404`.** Nothing in the contract indicates a transient
  condition, and the v1 resource family may simply be gone.
- **Do not assume idempotency headers exist.** Stoplight documents none anywhere.
  See `conventions/stoplight-conventions.yml`.
- **Do not assume a rate limit signal.** No `X-RateLimit-*`, `RateLimit-*` or
  `Retry-After` behaviour is documented — see `rate-limits/stoplight-rate-limits.yml`.
