---
name: Pay a contractor end-to-end
description: Onboard a contractor, put a contract in place, run payroll, and retrieve payslips using the Cadana Workforce API.
api: openapi/cadana-workforce-management-openapi.yaml
operations: [createPerson, createContracts, getContractSigningUrl, createPayroll, savePayroll, approvePayroll, getPayrollPayslipLinks]
---

# Pay a contractor end-to-end

Base URL `https://api.cadanapay.com` (sandbox `https://dev-api.cadanapay.com`), all paths under `/v1/`.
Authenticate every request with `Authorization: Bearer <API_KEY>`.

## Steps

1. **Create the contractor** — `createPerson` (`POST /v1/persons`). Set the employment type to contractor and supply compensation and payment info. Required payment fields vary by payout corridor; consult `getPaymentRequirements` for the target currency/method.
2. **Put a contract in place** — `createContracts` (`POST /v1/contracts`) from a template, then `getContractSigningUrl` (`GET /v1/contracts/{contractId}/signingUrl`) to get a signing link (links last 336 hours).
3. **Run payroll** — `createPayroll` (`POST /v1/payrolls`), then `savePayroll` (`POST /v1/payrolls/{payrollId}/save`), then `approvePayroll` (`POST /v1/payrolls/{payrollId}/approve`). Approval disburses.
4. **Retrieve payslips** — once the payroll is `Completed`, `getPayrollPayslipLinks` (`GET /v1/payrolls/{payrollId}/payslip-links`) returns short-lived presigned links.

## Rules

- **Idempotency**: on create operations that accept `idempotencyKey`, send a stable UUID to make retries safe (see `conventions/cadana-conventions.yml`).
- **Sandbox testing**: set `payrollDate` to a past date so the payroll completes immediately on approval (`sandbox/cadana-sandbox.yml`).
- **Events**: subscribe to `payroll.status.updated`, `contract.signed`, and `invoice.contractor.paid` webhooks (`asyncapi/cadana-webhooks.yml`) instead of polling.
- **Errors**: 409 means the resource is in a state that does not allow the transition; refetch state before retrying (`errors/cadana-problem-types.yml`).
