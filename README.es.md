# Proptech Agentic Scoring PoC

Microservicio de IA especializado en el sector inmobiliario que automatiza la evaluación de solvencia de inquilinos mediante la extracción documental agéntica y un motor de reglas determinista.

## Caso de Uso Principal: Evaluación Automatizada de Solvencia

En el mercado de alquileres tradicional, el procesamiento de postulaciones es un cuello de botella operativo: los agentes inmobiliarios pierden horas revisando manualmente PDFs de documentos de identidad y recibos de sueldo para calcular ratios financieros.

Este PoC implementa un flujo automatizado de punta a punta:

1. **Ingestión por Webhook:** Un prospecto (ej. María Gómez) envía sus documentos de identidad y comprobantes de ingresos digitales a través de un canal integrado. El sistema captura el payload y activa el caso de uso `ProcessTenantApplicationUseCase`.
2. **Extracción con IA Documental:** El adaptador de IA procesa el documento subido a través de un puerto abstracto (`DocumentExtractorPort`), extrayendo en milisegundos datos estructurados clave (Nombre completo, salario neto y empresa empleadora).
3. **Motor de Solvencia:** El `ScoringEngine` aplica de forma estricta la regla de negocio inmobiliaria, validando que la relación alquiler/ingreso sea menor o igual al 30%.
4. **Dashboard B2B en Tiempo Real:** El resultado de la evaluación (_"Apto - Riesgo Bajo"_ o _"Rechazado - Riesgo Alto"_) se despacha instantáneamente al frontend gestionado con Zustand, permitiendo que el equipo comercial tome decisiones en segundos sin abrir archivos de forma manual.

En este PoC, el usuario puede cargar documentos reales o simulados, pero su lectura se simula mediante el `MockDocumentAIAdapter` predeterminado, que devuelve datos financieros deterministas. El `rentAmount` enviado por el usuario sí se utiliza en el cálculo real de solvencia. En el futuro, un adapter de Google Cloud Document AI podrá reemplazar el mock mediante el `DocumentExtractorPort` sin modificar el caso de uso ni el motor de scoring.

## Arquitectura y Gobernanza Técnica

El proyecto está construido bajo una estricta **Arquitectura Hexagonal (Puertos y Adaptadores)** con **Vertical Slicing** y gobernado bajo un enfoque de **Spec-Driven Development (SDD)**:

- **Dominio Puro (`src/domain/`):** Modelos de negocio aislados y contratos de puertos sin dependencias de frameworks ni infraestructura.
- **Capa de Aplicación (`src/application/`):** Casos de uso que orquestan las reglas de negocio y la lógica de solvencia.
- **Infraestructura (`src/infrastructure/`):** Adaptadores concretos para NestJS, controladores de webhooks y mocks de IA documental.
- **Gobernanza IA (`.ai/.constitution`):** Reglas inquebrantables del repositorio que dictan el uso estricto de TypeScript, la separación de capas y el uso exclusivo de Zustand en el frontend.
