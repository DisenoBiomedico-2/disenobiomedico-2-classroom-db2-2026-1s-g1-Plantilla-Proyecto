# Uso de IA

Registro del uso de inteligencia artificial generativa (ChatGPT, Claude, Copilot, etc.) en el
desarrollo del proyecto. **Política:** este archivo declara el uso de IA, no lo prohíbe — usar
IA no es una falta; no declarar un uso real, sí lo es.

Se edita como cualquier otro documento del proyecto: en la rama del Issue correspondiente,
agregando una fila a la tabla de esa sección antes de pedir la revisión del Pull Request. No
editen ni borren filas de otras personas.

## Cómo declarar un uso

Una sección por Issue (creen la sección si es la primera vez que se usa IA en ese Issue), con
una fila por cada uso:

```
## Issue #(número) — (título del issue)

| Fecha | Herramienta | Documento/parte (permalink) | Para qué se usó | Qué se hizo con el resultado | Corrección/verificación humana | Responsable |
|---|---|---|---|---|---|---|
| YYYY-MM-DD | (ej. ChatGPT, Claude Sonnet 5) | [ruta#Lx-Ly](permalink) | (qué se le pidió a la IA) | (ej. "se usó como borrador y se reescribió", "se usó tal cual", "se verificó contra la norma X") | (qué se verificó o corrigió a mano antes de incorporarlo) | (usuario de GitHub) |
```

El permalink se obtiene desde la vista `code` del archivo: seleccionar la línea o rango →
`...` → **Copy permalink**.

## Ejemplo

```
## Issue #18 — Presupuesto Detallado y BOM (Bill of Materials)

| Fecha | Herramienta | Documento/parte (permalink) | Para qué se usó | Qué se hizo con el resultado | Corrección/verificación humana | Responsable |
|---|---|---|---|---|---|---|
| 2026-11-01 | ChatGPT | [Presupuesto_Detallado.md#L10-L25](https://github.com/DisenoBiomedico-2/<repo>/blob/<sha>/02_DHF_Proceso_Diseno/.../Presupuesto_Detallado.md#L10-L25) | Redactar la justificación técnica de 3 componentes de la BOM | Se usó como borrador y se reescribió con el lenguaje del equipo | Se verificó que cada precio coincidiera con la cotización adjunta en el issue | anaperez |
```

---

<!-- Agreguen sus secciones debajo de esta línea -->
