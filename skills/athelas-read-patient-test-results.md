---
name: Read a patient's RPM test results
description: >-
  Authenticate to the Athelas Enterprise API, look up a site's patients, fetch a
  patient, and read that patient's remote-monitoring device test results.
api: openapi/athelas-enterprise-openapi.yml
operations: [login, get-1, get-patients, get, view-test-results]
---

# Read a patient's RPM test results

Use the Athelas Enterprise API (base URL `https://api.athelas.com/enterprise/v1`).

## Steps

1. **Authenticate** — `POST /auth/login` (`login`); use the returned Bearer token
   (60-minute lifetime) as `Authorization: Bearer <token>` on all calls.
2. **Get the site** — `GET /sites/{id}` (`get-1`) to confirm the site.
3. **List patients in the site** — `GET /sites/{id}/patients` (`get-patients`) to get
   patient ids.
4. **Fetch a patient** — `GET /patients/{id}` (`get`) for demographics/status.
5. **Read test results** — `GET /patients/{id}/test_results` (`view-test-results`).
   Results are returned **most-recent first** and cover blood pressure/heart rate,
   weight, and glucose readings.

## Rules

- To receive readings **pushed** in real time instead of polling, onboard the Test
  Results Webhook (`asyncapi/athelas-webhooks.yml`) — contact `aki@athelas.com`.
- Re-authenticate when a token exceeds 60 minutes.
- Error bodies are `application/json`; a `400` indicates an invalid id or request —
  see `errors/athelas-problem-types.yml`.
