---
name: File a statutory return and remit to authorities
description: Discover jurisdiction requirements, submit filing fields, create and approve a statutory filing, then remit to the government authority.
api: openapi/cadana-statutory-compliance-openapi.yaml
operations: [listJurisdictions, getRequiredFields, putPersonFields, putBusinessFields, createFiling, approveFiling, getFiling, createRemittance, getComplianceSummary]
---

# File a statutory return and remit to authorities

Base URL `https://api.cadanapay.com` (sandbox `https://dev-api.cadanapay.com`), paths under `/v1/`.
Auth: `Authorization: Bearer <API_KEY>`. Several statutory operations are **Beta** — contact Cadana to enable access for your business.

## Steps

1. **Find the jurisdiction** — `listJurisdictions` (`GET /v1/statutory/jurisdictions`), then `getRequiredFields` (`GET /v1/statutory/jurisdictions/{country_code}/required-fields`) — a discovery endpoint listing what person/business data must be collected.
2. **Submit filing data** — `putPersonFields` (`PUT /v1/statutory/persons/{person_id}/fields/{country_code}`) and `putBusinessFields` (`PUT /v1/statutory/businesses/{business_id}/fields/{country_code}`) for the fields marked `scope: filing`.
3. **Create the filing** — `createFiling` (`POST /v1/statutory/filings`).
4. **Approve for submission** — `approveFiling` (`POST /v1/statutory/filings/{filing_id}/approve`). A `409` means requirements are unsatisfied or the filing is in an invalid state.
5. **Track & remit** — `getFiling` (`GET /v1/statutory/filings/{filing_id}`), then `createRemittance` (`POST /v1/statutory/remittances`) to pay the authority with automatic FX conversion.
6. **Prove compliance** — `getComplianceSummary` (`GET /v1/statutory/compliance/summary`).

## Rules

- Submit all required fields (step 1) before creating a filing, or expect `400`/`422`.
- Drive state changes from webhooks: `filing.approved`, `filing.submitted`, `filing.accepted`, `remittance.completed` (`asyncapi/cadana-webhooks.yml`).
- A `409` on approve/cancel/amend means the filing is not in a permissible state — refetch with `getFiling` first (`errors/cadana-problem-types.yml`).
