---
name: Estimate and calculate global payroll tax
description: Use the Cadana Global Tax Engine to estimate gross-to-net salary and calculate taxes for a person across 150+ countries.
api: openapi/cadana-global-tax-openapi.yaml
operations: [listSupportedLocations, listCountryTaxFields, estimateTaxes, calculateTaxesForPerson]
---

# Estimate and calculate global payroll tax

Base URL `https://api.cadanapay.com` (sandbox `https://dev-api.cadanapay.com`), paths under `/v1/`.
Auth: `Authorization: Bearer <API_KEY>`.

## Steps

1. **Discover coverage** — `listSupportedLocations` (`GET /v1/tax/locations`) returns supported countries and their valid states/regions.
2. **Discover required fields** — `listCountryTaxFields` (`GET /v1/tax/fields`) returns the country-specific additional fields needed to run a calculation.
3. **Quick estimate** — `estimateTaxes` (`POST /v1/tax/estimate`) for a stateless gross-or-net salary estimate given a country and amount/type.
4. **Full calculation** — `calculateTaxesForPerson` (`POST /v1/tax/calculate`) for an existing Cadana person; their address determines jurisdiction and their stored tax profile provides inputs (override with `salary` when supplied).

## Rules

- Collect the fields from step 2 before calling step 4, or the calculation may return `400`.
- The estimate endpoint is stateless (no person required); the calculate endpoint requires a persisted person with a tax profile (`getPersonTaxProfile` / `updatePersonTaxProfile` in the Workforce API).
