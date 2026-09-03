# Project Memory

## Current Agreement

- The current focus is the NestJS backend.
- Automated tests are intentionally omitted for now.
- The frontend may send real or simulated identity and income documents.
- `MockDocumentAIAdapter` is the default document extractor for the PoC.
- The mock simulates document reading and returns deterministic financial data.
- The mock does not inspect the binary content of uploaded documents.
- `rentAmount` is received from the user and is used in the real solvency calculation.
- The solvency rule is `rentAmount / netSalary <= 0.30`.
- Google Cloud Document AI is a future replaceable adapter behind `DocumentExtractorPort`.
- The use case and scoring engine must not depend on a concrete document AI provider.
- The backend is expected to run locally on port `3001`.

## Implementation Scope

- Implement the domain contracts and scoring engine first.
- Implement `ProcessTenantApplicationUseCase` next.
- Add the mock extraction adapter and NestJS webhook adapter afterward.
- Avoid adding a database or real AI provider until explicitly requested.
