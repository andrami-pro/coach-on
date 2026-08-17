# Módulos — material conservado, todavía no portado

Coach-On empezó en marzo de 2026 como un tutor de inglés. Ese material se conserva
aquí porque la idea es que el coach acabe teniendo **varios módulos** —inglés,
proyectos, finanzas— y no solo el plan de vida.

**Nada de esta carpeta está desplegado.** Es materia prima para la Fase 2. Portar un
módulo significa convertir su system prompt en un **Model** del Workspace de Open WebUI,
que es el punto de extensión nativo descrito en la §3.2 del `PLAN.md`. No requiere tocar
ningún archivo de upstream.

## Por qué vive en `docs/coach-on/`

Estaba suelto en `.agent/`, `.agents/` y `.claude/`, triplicado y sin trackear. Dos
problemas: existía en un solo disco sin copia, y `.agents/` en la raíz es un nombre que
upstream puede crear cualquier día —`AGENTS.md` ya es convención— lo que sería un
conflicto en cada rebase. `docs/coach-on/` es namespace nuestro y upstream nunca lo va
a crear.

## `ingles/`

| Archivo | Qué es | Origen |
|---|---|---|
| `coach-on-english-system-prompt.md` | System prompt del tutor, perfil IT/product builder | Original de Andres |
| `coach-on-english-farm-system-prompt.md` | Variante para vida de granja en Australia | Original de Andres |
| `skill-english-vocabulary/` | Skill de vocabulario diario, 35 palabras y test | **Terceros**, con su LICENSE |
| `skills-lock.json` | Procedencia y hash de la skill anterior | Generado |

La skill de vocabulario viene de `ayeshasyesda06-dev/ai-vocab-builder` (GitHub). Estaba
en tres copias idénticas; aquí queda una. `skills-lock.json` guarda el hash, así que se
puede reinstalar desde el origen en vez de arrastrar el vendorizado.

## Un aviso antes de reutilizar estos prompts

Los dos system prompts de inglés dicen *"patient, encouraging"*. Para un tutor de idiomas
está bien. **Para los agentes del plan de vida, no**: la §3.3 bis del `PLAN.md` prohíbe la
adulación, y no por tono —un modelo complaciente produce exactamente el daño que el método
existe para evitar. Si alguien copia la estructura de estos prompts para el entrevistador o
el facilitador de pareja, tiene que dejar fuera esa parte.
