# Plan: experiencia gamificada de onboarding a GitHub (Semana 1)

Estado: **borrador para discutir** — no implementado todavía.

## 1. Problema que resuelve

Los equipos llegan a la Semana 1 teniendo que aprender simultáneamente: GitHub (Issues,
Project Board, Milestones, commits que cierran Issues), la metodología de evaluación del
curso ("si no está en el Issue, no existe") y el contenido de Diseño Biomédico 2. Mezclar
las tres cosas en la primera entrega real genera errores que no son de diseño sino de
mecánica de herramienta (issue cerrado sin evidencia válida, tareas que nunca llegan al
Project Board, archivos en la carpeta incorrecta del DHF), y esos errores cuestan tiempo de
revisión del profesor y nota de los estudiantes por un motivo que no tiene que ver con el
proyecto biomédico en sí.

La idea: un espacio de práctica de bajo riesgo, la semana 1, exclusivamente para la mecánica
de GitHub — para que cuando empiece a contar el proyecto real, la fricción ya esté resuelta.

## 2. Qué exige hoy el flujo real (línea base para diseñar las misiones)

Tomado de `README.md` de esta plantilla, tal como está hoy:

| # | Mecanismo real | Dónde vive |
|---|---|---|
| 1 | Crear un **Project** (vista Table) en la pestaña Projects | Projects del repo |
| 2 | Crear 3 **Milestones**: `Semana 5 - Diseño Conceptual`, `Semana 11 - Pruebas y Validación`, `Semana 17 - Entrega Final` | Issues → Milestones |
| 3 | Cargar tareas al Project Board, asignar **Responsable** y **Fecha** | Project Board |
| 4 | Cerrar un Issue de código/diseño con evidencia válida: `fixes #N` en el mensaje de commit | Commits |
| 5 | Cerrar un Issue de documento/foto arrastrando el archivo al comentario y cerrando manualmente | Comentario del Issue |
| 6 | Ubicar cada entregable en la carpeta correcta del DHF (`01_..` a `05_..`, con subcarpetas de `02_DHF_Proceso_Diseno` y `03_Ingenieria_Tecnica`) | Estructura de carpetas |
| 7 | Personalizar el README del equipo (ícono de lápiz, editar directamente en GitHub o clonado) | README.md |

Todo lo que seguimos exigiendo pero que el README no explicita (y que igual es parte de la
mecánica real): aceptar la invitación/el repo generado por GitHub Classroom, clonar o editar
vía web, hacer push directo a `main` (no hay flujo de Pull Requests en este README actual).

**Pendiente de confirmar con Oscar** antes de construir nada (ver sección 6): si el flujo de
revisión semanal con comentarios en el Issue sigue vigente en los repos de equipo reales (no
aparece en este README), y si cambió algo más del esquema de evaluación desde la última vez
que trabajamos esto.

## 3. Concepto del juego

Nombre de trabajo: **"MedGit: Simulacro de Laboratorio"**.

Narrativa mínima (biomédica, consistente con el curso): el equipo acaba de "ingresar" a un
laboratorio de dispositivos médicos ficticio. Antes de tocar un proyecto real, debe demostrar
que puede operar el sistema de trazabilidad del laboratorio (= GitHub) resolviendo un
conjunto de "órdenes de trabajo" (= Issues) ya cargadas en un repo de práctica. Cada orden
resuelta correctamente desbloquea la siguiente. Al completar todas, el equipo queda
"certificado" para abrir su proyecto real.

Por qué esta narrativa y no una genérica: reutiliza vocabulario que van a necesitar de todas
formas (DHF, trazabilidad, evidencia) en vez de enseñar un vocabulario de juego que después
hay que traducir mentalmente al vocabulario real del curso.

## 4. Misiones propuestas (cada una ejercita exactamente un mecanismo de la tabla §2)

Formato: un repo de práctica (plantilla propia, separada de `Plantilla-Proyecto`) con 6-8
Issues pre-cargados como "órdenes de trabajo", cada uno con instrucciones de qué hacer y qué
NO hacer (incluyendo errores comunes reales, a propósito).

1. **Orden 0 — Alta en el sistema**: aceptar el repo, editar el README (cambiar un
   placeholder por un dato real) y hacer el primer push/commit directo a `main`.
2. **Orden 1 — Tablero de control**: crear el Project (vista Table) y cargar las órdenes 2-6
   en él.
3. **Orden 2 — Los 3 hitos**: crear los 3 Milestones exactos (nombre y fecha) y asignar cada
   orden pendiente al hito que le corresponde según su contenido.
4. **Orden 3 — Responsable y fecha**: asignar un Responsable (de su propio equipo) y una
   fecha límite a cada tarjeta del Project Board.
5. **Orden 4 — Evidencia técnica**: resolver un issue "de código/diseño" subiendo un archivo
   dummy a la subcarpeta correcta de `03_Ingenieria_Tecnica` y cerrándolo vía `fixes #N` en
   el commit — no manualmente desde el botón "Close".
6. **Orden 5 — Evidencia documental**: resolver un issue "de documento" arrastrando un
   archivo al comentario y cerrando manualmente.
7. **Orden 6 — Trampa a propósito** (la más importante pedagógicamente): un issue que
   alguien "ya cerró" pero mal — sin evidencia, o cerrado con el botón en vez de `fixes #N`,
   o el archivo en la carpeta incorrecta del DHF. La misión es *detectar* el error y
   corregirlo, no solo ejecutar un paso. Esto practica exactamente el tipo de error que hoy
   le cuesta nota real a los equipos.

## 5. Verificación y "puntaje" (para que no recaiga en revisión manual del profesor)

Propuesta: un GitHub Action (`workflow_dispatch` + disparado también por `issues`, `push`) en
el repo de práctica que revisa, vía la API de GitHub, si cada condición de la orden
correspondiente ya se cumplió (milestone existe con el nombre exacto, issue cerrado con
referencia de commit vs. cerrado manualmente, archivo presente en la ruta esperada, tarjeta
en el Project con responsable y fecha). El resultado se postea como comentario actualizado en
un Issue fijo "Progreso" del propio repo del equipo — mismo patrón ya usado en
`curso-admin` (revisar vía API en vez de pedirle al estudiante que reporte manualmente).

Esto es exactamente la misma clase de automatización que ya construimos para
`revisar_pendientes.sh` / `marcar-revision-pendiente.yml`, aplicada a un contexto de
práctica en vez de revisión real — así que reutilizamos patrones y código ya probados, no
diseñamos de cero.

## 6. Decisiones abiertas (necesito que las resuelvas antes de construir)

- **Repo de práctica**: ¿uno individual por estudiante (vía Classroom, mismo patrón que ya
  usamos) o uno por equipo? Individual da práctica a cada integrante; por equipo es más
  rápido de calificar/crear pero dos personas pueden terminar sin haber tocado GitHub.
- **Peso en la nota**: ¿completamente práctica sin nota (solo gate informal), o un
  pequeño porcentaje simbólico para garantizar que efectivamente lo hagan en semana 1?
- **Vigencia del contenido de referencia**: confirmar si el flujo real sigue siendo
  exactamente el de este README (`fixes #N`, sin Pull Requests, 3 milestones) o si hay
  cambios recientes que no alcancé a ver porque no tengo ya acceso a `curso-admin` desde
  este contenedor.
- **Momento exacto**: ¿antes de recibir el repo real del proyecto, o en paralelo la primera
  semana con margen de 2-3 días?

## 7. Fases de construcción (una vez resueltas las decisiones de §6)

1. Crear el repo plantilla de práctica + manifiesto de las 6-8 órdenes (igual patrón que
   `issues_iniciales/manifest.tsv` de `curso-admin`).
2. Script de siembra (clonando el patrón de `crear_issues_iniciales.sh`): crea Issues,
   Milestones y Project Board ya precargados con los errores intencionales de la Orden 6.
3. GitHub Action de verificación/puntaje (§5).
4. Piloto con un solo equipo o un estudiante voluntario antes de lanzarlo a todo el curso.
