# Módulo TEA (Testing / QA / Validación) en BMAD-METHOD: Estado del conocimiento actual

---

## 1. Resumen Ejecutivo

El módulo TEA en BMAD ha pasado de ser reactivo y opaco a ser **preventivo, automatizable y auditable**. Los avances más importantes son: la **atomización de `/validate-prd`** como workflow independiente invocable directamente, que por primera vez habilita TDD real sobre workflows individuales sin necesidad de invocar el agente completo; la **extensión del Layer 1 Validator a archivos CSV**, que cierra el gap crítico donde los errores de referencias rotas solo se detectaban en runtime; la **instalación no-interactiva** mediante flags CLI, prerequisito indispensable para cualquier pipeline de pruebas automatizado; y la **revisión dual de código** (Augment Code + CodeRabbit adversarial), que eleva los estándares de QA sobre el propio tooling de BMAD. El resultado es un módulo TEA con soporte real para CI/CD, validación estática multi-formato y testing granular por workflow.

---

## 2. Inventario de Capacidades TEA

---

### Capacidad 1: `/validate-prd` como workflow autónomo invocable

| Campo | Detalle |
|---|---|
| **Qué es** | `validate-prd` expuesto como slash command autónomo directo, separado del ciclo create/edit. |
| **Qué hacía antes** | Era un paso interno dentro de un workflow unificado. No podía invocarse de forma aislada; requería ejecutar el ciclo completo create→edit→validate mediante un agente orquestador. |
| **Estado actual** | `/validate-prd` es un workflow de primera clase. Se puede invocar directamente, en cualquier momento, sin dependencia del ciclo completo ni de un agente intermediario. |
| **Impacto en TEA** | **Crítico.** Habilita TDD real sobre documentación de producto: validar sin crear ni editar. Permite ejecutar pruebas de validación como paso aislado en un pipeline CI/CD: `bmad /validate-prd --input=prd.md`. Antes esto requería un agente custom completo. |

---

### Capacidad 2: Testing aislado por workflow (arquitectura microworkflows)

| Campo | Detalle |
|---|---|
| **Qué es** | Todos los workflows existen como archivos `workflow-*.md` independientes y autocontenidos. |
| **Qué hacía antes** | Testear un workflow requería invocar el agente completo, con todo su overhead de contexto, dependencias y estado compartido. Testing aislado era arquitecturalmente imposible. |
| **Estado actual** | Cada workflow es un módulo autocontenido testeable de forma independiente. *"Testing de workflows: Directo (test workflows aisladamente) → Permite TDD sobre workflows individuales."* |
| **Impacto en TEA** | **Alto.** Reduce radicalmente el coste de pruebas: un test de `/technical-research` no arrastra el contexto de `/create-prd`. Permite matrices de pruebas paralelas: `Promise.all([exec('/domain-research'), exec('/market-research'), exec('/technical-research')])`. La granularidad hace que los errores sean localizables en un workflow específico, no en un agente opaco. |

---

### Capacidad 3: Validación Layer 1 extendida a archivos CSV

| Campo | Detalle |
|---|---|
| **Qué es** | El Layer 1 Validator escanea archivos `.csv` en busca de referencias rotas a workflows. Cobertura verificada: **501 referencias en 212 archivos CSV**. |
| **Qué hacía antes** | El Layer 1 solo validaba archivos Markdown. Los CSVs usados como configuración de workflows quedaban fuera del análisis. Una referencia rota en un CSV se detectaba únicamente en runtime, cuando el workflow ya había fallado. |
| **Estado actual** | Validación estática preventiva multi-formato. Antes del primer deploy, el sistema detecta referencias inválidas en CSVs: `✓ 501 referencias validadas / ✓ 212 archivos CSV procesados`. |
| **Impacto en TEA** | **Crítico.** Transforma la detección de errores de **reactive (runtime)** a **proactive (pre-deploy)**. Directamente usable como gate en pre-commit hooks o en pipelines CI/CD. Para workflows con configuración data-driven (ej. compliance en fintech, configuraciones de steps en CSV), elimina una clase entera de fallos silenciosos. Los agentes autónomos que consumen estos workflows no fallarán por referencias inválidas no detectadas. |

---

### Capacidad 4: Enrutamiento multi-step confiable (`stepsCompleted`)

| Campo | Detalle |
|---|---|
| **Qué es** | El enrutamiento entre steps de workflows multi-paso (ej. step-05→step-06 en `/technical-research`) y el tracking de `stepsCompleted` funcionan de forma correcta y verificable. |
| **Qué hacía antes** | Existía un bug de enrutamiento donde step-05 no pasaba correctamente a step-06 en `/technical-research`. Los valores de `stepsCompleted` eran incorrectos, rompiendo el tracking de progreso. |
| **Estado actual** | El flujo completa end-to-end sin intervención. `stepsCompleted` refleja el estado real de ejecución en cada punto del workflow. |
| **Impacto en TEA** | **Alto.** Las pruebas de regresión end-to-end sobre workflows multi-step son ahora confiables. El tracking correcto de `stepsCompleted` permite hacer assertions sobre el estado de progreso en tests de integración: se puede verificar que un workflow llegó exactamente al paso esperado. |

---

### Capacidad 5: Revisión dual de código (Augment Code + CodeRabbit adversarial)

| Campo | Detalle |
|---|---|
| **Qué es** | Sistema de doble revisión sobre el propio código de BMAD: **Augment Code** para auditoría estándar + **CodeRabbit** en modo adversarial (busca activamente debilidades). |
| **Qué hacía antes** | El código de BMAD carecía de un pipeline de QA estructurado para sus contribuciones. No había revisión automatizada dual. |
| **Estado actual** | Cada PR al repositorio BMAD pasa por dos filtros complementarios: Augment (cobertura amplia) y CodeRabbit (adversarial, busca lo que Augment no encuentra). Los dos enfoques son ortogonales, no redundantes. |
| **Impacto en TEA** | **Alto para la calidad del propio BMAD como plataforma.** Los workflows, validators y CLI que usa TEA son revisados con estándares de QA más altos. La deuda técnica en el tooling de pruebas se reduce activamente. Para equipos que adoptan este patrón de revisión dual en sus propios proyectos BMAD, es un modelo directamente exportable. |

---

### Capacidad 6: Revisión automática funcional en PRs de forks

| Campo | Detalle |
|---|---|
| **Qué es** | El workflow de CodeRabbit usa el trigger `pull_request_target` con permisos corregidos, habilitando revisión automática en PRs externos. |
| **Qué hacía antes** | El evento `pull_request` causaba errores 403 en cualquier PR proveniente de un fork externo. La revisión automática era efectivamente inoperante para contribuciones externas. |
| **Estado actual** | CodeRabbit puede leer el código y publicar comentarios de revisión en PRs externos. El trigger `pull_request_target` tiene acceso a los secrets del repositorio base con las restricciones de seguridad correctas. |
| **Impacto en TEA** | **Alto para proyectos open-source o con equipos distribuidos.** El pipeline de QA automatizado funciona sobre el 100% de las PRs, no solo las internas. La cobertura de revisión pasa de parcial a completa. |

---

### Capacidad 7: Instalación no-interactiva — habilitador de CI/CD

| Campo | Detalle |
|---|---|
| **Qué es** | 10 flags CLI para instalación completamente no-interactiva: `--directory`, `--modules`, `--tools`, `--user-name`, `--communication-language`, `--document-output-language`, `--output-folder`, `-y/--yes`. |
| **Qué hacía antes** | La instalación de BMAD requería intervención humana en prompts interactivos. Era imposible en entornos sin TTY (Docker, CI runners, GitHub Actions). |
| **Estado actual** | `bmad init --directory ./project --modules "module-a" -y` funciona en cualquier entorno no-interactivo. |
| **Impacto en TEA** | **Crítico como habilitador.** Es el prerequisito que desbloquea todo el testing automatizado en CI/CD. Sin esta capacidad, no es posible escribir pipelines de prueba que inicialicen BMAD como parte del setup del entorno de test. Un `Dockerfile` o un step de GitHub Actions puede preparar el entorno BMAD antes de ejecutar la suite completa. |

---

### Capacidad 8: Vocabulario de invocación estandarizado

| Campo | Detalle |
|---|---|
| **Qué es** | Unificación del vocabulario de invocación a "load and follow" / "load" en todos los archivos de definición de workflows y prompts del sistema. |
| **Qué hacía antes** | Los archivos de steps usaban terminología mezclada: "invoke", "run", "execute" indistintamente para la misma operación. |
| **Estado actual** | Vocabulario único y predecible en todos los archivos de definición de workflows. |
| **Impacto en TEA** | **Medio.** Cualquier lógica de test que parsee instrucciones de workflows (ej. asertando que el sistema genera el comando correcto) deja de recibir variaciones inesperadas. Reduce falsos negativos en tests de integración que verifican el output textual de los agentes. Para LLMs que actúan como test agents, el lenguaje consistente mejora la fiabilidad de las aserciones. |

---

### Capacidad 9: Steps desacoplados de estructura de directorios

| Campo | Detalle |
|---|---|
| **Qué es** | Variable `workflow_path` eliminada de 16 archivos de steps. Los steps no referencian su ubicación física. |
| **Qué hacía antes** | Los steps dependían de `workflow_path` para referenciar su ubicación, generando acoplamiento implícito entre el step y la estructura de directorios del proyecto. |
| **Estado actual** | Los steps son modulares e independientes de su ubicación física. |
| **Impacto en TEA** | **Medio.** Los steps son portables y testeables en aislamiento sin necesidad de reproducir la estructura de directorios completa como fixture de prueba. Tests que verificaban comportamiento de steps con rutas relativas dejan de ser frágiles ante refactorizaciones de directorio. |

---

## 3. Notas de Contexto

### Testing automatizado

La combinación de **instalación no-interactiva** + **workflows como comandos autónomos** permite por primera vez un pipeline de pruebas completo sin intervención humana:

```yaml
# GitHub Actions — Pipeline TEA completo
steps:
  - name: Setup BMAD
    run: bmad init --directory ./test-env --modules "core" -y

  - name: Test validate workflow aislado
    run: bmad /validate-prd --input=fixtures/prd-valid.md

  - name: Test Layer 1 Validator (CSV)
    run: bmad validate --layer=1   # detecta referencias rotas en 212 CSVs

  - name: Test routing técnico end-to-end
    run: bmad /technical-research --output=tech.json
```

---

### Calidad de pruebas

La validación Layer 1 extendida a CSV introduce **validación estática de integridad multi-formato** en TEA. La cobertura de pruebas estáticas pasó de parcial (solo `.md`) a multi-formato. El gap de los CSVs representaba una clase entera de errores no detectables antes del runtime. El mismo patrón es extensible a JSONs, YAMLs y TOMLs en el futuro.

La revisión dual (Augment + CodeRabbit adversarial) afecta la calidad indirecta: el propio código de BMAD que ejecuta los tests pasa por estándares más altos, lo que reduce bugs en el tooling de pruebas.

---

### Integración con workflows o agentes AI

| Elemento | Impacto en TEA con agentes |
|---|---|
| Microworkflows atómicos | Un agente de testing puede invocar y observar cada workflow de forma aislada sin orquestar el agente completo. |
| `stepsCompleted` correcto | Un agente puede hacer assertions sobre el estado de progreso real del workflow. |
| Verbos estandarizados | LLMs que actúan como test agents reciben instrucciones sin ambigüedad terminológica. |
| RETURN PROTOCOL (Party Mode) | Sesiones largas de testing con múltiples iteraciones no pierden contexto al finalizar un ciclo. Crítico para suites de regresión largas con agentes autónomos. |
| Array `targets` en OpenCode | El routing de agentes de test custom hacia `.opencode/agent/` vs `.opencode/command/` es determinístico, eliminando instalaciones incorrectas de fixtures de prueba. |

---

### Usabilidad para desarrolladores

- **Curva de entrada reducida**: Ejecutar `/validate-prd` en un solo comando reemplaza la necesidad de entender la arquitectura completa del agente orquestador para arrancar con testing.
- **Cache npm confiable**: La corrección del flag `--prefer-offline` elimina una fuente de fallos intermitentes en entornos de CI donde el cache podía estar obsoleto.
- **`bmad-help` con `communication_language`**: Equipos multilingües que trabajan en TEA reciben documentación de ayuda en el idioma configurado del proyecto, no en inglés por defecto.
- **`package-lock` sincronizado** (471 líneas de dependencias huérfanas eliminadas): Las instalaciones en entornos de CI son reproducibles. Los builds de test no fallan por conflictos de lock file.

---

## 4. Conclusión

El módulo TEA en BMAD-METHOD ha alcanzado el siguiente estado de madurez:

| Dimensión | Estado actual |
|---|---|
| **Invocación de pruebas** | Workflows directamente invocables como comandos aislados, sin dependencia de agente orquestador. |
| **Validación estática** | Layer 1 cubre tanto archivos Markdown como CSV (501 refs / 212 archivos). Detección pre-deploy. |
| **Automatización CI/CD** | Instalación no-interactiva con 10 flags CLI. Pipeline completo ejecutable sin TTY. |
| **Calidad del tooling** | Revisión dual (Augment + CodeRabbit adversarial) sobre el código de BMAD. |
| **Fiabilidad de workflows** | Enrutamiento multi-step correcto. `stepsCompleted` como fuente de verdad para assertions. |
| **Integración con agentes AI** | Vocabulario estandarizado, routing determinístico, RETURN PROTOCOL para sesiones largas. |

La versión actual de BMAD es el **baseline mínimo viable** para operar TEA con cobertura real y automatización en CI/CD.

---

*Análisis realizado a partir de: `analisis_comparativo_bmad_alpha22_vs_beta7.md` y `analisis_comparativo_bmad_beta7_vs_beta8.md`*
*Fecha: 18 de febrero de 2026*
