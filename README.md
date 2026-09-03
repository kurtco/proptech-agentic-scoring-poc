# Proptech Agentic Scoring PoC

An AI-powered microservice built for the real estate sector that automates tenant solvency evaluation through agentic document extraction and a deterministic rules engine.

## Core Use Case: Automated Solvency Evaluation

In traditional residential leasing, application processing is a major operational bottleneck. Real estate agents spend hours manually reviewing PDF identity documents and pay stubs to calculate financial debt-to-income ratios.

This PoC implements a seamless, end-to-end automated workflow:

1. **Webhook Ingestion:** A prospective tenant (e.g., Maria Gomez) submits their digital identity and income verification documents via an integrated channel. The system captures the payload and triggers the `ProcessTenantApplicationUseCase`.
2. **Document AI Extraction:** An AI adapter processes the uploaded document through an abstracted port (`DocumentExtractorPort`), extracting structured data points (full name, net salary, and employer company) in milliseconds.
3. **Solvency Engine:** The `ScoringEngine` strictly evaluates the core real estate business rule, verifying whether the rent-to-income ratio is less than or equal to 30%.
4. **Real-Time B2B Dashboard:** The evaluation outcome (_"Qualified - Low Risk"_ or _"Rejected - High Risk"_) is instantly dispatched to the frontend managed via Zustand, allowing commercial teams to make decisions in seconds without manually opening PDFs.

For this PoC, users may upload real or simulated documents, but document reading is simulated by the default `MockDocumentAIAdapter`, which returns deterministic financial data. The user's `rentAmount` is used in the actual scoring calculation. A future Google Cloud Document AI adapter can replace the mock through the `DocumentExtractorPort` without changing the use case or scoring engine.

## Architecture & Technical Governance

The project is structured following strict **Hexagonal Architecture (Ports and Adapters)** with **Vertical Slicing**, governed under a **Spec-Driven Development (SDD)** approach:

- **Pure Domain (`src/domain/`):** Isolated business models and port contracts completely free of framework or infrastructure dependencies.
- **Application Layer (`src/application/`):** Use cases orchestrating business rules and solvency logic.
- **Infrastructure (`src/infrastructure/`):** Concrete adapters for NestJS, webhook controllers, and document AI mocks.
- **AI Governance (`.ai/.constitution`):** Inviolable repository rules mandating strict TypeScript usage, layer isolation, and the exclusive use of Zustand on the frontend.
