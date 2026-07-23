---
name: Enroll an RPM patient and ship a device
description: >-
  Authenticate to the Athelas Enterprise API, create a site and prescriber, create a
  patient under that site, verify the shipping address, and queue a remote-monitoring
  device for drop shipment.
api: openapi/athelas-enterprise-openapi.yml
operations: [login, create-1, create-2, create, verify-address, address-verified, queue-rpm-device-for-shipping]
---

# Enroll an RPM patient and ship a device

Use the Athelas Enterprise API (base URL `https://api.athelas.com/enterprise/v1`,
staging `https://staging-api.athelas.com/enterprise/v1`).

## Steps

1. **Authenticate** — `POST /auth/login` (`login`) with `email` and `password`. Save
   the returned Bearer token; it expires after **60 minutes**, so re-run `login` when
   requests start failing. Send `Authorization: Bearer <token>` on every call below.
2. **Create a site** — `POST /sites/create` (`create-1`). Sites group patients and
   prescribers; at least one site must exist before creating a prescriber.
3. **Create a prescriber** — `POST /prescribers/create` (`create-2`), associating the
   prescriber (the patient's doctor) with the site from step 2.
4. **Create the patient** — `POST /patients/create` (`create`) under the site.
5. **Verify the shipping address** — `POST /patients/{id}/verify_address`
   (`verify-address`) to queue verification, then poll
   `GET /patients/{id}/address_verified` (`address-verified`) until it succeeds.
6. **Queue the device** — `POST /test_types/{id}/ship_rpm_device`
   (`queue-rpm-device-for-shipping`) to drop-ship the RPM device to the patient.

## Rules

- Ordering matters: **site → prescriber → patient**. Creating a prescriber before any
  site exists returns `400`.
- All operations document a `400` for malformed input; error bodies are
  `application/json` (not RFC 9457) — see `errors/athelas-problem-types.yml`.
- No idempotency-key contract is documented; do not assume safe retries on writes.
- Test with the published demo login (`sandbox/athelas-sandbox.yml`) to receive the
  Athelas Demo Token before touching production.
