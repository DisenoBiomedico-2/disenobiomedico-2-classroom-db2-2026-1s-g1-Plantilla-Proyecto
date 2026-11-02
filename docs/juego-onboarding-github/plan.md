# Plan: flujo real de trabajo con ramas/PR/revisión + juego de onboarding (Semana 1)

Estado: **borrador para discutir** — no implementado todavía.

Confirmado con Oscar (2026-11-01): el flujo de ramas + Pull Request + revisión entre pares +
CI descrito en §2 **es el nuevo flujo real del curso**, no solo del juego — reemplaza al
`fixes #N` + push directo documentado hoy en `README.md`. El juego de la Semana 1 (§4) ensaya
exactamente este flujo real, no una versión simplificada. El uso de IA está **permitido si se
declara**; la falta sancionable es no declarar un uso real, no el uso en sí.

## 0. Hallazgo crítico (2026-11-01): gran parte de esto ya existe en `curso-admin`

Revisé `DisenoBiomedico-2/curso-admin` (no lo tenía clonado en este contenedor). Cambia
bastante el alcance de este plan:

**Ya construido, probado y en producción desde el 2026-10-21** (`concepto-aprobacion/` +
`scripts/exigir_concepto_aprobacion.sh`): cubre el paso 5 completo del flujo de Oscar —
exige que cada aprobación de PR lleve un concepto con tres etiquetas (`Revisé`, `Hallazgos`,
`Decisión`, mínimo 80 caracteres), publicado como *status check* `concepto-de-aprobacion`
que bloquea la fusión si no se cumple, con bot de ayuda en el PR y guía fija
(`APROBAR_UN_PR.md`). 17 pruebas unitarias + pruebas en vivo en un repo de prueba. Activado
en los 6 repos de equipo (biomed, los-errantes, los-100, Bionex, MedDesign, Bionova).

**También ya existe**: el ruleset `pr-obligatorio` en cada repo de equipo ya exige 1
aprobación para fusionar a `main` — la protección de rama de mi §3.5 original ya está
resuelta, no hay que construirla. Y `scripts/reporte_contribucion_docs.sh` ya extrae, por
estudiante y por commits/líneas reales de la API, exactamente las señales objetivas de
colaboración/contribución que pedía el paso 7 del flujo (con un modo `--aprobaciones` para
auditar revisiones) — no hay que construir un verificador nuevo para eso.

**Lo que de verdad falta** (nada de lo anterior lo cubre):
- Validación de nombre de rama descriptivo (paso 3) — no encontré nada parecido en
  `curso-admin`.
- `USO-IA.md` (paso 4) — solo aparece mencionado en un checklist de una plantilla de PR que
  **se retiró** de `Plantilla-Proyecto` por chocar con la plantilla propia de cada equipo
  (ver hallazgo siguiente). El archivo, su formato y cualquier verificación de que se
  actualizó cuando se declaró uso de IA no existen todavía en ningún repo.

**Hallazgo más importante — esta plantilla pública está desincronizada de la realidad**:
`Plantilla-Proyecto` (este mismo repo) no tiene `.github/` en absoluto: ni el workflow de
concepto de aprobación, ni una plantilla de PR, ni el ruleset `pr-obligatorio`. Su
`README.md` sigue describiendo el flujo viejo (`fixes #N`, push directo, 3 milestones) pese
a que los 6 repos de equipo ya operan con PR + revisión + concepto, y en realidad son **4**
milestones (`curso-admin/README.md` lista "Semana 2 - Definición del Reto" además de las
otras 3; esta plantilla no la tiene). Tampoco encontré ningún script en `curso-admin` que
cree el ruleset `pr-obligatorio` o propague la plantilla de PR / el workflow de concepto a un
repo nuevo — todo lo que hoy corre en los 6 repos de equipo parece haberse configurado a mano
repo por repo, no vía automatización. Si se crea un equipo nuevo hoy desde esta plantilla, no
heredaría nada de esto.

Esto separa el trabajo en dos frentes, no uno:
- **(A) Poner al día `Plantilla-Proyecto`** para que cualquier repo nuevo nazca ya con PR
  obligatorio + concepto de aprobación + plantilla de PR + 4 milestones correctos — y que
  `crear_repos_grupos.sh` (en `curso-admin`) cree el ruleset `pr-obligatorio` automáticamente,
  como ya hace con el ruleset `restriction` de `.github/workflows/**`.
- **(B) Construir lo que de verdad falta**: nombre de rama válido + `USO-IA.md`.
- El juego de Semana 1 (§4) depende de (A) — no tiene sentido practicar sobre una plantilla
  desactualizada respecto a lo que ya viven los equipos reales.

## 1. Problema que resuelve

Dos problemas relacionados:
1. El flujo real ya es exigente (branching, PR, revisión de pares con concepto, CI) desde
   hace dos semanas en los repos de equipo — pero la plantilla pública que usan los equipos
   NUEVOS no lo refleja (ver §0), y nada de eso se practica antes de la primera entrega real.
2. Dos piezas puntuales del flujo (nombre de rama, declaración de IA) no tienen ninguna
   automatización todavía, en ningún repo — dependen de que cada estudiante se acuerde solo.

La idea: (a) construir la infraestructura de GitHub que haga el flujo casi auto-exigible
(plantillas, checks obligatorios, protección de rama), y (b) un espacio de práctica de bajo
riesgo en la Semana 1 para ensayar exactamente esa infraestructura antes de que cuente sobre
el proyecto real.

## 2. El flujo real, paso a paso, con su mecanismo concreto en GitHub

| # | Paso (como lo describe Oscar) | Mecanismo concreto en GitHub | Construir |
|---|---|---|---|
| 1 | Un Issue inicial encarga organizar el Project Board | Issue fijo tipo "Orden 0" en el manifiesto de siembra (igual patrón que `issues_iniciales/manifest.tsv` de `curso-admin`) | Issue + instrucciones |
| 2 | Asignar responsables a cada tarea (pueden ser varios); el estudiante debe autoasignarse ≥1 | Campo **Assignees** del Issue (multi-persona nativo) + tarjeta en Project Board | Ninguno nuevo — ya es nativo |
| 3 | Para desarrollar su tarea, crea una rama con nombre descriptivo (no genérico tipo `usuario-patch-1`) | `git checkout -b <slug-descriptivo>`; validación automática de que el nombre no sea el patrón por defecto de GitHub y sí sea descriptivo | **Action** que corre on `push`/`pull_request` y falla un *status check* si el nombre no cumple |
| 3b | "Hoy la creación de la rama tiene una plantilla que obliga a llenar información" | GitHub no tiene plantilla nativa para *crear una rama*; lo más cercano es la **plantilla de Pull Request**, que se llena al abrir el PR (normalmente inmediatamente después de crear la rama y el primer commit) | `.github/PULL_REQUEST_TEMPLATE.md` — ver §3 |
| 4 | Si usó IA, declarar en `USO-IA.md`: herramienta, documento/parte (con permalink), qué hizo con el resultado | Archivo en la raíz del repo, formato de log append-only | Plantilla de `USO-IA.md` + recordatorio en la plantilla de PR — ver §3 |
| 5 | La rama se fusiona solo con revisión/aprobación del compañero correcto (el otro asignado al mismo Issue); quien revisa llena datos para que el CI pase y se habilite el merge | Ruleset `pr-obligatorio` (≥1 aprobación) + *status check* `concepto-de-aprobacion` | **YA EXISTE** y está activo en los 6 repos de equipo (`concepto-aprobacion/` + `exigir_concepto_aprobacion.sh` en `curso-admin`, desde 2026-10-21) — falta solo traerlo a `Plantilla-Proyecto` (ver §0) |
| 6 | El estudiante también actúa como revisor del trabajo de otro | Mismo mecanismo de review de GitHub, desde el otro lado | **YA MEDIBLE** — `reporte_contribucion_docs.sh --aprobaciones` (`curso-admin`) ya audita esto vía API |
| 7 | Aportar al trabajo de un compañero con el mismo Issue, en la misma rama (no anidar ramas) | Varios colaboradores push a la misma rama (ya tienen permiso de escritura) | **YA MEDIBLE** — `reporte_contribucion_docs.sh` ya cuenta commits y líneas reales por estudiante; solo falta una nota en la plantilla de PR, no automatización nueva |
| 8 | Día de reunión: el profesor revisa el Project Board, sigue el link al documento/carpeta, verifica `USO-IA.md`, verifica rama fusionada + Issue cerrado, revisa las razones del revisor y puede pedir cambios | Flujo manual del profesor, apoyado en un reporte (ver §5) | Extensión de `revisar_pendientes.sh` — ver §5 |

**Corrección (2026-11-01, tras leer el repo real de Bionex)**: lo que escribí arriba sobre
"Closes #N" estaba mal. La plantilla de PR real que ya usan los equipos **no** usa esa
palabra clave — solo pide el número del issue después de un `#` suelto (ver texto exacto en
§3.1), así que fusionar el PR **no** cierra el Issue automáticamente. Y encontré algo que no
esperaba: `marcar-revision-pendiente.yml` (el workflow de agosto, reabrir/cerrar un Issue con
comentario, de uso exclusivo del profesor) **sigue vivo** en Bionex, corriendo en paralelo al
flujo nuevo de PR. Esto aclara el paso 8 del flujo de Oscar: hay **dos ciclos de revisión
distintos**, no uno:
1. **Revisión entre pares** (concepto de aprobación) — gatilla la fusión del PR. Ya existe.
2. **Revisión del profesor** (comentarios con permalinks en el Issue + este workflow) —
   gatilla el cierre *definitivo* del Issue, después de que el PR ya se fusionó. Es el mismo
   sistema que construimos en agosto, sigue siendo la pieza que decide si "el issue debería
   quedar closed" del paso 8 — no hay que tocarlo, solo hay que traerlo también a
   `Plantilla-Proyecto` (tampoco está ahí, ver §0).

## 3. Qué falta construir de verdad (actualizado tras revisar `curso-admin`, §0)

Dos frentes, como quedó en §0: (A) poner al día `Plantilla-Proyecto` con lo que ya existe en
los repos de equipo, (B) construir las dos piezas genuinamente nuevas (nombre de rama,
`USO-IA.md`). Nada de branch protection ni de validación de contenido de revisión hace falta
construirlo — ya existe y ya corre.

### 3.1 Traer a `Plantilla-Proyecto` lo que ya existe en los repos de equipo (frente A)
- El ruleset `pr-obligatorio` — hoy no hay ningún script en `curso-admin` que lo cree; parece
  haberse configurado a mano en cada repo de equipo. Hace falta un script tipo
  `crear_ruleset_workflows.sh` pero para `pr-obligatorio`, y añadirlo a `crear_repos_grupos.sh`
  para que los repos nuevos nazcan ya protegidos.
- El workflow `concepto-de-aprobacion.yml` + la guía `APROBAR_UN_PR.md` (ya probados, de
  `concepto-aprobacion/` en `curso-admin`) — propagarlos aquí y luego a los 6 repos.
- `marcar-revision-pendiente.yml` — el workflow de agosto (reabrir/cerrar Issue con
  comentario, exclusivo del profesor) **tampoco está** en `Plantilla-Proyecto` hoy, pese a
  seguir vivo y en uso en los 6 repos de equipo (confirmado en Bionex). Se trae igual, sin
  modificarlo.
- **Leí el archivo real de Bionex** (`.github/pull_request_template.md`), textual:
  ```
   # Qué se hizo

   # Qué decisión se tomó y por qué

   # Qué queda pendiente

   # Con esto empezamos a trabajar o cerramos
  (escribe el número del issue justo después del '#' sin espacios)

  #(número del issue)
  ```
  Nota: es un `#N` suelto, sin palabra clave `Closes`/`Fixes` — fusionar el PR no cierra el
  Issue solo (ver corrección en §2). Reutilizar este texto tal cual, sin inventar uno nuevo;
  es donde se agrega el checkbox de IA (ver 3.3).
- Corregir el `README.md` de esta plantilla: describe `fixes #N` + push directo y 3
  milestones; la realidad son PR+revisión+concepto+cierre del profesor, y 4 milestones (falta
  "Semana 2 - Definición del Reto").

### 3.2 `USO-IA.md` (plantilla, vive en la raíz de cada repo) — nuevo, frente B
Log append-only, una entrada por uso declarado:
```
## [Fecha] — [Nombre del estudiante]
- Herramienta: (ej. ChatGPT, Claude, Copilot)
- Documento/parte: (ruta del archivo) — permalink: (link a la línea/rango exacto)
- Qué se hizo con el resultado: (ej. "se usó como borrador y se reescribió", "se verificaron
  las cifras contra la norma X", "se usó tal cual")
```

### 3.3 Agregar a la plantilla de PR real (no crear una nueva, ver 3.1)
- Checkbox: "¿Usaste IA para generar o asistir este documento?" Sí/No → si Sí, recordatorio
  de completar `USO-IA.md` en este mismo PR.
- Checklist: nombre de rama descriptivo / documento enlazado en la tarjeta del Project Board.

### 3.4 Action — nombre de rama válido — nuevo, frente B
Dispara en `pull_request` (opened/synchronize). Rechaza:
- Patrones por defecto de GitHub: `^[\w.-]+-patch-\d+$`.
- Nombres sin estructura descriptiva (ej. largo mínimo, debe tener al menos una palabra
  reconocible, no solo números/hashes).
Se recomienda convención `<issue>-<slug-descriptivo>` (ej. `14-marco-teorico`) — así el
nombre queda automáticamente ligado al Issue sin pedirle al estudiante que lo escriba dos
veces, y es trivial de validar con una expresión regular.
Falla como *status check* requerido, agregado al mismo ruleset `pr-obligatorio` (mismo
mecanismo que ya usa `exigir_concepto_aprobacion.sh` para el check de concepto) → bloquea el
botón de merge mientras no se corrija.

*No* se automatiza (fase 1) la validación de que el revisor sea exactamente "el otro
asignado al Issue" — eso lo confirma el profesor el día de la reunión (es justo el punto 8
del flujo: pregunta sobre las razones dadas). Se puede automatizar después si se vuelve un
problema recurrente.

## 4. El juego de Semana 1 — "MedGit: Simulacro de Proyecto"

**Actualizado (2026-11-02) con los ajustes de Oscar — esta es la versión final, construida e
implementada.** Reemplaza la narrativa de "laboratorio médico ficticio" con "órdenes de
trabajo" (versión descartada): ya no se habla de laboratorio ni de "orden" — todo se nombra
como lo que es, un **issue**, dentro de un **proyecto** de práctica. 4 issues (no 6), cada
uno un objeto real del manifiesto `scripts/issues_juego/` en `curso-admin`:

1. **Issue 0 — Organiza el proyecto**: no produce documento. Pide crear una vista
   **Roadmap** del Project (no solo Table), y para cada issue (`1`, `2`, `3`) asignar
   Responsable(s) **y fechas de inicio/fin** dentro del milestone "Semana 1 - Onboarding"
   (vence 7 días después de sembrado), guardando la vista (**Save view**). Cada integrante
   debe quedar asignado a ≥1 issue.
2. **Issue 1 — Protocolo de bioseguridad**: asignado a 2 personas a propósito. Rama
   descriptiva, documento en `proyecto_ficticio/`, PR con la plantilla real, revisión con
   concepto del otro asignado, fusión, enlazar en el Project Board, cerrar.
3. **Issue 2 — Ficha del equipo (con IA)**: igual mecánica que el 1, pero exige usar IA y
   declarar en `USO-IA.md` (formato estándar, con permalink) antes de pedir la revisión —
   practica la declaración, no solo la mecánica de Git.
4. **Issue 3 — Algo no cuadra (la trampa)**: `crear_juego_onboarding.sh` ya deja una rama con
   nombre genérico (`oscampo-patch-1`) y un commit, pero **no abre el PR** — eso lo hace el
   equipo, y ahí mismo ven el check `nombre-de-rama-valido` en rojo. (Se descartó que el
   script abriera el PR por ellos: requeriría permiso de escritura sobre Pull Requests que
   `ORG_ADMIN_TOKEN` no tiene, y de todas formas abrir el PR es parte de lo que este issue
   debe practicar — mejor diseño, no solo una limitación técnica.)

Reutiliza exactamente los artefactos de §3 — el repo de práctica corre con las mismas
plantillas/Actions/protecciones que los repos reales, así la práctica es 1:1, no una
simulación aparte que hay que volver a traducir.

**Apoyo visual para quien nunca usó GitHub (2026-11-02)**: Oscar notó que el issue `2`
(el más denso: rama + IA + permalink + tabla de `USO-IA.md` + PR) puede resultar confuso sin
ver cómo se ve cada paso. En vez de producir video (esta sesión no puede grabar pantalla), se
desglosó el issue en 7 micro-pasos explícitos con 5 gifs cortos embebidos por URL
(`DB2-Training/docs/gifs/*.gif`, vía `raw.githubusercontent.com` para que funcionen desde
cualquier repo generado de la plantilla sin duplicar archivos). La guía de qué grabar y con
qué nombre queda en `DB2-Training/docs/gifs/README.md` — **pendiente que Oscar grabe y suba
los 5 archivos reales**, los embeds hoy muestran ícono roto hasta entonces. Los mismos gifs
sirven para los issues `1` y `3` (comparten la mecánica de rama/PR/revisión) y para el flujo
real del proyecto — no hay que grabar nada aparte si se reutilizan ahí también.

## 5. Verificación

Lo que el profesor necesita ver antes/durante la reunión (paso 8 del flujo) ya está casi
todo cubierto por herramientas existentes en `curso-admin`:
- ¿Rama fusionada y PR cerrado? ¿La revisión tenía concepto válido? → ya lo decide el check
  `concepto-de-aprobacion`, visible directamente en el PR; `reporte_contribucion_docs.sh
  --aprobaciones` lo audita en lote para todo un corte.
- ¿Quién contribuyó realmente a cada rama compartida? → `reporte_contribucion_docs.sh`
  (commits y líneas reales por estudiante).

Lo único que falta agregar (una vez exista `USO-IA.md`, §3.2): ¿el PR declaró uso de IA? Si
sí, ¿hay una entrada correspondiente en `USO-IA.md` con permalink? — esto sí es nuevo, lo más
simple es una verificación puntual dentro de `reporte_contribucion_docs.sh` (ya recorre PRs
fusionados del corte) en vez de un script aparte.

## 6. Rúbrica de puntaje/penalidad (borrador — necesito que ajustes los pesos exactos)

| Etapa | Qué se evalúa | Tipo | Peso sugerido |
|---|---|---|---|
| Organización del Project Board | Tareas del corte actual cargadas y asignadas | Automático | Bajo (requisito de entrada) |
| Autoasignación | Cada estudiante tiene ≥1 tarea propia | Automático | Bajo |
| Nombre de rama + plantilla de PR | Descriptivo, plantilla completa | Automático (bloquea merge) | Estructural — no es que "reste puntos", es que no deja avanzar |
| `USO-IA.md` | Declarado cuando corresponde | Automático (¿existe archivo tocado en el PR?) + confianza en que la declaración es honesta | **Alto si se detecta uso no declarado** — es una falta de integridad académica, no un descuento menor |
| Revisión entre pares (como autor) | Recibió una revisión con concepto válido antes de fusionar | **Ya automático** — check `concepto-de-aprobacion` existente | Medio-alto |
| Revisión entre pares (como revisor) | Hizo al menos una revisión real en el corte | **Ya medible** — `reporte_contribucion_docs.sh --aprobaciones` | Medio |
| Colaboración en rama compartida | Aporte real cuando el Issue tiene 2+ responsables | Manual (profesor compara commits por autor) | Medio |
| Reunión con el profesor | Calidad de las razones del revisor, estado real coincide con lo reportado | 100% manual/discrecional | Alto — instancia final de verificación humana |

Quedan por decidir (tu llamada, no la mía): valores numéricos exactos, y si alguna etapa
amerita ser requisito de aprobación del corte en vez de solo puntaje.

## 7. Decisiones de §7 (cerradas el 2026-11-01)

- **Repo de práctica del juego — RESUELTO**: por equipo, no individual. Así se dan las
  interacciones reales entre compañeros (Issues con varios asignados, aprobación de PR entre
  pares) que no existen en un repo de una sola persona. El puntaje, sin embargo, es
  **individual** — cada integrante se evalúa por sus propias acciones dentro del repo
  compartido (igual principio que `reporte_contribucion_docs.sh` ya usa para el proyecto
  real: señales objetivas por persona, no una nota de equipo repartida por igual).
- **Peso en la nota del juego de Semana 1 — RESUELTO**: sin nota, pero **requisito** para
  continuar en el curso (pass/fail) — no suma puntos, pero no completarlo bloquea avanzar
  (por ejemplo, no se habilita el repo del proyecto real hasta certificarse).
- **Mínimo de personas por Issue real — RESUELTO**: recomendación, no regla dura. Un Issue
  con 1 solo responsable simplemente pide la revisión a cualquier compañero del equipo, no
  necesariamente a otro asignado al mismo Issue.
- **Automatizar "el revisor correcto" — RESUELTO**: no por ahora. El profesor ya lo confirma
  en la reunión semanal (pregunta las razones del revisor); se reconsidera solo si aparece un
  problema real.

## 8. Fases de construcción (revisado tras §0)

1. ~~Leer la plantilla de PR real de un repo de equipo~~ — hecho, ver §3.1 (texto exacto de
   Bionex).
2. ~~Traer a `Plantilla-Proyecto`: workflow `concepto-de-aprobacion.yml` + `APROBAR_UN_PR.md`,
   `marcar-revision-pendiente.yml`, plantilla de PR real + checkbox de IA~~ — hecho
   (commit `8884207`, rama `feature/juego-onboarding-github`). También se agregó `USO-IA.md`
   (la plantilla) porque el README y el PR template ya lo referencian.
3. ~~Corregir el `README.md` de `Plantilla-Proyecto`~~ — hecho en el mismo commit: 4
   milestones (ya automatizados al crear el repo, corregido también que el equipo NO los crea
   a mano), flujo de ramas+PR+revisión con concepto+cierre del profesor en vez de `fixes #N`.
4. ~~Script del ruleset `pr-obligatorio` + wiring en `crear_repos_grupos.sh`~~ — hecho en
   `curso-admin` (commit `11f2f17`, pusheado a `main`): `scripts/crear_ruleset_pr_obligatorio.sh`,
   probado contra Bionex (detecta el existente, no lo duplica) y contra esta misma plantilla
   (se niega correctamente porque su `main` todavía no tiene el workflow — ver pendiente
   abajo). También corregí de paso una nota desactualizada del README de `curso-admin` que
   decía "sin activar" sobre el check de concepto, cuando ya está activo desde el 21 de
   octubre.
   **Nota de cronograma (2026-11-01)**: todo el punto 2-3 vive hoy solo en la rama
   `feature/juego-onboarding-github` de `Plantilla-Proyecto`, no en su `main` -- el ruleset
   `pr-obligatorio` no se puede crear ahí hasta fusionar (el script se niega a propósito), y
   los repos nuevos seguirían sin heredar nada de esto. Oscar aclaró que el juego entra en uso
   hasta el **próximo semestre** (el actual sigue en curso) -- sin prisa por fusionar a `main`
   todavía; seguimos acumulando en la rama.
5. ~~Action de nombre de rama válido~~ — hecho (commits `23fccea` en `Plantilla-Proyecto` y
   `451dd08` en `curso-admin`): nuevo workflow `nombre-de-rama-valido.yml` (rechaza el patrón
   `usuario-patch-N` y nombres no descriptivos, sin necesitar la API), y
   `crear_ruleset_pr_obligatorio.sh` ahora exige los dos checks (concepto + nombre de rama).
6. **Hallazgo importante (2026-11-02)**: `USO-IA.md` **ya existe** en los 5 repos de equipo
   que revisé (Bionex, MedDesign, los-errantes, biomed, los-100) -- pero cada equipo inventó
   su propio formato, **los 5 son distintos entre sí** (narrativo por tarea, por tema, tabla
   por Issue, tabla única, mezcla) y ninguno usa permalinks a líneas específicas como se pidió
   originalmente. Se definió con Oscar el formato estándar (tabla por Issue, inspirada en la de
   `los-errantes` -- la más completa -- con el permalink real agregado): Fecha, Herramienta,
   Documento/parte (permalink), Para qué se usó, Qué se hizo con el resultado,
   Corrección/verificación humana, Responsable. Ya actualizado en el `USO-IA.md` de
   `Plantilla-Proyecto` (commit pendiente de este mismo turno). **Decisión de Oscar**: no se
   migran retroactivamente los 5 formatos existentes -- quedan como están, el estándar nuevo
   rige solo para el juego y los repos que se creen el próximo semestre.
7. Agregar la verificación de `USO-IA.md` (contra el formato estándar) a
   `reporte_contribucion_docs.sh` -- **diferido**: no tiene sentido construirlo ya, porque
   ningún repo real usa todavía el formato estándar (ver punto 6) y ese script alimenta la
   calificación del semestre en curso. Retomar cuando el formato estándar ya esté en uso real
   (próximo semestre).
8. Propagar todo lo anterior a los 6 repos de equipo existentes (`propagar_archivo_plantilla.sh`
   para los archivos; `crear_ruleset_pr_obligatorio.sh` ya no hace falta ahí, ya lo tienen) --
   también diferido al próximo semestre, junto con el punto 7, para no interferir con el
   semestre en curso.
9. ~~Repo de práctica del juego (Semana 1)~~ — hecho (2026-11-02): repo plantilla
   `DisenoBiomedico-2/DB2-Training` creado a mano por Oscar (esta sesión no puede crear repos
   nuevos ni cambiar configuración de repo -- ambos bloqueados por el proxy del entorno;
   Oscar lo creó y lo marcó *Template repository*), poblado con los mismos artefactos de
   `Plantilla-Proyecto` + README con la narrativa. En `curso-admin`: manifiesto de las 4
   órdenes, `crear_juego_onboarding.sh` (siembra Issues + Project Board + PR roto de la orden
   3) y `crear_repos_juego.sh` / `crear-repos-juego.yml` (crea el repo de práctica de cada
   equipo del roster, manual únicamente -- no se dispara al editar los rosters como sí hace
   "Crear repos de grupo", para no invitar estudiantes sin pedirlo explícitamente). Todo
   commit `fe83022` en `curso-admin`, main.
10. Piloto con un equipo de prueba (`oscampo`, `oscamp0`, `ivasaor`) — **hecho, exitoso
    (2026-11-02)**. Corrido tres veces contra `prueba-juego-2-practica`:
    - 1ª vuelta: repo creado, milestone "Semana 1 - Onboarding" con fecha correcta, los 4
      Issues asignados al milestone, Project Board vinculado y poblado, rama rota del issue 3
      pre-sembrada y verificada (contenido + nombre correctos).
    - Oscar probó la vista Roadmap en vivo y encontró que faltaba activar **Markers →
      Milestone** para ver las marcas de inicio/fin del hito — se corrigió en el issue 0 del
      juego *y* en el README real de `Plantilla-Proyecto` (la vista Roadmap+Markers pasó de
      "opcional" a ser el paso principal, igual mecánica que usarán para los 3 cortes reales).
    - 2ª vuelta, con `ivasaor` agregado al roster de prueba (no es miembro de la org, a
      diferencia de `oscampo`/`oscamp0`): confirmado en vivo por Oscar que `ivasaor` sí recibe
      una invitación real con estado "Pending Invite" y correo de GitHub -- a diferencia de
      los miembros de la org, que entran directo sin invitación. Confirma que el flujo real de
      invitación para estudiantes (siempre outside collaborators) funciona como se espera; no
      era un bug, solo una diferencia de comportamiento según el tipo de cuenta invitada.
    - Nota operativa: esta sesión de Code no puede crear repos, cambiar su configuración, ni
      leer/escribir `collaborators`/`invitations` por la API -- todas esas acciones las tuvo
      que hacer Oscar a mano o disparando el workflow vía `POST .../dispatches` (REST, sin
      GraphQL) desde aquí. El workflow en sí corre sin problema en la infraestructura de
      GitHub.
11. **Pivote: repo de equipo → repo individual + bot `botDB2`** — **hecho, verificado de punta
    a punta (2026-11-02)**. Oscar identificó que con repo compartido todos los integrantes
    reales de un equipo intentarían resolver los mismos Issues al mismo tiempo (condición de
    carrera), y que varias de las "trampas" planeadas para el antiguo Issue 4 (cambiar el
    nombre de la rama ya abierta, archivo fuera de cualquier PR) eran confusas o imposibles en
    la práctica. Se rediseñó a **un repo por estudiante** (`<usuario>-practica`) con una cuenta
    bot real (`botDB2`, miembro de la org) como "compañero de práctica" -- deja ramas con
    trabajo empezado (a veces rotas a propósito, a veces incompletas) y aprueba los PR cuando
    se le pide revisión. Esto también resuelve de forma natural la restricción nativa de
    GitHub de que el autor de un PR no puede aprobar su propio PR. Detalle completo en
    `curso-admin/README.md` (sección "Juego de onboarding").
    - Piloto repetido con el nuevo diseño (`oscampo-practica`, `oscamp0-practica`,
      `ivasaor-practica`): repos, invitaciones (estudiante + `botDB2`), milestone, 5 issues,
      Project Board y las dos ramas pre-sembradas por el bot (`botDB2-patch-1` para el issue 3,
      `estado-del-arte-proyecto` con un `<!-- TODO -->` para el nuevo issue 4 "continuar el
      trabajo de un compañero") -- todo verificado contra el contenido real vía API.
    - **Bug encontrado y corregido**: `revision-del-companero.yml` nunca se disparaba --
      el string multilínea del concepto de aprobación no tenía sus líneas de continuación
      indentadas al nivel del *block scalar* de YAML (`run: |`), así que el parser cortaba el
      bloque antes de tiempo y las líneas siguientes quedaban como claves sueltas a nivel raíz
      del documento, corrompiendo el YAML completo (GitHub ni siquiera podía leer el campo
      `name:`, y el workflow aparecía con el nombre del archivo). Corregido con `$'...\n...'`
      (ANSI-C quoting de bash) en una sola línea lógica, propagado a `DB2-Training` y a los
      tres repos de piloto.
    - **Confirmado en vivo**: Oscar abrió un PR real en `oscamp0-practica` (#6) y pidió la
      revisión a `botDB2` -- el workflow se disparó, y `botDB2` publicó la aprobación con el
      concepto de ejemplo completo.
    - **Hallazgo pedagógico (mismo día, señalado por Oscar)**: ese PR de prueba tenía la
      plantilla completamente vacía (sin llenar ninguna sección, IA sin declarar, checklist
      sin marcar, sin issue enlazado) y un archivo (`prueba.md`, "Lorem ipsum") sin relación a
      ningún issue real -- y `botDB2` aprobó igual, con el mismo concepto enlatado de siempre.
      El bot nunca miraba el contenido, solo generaba una respuesta fija para pasar el check:
      vaciaba de sentido justo lo que el juego debía enseñar (que llenar la plantilla,
      declarar IA y enlazar el issue, importa). Decisión de Oscar entre tres niveles de rigor
      propuestos: validación **formal** de la plantilla (sin juzgar el contenido del
      documento, que es juicio humano difícil de automatizar de forma confiable).
      `revision-del-companero.yml` se reescribió para parsear el cuerpo real del PR contra la
      plantilla (`.github/pull_request_template.md`) y decidir `APPROVE` o `REQUEST_CHANGES`:
      secciones llenas, declaración de IA respondida Sí/No (no el placeholder "Sí / No"),
      checklist marcado, número de issue válido enlazado. Si falta algo, el bot pide cambios
      listando exactamente qué; si está completo, aprueba citando lo que el PR dice haber
      hecho (en vez de un texto genérico). Verificado en vivo contra el mismo PR #6: con la
      plantilla vacía, `botDB2` respondió `CHANGES_REQUESTED` con la lista correcta de lo que
      faltaba; tras llenarla correctamente, respondió `APPROVE` citando el contenido real de
      "Qué se hizo". Propagado a `DB2-Training` y a los tres repos de piloto (incluida la rama
      del PR abierto en `oscamp0-practica`).
    - Con esto, el flujo completo del juego (creación, siembra, issues, revisión automática
      con validación real de la plantilla) queda verificado de punta a punta.
12. **Hallazgo: `nombre-de-rama-valido` fallaba sin ninguna guía de cómo corregirlo** --
    **hecho (2026-11-02)**. Siguiendo probando sobre el mismo PR, Oscar abrió uno desde una
    rama con el nombre genérico por defecto (`oscampo-patch-1`) y encontró que el check
    correspondiente fallaba en rojo sin explicar qué hacer. Se agregó un comentario
    automático (mismo patrón de "marca HTML + actualizar en vez de recrear" que
    `concepto-de-aprobacion.yml`) que explica cómo renombrar la rama desde la pestaña
    Branches.
    - **Corrección sobre la marcha**: el primer texto del comentario afirmaba que renombrar
      la rama desde la pestaña Branches "actualiza el PR automáticamente -- no hace falta
      abrir uno nuevo". Oscar lo probó en vivo (PR real, método oficial: ícono de lápiz en
      Branches) y el PR **sí se cerró**, exactamente igual que si se hubiera borrado la rama
      y creado una nueva -- confirmando el mismo punto de confusión identificado el
      2026-11-01 que motivó todo el rediseño del issue 4 original (ver abajo, "Pivote"). La
      afirmación sobre el comportamiento de GitHub era incorrecta (al menos en este
      contexto); corregida: el comentario ahora dice honestamente que renombrar **sí** cierra
      el PR, que eso es normal, y que el siguiente paso es abrir un PR nuevo desde la rama ya
      renombrada (el trabajo no se pierde). Revisado también `3-algo-no-cuadra.md` (issue del
      juego): su Paso 4 ya decía esto correctamente desde el principio ("puede requerir...
      cerrando el que estaba mal"), así que no necesitó corrección -- solo el comentario
      nuevo tenía la afirmación equivocada. Propagado a `DB2-Training` y a los tres repos de
      piloto.
    - **Lección para el resto del juego**: cualquier guía futura sobre "cómo corregir algo en
      un PR ya abierto" debe asumir que tocar el nombre de la rama (por cualquier método)
      cierra el PR -- no hay atajo conocido para evitarlo.
13. **Hallazgo: corregir el nombre de rama obliga a llenar la plantilla del PR dos veces** --
    **hecho (2026-11-02)**. Siguiendo de §12: Oscar confirmó con captura de pantalla que
    renombrar desde la pestaña Branches (el método "oficial") sí cierra el PR, y notó que
    abrir el PR nuevo significa volver a escribir toda la descripción desde cero -- "doloroso"
    para un estudiante. Dos mitigaciones, ninguna elimina el problema de raíz (no hay forma de
    renombrar una rama sin cerrar su PR), pero lo reducen:
    - **Issue `3` reordenado**: antes, el flujo era abrir el PR con el nombre genérico
      `botDB2-patch-1` primero, ver el check fallar, y recién ahí corregir -- exactamente el
      camino que fuerza a llenar la plantilla dos veces. Ahora el issue enseña a reconocer el
      patrón `usuario-patch-N` (ya visto en el issue `1`) y renombrar la rama *antes* de que
      exista cualquier PR sobre ella -- sin PR abierto, renombrar no tiene efectos
      secundarios. Se pierde la experiencia de "ver un check en rojo" para este caso
      específico, pero esa lección ya se cubre en todo PR vía `concepto-de-aprobacion`
      (siempre rojo hasta que `botDB2` aprueba), así que no es una pérdida real. Se agregó el
      gif 11 (`renombrar-rama.gif`, pendiente de grabar por Oscar).
    - **Para el caso no previsto** (un estudiante crea su propia rama genérica por accidente,
      ej. editando un archivo directo desde `main` sin crear rama antes): el comentario del
      check `nombre-de-rama-valido` ahora sugiere copiar la descripción del PR antes de que
      se cierre (sigue visible en *Pull requests* → filtro *Closed*) en vez de reescribirla, y
      da el tip preventivo de crear la rama con buen nombre antes de tocar archivos.
    - Propagado a `DB2-Training`, a los tres repos de piloto, y al cuerpo ya creado del issue
      `3` en los tres (vía `PATCH .../issues/{n}`, para que coincida con el texto nuevo).
14. **Hallazgo: `botDB2` no aparecía al asignar issues (probando como `ivasaor`), y pregunta
    sobre ocultar a `oscampo`** -- **hecho (2026-11-02)**. Probando como `ivasaor` en
    incógnito, Oscar solo vio a `oscampo` y a sí mismo en el selector de asignados del
    Project Board -- no a `botDB2`. Verificado con `GET /repos/.../assignees/botDB2` (204):
    `botDB2` **sí** es asignable técnicamente; lo que Oscar vio es, con toda probabilidad, la
    lista corta de "sugeridos" que GitHub muestra por defecto en ese selector (no se confirmó
    con una búsqueda explícita por nombre, pero la API es la fuente de verdad de a quién se
    puede asignar). Oscar preguntó además si podía ocultar su propio perfil de esa lista.
    Respuesta: no es posible mientras sea **Owner** de la organización -- GitHub da acceso
    automático a todos los repos a los Owners, sin forma de excluir uno de un repo puntual.
    La alternativa real (mover la administración a una cuenta de servicio aparte, bajando el
    perfil personal de Oscar a un rol sin acceso automático) se le presentó como opción, pero
    Oscar eligió la solución simple: solo guiar por texto. El issue `0` ahora presenta a
    `botDB2` ("tu compañero durante esta práctica, a quien vas a usar para algunas tareas
    específicas que se te explicarán más adelante") y a `oscampo` explícitamente, y aclara
    que no se les debe asignar ningún issue aunque aparezcan como opciones. De paso, se
    corrigió la voz plural que quedaba en el resto del archivo (resabio del modelo de equipo
    anterior al pivote) a singular, para que quedara consistente con el resto del juego.
    Propagado a `DB2-Training`, a los tres repos de piloto, y al cuerpo ya creado del issue
    `0` en los tres.
15. **Causa real de `botDB2` ausente en el selector del Project Board** -- **hecho
    (2026-11-02)**. El acceso a un Project (v2) de GitHub es un control **separado** de ser
    colaborador del repo (`updateProjectV2Collaborators`, vía GraphQL) -- `crear_project.sh`
    solo le daba ese acceso al estudiante, nunca a `botDB2` (que sí es colaborador del repo,
    por eso la API REST `/assignees/botDB2` confirmaba 204 pero el Project Board igual no lo
    mostraba). `crear_juego_onboarding.sh` ahora pasa también a `botDB2` al crear el Project
    de un repo nuevo. Para los tres repos de piloto ya creados: nuevo script
    `dar_acceso_project.sh` (+ workflow `dar-acceso-project.yml`) que corrige un Project
    existente sin crear uno nuevo -- corrido contra los tres, confirmado en el log de Actions
    ("acceso Write otorgado: botDB2").
16. **Hallazgo: nada revisa si lo que el estudiante hizo en el issue `0` está bien antes de
    cerrarlo** -- **decisión tomada (2026-11-02)**. Probando como `ivasaor`, Oscar cerró el
    issue `0` con solo el comentario "hecho." y preguntó si algo verificaba el contenido real
    (vista Roadmap creada, Markers activados, auto-asignación, fechas) -- "respuesta corta:
    no". Comparado con el proyecto real (README de `Plantilla-Proyecto`, §3): ahí fusionar el
    PR tampoco cierra el Issue -- el profesor revisa el documento después del merge y recién
    entonces se cierra. El juego actual no modela ese paso para los issues `1`-`4` (le dice al
    estudiante que cierre apenas se fusiona el PR). Se le presentaron tres opciones a Oscar
    (dejarlo así, agregar el patrón solo por texto, o explorar automatizarlo con el bot) --
    **eligió dejarlo así**: el juego practica la mecánica (ramas, PR, revisión con concepto,
    declaración de IA), no hay contenido real que un profesor revisaría, y simular esa
    revisión sería "teatro sin sustancia". Sin cambios de código para este punto.
17. **Oscar reconsideró §16**: "necesitamos que haya una revisión de que el issue 0 se
    cumplió correctamente... si no pasa nada si lo haces bien o mal, no hay dinámica de
    juego". A diferencia de los issues `1`-`4` (contenido real, sin nada que automatizar sin
    juicio humano), el issue `0` **sí** es puramente mecánico (vista Roadmap, autoasignación,
    fechas) -- verificable sin juicio de contenido. **Hecho (2026-11-02)**: sonda GraphQL
    (`scripts/sonda_project.sh` + `.github/workflows/sonda-project.yml` en `curso-admin`,
    herramienta exploratoria permanente) contra el Project real de `ivasaor-practica` confirmó
    que SÍ es verificable (vista Roadmap, asignados por issue, fechas Start/Target date) -- y
    de paso confirmó el problema real: `ivasaor` había autoasignado los 4 issues y creado la
    vista Roadmap, pero **ninguno tenía fecha de inicio ni final puesta**. "Markers →
    Milestone" no está expuesto en el schema público, queda fuera de lo verificable.
    - Nuevo workflow `revision-issue-0.yml` en `DB2-Training`: al cerrarse el issue "0
      Organiza el proyecto", `botDB2` verifica (vía GraphQL) la vista Roadmap, la
      autoasignación y las fechas de cada issue `1`-`4`; si falta algo, **reabre el issue**
      con un comentario listando exactamente qué falta (mismo patrón que el profesor reabre
      un issue con observaciones en el proyecto real, README §3); si todo está completo, lo
      deja cerrado con un comentario de confirmación. Verificado en seco contra los datos
      reales capturados de `ivasaor-practica` (caso incompleto → reabre con la lista correcta
      de 8 faltantes; caso completo simulado → no reabre, confirma). Propagado a los tres
      repos de piloto.
    - Requiere ampliar `BOT_COMPANERO_TOKEN`: permiso de repo "Issues: Read and write" +
      permiso de organización "Projects: Read" -- pendiente de que Oscar lo agregue (solo se
      puede editar logueado como `botDB2`); prueba en vivo pendiente hasta entonces.
    - Oscar también planteó que el juego necesita **puntaje** para dar motivación ("pensemos
      en que se trata de un juego") -- contradice el "pass/fail, sin nota" fijado en todos los
      README hasta ahora.
18. **Sistema de puntaje** -- **hecho (2026-11-02)**. Decisiones de Oscar: el puntaje **sí
    cuenta para algo real** -- se necesita 80/100 o más para que el juego cuente como
    aprobado (no basta con cerrar los 5 issues de cualquier forma); 5 puntos de penalidad por
    cada "vuelta extra". Posible uso futuro, sin decidir todavía: bonificación sobre la nota
    del primer corte del proyecto real.
    - Fórmula: empieza en 100, -5 por cada `REQUEST_CHANGES` de `botDB2` en cualquier PR del
      repo, -5 por cada reapertura del issue `0`. Decisión de diseño propia (no de Oscar,
      simplificación técnica): contar las vueltas de forma **global**, no atribuidas a un
      issue específico -- evita depender de que GitHub enlace correctamente cada PR con su
      issue, enlace que se rompe si el PR se cierra y se abre uno nuevo (ej. al corregir un
      nombre de rama, ver §13). Se recalcula completo cada vez (nunca acumula historial),
      mismo principio que el resto de los checks del juego.
    - Nuevo `.github/scripts/actualizar_puntaje.js` en `DB2-Training`, llamado al final de
      `revision-del-companero.yml` y `revision-issue-0.yml` (ambos workflows ahora hacen
      checkout del repo para poder ejecutarlo). Publica el resultado en un issue marcador
      nuevo, **"📊 Tu puntaje"**, creado por `crear_juego_onboarding.sh` (sin milestone, para
      no contar como una tarea más) -- para los tres repos de piloto ya sembrados, creado a
      mano vía REST (`gh issue create` usa GraphQL internamente, bloqueado desde esta sesión;
      `gh api POST .../issues` sí funciona).
    - Verificado en seco (no en vivo, requiere el permiso nuevo del token -- ver abajo) contra
      datos simulados: 2 vueltas (1 reapertura + 1 `CHANGES_REQUESTED`) -> 90/100, calculado
      y formateado correctamente.
    - Requiere ampliar `BOT_COMPANERO_TOKEN`: permiso de repo "Issues: Read and write" (ya
      necesario para `revision-issue-0.yml`, ver §15) y permiso de organización
      "Projects: Read" (también ya necesario ahí) -- no hace falta nada adicional solo para
      el puntaje, reutiliza los mismos dos permisos. Prueba en vivo pendiente de que Oscar
      los agregue.
    - Nota operativa: al propagar a los repos de piloto, un `git push` a una rama vieja
      (`archivo-de-prueba`, ya renombrada por Oscar a `nuevo-archivo-de-prueba` en una prueba
      anterior) recreó por accidente esa rama en `oscamp0-practica` desde una copia local
      obsoleta de esta sesión -- no se pudo borrar (el proxy de esta sesión bloquea `git push
      --delete` y `DELETE .../git/refs/...`), queda ahí sin usarse, inofensiva (ningún PR la
      referencia). Oscar puede borrarla a mano si quiere prolijidad.
19. **Prueba en vivo del permiso nuevo del token + hallazgo del puntaje engañoso** -- **hecho
    (2026-11-02)**. Oscar agregó los permisos nuevos a `BOT_COMPANERO_TOKEN` (es un token
    **classic**, no fine-grained como se asumió al principio -- `repo` ya cubría Issues
    read/write, solo hizo falta agregar `read:project`). Reabrir/cerrar el issue `0` de
    `ivasaor-practica` confirmó `revision-issue-0.yml` funcionando en vivo end-to-end
    (reabre con la lista correcta, puntaje se actualiza). Intentar probar el camino del PR
    (`revision-del-companero.yml`) reveló una limitación nueva de esta sesión: **un PR creado
    enteramente desde aquí (incluso vía API, atribuido a `oscampo`) no dispara ningún
    workflow** -- ni los que ya funcionaban antes (`nombre-de-rama-valido`,
    `concepto-de-aprobacion`). Un PR creado por un humano real en el navegador sí dispara
    todo con normalidad (confirmado antes en esta misma sesión, PR #6/#7). Pendiente: Oscar
    debe probar ese camino él mismo con un PR real para confirmar el puntaje ahí también.
    - **Hallazgo real, de Oscar**: con el issue `0` cerrado (1 reapertura) la fórmula daba
      95/100 -- parecía "casi terminado" sin haber tocado los issues `1`-`4`. Nueva fórmula
      (ver cambios de código en `DB2-Training`, mismo día): `20 * issues_completados - 5 *
      vueltas_totales` -- 20 puntos por cada uno de los 5 issues ya **cerrado** (progreso
      real, verificable sin ambigüedad), penalidad global de vueltas igual que antes. El
      veredicto aprobado/no-aprobado (80/100) solo se muestra cuando los 5 están cerrados;
      antes de eso el marcador solo informa progreso. Verificado en vivo: con el issue `0`
      reabierto otra vez (2 reaperturas, 0/5 completados) el marcador mostró correctamente
      **0/100**, no un número inflado.
20. **Bug real de GitHub: la API de Projects v2 no devuelve Start date/Target date** --
    **hecho (2026-11-02)**. Oscar reportó que `ivasaor` puso las fechas (capturas con "Nov
    4, 2026" etc. visibles en la columna de la tabla) y el bot las seguía marcando como
    faltantes. Investigación:
    - Confirmado que el dato SÍ estaba guardado del lado de GitHub: Oscar recargó la página
      completa y las fechas seguían ahí.
    - Sondeado con `__typename` (nuevo, agregado a `sonda_project.sh`) qué tipos de
      `fieldValues` devuelve realmente la API para cada item: `ProjectV2ItemFieldUserValue`,
      `Repository`, `Milestone`, dos `ProjectV2ItemIssueFieldValue` sin identificar, `Title`,
      `Status` -- **nunca** un `ProjectV2ItemFieldDateValue`, ni siquiera para los campos
      automáticos Created/Updated/Closed (que también son tipo fecha).
    - Probado un camino de consulta distinto, `fieldValueByName(name: "Start date")`
      directo sobre el item -- también vacío.
    - Para descartar que fuera un problema de editar desde touch/tablet (la hipótesis
      inicial, porque las capturas de `ivasaor` eran de un iPad): Oscar editó las fechas él
      mismo **desde su PC** -- la sonda, corrida de nuevo, **seguía sin ver el valor**.
      Descarta touch/tablet como causa: es un problema general del campo tipo fecha en la
      API pública de Projects v2, reproducible sin importar el dispositivo.
    - **Decisión**: se quitó la verificación de fechas de `revision-issue-0.yml` -- mantenerla
      bloquearía a cualquier estudiante sin que hubiera nada mal de su parte. Ahora solo
      verifica vista Roadmap + autoasignación (ambos confirmados confiables). El comentario
      de éxito le pide al estudiante que revise las fechas él mismo, sin automatizarlo.
    - **Corrección de puntaje**: las 5 reaperturas que `ivasaor` sufrió por este bug (todas
      con "falta la fecha" en el cuerpo) no se pudieron borrar del historial del repo --
      intentar `DELETE .../issues/comments/{id}` fue bloqueado por el clasificador de
      permisos de la sesión (acción destructiva en un sistema externo). En vez de eso,
      `actualizar_puntaje.js` ahora excluye del conteo cualquier comentario de reapertura
      que contenga "falta la fecha" (único del chequeo ya removido) -- corrige el puntaje
      sin tocar el historial de GitHub. Verificado en vivo: issue `0` de `ivasaor` cerrado
      de nuevo sin reabrir, puntaje correcto en **20/100 (1/5 completado, 0 vueltas)**.
    - **Confirmación definitiva (mismo día)**: Oscar notó que en `biomed` (repo de equipo
      real, Project creado a mano, en uso activo desde hace meses) el panel "Fields" del
      issue SÍ muestra Start date/Target date editables ahí mismo -- a diferencia de
      `ivasaor-practica`, donde esa sección ni aparece. Esto sugería que el bug podía ser
      específico de cómo `crear_project.sh` crea los Projects por script. Se sondeó
      `biomed` con la misma consulta GraphQL contra el issue exacto de la captura (`#20`,
      "14 Definición de Estrategia Regulatoria...", con Start date Sep 24 y Target date Oct
      9 visibles en la UI) -- **mismo resultado: la API tampoco devuelve el valor ahí**.
      Descarta la hipótesis de "es por cómo se creó el Project vía script": el bug se
      reproduce igual en un Project real, creado a mano, con datos en uso real. Confirma que
      la decisión de quitar el chequeo automático de fechas fue correcta y no hay arreglo
      posible desde este lado -- es un límite real de la API pública de GitHub Projects v2.
      La diferencia del panel "Fields" (presente en `biomed`, ausente en
      `ivasaor-practica`) queda como misterio cosmético aparte, sin relación con la
      disponibilidad del dato por API.
    - Pendiente, sin prisa: decidir cuándo fusionar `feature/juego-onboarding-github` a
      `main` -- el juego entra en uso hasta el próximo semestre. Posible seguimiento, no
      urgente: reportarle este bug a GitHub Support (reproducible con el issue `#20` de
      `biomed` como ejemplo real y verificable).
21. **Investigación web antes de descartarlo del todo** -- **hecho (2026-11-02)**. Oscar
    pidió no descartar el chequeo de fechas sin revisar ampliamente si es un bug conocido.
    Búsqueda + dos fetches completos:
    - [Issue #66, mochan-tk/agentic-dev-kit-for-codex](https://github.com/mochan-tk/agentic-dev-kit-for-codex/issues/66)
      -- reporta `gh project item-list --format json` devolviendo `null` para campos de
      fecha personalizados llamados literalmente "Start date"/"Target date" -- pero dice que
      el GraphQL `fieldValues` crudo (mismos fragmentos que usamos: `ProjectV2ItemFieldDateValue`)
      **sí** devolvía el valor correcto ahí. Es decir: en su caso el bug estaba en el CLI, no
      en GraphQL -- el opuesto de lo que esperábamos.
    - [Discusión de la comunidad #195849, "Timestamp fields in GitHub Projects"](https://github.com/orgs/community/discussions/195849)
      -- un comentario (OffCrazyFreak, jul 2026) confirma el mismo síntoma que nosotros, pero
      para los campos automáticos **Created/Updated/Closed** (no para campos de fecha
      personalizados como los nuestros): "Querying `ProjectV2Item.fieldValues` returns
      entries for every other field on an item... but nothing at all for the CREATED /
      UPDATED / CLOSED fields." Sin respuesta de GitHub, discusión marcada "Unanswered".
    - **Ninguna fuente encontrada confirma exactamente nuestro síntoma** (campo de fecha
      *personalizado*, tipo DATE, invisible tanto por GraphQL `fieldValues` como por
      `fieldValueByName` como por el CLI `gh project item-list`). Para asegurarse de que no
      era simplemente que el CLI "sí" funcionara (como en el caso de mochan-tk), se probó
      `gh project item-list 56 --owner DisenoBiomedico-2 --format json` contra
      `ivasaor-practica` -- **el campo ni siquiera aparece como clave en el JSON** (más
      grave que un `null`: directamente ausente). Confirma que en nuestro caso **ningún**
      camino (GraphQL `fieldValues`, `fieldValueByName`, ni el CLI oficial) expone el valor,
      a diferencia del caso de mochan-tk donde GraphQL sí funcionaba.
    - **Conclusión**: parece ser un bug genuino de GitHub Projects v2 para campos de fecha
      personalizados -- posiblemente menos reportado que el de los campos automáticos
      Created/Updated/Closed (ese sí tiene un hilo de comunidad), pero con el mismo patrón
      de fondo (GitHub expone el campo en `ProjectV2.fields` pero no su valor por item). La
      decisión de quitar el chequeo automático de fechas del bot se mantiene -- no hay
      camino de API conocido, documentado o encontrado que lo resuelva hoy. Vale la pena
      reportarlo a GitHub Support con la evidencia reunida (dos proyectos reales,
      `ivasaor-practica` y `biomed`, tres caminos de consulta distintos, todos fallando
      igual) cuando Oscar tenga tiempo -- no es la prioridad ahora mismo del juego.
