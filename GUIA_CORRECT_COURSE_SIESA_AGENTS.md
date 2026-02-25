# Guía Completa: Correct Course en Siesa-Agents

## ¿Qué es Correct Course?

**Correct Course** es el workflow de gestión de cambios durante la ejecución de un sprint. Se activa cuando surge un problema significativo que obliga a modificar el rumbo del proyecto: un nuevo requerimiento, una limitación técnica descubierta, un malentendido de requisitos originales, o un pivote estratégico.

**Cuándo usarlo:** Solo para cambios SIGNIFICATIVOS que afectan la dirección del proyecto. No para ajustes menores del día a día.

---

## Cómo activarlo

Desde Claude Code, ejecutar:

```
/correct-course
```

O bien activarlo como skill desde el menú de workflows.

---

## Documentos de entrada requeridos

El workflow busca automáticamente (en `_bmad-output/planning-artifacts/`):

| Documento | Descripción | Obligatorio |
|-----------|-------------|-------------|
| PRD | Requisitos del producto | Sí |
| Epics y Stories | Listado completo de épicas | Sí |
| Architecture | Decisiones de arquitectura | Sí |
| UX Design | Especificaciones de UI/UX | Solo si hay cambios de interfaz |
| Tech Spec | Especificación técnica | Opcional |

Si algún documento clave no está disponible, el workflow se detiene y solicita acceso.

---

## Salida generada

**Archivo:** `_bmad-output/implementation-artifacts/sprint-change-proposal-{fecha}.md`

Contiene el **Sprint Change Proposal** con 5 secciones:
1. Resumen del problema
2. Análisis de impacto
3. Enfoque recomendado
4. Propuestas de cambio detalladas (formato antes/después)
5. Plan de handoff para implementación

---

## Flujo paso a paso

### Paso 1 — Inicializar el cambio

El workflow pregunta:
- ¿Qué problema o cambio específico requiere navegación?
- ¿Están disponibles PRD, Epics, Architecture, UI/UX?
- ¿Modo de trabajo preferido?
  - **Incremental** (recomendado): Refinar cada cambio en colaboración
  - **Batch**: Presentar todos los cambios juntos al final

> Si el trigger del cambio no está claro → **HALT**: no puede continuar sin entender qué ocurrió.

---

### Paso 0.5 — Descubrir y cargar documentos

El sistema escanea automáticamente los patrones de archivos configurados. Reporta qué encontró:

```
✓ Cargado {prd_content} desde 5 archivos: prd/index.md, prd/requirements.md, ...
✓ Cargado {architecture_content} desde: Architecture.md
○ No se encontraron archivos ux_design
```

---

### Paso 2 — Ejecutar el Checklist de Análisis

Se trabaja interactivamente el checklist completo. Cada ítem se marca como:

- `[x]` Done — completado
- `[N/A]` Skip — no aplica
- `[!]` Action-needed — requiere atención

**Sección 1: Entender el trigger y contexto**
- `1.1` Identificar la story que reveló el problema (ID + descripción)
- `1.2` Definir el problema con precisión y categorizarlo:
  - Limitación técnica descubierta en implementación
  - Nuevo requerimiento de stakeholders
  - Malentendido de requisitos originales
  - Pivote estratégico o cambio de mercado
  - Enfoque fallido que requiere solución diferente
- `1.3` Reunir evidencia concreta (errores, feedback, restricciones técnicas)

> Sin trigger claro o sin evidencia → **HALT**

**Sección 2: Evaluación de impacto en Epics**
- `2.1` ¿Puede completarse el epic actual como se planeó? Si no, ¿qué modificaciones?
- `2.2` Determinar cambios requeridos a nivel de epic:
  - Modificar scope o criterios de aceptación
  - Agregar nuevo epic
  - Remover o diferir epic
  - Redefinir completamente el epic
- `2.3` Revisar todos los epics restantes en busca de impactos y dependencias
- `2.4` ¿El problema invalida futuros epics o requiere crear nuevos?
- `2.5` ¿Debe cambiar el orden o prioridad de los epics?

**Sección 3: Análisis de conflictos en artefactos**
- `3.1` **PRD:** ¿Conflicta con objetivos centrales? ¿El MVP sigue siendo alcanzable?
- `3.2` **Architecture:** Revisar componentes, patrones, stack tecnológico, modelos de datos, APIs, integraciones
- `3.3` **UI/UX:** Impacto en componentes, flujos, wireframes, patrones de interacción, accesibilidad
- `3.4` **Otros artefactos:** Scripts de deploy, IaC, monitoreo, testing, CI/CD, documentación

**Sección 4: Evaluación de caminos a seguir**
- `4.1` Evaluar **Opción 1: Ajuste Directo**
  - ¿Puede resolverse modificando stories existentes o agregando nuevas?
  - ¿Mantiene timeline y scope?
  - Esfuerzo: [Alto/Medio/Bajo] / Riesgo: [Alto/Medio/Bajo]
- `4.2` Evaluar **Opción 2: Rollback Potencial**
  - ¿Revertir stories completadas simplificaría resolver el problema?
  - ¿Qué stories revertir? ¿Vale el esfuerzo?
  - Esfuerzo: [Alto/Medio/Bajo] / Riesgo: [Alto/Medio/Bajo]
- `4.3` Evaluar **Opción 3: Revisión del MVP del PRD**
  - ¿El MVP original sigue siendo alcanzable?
  - ¿Hay que reducir o redefinir scope?
  - ¿Qué se difiere a post-MVP?
  - Esfuerzo: [Alto/Medio/Bajo] / Riesgo: [Alto/Medio/Bajo]
- `4.4` **Seleccionar el camino recomendado** con justificación considerando:
  - Esfuerzo e impacto en timeline
  - Riesgo técnico y complejidad
  - Impacto en moral y momentum del equipo
  - Sostenibilidad a largo plazo
  - Expectativas de stakeholders y valor de negocio

**Sección 5: Componentes del Sprint Change Proposal**
- `5.1` Crear resumen del problema identificado
- `5.2` Documentar impacto en epics y necesidades de ajuste de artefactos
- `5.3` Presentar camino recomendado con justificación completa
- `5.4` Definir impacto en MVP del PRD y plan de acción de alto nivel
- `5.5` Establecer plan de handoff de agentes

**Sección 6: Revisión final y handoff**
- `6.1` Verificar completitud del checklist
- `6.2` Verificar consistencia y claridad del Sprint Change Proposal
- `6.3` Obtener aprobación explícita del usuario
- `6.4` Confirmar próximos pasos y plan de handoff

> Sin aprobación del usuario o responsabilidades de handoff poco claras → **HALT**

---

### Paso 3 — Redactar propuestas de cambio específicas

Para cada artefacto afectado, el workflow genera propuestas en formato **antes → después**.

**Formato para Stories:**
```
Story: [STORY-123] Nombre de la Story
Sección: Criterios de Aceptación

ANTES:
- El usuario puede iniciar sesión con email/contraseña

DESPUÉS:
- El usuario puede iniciar sesión con email/contraseña
- El usuario puede habilitar 2FA via app autenticadora

Justificación: Requerimiento de seguridad identificado durante implementación
```

**Para PRD:** Secciones exactas, contenido actual vs. cambios propuestos, impacto en scope MVP.

**Para Architecture:** Componentes afectados, cambios en diagramas, efectos de propagación.

**Para UI/UX:** Pantallas o componentes referenciados, cambios en wireframes/flujos, impacto en experiencia de usuario.

En modo **Incremental**: cada propuesta se presenta individualmente con opciones:
- `[a]` Aprobar
- `[e]` Editar
- `[s]` Saltar

En modo **Batch**: todas las propuestas se presentan juntas al final.

---

### Paso 4 — Generar el Sprint Change Proposal

Documento final con 5 secciones:

**Sección 1 — Resumen del Problema**
- Declaración clara del problema que triggereó el cambio
- Contexto: cuándo/cómo se descubrió
- Evidencia o ejemplos

**Sección 2 — Análisis de Impacto**
- Impacto en epics (cuáles y cómo)
- Impacto en stories (actuales y futuras)
- Conflictos en artefactos (PRD, Architecture, UI/UX)
- Impacto técnico (código, infraestructura, deploy)

**Sección 3 — Enfoque Recomendado**
- Camino elegido: Ajuste Directo / Rollback / Revisión MVP
- Justificación clara
- Estimación de esfuerzo, evaluación de riesgo, impacto en timeline

**Sección 4 — Propuestas de Cambio Detalladas**
- Todas las propuestas refinadas del Paso 3
- Agrupadas por tipo de artefacto
- Cada cambio con antes/después y justificación

**Sección 5 — Handoff para Implementación**
- Clasificación del scope del cambio:
  - **Menor**: Implementación directa por el equipo de dev
  - **Moderado**: Reorganización de backlog (PO/SM)
  - **Mayor**: Replan fundamental (PM/Arquitecto)
- Destinatarios del handoff y sus responsabilidades
- Criterios de éxito para la implementación

---

### Paso 5 — Finalizar y enrutar para implementación

El workflow pide aprobación explícita:

```
¿Apruebas este Sprint Change Proposal para implementación? (sí / no / revisar)
```

Si **no / revisar** → vuelve al paso 3 (cambios en propuestas) o paso 4 (cambios en estructura del proposal).

Si **sí** → determina el scope y hace el handoff apropiado:

| Scope | Destinatario | Entregables |
|-------|-------------|-------------|
| Menor | Equipo de desarrollo | Propuestas de edición + tareas de implementación |
| Moderado | Product Owner / Scrum Master | Sprint Change Proposal + plan de reorganización de backlog |
| Mayor | Product Manager / Arquitecto | Sprint Change Proposal completo + aviso de escalación |

---

### Paso 6 — Completar el workflow

Resumen final:
- Problema abordado
- Scope del cambio clasificado
- Artefactos modificados
- Enrutado a: [destinatarios]

Entregables confirmados:
- Documento Sprint Change Proposal
- Propuestas de edición con antes/después
- Plan de handoff para implementación

---

## Las 3 opciones de camino a seguir

| Opción | Descripción | Cuándo usarla |
|--------|-------------|---------------|
| **Ajuste Directo** | Modificar/agregar stories dentro del plan existente | El problema es contenido y no afecta la estructura del proyecto |
| **Rollback Potencial** | Revertir stories completadas para simplificar la resolución | Deshacer trabajo reciente simplifica significativamente el problema |
| **Revisión del MVP** | Reducir o redefinir el scope del MVP | El problema pone en duda la viabilidad del MVP original |

También existe la opción **Híbrida** cuando la solución óptima combina elementos de varias opciones.

---

## Los 3 niveles de scope

| Scope | Quién actúa | Ejemplos |
|-------|-------------|----------|
| **Menor** | Dev team directamente | Agregar criterio de aceptación, ajustar subtarea |
| **Moderado** | PO + SM coordinan | Reorganizar backlog, priorizar epics, replanning |
| **Mayor** | PM + Arquitecto involucrados | Cambio de arquitectura, pivot de MVP, nuevo epic estratégico |

---

## Notas importantes de ejecución

- Este workflow es para cambios **SIGNIFICATIVOS** que afectan la dirección del proyecto.
- El trabajo es **colaborativo**: el usuario toma las decisiones finales.
- El análisis debe ser **factual**, no orientado a culpar.
- Tratar los cambios como **oportunidades de mejora**.
- Mantener el **contexto de conversación** durante todo el workflow.
- Comunicación en **Español**, documentos generados en **English** (configuración actual de Siesa-Agents).

---

## Archivos del workflow en el sistema

```
_bmad/bmm/workflows/4-implementation/correct-course/
├── workflow.yaml      # Configuración del workflow (variables, rutas, input patterns)
├── instructions.md    # Pasos del workflow (Steps 1-6)
└── checklist.md       # Checklist de análisis (Secciones 1-6)
```

**Salida:** `_bmad-output/implementation-artifacts/sprint-change-proposal-{fecha}.md`
