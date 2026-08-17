# coach-on

Fork de Open WebUI (`upstream`: open-webui/open-webui · `origin`: andrami-pro/coach-on). Es la interfaz conversacional del proyecto **Plan de Vida — Andres & Marine**.

**Lee `docs/coach-on/PLAN.md` antes de trabajar.** Contiene el diagnóstico verificado del repo, los breaking changes de upstream, el plan de actualización con su punto de no retorno, y la arquitectura.

## La regla que gobierna todo: el fork se mantiene delgado

Cada archivo de upstream que modifiquemos es un conflicto en cada actualización futura. Hoy la divergencia de código es **cero**: 4 commits que añaden 2 archivos nuevos que upstream no tiene.

Antes de proponer tocar código de Open WebUI, agota sus puntos de extensión: **Models** con system prompt, **Skills** (`$`), **Tools** vía tool server OpenAPI o MCP, **Functions** (Pipes/Filters/Actions), **Knowledge/RAG**, **Artifacts**, **Automations**, **Notes**.

Si algo obliga a modificar upstream, **dilo y pídelo explícitamente**. No se descubre en el siguiente merge.

Corolarios:

- **No edites `.gitignore`** — es de upstream. Lo local va a `.git/info/exclude`.
- **Archivos nuevos con nombres que upstream no usa.** Es lo que hace que nuestros compose nunca colisionen. Lo nuestro va en `docs/coach-on/`, que upstream nunca va a crear.
- **En un conflicto de rebase, se renombra lo nuestro, nunca lo suyo.**
- Excepción conocida: **este `CLAUDE.md` está en la raíz** porque es donde Claude Code lo lee. Si algún día upstream añade el suyo, es un conflicto de un archivo, previsto y trivial — se concatenan.

## Despliegue

- VPS **145.223.34.108**. Dominios `coach-on.andrami.pro` y `dev.coach-on.andrami.pro` detrás de nginx-proxy.
- La instancia viva es **dev**; el slot **prod** no sirve y se usa como banco de pruebas.
- El despliegue va por un **stack de Portainer** (el 26), cuyo compose es una copia estática sin `.git`. Un `git push` **no** actualiza el VPS: el cambio hay que aplicarlo también en Portainer.
- Los datos están en un volumen Docker, **no** en `backend/data/` de este repo, que está vacío. El volumen se llama **`coach-on-dev_open-webui-dev-data`** — con el prefijo del proyecto. `open-webui-dev-data` a secas **no existe**, y montar ese nombre no falla: Docker crea uno vacío, que es como se hace una copia de seguridad vacía sin enterarse. El de prod no existe todavía.
- La imagen va **fijada a un tag de versión**, nunca a `:main`. `:main` es la punta de desarrollo de upstream: un `pull` en mal día rompe la instancia y las migraciones no tienen downgrade.

## Antes de cualquier `docker compose pull` o `up -d` sobre datos reales

Upstream declara en seis releases entre 0.9.0 y 0.11.0 que **el downgrade después de migrar no está soportado**. Hay 21 migraciones nuevas desde nuestro punto de fork.

**Nunca ejecutes el bloque D de `docs/coach-on/PLAN.md` sin:** copia del volumen verificada y guardada fuera del VPS, ensayo completo en el slot prod, y confirmación explícita de Andres en ese momento. Aunque el plan ya esté aprobado.

`tar tzf` **no basta** como verificación: valida igual de bien un archivo vacío. La copia solo cuenta si además coincide el `sha256sum` entre origen y destino, `PRAGMA integrity_check` sobre el `webui.db` de dentro devuelve `ok`, y un `SELECT COUNT(*)` sobre `user` y `chat` devuelve datos reales.

## La especificación de los agentes no vive aquí

Vive en la bóveda de Obsidian, y es la fuente de verdad:

```
/home/andrami/obsidian-folder/3 🪴 Personal/Plan de Vida - Andres & Marine/
    06 - Convenciones de captura.md    el contrato: frontmatter + reglas de conducta
    03b - Investigación Capa Conversacional.md    cómo preguntan, cuándo paran
/home/andrami/obsidian-folder/.claude/skills/{ikigai,capturar,divergencia}/SKILL.md
```

Los Models de coach-on son un **puerto** de esas skills, no un rediseño. Para trabajar en ellos, abre la sesión con `--add-dir /home/andrami/obsidian-folder`. Si cambia una regla de conducta, cambia primero en `06` y después en el puerto.

## Restricciones del proyecto

- **Coste recurrente cero.** El tablero anterior murió por una suscripción. Sin SaaS de pago; modelos por uso vía API ya contratada.
- **Nadie edita frontmatter a mano.** La entrada es siempre formulario o conversación con un agente.
- **Los agentes no adulan.** No es tono: la IA aduladora deja a la gente más convencida de tener razón y menos dispuesta a disculparse (Cheng et al., *Science*, 2026), y este sistema entrevista a dos personas por separado y luego las sienta a contrastar. Si alguien propone suavizar el tono de los agentes, la respuesta es no, y el porqué está en `docs/coach-on/PLAN.md` §3.3 bis.
- **La fuente de verdad de los datos es la bóveda**, nunca una base de datos de coach-on.
