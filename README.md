*Plantilla de tu Proyecto: Edita esta plantilla usando el ícono del lápiz y coloca la información de tu grupo.*


# Proyecto de Diseño Biomédico 2

**Integrantes del Equipo:**
*   Estudiante 1
*   Estudiante 2
*   Estudiante 3
*   ...

**Nombre del Proyecto:** [Escribir aquí el nombre]
**Stakeholder/Cliente:** [Nombre de la empresa, institución o usuario]

---

## ⚠️ Instrucciones de Gestión (LEER OBLIGATORIAMENTE)
<details> <summary> Click aquí para expandir </summary>
   
Para la evaluación de este curso, utilizamos la metodología **"Si no está en el Issue, no existe"**.

### 1. Configuración Inicial (Semana 1)
El repo de su equipo **ya nace con esto hecho automáticamente**, no hay que crearlo:
*   Los 4 milestones del semestre, con fecha límite: `Semana 2 - Definición del Reto`,
    `Semana 5 - Diseño Conceptual`, `Semana 11 - Pruebas y Validación`, `Semana 17 - Entrega Final`.
*   Todas las tareas iniciales (`0.0`, `0.1`, `01`-`32`) ya creadas como Issues y asignadas al
    milestone correspondiente.
*   El **Project Board** ("Cronograma del Proyecto - *su repo*"), ya vinculado al repositorio,
    con todos esos Issues importados y acceso de escritura para todo el equipo.

Lo único que queda a mano, porque GitHub no lo permite por API, es la **vista** del Project:
1.  Abran la pestaña **Projects** → el Project ya vinculado.
2.  Creen una vista **Roadmap** (`+` junto a las vistas existentes → Layout: Roadmap). Si
    GitHub les pide crear los campos **Start date** y **Target date** (o **End date**),
    créenlos.
3.  En el menú de configuración de la vista (ícono de controles deslizantes), activen
    **Markers → Milestone** — así se ve en el Roadmap la marca de inicio y fin de cada uno
    de los 3 cortes del semestre (Semana 1 a 5, 6 a 11, 12 a 17).
4.  Para cada tarea: asignen un **Responsable** (puede ser más de uno) y coloquen una
    **fecha de inicio y una fecha final**, dentro de la ventana de su milestone —
    cada integrante debe quedar asignado a al menos una tarea propia.
5.  Guarden la vista (**Save view**) para que quede disponible para todo el equipo.
6.  Opcional: creen una vista adicional tipo **Table** (con la columna **Milestone**
    agregada) o **Board** agrupada por `Status`, para otras formas de ver el mismo tablero.

### 2. Cómo desarrollar y entregar una tarea
El profesor **NO revisará** carpetas al azar buscando archivos. El profesor revisará el
**Project Board** y los Pull Requests. No se trabaja con push directo a `main`: cada tarea se
desarrolla en su propia rama y llega a `main` mediante un **Pull Request (PR)** revisado por
un compañero.

1.  **Creen una rama** para el Issue que les corresponde, con un nombre **descriptivo** —
    *no* el genérico que GitHub ofrece por defecto al editar un archivo desde la web (del
    tipo `usuario-patch-1`). Ejemplos válidos: `marco-teorico`, `diseno-de-detalle`,
    `protocolo-pruebas-usabilidad`.
2.  Trabajen su documento en esa rama. Si **usaron IA** para generarlo o asistirlo, declárenlo
    en [`USO-IA.md`](USO-IA.md) antes de pedir la revisión: qué herramienta, para qué parte
    (con un [permalink](https://docs.github.com/articles/getting-permanent-links-to-files) a
    las líneas) y qué hicieron con el resultado. Usar IA no es una falta — no declararlo
    cuando sí se usó, sí lo es.
3.  **Abran el Pull Request** hacia `main`. Se carga automáticamente una plantilla: complétenla
    (qué se hizo, qué decisión se tomó y por qué, qué queda pendiente, el checklist, y el
    número del Issue al final).
4.  **Pidan la revisión a un compañero.** Para que el PR se pueda fusionar, la aprobación debe
    traer un concepto escrito (no basta un "ok" o "LGTM") — ver
    [`.github/APROBAR_UN_PR.md`](.github/APROBAR_UN_PR.md) para el formato exacto. Mientras
    falte o no sea válido, el check `concepto-de-aprobacion` queda en rojo al final del PR y
    no se habilita el botón de fusión.
5.  Al fusionarse el PR, el documento ya queda en `main` — pero eso **no cierra el Issue**.
    El cierre definitivo lo decide el profesor (ver sección 3).

### 3. Revisión del profesor
Después de fusionado su PR, el profesor revisa el documento y deja sus observaciones como
**comentarios del Issue correspondiente**, cada uno con un
[permalink](https://docs.github.com/articles/getting-permanent-links-to-files) a la línea o
rango exacto del documento (vista `code` del archivo → seleccionar línea(s) → `...` →
**Copy permalink**). Si hay observaciones, reabre el Issue con un comentario pidiendo que se
corrija; si no corrigen dentro del plazo, lo cierra sin más oportunidad. Cuando corrijan
(en una rama/PR nueva o adicional sobre el mismo Issue), comenten en el Issue y ciérrenlo
ustedes, o esperen a que el profesor lo revise y lo cierre.

---

## 📂 Estructura del Repositorio (Design History File)

Este repositorio organiza el proyecto separando la evidencia documental de los archivos técnicos de ingeniería.

*   **/01_Gestion_del_Proyecto**: Cronograma, Presupuesto, Licencias, Cartas de intención, Acuerdos legales,...

*   **/02_DHF_Proceso_Diseno**: **DOCUMENTACIÓN (INFORMES Y EVIDENCIA)**
    *   *Fase_1_Investigacion*: Definición de requerimientos, normas y estado del arte, etc
    *   *Fase_2_Conceptual*: Matrices de selección, bocetos y definición de arquitectura, etc
    *   *Fase_3_Diseno_Detalle*: **Justificación ingenieril.** Aquí van los informes de cálculos, resultados de simulaciones, BOM final y especificaciones técnicas.
    *   *Fase_4_Validacion*: Protocolos de pruebas, resultados de validación con usuarios, análisis estadístico, etc

*   **/03_Ingenieria_Tecnica**: **ARCHIVOS FUENTE EDITABLES (HERRAMIENTAS)**
    *   *Calculos_Simulaciones*: Archivos de Excel, Scripts (MATLAB/Python), Simulaciones (Ansys/Proteus), simulaciones de SolidWorks, etc.
    *   *Mecanica_CAD*: Archivos nativos de modelado 3D y planos de fabricación.
    *   *Electronica*: Esquemáticos, diseño de PCB (Gerbers) y listas de materiales.
    *   *Software_Firmware*: Código fuente documentado.

*   **/04_Multimedia_Prototipo**: **MATERIAL DE DIVULGACIÓN.**
    *   Fotos de alta calidad del prototipo final.
    *   Video de funcionamiento (Demo) para la presentación final.
    *   Renders comerciales o material para el Póster/Pitch.

*   **/05_Reporte_Final_ABET**: 
    *   Aquí se entregará el documento final compilado ("Capstone Project") siguiendo la plantilla institucional.
    *   Este documento debe alimentarse de la información generada en la carpeta `02_DHF`.

</details>

---

## 📝 Resumen del Problema
*(Editar esta sección en la Semana 2)*
Describa brevemente en 1 párrafo cuál es la necesidad clínica o problema que están resolviendo.
