# Protocolo de Sincronización Multi-IA (Claude ↔ Antigravity)

Este repositorio privado (`Uanify/tombstone-mes-ops`) es la **fuente única de verdad (SSOT)** para el seguimiento operativo, técnico y financiero de la implementación del **Sistema MES Tombstone Hats**.

Costo de operación: **`$0 USD`** (GitHub Private Repo + GitHub API).

---

## Reglas Obligatorias para Claude (Ricardo) y Antigravity (Andrés)

Para trabajar en paralelo sin pisarse cambios ni duplicar esfuerzos, **ambas IAs deben seguir estos 4 pasos en cada sesión**:

### 1. Leer antes de actuar (`PULL`)
- Ejecuta `git pull origin main` (o consulta `ops-status.json` vía GitHub API).
- Revisa:
  - `meta.projectStatus` y `meta.currentPhase`.
  - `aiHandoff[0]` (última entrada de la otra IA: qué hizo y qué dejó pendiente).
  - `tasks[].aiLock` para verificar qué tareas y archivos están tomados actualmente.

### 2. Reclamar tarea antes de editar código (`LOCK`)
- Nunca modifiques archivos que aparezcan en el `aiLock.files` de una tarea tomada por la otra IA.
- Antes de empezar una tarea, actualiza `ops-status.json` agregando tu lock:
  ```json
  "aiLock": {
    "agent": "claude",
    "user": "Ricardo",
    "files": ["backend/src/andon.ts"],
    "since": "2026-11-25T10:00:00-06:00"
  }
  ```
- Haz `git commit` + `git push` del lock de inmediato.

### 3. Liberar y documentar al terminar (`HANDOFF`)
- Cambia `status` de la tarea (`in_progress` → `review` o `done`) y pon `"aiLock": null`.
- Agrega un nuevo registro al **inicio** del arreglo `aiHandoff` (índice `0`) indicando:
  - `actor`: `"Andrés · Antigravity"` o `"Ricardo · Claude"`
  - `summary`: Resumen ejecutivo en 1–2 líneas
  - `completed`: Lista concreta de avances
  - `nextSteps`: Siguiente acción recomendada para la otra IA o el equipo
  - `touchedFiles`: Archivos modificados en el repo de código

### 4. Sincronizar (`PUSH`)
- Actualiza `meta.lastUpdated` y `meta.lastUpdatedBy`.
- Ejecuta `git pull --rebase origin main && git push origin main`.
- Si el panel web se usa manualmente desde el navegador, el guardado utiliza validación optimista por `SHA` de GitHub para evitar sobrescribir cambios recientes de la otra persona o IA.
