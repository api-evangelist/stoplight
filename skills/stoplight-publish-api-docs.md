---
name: stoplight-publish-api-docs
description: >-
  Publish and unpublish hosted API documentation through the Stoplight v1 Platform
  API, including the irreversible anonymous-publish path.
api: Stoplight v1 Platform API
api_base: https://api.stoplight.io/v1
spec: openapi/stoplight-platform-v1-openapi.yml
operations:
  - POST_versions-versionId-publish
  - PUT_versions-versionId-unpublish
  - PUT_versions-versionId-import
  - POST_versions-publish-anon
generated: '2026-08-27'
method: generated
source: openapi/stoplight-platform-v1-openapi.yml
---

# Publish hosted API documentation with Stoplight

> **Read this first.** Grounded in the **v1 (Classic)** Stoplight Platform API — the
> only contract Stoplight has published for its own service. Stoplight ships
> migration guides away from V1 and the export URL it referenced from this contract
> now 404s (probed 2026-08-27). Treat this as unverified against a live service. The
> supported modern path for publishing project content is the Stoplight CLI
> (`cli/stoplight-cli.yml`).

## The reversibility rule — read before you act

This API contains one reversible action and one that can never be taken back.

| Action | Reversal | Window |
|---|---|---|
| `POST /versions/{versionId}/publish` | `PUT /versions/{versionId}/unpublish` | **Not stated by Stoplight.** Do not assume it is unbounded. |
| `POST /versions/publish/anon` | **None. Ever.** | — |
| `PUT /versions/{versionId}/import` | None documented | — |

Stoplight states the anonymous limitation in its own operation description:
*"Cannot update/remove the documentation. Cannot choose the subdomain. Cannot choose
the version. Cannot add theming."* Once an anonymous publication exists, the
documentation is public and you cannot delete it. **Never call
`POST_versions-publish-anon` on behalf of a user without explicit confirmation, and
never with a document that could contain anything private.**

## Flow A — publish a version you own (reversible)

1. **Authenticate** with the `Authorization` header API key. As with every operation
   in this contract, the scheme is declared but never applied, so the contract does
   not tell you it is required — the `401` response is the only hint. Send it.

2. **Optionally load content first** with `PUT_versions-versionId-import`:

   ```
   PUT https://api.stoplight.io/v1/versions/{versionId}/import
   ```

   Import **overwrites** the version's specification content. There is no undo
   operation and no dry-run flag. If the current content matters, export it first
   with the `stoplight-export-api-description` skill.

3. **Publish** with `POST_versions-versionId-publish`:

   ```
   POST https://api.stoplight.io/v1/versions/{versionId}/publish
   ```

4. **Reverse it if needed** with `PUT_versions-versionId-unpublish`:

   ```
   PUT https://api.stoplight.io/v1/versions/{versionId}/unpublish
   ```

   This is the documented reversal of step 3. Stoplight publishes no time limit on
   it — record that as unknown, not as unlimited.

## Flow B — anonymous publish (IRREVERSIBLE)

`POST https://api.stoplight.io/v1/versions/publish/anon`

The request body is one of:

```json
{ "specData": { } }
```

or

```json
{ "url": "http://petstore.swagger.io/v2/swagger.json" }
```

`specData` may be an object or a string; `url` points at a Swagger or RAML document.

The `200` response carries the published documentation URL:

```json
{ "url": "https://swagger-petstore.api-docs.io/v1.0.0" }
```

`url` is the only identifier you get back, and it is the only handle that will ever
exist for this publication. Store it.

## Errors

- `400` — malformed body (anonymous publish only).
- `401` — credentials missing or rejected.
- `404` — the `versionId` does not exist.
- `500` — server error (anonymous publish only).

All reference the `standarderror` schema, whose body is **empty** in the published
contract. See `errors/stoplight-problem-types.yml`.

## What you must not do

- **Do not retry a failed publish blindly.** Stoplight documents no idempotency key,
  no request-id and no replay window (`conventions/stoplight-conventions.yml`). A
  retried anonymous publish creates a second permanent publication.
- **Do not import to "test" a document.** Use a local Prism mock instead:
  `prism mock <document>` — see `sandbox/stoplight-sandbox.yml`.
