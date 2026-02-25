# Guía Experta: El Framework BMAD (Business Model Agent Definition)

Esta guía técnica desglosa la arquitectura, fases y flujos de trabajo del framework BMAD, diseñada para transformar a un desarrollador en un experto en su operación. BMAD es un sistema de orquestación de agentes diseñado para guiar el ciclo de vida completo del desarrollo de software, desde la ideación hasta la documentación final.

## 1. Arquitectura del Sistema

BMAD se organiza en módulos que residen en el directorio `_bmad/`. Para el desarrollo de software, el módulo principal es **BMM**.

### Módulos Principales
*   **BMM (Business Model Management)**: El núcleo del ciclo de vida de desarrollo de software (SDLC). Contiene las 5 fases de ejecución.
*   **BMB (Business Model Builder)**: "Meta-módulo" con herramientas para crear nuevos módulos, agentes y workflows dentro de BMAD.
*   **CIS (Creative Innovation Strategy)**: Módulo especializado en innovación, Design Thinking y storytelling.
*   **CORE**: Utilidades base y funcionalidades compartidas (ej. brainstorming, manejo de tareas).

### Estructura de Archivos Clave
*   `_bmad/bmm/config.yaml`: Archivo maestro de configuración. Define:
    *   `output_folder`: Típicamente `_bmad-output`.
    *   `planning_artifacts`: Donde se guardan PRDs, Arquitectura, Épicas.
    *   `implementation_artifacts`: Donde se guarda el código y estado del sprint.
    *   `user_name` / `communication_language`: Preferencias del usuario.
*   `_bmad/bmm/workflows/{fase}-{nombre}`: Directorios que contienen las definiciones de cada fase.

---

## 2. Punto de Entrada: Inicialización del Proyecto

Antes de comenzar cualquier fase, **siempre** se deben invocar estos dos workflows de gestión:

### `workflow-init` — Inicializar un proyecto nuevo
El punto de partida obligatorio para cualquier proyecto BMM nuevo. Este comando:
1.  **Determina el nivel y tipo** de proyecto (simple, estándar, complejo).
2.  **Crea la ruta de workflow** adecuada según el tipo de proyecto.
3.  **Genera el archivo de estado** YAML que será leído por `workflow-status`.

> **Cuándo usarlo:** Al comenzar un proyecto desde cero. Sin este paso, los agentes no tienen contexto sobre la naturaleza del proyecto ni el camino a seguir.

### `workflow-status` — ¿Qué hago ahora?
Un verificador ligero que responde la pregunta **"¿Cuál es el siguiente paso?"** para cualquier agente. Lee el archivo de estado YAML generado por `workflow-init` y muestra el estado actual del workflow.

> **Cuándo usarlo:** Al retomar un proyecto entre sesiones o cuando no sabes en qué fase estás.

---

## 3. El Ciclo de Vida BMM (Las 5 Fases)

El flujo de trabajo está diseñado secuencialmente. Cada fase produce **artefactos** que sirven de entrada obligatoria para la siguiente.

### Fase 1: Análisis (1-analysis)
**Objetivo:** Definir el "Qué" y validar la viabilidad antes de comprometer recursos.
*   **Workflows Principales:**
    *   `create-product-brief`: Genera una visión de alto nivel del producto.
    *   `research`: Realiza investigación profunda en tres ejes: Mercado, Técnico y Dominio.
*   **Artefactos de Salida:**
    *   `product-brief.md`: Resumen ejecutivo.
    *   Reportes de investigación (Market/Tech/Domain Analysis).

### Fase 2: Planificación (2-plan)
**Objetivo:** Especificar requerimientos detallados y experiencia de usuario.
*   **Workflows Principales:**
    *   `prd` (Product Requirements Document): Define alcance, funcionalides, y criterios de éxito.
    *   `create-ux-design`: Define el sistema de diseño, flujos de usuario (User Journeys) y patrones UX.
*   **Artefactos de Salida:**
    *   `prd.md`: Documento maestro de requerimientos. **Crítico**: Es la fuente de la verdad para fases posteriores.
    *   `ux-design-specification.md`: Especificaciones de diseño.
    *   **Artefactos Visuales (HTML)**: El workflow de UX también genera visualizadores interactivos como `ux-color-themes.html` y `ux-design-directions.html` para validar paletas y estilos en el navegador.

### Fase 3: Solución (3-solutioning)
**Objetivo:** Definir el "Cómo" técnico y desglosar el trabajo en unidades ejecutables.
*   **Workflows Principales:**
    *   `create-architecture`: Toma el PRD y define el stack técnico, patrones de base de datos, API y estructura de código.
    *   `create-epics-and-stories`: Divide el PRD y la Arquitectura en Épicas y Historias de Usuario.
    *   `check-implementation-readiness`: "Quality Gate". Verifica que el PRD, Arquitectura y Épicas sean coherentes antes de permitir código.
*   **Artefactos de Salida:**
    *   `architecture.md`: Decisiones arquitectónicas (ADRs).
    *   `epics.md` (o carpeta `epics/`): Lista maestra de funcionalidades a construir.
    *   `readiness-report.md`: Aprobación para iniciar desarrollo.

### Fase 4: Implementación (4-implementation)
**Objetivo:** Construcción iterativa del software. Es la fase más compleja con múltiples sub-procesos.
*   **Gestión del Sprint:**
    *   `sprint-planning`: Lee todas las épicas/historias y genera el archivo de estado.
    *   `sprint-status`: Escanea el proyecto para actualizar el estado real del trabajo.
    *   **Artefacto Crítico**: `sprint-status.yaml`. Actúa como la "base de datos" del progreso.
*   **Ejecución:**
    *   `create-story`: Prepara el archivo de una historia específica (`stories/epic-story-id.md`) con contexto técnico detallado.
    *   `dev-story`: El workflow "worker". Implementa el código, escribe tests y verifica cumplimiento.
    *   `code-review`: Revisión adversarial del código generado.
*   **Flujo de Estado de una Historia:**
    `Backlog` -> `Ready for Dev` (archivo creado) -> `In Progress` -> `Review` -> `Done`.

### Fase 5: Documentación (5-documentation)
**Objetivo:** Generar entregables finales y manuales.
*   **Workflows Principales:**
    *   `create-user-guide`: Genera manuales de usuario (en MD/PDF) basándose en las épicas implementadas.
    *   `document-project`: Genera documentación técnica para mantenimiento (Project Context).

---

## 4. Conceptos Técnicos Avanzados

Para dominar BMAD, debes entender cómo opera bajo el capó:

### Step-File Architecture
BMAD no carga instrucciones gigantescas. Usa "Just-In-Time Loading":
1.  Cada workflow tiene una carpeta `steps/` con archivos Markdown pequeños (`step-01-init.md`, `step-02-analysis.md`, etc.).
2.  El agente lee **solo el paso actual**.
3.  Esto maximiza la ventana de contexto y mantiene al agente enfocado en una tarea atómica a la vez.

### State Tracking & Frontmatter
Los workflows mantienen estado persistente escribiendo en el encabezado YAML (frontmatter) de los documentos de salida:
```yaml
---
stepsCompleted: ['init', 'discovery', 'analysis']
currentPhase: 'implementation'
---
```
Esto permite pausar y reanudar workflows complejos sin perder el contexto.

### Roles de Agentes (Colaboración entre Pares)
BMAD asigna "personas" a los agentes. No son asistentes serviles, son **pares expertos**:
*   **PM (Product Manager)**: Visión, alcance.
*   **Architect**: Decisiones técnicas, patrones, seguridad.
*   **Dev**: Implementación, testing.
*   **TEA (Test Engineering Architect)**: Calidad, trazabilidad.
*   **UX**: Diseño, experiencia.

## 5. Rutas Críticas
Si necesitas navegar manualmente:
*   **Definiciones de Workflows**: `_bmad/bmm/workflows/`
*   **Configuración Local**: `_bmad/bmm/config.yaml`
*   **Salida (Default)**: `_bmad-output/` (al nivel raíz del proyecto)

---
*Generado por Claude Code - Experto BMAD*
