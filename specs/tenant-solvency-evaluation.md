# Tenant Solvency Evaluation

**Status:** Draft v1
**Scope:** End-to-end evaluation of a tenant application submitted through the ingestion adapter.

## Objective

Evaluate whether a tenant qualifies for a rental property using extracted financial data and one deterministic solvency rule. The evaluation must be reproducible for the same input.

## Actors

- **Ingestion adapter:** receives the application and starts the use case.
- **Document extractor:** converts identity and income documents into typed financial data.
- **Scoring engine:** applies the solvency rule without AI or framework dependencies.
- **Dashboard consumer:** receives the resulting decision for commercial review.

## Input Contract

An application contains:

```ts
type TenantApplication = {
  applicationId: string;
  rentAmount: number;
  identityDocument: DocumentReference;
  incomeDocument: DocumentReference;
};

type DocumentReference = {
  documentId: string;
  content: unknown;
};
```

The extractor returns:

```ts
type FinancialData = {
  fullName: string;
  netSalary: number;
  employerCompany: string;
};
```

Amounts must be positive finite numbers. The application currency is the same for `rentAmount` and `netSalary`; currency conversion is outside this specification.

## Decision Contract

```ts
type ScoringResult = {
  applicationId: string;
  rentToIncomeRatio: number;
  decision: "QUALIFIED" | "REJECTED";
  riskLabel: "LOW" | "HIGH";
};
```

The presentation layer may map these values to localized labels, including `Qualified - Low Risk` and `Rejected - High Risk`.

## Functional Requirements

### FR-001: Receive an application

The ingestion adapter must pass a typed application to `ProcessTenantApplicationUseCase` and preserve `applicationId` throughout the flow.

### FR-002: Extract data through a port

The use case must depend on a `DocumentExtractorPort`, not on a concrete AI provider. The extractor must process the identity and income documents and return `FinancialData`.

### FR-003: Calculate the ratio

The scoring engine must calculate:

```text
rentToIncomeRatio = rentAmount / netSalary
```

### FR-004: Apply the decision rule

- If `rentToIncomeRatio <= 0.30`, return `QUALIFIED` with `LOW` risk.
- If `rentToIncomeRatio > 0.30`, return `REJECTED` with `HIGH` risk.

The comparison is inclusive at exactly `0.30`.

### FR-005: Publish the result

After a successful evaluation, the application must dispatch the complete `ScoringResult` through an output port or equivalent adapter. The domain and application layers must not depend on dashboard, HTTP, or NestJS types.

### FR-006: Reject invalid or incomplete data

The use case must not score an application when documents cannot be extracted or when `rentAmount` or `netSalary` is not a positive finite number. It must return a typed failure or throw a domain/application error; the transport-specific error mapping belongs to infrastructure.

## Acceptance Criteria

- **AC-001:** Given rent `300` and net salary `1000`, the result is `QUALIFIED`, `LOW`, and ratio `0.30`.
- **AC-002:** Given rent `301` and net salary `1000`, the result is `REJECTED`, `HIGH`, and ratio `0.301`.
- **AC-003:** The result contains the original `applicationId` and the calculated ratio.
- **AC-004:** A failed document extraction produces no scoring result and no success publication.
- **AC-005:** Zero, negative, `NaN`, or infinite rent/salary values are rejected before division.
- **AC-006:** The scoring engine can be tested without NestJS, an HTTP server, an AI provider, or a database.
- **AC-007:** A concrete extractor can be replaced by a mock through the port contract.

## Architectural Constraints

- Domain code is pure TypeScript and imports no NestJS, ORM, HTTP, or provider SDK types.
- Dependencies point inward: infrastructure -> application -> domain.
- Each business feature keeps its domain, application, and infrastructure code together as a vertical slice.
- Frontend global state uses Zustand; Redux, MobX, and equivalent global stores are not allowed.
- TypeScript strictness is mandatory and `any` is forbidden.

## Out Of Scope

- Authenticating or validating document identity.
- OCR/provider selection, retries, or confidence scoring.
- Currency conversion.
- Multiple applicants, guarantors, debts, or additional scoring factors.
- Persistence, authentication, deployment, and a specific webhook or dashboard transport.
