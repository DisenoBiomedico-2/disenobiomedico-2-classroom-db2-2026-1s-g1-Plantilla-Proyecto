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

Nota sobre el cierre de Issues: con "Closes #N" (o "Fixes #N") en la **descripción del PR**,
GitHub cierra el Issue automáticamente al fusionar — no hace falta un mecanismo nuevo para
"el Issue debe quedar closed", ya es nativo de GitHub en cuanto el PR correcto se fusiona.

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
- La plantilla de PR real que ya usan los equipos (`.github/pull_request_template.md`: Qué se
  hizo / Qué decisión se tomó y por qué / Qué queda pendiente / `Close #`) — **necesito leer
  el archivo real de un repo de equipo** (ej. Bionex) antes de recrearla aquí; no quiero
  inventar de nuevo un texto que ya existe y que los estudiantes ya conocen. Es también donde
  se agrega el nuevo checkbox de IA (ver 3.3).
- Corregir el `README.md` de esta plantilla: describe `fixes #N` + push directo y 3
  milestones; la realidad son PR+revisión+concepto y 4 milestones (falta "Semana 2 -
  Definición del Reto").

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

## 4. El juego de Semana 1 — "MedGit: Simulacro de Laboratorio"

Mismo concepto narrativo que la versión anterior de este plan (laboratorio médico ficticio,
"órdenes de trabajo" = Issues, "certificación" al completarlas), pero ahora las órdenes
ensayan el flujo **completo** de §2, no una versión simplificada:

1. **Orden 0**: aceptar el repo de práctica, organizar el Project Board con las órdenes 1-6.
2. **Orden 1**: asignarse responsables (mínimo 2 personas por orden, para poder practicar
   revisión cruzada real) y autoasignarse al menos una.
3. **Orden 2**: crear una rama con nombre descriptivo ligado a la orden, abrir el PR,
   completar la plantilla.
4. **Orden 3 (con IA)**: una orden que explícitamente pide usar una IA para redactar un
   fragmento, y declarar correctamente en `USO-IA.md` — practica la declaración, no solo la
   mecánica de Git.
5. **Orden 4**: actuar como revisor de la rama de un compañero (el otro asignado a la misma
   orden): dejar una revisión con contenido real y aprobar o pedir cambios.
6. **Orden 5**: colaborar en la rama de un compañero con el mismo Issue (co-escribir sin
   crear una rama anidada).
7. **Orden 6 — trampa a propósito**: un PR que alguien "ya abrió" pero con nombre de rama
   genérico, o con `USO-IA.md` no declarado a pesar de evidencia de uso de IA, o con una
   revisión tipo "lgtm" — la misión es detectar y corregir el error, no solo ejecutar un paso.

Reutiliza exactamente los artefactos de §3 — el repo de práctica corre con las mismas
plantillas/Actions/protecciones que los repos reales, así la práctica es 1:1, no una
simulación aparte que hay que volver a traducir.

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

## 7. Decisiones aún abiertas

- **Repo de práctica del juego**: ¿individual por estudiante o por equipo? (sigue abierta de
  la versión anterior de este plan).
- **Peso en la nota del juego de Semana 1**: ¿gate informal sin nota, o un pequeño porcentaje
  simbólico?
- **Mínimo de personas por Issue real**: el flujo de revisión cruzada (paso 5-6) requiere que
  cada Issue tenga ≥2 asignados. ¿Es una regla dura (ningún Issue con 1 solo responsable) o
  una recomendación?
- **Automatizar "el revisor correcto"** (fase 2, no ahora): ¿vale la pena construir la
  verificación de que quien aprobó es específicamente el otro asignado al Issue, o el control
  manual del profesor en la reunión es suficiente?

## 8. Fases de construcción (revisado tras §0)

1. Leer la plantilla de PR real de un repo de equipo (ej. Bionex) para no reinventarla.
2. Traer a `Plantilla-Proyecto`: ruleset `pr-obligatorio` (nuevo script, estilo
   `crear_ruleset_workflows.sh`), workflow `concepto-de-aprobacion.yml` + `APROBAR_UN_PR.md`
   (ya probados en `curso-admin/concepto-aprobacion/`), y la plantilla de PR real del paso 1
   con el checkbox de IA agregado.
3. Corregir el `README.md` de `Plantilla-Proyecto` (4 milestones, flujo PR+revisión+concepto
   en vez de `fixes #N`).
4. Agregar la creación del ruleset `pr-obligatorio` a `crear_repos_grupos.sh`, para que los
   repos nuevos nazcan protegidos (igual patrón que ya existe para el ruleset `restriction`).
5. Construir lo genuinamente nuevo: `USO-IA.md` (plantilla) + Action de nombre de rama válido
   (agregada como status check al mismo `pr-obligatorio`).
6. Agregar la verificación de `USO-IA.md` a `reporte_contribucion_docs.sh`.
7. Propagar todo lo anterior a los 6 repos de equipo existentes (`propagar_archivo_plantilla.sh`).
8. Repo de práctica del juego (Semana 1), reutilizando los artefactos ya puestos al día.
9. Piloto con un equipo o estudiante voluntario antes de lanzar a todo el curso.
