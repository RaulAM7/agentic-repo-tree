# scaffolding-guidelines.md
Guía de scaffolding para **create-agentic-repo** (Harness-first, context-lean, tri-runner)

> Objetivo: que el scaffolder cree repos “agentic” repetibles para trabajar desde CLI con **Claude / Codex / Antigravity**, minimizando **context bloat** y maximizando **convergencia** (Seed → Distill → Spec → Ship → QA).

---

## 1) Principios (no negociables)

### 1.1 Seed → Distill → Operate
- **Seed (contexto riquísimo 1)**: el humano vuelca TODO lo previo en `00_inbox/` sin ordenar.
- **Distill (contexto riquísimo 2)**: la IA destila ese batiburrillo en un set pequeño, usable y estándar en `02_context/` y prepara `03_specs/`.
- **Operate**: el trabajo diario se guía por una **spec** (SDD light) y por el **harness** (rules + taskflow + skills).

### 1.2 Context engineering (anti “context rot”)
Diseñamos para un recurso finito: **context window**.
- “Always-on” debe ser **mínimo**.
- El conocimiento pesado vive en **skills** y se carga **on-demand**.
- Preferimos **artifacts** (archivos) a conversación larga.
- Evitamos “barroquismo”: procesos cortos, densidad alta.

### 1.3 Harness-first
El repo no es “un árbol bonito”; es un **sistema de control**:
- Qué ve el agente siempre (Rules).
- Qué ve solo cuando toca (Skills).
- Cómo se valida (QA gate).
- Cómo se encapsula la intención (Spec).

### 1.4 Abstract-first → contextualized later
El scaffold es **abstracto** y sirve para:
- repos 100% markdown
- o evolucionar a mini app interna (añadiendo `src/` más tarde)
Sin sobre-ingeniería inicial.

### 1.5 Marketer-friendly
- El sistema debe funcionar para proyectos de marketing / BD / ops.
- Specs y outputs priorizan claridad operativa y entregables (emails, propuestas, parrillas, research, landings).
- No asumimos tests/linting sofisticados; el feedback loop puede ser checklist.

---

## 2) Qué genera el scaffolder (visión global)

El scaffolder crea un proyecto con 5 zonas:

1) **00_inbox/**  
   Entrada “cruda” y completa del humano.

2) **01_harness/**  
   Always-on, lean, estable. Es “el manual del entorno”.

3) **02_context/**  
   Contexto destilado y pequeño (la “caja blanca” usable).

4) **03_specs/**  
   Spec-driven work (SDD light). “Qué hacemos ahora”.

5) **04_outputs/ + 05_scratch/**  
   Entregables finales y basura controlada.

Además:
- **shared/**: assets compartidos por Claude/Codex/Antigravity (skills + agents).
- **runners/**: adaptadores por tool (cómo ejecutar el flujo en cada CLI).

---

## 3) Detalle por carpeta y por archivo

### 3.1 `00_inbox/` — Contexto riquísimo 1 (batiburrillo)
**Propósito:** bajar la fricción a “capturar” contexto.
- Aquí se suelta TODO: notas, dumps, briefs previos, emails, posts, specs antiguas.
- Regla: **no ordenar**, **no reescribir**, **no limpiar**.

**Archivo clave**
- `00_inbox/README.md`: reglas de uso (drop-only).

---

### 3.2 `01_harness/` — Always-on (mínimo y potente)
**Propósito:** controlar el comportamiento del agente y el loop de trabajo sin gastar tokens de más.

**Archivos**
- `RULES.md`  
  Reglas base que conviene inyectar siempre:
  - anti-alucinación (`Unknown` + preguntas)
  - anti-bloat (no copiar inbox, `02_context` debe caber en 5 min)
  - output discipline (todo en `04_outputs/`)
  - “stop conditions” (QA gate antes de cerrar)
  - naming / rutas / formato de entregables
  **Debe ser corto.** (ideal 20–80 líneas; upper bound ~200–300 si de verdad aporta)

- `TASKFLOW.md`  
  Runbook en 1 página:
  - Seed → Distill → Spec → Ship → QA
  - Incluye prompts copy/paste para el Distill y para escribir spec.
  - Define el QA gate (checklist).
  - Recalca: “1 spec activa a la vez”.

- `SKILLS_INDEX.md`  
  Lista corta (1–2 líneas por skill):
  - qué hace
  - cuándo se usa
  - si es “pesada” (manual) o “ligera”

- `STACK.md` (opcional, recomendado)
  Solo si hay stack de código. Muy corto:
  - runtime/framework (si aplica)
  - convenciones de estructura
  - comandos típicos (build/test/dev)
  Si el proyecto es solo docs, puede quedar en “N/A”.

---

### 3.3 `02_context/` — Contexto riquísimo 2 (destilado)
**Propósito:** reemplazar conversación larga por contexto legible y estable.

**Archivos**
- `BRIEF.md`  
  10–20 líneas. Responde:
  - qué es
  - para quién
  - outcome medible
  - horizonte temporal
  - “qué se considera éxito”

- `FACTS.md`  
  Hechos verificables + fuente.
  - No opinión.
  - Si no hay fuente → marcarlo.

- `CONSTRAINTS.md`  
  Restricciones reales:
  - presupuesto / tiempo
  - limitaciones de stack
  - no-go’s
  - tono / branding si aplica

- `LINKS.md`  
  URLs + 1 línea por qué importan.

- `GLOSSARY.md` (opcional)
  Solo si hay jerga que el agente necesita para no confundirse.

**Regla:** `02_context` debe ser “lectura de 5 minutos”.

---

### 3.4 `03_specs/` — SDD light (por tarea)
**Propósito:** evitar el “chair-to-screen bottleneck” sin escribir una biblia.
Cada tarea relevante vive como **spec**.

**Estructura**
- `now/`  
  Solo 1 spec activa (o muy pocas). Ej: `001_now.md` o `001_<slug>.md`.

- `backlog.md`  
  Lista corta (10–30 items). 1 línea por item.

- `decisions.md`  
  Decisiones tomadas/pendientes, 1 línea.

**Contenido mínimo de una spec (SDD light)**
- Outcome (qué debe existir al final)
- Scope / No-scope
- Inputs disponibles (archivos, links, datos)
- Deliverable exacto (formato y ubicación)
- Acceptance criteria (checklist 8–15 checks)
- Edge cases / riesgos (bullets)
- Open questions (máx 8)

---

### 3.5 `04_outputs/` — Entregables
**Propósito:** una carpeta única para todo lo entregable.
- Nombres recomendados: `YYYY-MM-DD_<tema>_v1.md` (o similar)
- Nada de outputs desperdigados.

---

### 3.6 `05_scratch/` — Basura controlada
**Propósito:** permitir experimentar sin ensuciar contexto.
- aquí van borradores, exploraciones, textos largos, “brain dumps”
- si algo se vuelve estable, se destila a `02_context/` o `03_specs/`

---

## 4) shared/: interoperabilidad Claude / Codex / Antigravity

### 4.1 `shared/skills/` (on-demand)
**Regla:** una skill = un problema concreto.

**Por qué skills**
- Permiten “cargar contexto pesado” solo cuando se invocan.
- Evitan contaminar el contexto always-on.
- Pueden incluir plantillas, ejemplos y pasos.

**Skills base recomendadas**
- `initial_context_building/` (pesada, bootstrap)
  - primera ejecución en proyecto nuevo: lee 00_inbox + harness + docs raíz + contexto existente → llena todos los slots de 02_context + siembra 03_specs + produce gap report
- `distill_context/` (pesada, manual)
  - convierte `00_inbox/` → `02_context/` + prepara `03_specs/`
- `write_spec/` (ligera/media)
  - genera una spec SDD light usando el contexto destilado
- `ship_output/` (ligera)
  - produce el entregable en `04_outputs/` conforme a spec
- `qa_review/` (ligera)
  - checklist y sanity checks

**Contenido típico de una skill**
- `SKILL.md` como entrypoint:
  - propósito
  - cuándo usar
  - inputs esperados
  - outputs exactos
  - pasos
  - anti-patrones
  - ejemplos cortos

### 4.2 `shared/agents/` (roles mínimos)
**Regla:** pocos agentes “tipo rol”; que no se solapen.
- Distiller
- Planner
- Maker
- Reviewer

Cada uno con 10–30 líneas y un “contrato mental” claro.

---

## 5) runners/: adaptadores por tool (sin duplicar el core)

**Propósito:** permitir a cada CLI tool operar el mismo flujo sin reescribir el harness.

Archivos:
- `runners/claude.md`
- `runners/codex.md`
- `runners/antigravity.md`

Contenido:
- cómo iniciar el flujo (dónde leer rules y taskflow)
- cómo invocar skills (si la tool soporta comandos) o cómo pegarlas manualmente
- comandos típicos (ejemplo de “open file”, “run task”)
- 10–30 líneas máximo

---

## 6) Reglas anti-bloat (criterios de diseño del scaffold)

### 6.1 Always-on minimal
- `01_harness` debe ser pequeño.
- Si algo pesa, se mueve a skill.

### 6.2 One spec at a time
- Evitar mega planes.
- Una spec activa reduce drift y tokens.

### 6.3 Archivos como memoria
- La conversación no es el sistema de memoria.
- La verdad vive en `02_context` y `03_specs`.

### 6.4 “Unknown” es feature
- Si falta info, no rellenar con imaginación.
- Marcar Unknown y listar preguntas.

---

## 7) Comportamiento esperado del scaffolder (CLI)

### 7.1 UX del comando
Debe soportar:
- `create-agentic-repo <name>`
- flags:
  - `--template minimal`
  - `--yes` (modo no interactivo)
  - `--no-git`
  - (opcional) `--dry-run`

### 7.2 Copiado y placeholders
- Copiar el template recursivamente.
- Reemplazar `{{PROJECT_NAME}}` en `.md`/`.txt`.
- Crear `.keep` para carpetas vacías.

### 7.3 “Next steps” al final
Imprimir:
1) entrar al folder
2) dropear contexto en `00_inbox`
3) ejecutar `initial-context-building` (ver `shared/skills/initial-context-building/SKILL.md`) para bootstrapping completo, o `distill-context` si el inbox ya tiene contenido y el contexto está iniciado

---

## 8) Criterios de calidad (Definition of Done del scaffolder)

Un scaffold está “bien” si:
- `01_harness/RULES.md` es lean y accionable.
- `TASKFLOW.md` permite operar sin recordar nada.
- La estructura reduce context rot:
  - always-on pequeño
  - skills on-demand
  - spec por tarea
- Un usuario puede crear un repo nuevo y producir un output en 10–20 min solo con markdown.
- El repo no obliga a elegir stack ni herramientas; permite crecer más tarde.

---

## 9) Roadmap (extensiones futuras sin romper la simplicidad)
- Añadir template `app` (Vite/Next) pero mantener harness igual.
- Añadir “hooks” (cuando aplique) para QA gate en CI.
- Añadir `shared/skills/*` para casos frecuentes (landing, outreach, research ICP, etc.).
- Añadir `--from` para clonar templates versionados.

---

## 10) “Copy/paste prompts” (filosofía)
Prompts deben vivir en:
- `01_harness/TASKFLOW.md` (los 2–3 esenciales)
- y/o dentro de cada skill (detalle pesado)

Evitar:
- Prompts gigantes always-on
- Plantillas redundantes
- Reglas genéricas tipo “write clean code”

---

Fin.