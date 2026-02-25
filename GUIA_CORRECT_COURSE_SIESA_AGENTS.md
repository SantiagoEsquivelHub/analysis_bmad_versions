# Correct Course en Siesa-Agents

## ¿Qué es?

Es el workflow para manejar cambios significativos que aparecen durante la ejecución de un sprint. Se usa cuando algo obliga a cambiar el rumbo del proyecto: una limitación técnica descubierta, un nuevo requerimiento, un malentendido de requisitos, o un pivote estratégico.

**No es para ajustes menores del día a día.** Es para cuando el cambio afecta la dirección del proyecto.

---

## Cómo activarlo

```
/correct-course
```

---

## Qué necesita para funcionar

Necesita acceso a los documentos del proyecto: PRD, Epics, Architecture y UX Design (este último solo si el cambio afecta la interfaz). Los busca automáticamente en `_bmad-output/planning-artifacts/`.

---

## Qué produce

Un **Sprint Change Proposal** guardado en `_bmad-output/implementation-artifacts/`. Contiene el análisis del problema, su impacto en los artefactos del proyecto, la recomendación de qué hacer, y quién debe ejecutarlo.

---

## Flujo paso a paso

**1. Entender el cambio**
El workflow pregunta qué ocurrió y verifica que los documentos del proyecto estén disponibles. También define el modo de trabajo: Incremental (refinar cada cambio uno a uno) o Batch (ver todo junto al final). Sin un trigger claro, se detiene.

**2. Analizar el impacto**
Revisa sistemáticamente qué se ve afectado: cuáles epics ya no pueden completarse como se planearon, si el PRD o la arquitectura entran en conflicto, y si el MVP sigue siendo alcanzable. También evalúa las tres opciones de solución (ver abajo) para recomendar la mejor.

**3. Proponer los cambios concretos**
Por cada artefacto afectado genera una propuesta en formato antes/después con su justificación. En modo Incremental el usuario aprueba, edita o descarta cada una. En modo Batch las ve todas juntas.

**4. Generar el Sprint Change Proposal**
Consolida todo en un documento: resumen del problema, análisis de impacto, enfoque recomendado, propuestas de cambio, y a quién se le entrega para implementar.

**5. Aprobar y enrutar**
El usuario aprueba el documento. Si hay correcciones vuelve al paso anterior. Si se aprueba, se clasifica el scope del cambio y se entrega a quien corresponda.

---

## Cómo funciona en general

Es una conversación guiada y colaborativa. El workflow estructura el análisis, el usuario toma las decisiones.

---

## Las 3 opciones de solución

Cuando el análisis termina, el workflow evalúa tres caminos posibles:

| Opción | Cuándo aplicar |
|--------|----------------|
| **Ajuste Directo** | El problema es contenido — se resuelve modificando o agregando stories sin tocar la estructura del proyecto |
| **Rollback** | Deshacer trabajo reciente simplifica significativamente la solución |
| **Revisión del MVP** | El problema pone en duda si el MVP original sigue siendo viable |

También puede ser una combinación de las anteriores.

---

## A quién va el resultado

Depende del tamaño del cambio:

| Scope | Quién actúa |
|-------|-------------|
| **Menor** | Dev team lo implementa directamente |
| **Moderado** | PO y SM coordinan la reorganización del backlog |
| **Mayor** | PM y Arquitecto toman decisiones estratégicas |
