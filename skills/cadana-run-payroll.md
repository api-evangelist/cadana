---
name: Create, approve, and reconcile a payroll
description: Drive the Cadana payroll lifecycle from creation through approval to payslip retrieval and reconciliation.
api: openapi/cadana-workforce-management-openapi.yaml
operations: [createPayroll, savePayroll, getPayroll, approvePayroll, getPayrollPayslipLinks, deletePayroll, retrievePayrolls]
---

# Create, approve, and reconcile a payroll

Base URL `https://api.cadanapay.com` (sandbox `https://dev-api.cadanapay.com`), paths under `/v1/`.
Auth: `Authorization: Bearer <API_KEY>`.

## Steps

1. **Create** — `createPayroll` (`POST /v1/payrolls`) with the pay period and included persons.
2. **Save** — `savePayroll` (`POST /v1/payrolls/{payrollId}/save`) to persist edits before approval.
3. **Inspect** — `getPayroll` (`GET /v1/payrolls/{payrollId}`) to confirm amounts and status.
4. **Approve** — `approvePayroll` (`POST /v1/payrolls/{payrollId}/approve`). This disburses. A payroll in `Processing` or `Completed` cannot be deleted.
5. **Retrieve payslips** — `getPayrollPayslipLinks` (`GET /v1/payrolls/{payrollId}/payslip-links`) once `Completed`; unprocessed entries appear under `notReady`.
6. **Reconcile / list** — `retrievePayrolls` (`GET /v1/payrolls`) to reconcile across periods.

## Rules

- **Money units**: workforce/payroll amounts are integer minor units (cents) — see `conventions/cadana-conventions.yml`.
- **Lifecycle**: status transitions are event-driven; listen for `payroll.created` and `payroll.status.updated` (`asyncapi/cadana-webhooks.yml`).
- **Cleanup**: use `deletePayroll` (`DELETE /v1/payrolls/{payrollId}`) only for payrolls not yet processing.
