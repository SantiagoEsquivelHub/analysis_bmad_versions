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

## Cómo funciona

El workflow es una conversación guiada. Primero entiende el problema, luego analiza el impacto en epics y artefactos (PRD, arquitectura, UX), luego propone los cambios concretos con formato antes/después, y finalmente genera el documento y lo enruta a quien corresponda.

Todo es colaborativo: el usuario toma las decisiones, el workflow estructura el análisis.

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
