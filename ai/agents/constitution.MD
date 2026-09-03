# .ai/.constitution

## Supremacy and Scope

This document is the absolute constitutional law of the repository. Every line of code, architectural decision, and generated artifact must comply with these rules without exception.

## Architectural Constraints

1. **Strict Hexagonal Architecture:** The domain layer must remain pure TypeScript with zero framework dependencies (no NestJS decorators, no ORM imports).
2. **Vertical Slicing:** Each business feature must be completely encapsulated within its own vertical module containing its domain, application, and infrastructure layers.
3. **Dependency Rule:** Dependencies must point inward. Infrastructure depends on Application and Domain; Domain depends on nothing.
4. **Document Extraction Boundary:** Application logic must depend on the `DocumentExtractorPort`, never on a concrete document AI provider. The PoC uses a deterministic mock adapter by default; real providers such as Google Cloud Document AI are replaceable infrastructure adapters and are not required for local development.

## Technology Stack Mandates

1. **State Management:** Zustand is the sole allowed tool for global state management. Redux, MobX, or similar heavy state containers are strictly prohibited.
2. **Frameworks:** Backend requires NestJS; Frontend requires Next.js App Router using React Server Components where appropriate.
3. **Type Safety:** Strict TypeScript configuration is mandatory. `any` types are strictly forbidden.
