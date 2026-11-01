# Plan: flujo real de trabajo con ramas/PR/revisión + juego de onboarding (Semana 1)

Estado: **borrador para discutir** — no implementado todavía.

Confirmado con Oscar (2026-11-01): el flujo de ramas + Pull Request + revisión entre pares +
CI descrito en §2 **es el nuevo flujo real del curso**, no solo del juego — reemplaza al
`fixes #N` + push directo documentado hoy en `README.md`. El juego de la Semana 1 (§4) ensaya
exactamente este flujo real, no una versión simplificada. El uso de IA está **permitido si se
declara**; la falta sancionable es no declarar un uso real, no el uso en sí.

## 1. Problema que resuelve

Dos problemas relacionados:
1. El flujo real se está volviendo más exigente (branching, PR, revisión de pares, CI,
   declaración de IA) justo cuando los equipos recién llegan al curso — alto riesgo de que la
   primera entrega real falle por mecánica de herramienta, no por el contenido biomédico.
2. No existe hoy ninguna automatización ni plantilla que haga cumplir ese flujo — depende
   100% de que cada estudiante se acuerde de cada paso.

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
| 5 | La rama se fusiona solo con revisión/aprobación del compañero correcto (el otro asignado al mismo Issue); quien revisa llena datos para que el CI pase y se habilite el merge | **Branch protection** en `main`: requiere PR + ≥1 aprobación + *status checks* obligatorios | Reglas de protección de rama + Action que valida que el review tenga contenido real (no solo "LGTM") — ver §3 |
| 6 | El estudiante también actúa como revisor del trabajo de otro | Mismo mecanismo de review de GitHub, desde el otro lado | Ninguno nuevo — se mide con la API (igual que `revisar_pendientes.sh`) |
| 7 | Aportar al trabajo de un compañero con el mismo Issue, en la misma rama (no anidar ramas) | Varios colaboradores push a la misma rama (ya tienen permiso de escritura) | Nota en la plantilla de PR, no requiere automatización |
| 8 | Día de reunión: el profesor revisa el Project Board, sigue el link al documento/carpeta, verifica `USO-IA.md`, verifica rama fusionada + Issue cerrado, revisa las razones del revisor y puede pedir cambios | Flujo manual del profesor, apoyado en un reporte (ver §5) | Extensión de `revisar_pendientes.sh` — ver §5 |

Nota sobre el cierre de Issues: con "Closes #N" (o "Fixes #N") en la **descripción del PR**,
GitHub cierra el Issue automáticamente al fusionar — no hace falta un mecanismo nuevo para
"el Issue debe quedar closed", ya es nativo de GitHub en cuanto el PR correcto se fusiona.

## 3. Artefactos nuevos a construir (para `Plantilla-Proyecto`, se propagan a los 6 repos de equipo)

### 3.1 `.github/PULL_REQUEST_TEMPLATE.md`
Debe forzar, como mínimo:
- `Closes #___` (vincula y automatiza el cierre del Issue al fusionar).
- Qué documento/carpeta resuelve esta rama (ruta dentro del DHF).
- Resumen de 2-3 líneas de lo hecho.
- Checkbox: "¿Usaste IA para generar o asistir este documento?" Sí/No.
  - Si Sí: recordatorio explícito de completar `USO-IA.md` en este mismo PR.
- Checklist de cierre: nombre de rama descriptivo / documento enlazado en la tarjeta del
  Project Board / `USO-IA.md` actualizado si aplica.

### 3.2 `USO-IA.md` (plantilla, vive en la raíz de cada repo de equipo)
Log append-only, una entrada por uso declarado:
```
## [Fecha] — [Nombre del estudiante]
- Herramienta: (ej. ChatGPT, Claude, Copilot)
- Documento/parte: (ruta del archivo) — permalink: (link a la línea/rango exacto)
- Qué se hizo con el resultado: (ej. "se usó como borrador y se reescribió", "se verificaron
  las cifras contra la norma X", "se usó tal cual")
```

### 3.3 Action — nombre de rama válido
Dispara en `pull_request` (opened/synchronize). Rechaza:
- Patrones por defecto de GitHub: `^[\w.-]+-patch-\d+$`.
- Nombres sin estructura descriptiva (ej. largo mínimo, debe tener al menos una palabra
  reconocible, no solo números/hashes).
Se recomienda convención `<issue>-<slug-descriptivo>` (ej. `14-marco-teorico`) — así el
nombre queda automáticamente ligado al Issue sin pedirle al estudiante que lo escriba dos
veces, y es trivial de validar con una expresión regular.
Falla como *status check* requerido → bloquea el botón de merge mientras no se corrija.

### 3.4 Action — contenido mínimo de la revisión
Dispara en `pull_request_review` (submitted). Verifica que el cuerpo de la revisión no esté
vacío/sea un simple "ok"/"lgtm" (longitud mínima + no coincide con una lista de respuestas
genéricas). Falla como *status check* requerido si no cumple.

*No* se automatiza (fase 1) la validación de que el revisor sea exactamente "el otro
asignado al Issue" — eso lo confirma el profesor el día de la reunión (es justo el punto 8
del flujo: pregunta sobre las razones dadas). Se puede automatizar después si se vuelve un
problema recurrente.

### 3.5 Branch protection en `main`
- Require pull request before merging.
- Require ≥1 approving review.
- Require status checks: nombre de rama válido + contenido de revisión válido.
- No permitir force-push ni bypass salvo el profesor (mismo patrón ya usado con el push
  ruleset de `.github/workflows/**`).

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

## 5. Verificación — extensión de `revisar_pendientes.sh`

Para el checklist previo a la reunión semanal (ya existe en `curso-admin`), agregar por
Issue/PR:
- ¿Rama fusionada y PR cerrado, o sigue abierto?
- ¿La revisión tiene contenido (no solo aprobación vacía)? ¿Quién la hizo?
- ¿El PR declaró uso de IA? Si sí, ¿hay una entrada correspondiente en `USO-IA.md` con
  permalink?
Esto le da al profesor, antes de entrar a cada Issue en la reunión, exactamente los datos que
el paso 8 del flujo dice que debe verificar — sin tener que abrir cada PR manualmente primero.

## 6. Rúbrica de puntaje/penalidad (borrador — necesito que ajustes los pesos exactos)

| Etapa | Qué se evalúa | Tipo | Peso sugerido |
|---|---|---|---|
| Organización del Project Board | Tareas del corte actual cargadas y asignadas | Automático | Bajo (requisito de entrada) |
| Autoasignación | Cada estudiante tiene ≥1 tarea propia | Automático | Bajo |
| Nombre de rama + plantilla de PR | Descriptivo, plantilla completa | Automático (bloquea merge) | Estructural — no es que "reste puntos", es que no deja avanzar |
| `USO-IA.md` | Declarado cuando corresponde | Automático (¿existe archivo tocado en el PR?) + confianza en que la declaración es honesta | **Alto si se detecta uso no declarado** — es una falta de integridad académica, no un descuento menor |
| Revisión entre pares (como autor) | Recibió una revisión con contenido real antes de fusionar | Automático + manual (calidad, se evalúa en la reunión) | Medio-alto |
| Revisión entre pares (como revisor) | Hizo al menos una revisión real en el corte | Automático (conteo vía API) | Medio |
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

## 8. Fases de construcción

1. `.github/PULL_REQUEST_TEMPLATE.md` + `USO-IA.md` (plantilla) en `Plantilla-Proyecto`.
2. Action de nombre de rama válido + Action de contenido mínimo de revisión.
3. Branch protection rules en `main` (requiere los dos checks + 1 aprobación).
4. Propagar los 3 artefactos anteriores a los 6 repos de equipo (mismo patrón que
   `propagar_archivo_plantilla.sh` de `curso-admin`).
5. Extender `revisar_pendientes.sh` con los datos de §5.
6. Repo de práctica del juego (Semana 1), reutilizando los artefactos de los pasos 1-3.
7. Piloto con un equipo o estudiante voluntario antes de lanzar a todo el curso.
