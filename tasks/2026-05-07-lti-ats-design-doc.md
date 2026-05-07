---
title: Diseño y documentación del sistema LTI ATS (LTI-FF)
date: 2026-05-07
priority: High
status: [ ] Open
estimated_effort: 1-2 días (6-10h efectivas)
tags: [design, documentation, product, architecture, ats, lean-canvas, c4, mermaid, uml]
---

# Diseño y documentación del sistema LTI ATS (LTI-FF)

## Role
**Owner:** Facundo Ferrari (FF) — actuando con doble sombrero: Product Manager (definición de valor, casos de uso, modelo de negocio) y Software Architect senior (modelo de datos, diseño de alto nivel, C4).

**Competencias requeridas:**
- Product discovery: Lean Canvas, propuesta de valor, análisis competitivo del mercado ATS (Greenhouse, Workday, Lever, Bullhorn, Teamtailor).
- Modelado UML / C4 / ERD.
- Mermaid (sintaxis para use-case, sequence, ER, C4 con `C4Context`/`C4Container`/`C4Component`).
- Familiaridad con dominios HR/recruiting (pipeline de candidatos, funnel, ATS-CRM, integraciones con job boards y calendarios).

**Stakeholders:** evaluador del bootcamp AI4Devs (audiencia primaria), futuro equipo técnico LTI (audiencia secundaria — el documento debe servir como onboarding técnico inicial).

## Context
- **Proyecto:** ejercicio AI4Devs-design-1 (rama `feat/design-ats-lti`). Repo limpio salvo `001-US.md` (user story en español) y `ReadMe.md` (consigna bilingüe).
- **Producto:** LTI, startup que construye "el ATS del futuro". Greenfield total — no hay código, solo brief de PM.
- **Diferenciadores estratégicos a destacar** (extraídos del US):
  1. Eficiencia operativa para HR (reducir time-to-hire, automatizar scoring/screening).
  2. Colaboración en tiempo real reclutador ↔ hiring manager (comentarios, ratings, decisiones compartidas, presencia).
  3. Automatizaciones (workflows, triggers de pipeline, comunicaciones, scheduling).
  4. Asistencia de IA (resumen de CVs, matching candidato↔puesto, generación de job descriptions, redacción de feedback, detección de sesgo).
- **Entregable final:** un único `.md` (`LTI-FF.md`) dentro de carpeta `lti/LTI-FF/` + `prompts.md` con los prompts utilizados.
- **Restricciones técnicas:**
  - Mermaid como lenguaje de diagramas (renderiza en GitHub).
  - Idioma: español por defecto (US escrita en español); términos técnicos en inglés cuando sea idiomático (e.g., "pipeline", "stage", "sourcing", "kanban", "webhook").
  - Ubicación exacta: `lti/LTI-FF/LTI-FF.md` y `lti/LTI-FF/prompts.md` (verificar que la consigna del ReadMe pida la subcarpeta `lti/`; si no, ubicar en raíz `LTI-FF/`).
- **Estado actual:** solo se entrega la **estructura de tracking** y un **outline** del documento final. El contenido completo se redacta en una iteración posterior.

## Instructions

### Fase 0 — Preparación (15 min)
- [ ] Confirmar ruta destino: `lti/LTI-FF/` vs `LTI-FF/` raíz (releer `ReadMe.md` y `001-US.md`).
- [ ] Crear carpeta `LTI-FF/` con archivos vacíos `LTI-FF.md` y `prompts.md`.
- [ ] Verificar que Mermaid renderiza en GitHub para `C4Container` (fallback: `flowchart` con estilo C4 si el renderer falla).

### Fase 1 — Investigación y propuesta de valor (1-1.5h)
- [ ] Benchmark rápido (5-7 competidores): Greenhouse, Lever, Workday Recruiting, Teamtailor, Ashby, Bullhorn, Recruitee. Anotar 1 fortaleza y 1 gap por cada uno.
- [ ] Definir propuesta de valor LTI en 1 párrafo + bullet list de 4-6 ventajas competitivas anclando los 4 pilares (eficiencia HR, colaboración real-time, automatización, IA).
- [ ] Identificar 6-8 funciones principales (job posting & multi-board distribution, candidate sourcing, pipeline kanban, AI screening, real-time collab, interview scheduling, analytics/reporting, compliance/GDPR).
- [ ] **Acceptance:** sección 1 redactada, lectura en <3 min, diferenciadores explícitos.

### Fase 2 — Lean Canvas (45 min)
- [ ] Completar 9 bloques: Problem, Customer Segments, Unique Value Proposition, Solution, Channels, Revenue Streams, Cost Structure, Key Metrics, Unfair Advantage.
- [ ] Diagramar como tabla Mermaid (`flowchart` con subgraphs) o tabla markdown 3x3 (recomendado: tabla markdown — más legible que Mermaid forzado).
- [ ] **Acceptance:** los 9 bloques completos, cada uno con 2-4 bullets concretos y medibles donde aplique (e.g., métricas con KPI y target).

### Fase 3 — Casos de uso (1.5h)
Seleccionar 3 casos que cubran los pilares diferenciadores:
- [ ] **UC-1: Publicar oferta y distribuirla a múltiples job boards** (eficiencia + automatización). Diagrama: Mermaid `sequenceDiagram` (Recruiter → ATS → Job Boards API → Candidate).
- [ ] **UC-2: Screening asistido por IA + colaboración recruiter↔manager** (IA + real-time collab). Diagrama: Mermaid `sequenceDiagram` con AI Service y notificaciones WebSocket.
- [ ] **UC-3: Programar entrevista con disponibilidad sincronizada** (automatización + colaboración). Diagrama: Mermaid `sequenceDiagram` con Calendar Integration.
- [ ] Cada UC: actor primario, precondición, flujo principal (numerado), flujos alternativos, postcondición, diagrama.
- [ ] **Acceptance:** 3 UC con diagrama renderizable, flujos numerados, alternativos cubiertos.

### Fase 4 — Modelo de datos (1h)
- [ ] Identificar entidades core: `Company`, `User` (con roles `Recruiter`/`HiringManager`/`Admin`), `Job`, `Candidate`, `Application`, `Stage`, `Interview`, `Evaluation`/`Feedback`, `Comment`, `Attachment` (CV), `Tag`, `Source` (job board), `Notification`.
- [ ] Para cada entidad: atributos con tipo (`UUID`, `String`, `Text`, `Enum`, `Timestamp`, `JSONB`, `Decimal`).
- [ ] Relaciones explícitas con cardinalidad (1:N, N:M con tablas pivote).
- [ ] Diagrama Mermaid `erDiagram`.
- [ ] **Acceptance:** mínimo 10 entidades, todas con PK/FK identificadas, ER diagrama renderiza.

### Fase 5 — Diseño de alto nivel (1h)
- [ ] Prosa: arquitectura propuesta (modular monolith con vista a microservicios, o microservicios desde el inicio — justificar). Stack tentativo: frontend SPA (React/Next), backend (Node/NestJS o Python/FastAPI), DB (PostgreSQL), cache (Redis), search (Elasticsearch/Meilisearch), real-time (WebSocket/Pusher), AI (OpenAI/Anthropic API + vector DB), object storage (S3), queue (SQS/RabbitMQ).
- [ ] Diagrama Mermaid `flowchart LR` mostrando: clientes (web, mobile), API gateway, servicios principales, DB, cache, queue, integraciones externas (job boards, email, calendar, AI provider).
- [ ] Mencionar cross-cutting concerns: auth (OAuth2/SSO), multi-tenancy, observabilidad, GDPR/data residency.
- [ ] **Acceptance:** prosa explica decisiones, diagrama coherente con prosa, integraciones externas identificadas.

### Fase 6 — Diagrama C4 profundizando un componente (1h)
- [ ] **Componente recomendado: AI Screening Service** (máximo diferenciador + más interesante de descomponer). Alternativas: Real-time Collaboration Service, Job Distribution Service.
- [ ] Diagramar 2 niveles:
  1. **C4 Container** (sistema completo): Mermaid `C4Container`.
  2. **C4 Component** (zoom al AI Screening Service): subcomponentes — `CV Parser`, `Embedding Generator`, `Match Scorer`, `Bias Detector`, `Prompt Orchestrator`, `Vector Store Adapter`, `LLM Client`.
- [ ] Mostrar relaciones e integraciones con DB principal, vector DB, queue, LLM provider.
- [ ] **Acceptance:** ambos niveles renderizables; el zoom Component justifica por qué se eligió ese servicio.

### Fase 7 — Ensamblaje y prompts.md (45 min)
- [ ] Ensamblar `LTI-FF.md` siguiendo el outline (ver abajo).
- [ ] Tabla de contenidos al inicio con anclas.
- [ ] Redactar `prompts.md` con: prompt de contexto inicial, prompt por sección, modelo usado (Claude Opus 4.7), iteraciones notables.
- [ ] Revisar render Mermaid en GitHub (push a rama y verificar visualmente).
- [ ] **Acceptance:** ambos archivos commiteados, diagramas renderizan, sin secciones TODO.

## Tone & Style
- **Idioma:** español neutro profesional; términos técnicos en inglés sin traducir cuando es idiomático.
- **Densidad:** documento de producto/arquitectura, no marketing. Bullets y tablas sobre prosa larga. Cada sección debe poder leerse de forma autónoma.
- **Diagramas:** preferir Mermaid; si un diagrama no rinde por limitaciones del renderer (C4 a veces falla), proveer fallback `flowchart` con estilo. Nombres de nodos en inglés (consistencia con identificadores técnicos), labels descriptivos en español.
- **Decisiones de arquitectura:** justificar siempre con 1 línea de "por qué" — evitar laundry list de buzzwords.
- **Sin emojis.**

## Expected Output

### Estructura de archivos
```
LTI-FF/
├── LTI-FF.md       # documento principal (todas las secciones)
└── prompts.md      # prompts utilizados con Claude
```

### Outline propuesto de `LTI-FF.md`
```markdown
# LTI - Applicant Tracking System (Diseño v0.1)
> Autor: Facundo Ferrari (FF) · Fecha: 2026-05-07

## Tabla de contenidos
1. Descripción y propuesta de valor
2. Funciones principales
3. Lean Canvas
4. Casos de uso principales
   4.1 UC-1 Publicar y distribuir oferta
   4.2 UC-2 Screening con IA + colaboración
   4.3 UC-3 Programación de entrevistas
5. Modelo de datos
6. Diseño de alto nivel
7. Diagrama C4 — zoom al AI Screening Service
8. Anexos / decisiones abiertas

## 1. Descripción y propuesta de valor
   - Qué es LTI (1 párrafo)
   - Problema que resuelve
   - Ventajas competitivas (bullets)

## 2. Funciones principales
   - 6-8 funciones con 1-2 líneas cada una

## 3. Lean Canvas
   - Tabla 3x3 con los 9 bloques

## 4. Casos de uso
   - Por cada UC: actor, precondición, flujo, alternativos, postcondición, diagrama Mermaid sequence

## 5. Modelo de datos
   - Lista de entidades con atributos tipados
   - Diagrama Mermaid erDiagram
   - Notas sobre multi-tenancy y soft deletes

## 6. Diseño de alto nivel
   - Prosa arquitectónica
   - Diagrama Mermaid flowchart
   - Stack tecnológico justificado
   - Cross-cutting concerns

## 7. Diagrama C4 — AI Screening Service
   - 7.1 C4 Container (sistema completo)
   - 7.2 C4 Component (AI Screening Service en detalle)
   - Justificación de la elección

## 8. Anexos
   - Decisiones abiertas / supuestos
   - Roadmap inicial sugerido
```

### Checklist de aceptación final
- [ ] `LTI-FF/LTI-FF.md` existe, > 600 líneas estimadas, todas las secciones del outline.
- [ ] Lean Canvas con 9 bloques completos.
- [ ] 3 casos de uso con diagrama Mermaid renderizable cada uno.
- [ ] Modelo de datos: ≥10 entidades, atributos tipados, ER diagrama.
- [ ] Diseño alto nivel: prosa + diagrama coherentes.
- [ ] C4: 2 niveles (Container + Component), componente elegido y justificado.
- [ ] Diagramas renderizan en GitHub (verificación visual post-push).
- [ ] `prompts.md` con prompts trazables por sección.
- [ ] Sin TODOs, sin placeholders, sin Lorem.
- [ ] Spell-check español + términos técnicos consistentes.

### Orden recomendado de ejecución
1. Fase 0 → 1 → 2 (bloque producto, ~2h)
2. Fase 4 → 5 → 6 (bloque arquitectura, ~3h) — hacer primero modelo de datos porque ancla todo lo demás
3. Fase 3 (casos de uso, ~1.5h) — después de arquitectura para que los UC referencien componentes reales
4. Fase 7 (ensamblaje, ~45min)

**Nota de estrategia:** alternar este orden vs el del entregable es deliberado — facilita coherencia técnica. El documento final mantiene el orden del entregable.
