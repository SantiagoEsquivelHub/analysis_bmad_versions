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
El workflow pregunta qué ocurrió y pide evidencia concreta: errores, feedback de stakeholders, restricciones descubiertas. También verifica que los documentos del proyecto estén disponibles y define el modo de trabajo: Incremental (refinar cada cambio uno a uno) o Batch (ver todo junto al final). Sin un trigger claro o sin evidencia, se detiene.

**2. Analizar el impacto**
Revisa en orden: qué epics ya no pueden completarse como se planearon, si alguno debe modificarse, diferirse o redefinirse, y si el cambio crea dependencias nuevas entre epics. Luego revisa los artefactos: si el PRD entra en conflicto, si la arquitectura necesita ajustes (componentes, APIs, modelos de datos), si la UX se ve afectada, y si el MVP sigue siendo alcanzable. Con todo eso evalúa las tres opciones de solución y recomienda la mejor.

**3. Proponer los cambios concretos**
Por cada artefacto afectado genera una propuesta en formato antes/después con su justificación. Cubre stories, PRD, arquitectura y UX según aplique. En modo Incremental el usuario aprueba, edita o descarta cada una antes de continuar. En modo Batch las ve todas juntas al final.

**4. Generar el Sprint Change Proposal**
Consolida todo en un documento: resumen del problema con su contexto y evidencia, análisis de impacto, enfoque recomendado con su justificación y estimación de riesgo, todas las propuestas de cambio, y el plan de handoff con criterios de éxito.

**5. Aprobar y enrutar**
El usuario revisa y aprueba el documento. Si hay correcciones vuelve al paso que corresponda. Una vez aprobado, se clasifica el scope del cambio (menor, moderado, mayor) y se entrega a quien debe ejecutarlo.

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
