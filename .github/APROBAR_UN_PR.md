# Cómo aprobar un Pull Request en este curso

Para fusionar un PR a `main` hace falta la aprobación de un compañero **con un concepto escrito**. Aprobar es un acto de ingeniería: significa que revisaste el trabajo y puedes decir qué revisaste y por qué lo das por bueno.

## Dónde se escribe

1. Abre el PR y entra a la pestaña **Files changed**.
2. Pulsa el botón verde **Review changes**.
3. Escribe el concepto en el cuadro de texto.
4. Elige **Approve** y pulsa **Submit review**.

Un comentario suelto en la conversación del PR **no cuenta**: tiene que ir dentro de la aprobación.

## Qué escribir

Copia estas tres etiquetas y complétalas. Las tres son obligatorias y el texto completo debe tener al menos 80 caracteres:

```text
Revisé: qué archivos o partes revisaste y contra qué (issue, criterio, norma).
Hallazgos: lo que encontraste, aunque sea "no encontré diferencias".
Decisión: por qué apruebas.
```

## Ejemplo válido

```text
Revisé: Presupuesto_Detallado.md contra el issue 18 y la lista de materiales.
Hallazgos: los precios coinciden con las cotizaciones adjuntas y el total suma correctamente.
Decisión: apruebo porque el documento cumple lo pedido en el issue.
```

## Lo que no sirve

`ok`, `Aprobado`, `Revisado`, `LGTM`, o un texto largo sin las tres etiquetas.

## Cosas a tener en cuenta

- Si al revisar encuentras algo que se debe corregir, no apruebes todavía: elige **Request changes** o deja tus comentarios, y aprueba cuando esté corregido.
- Cada vez que el autor sube un cambio nuevo al PR, las aprobaciones anteriores se descartan y hay que aprobar de nuevo, con concepto.
- Si hay varias aprobaciones en el PR, todas deben traer concepto.
- Los PR que no cambian ningún archivo (por ejemplo, los de sincronización con `main`) no requieren concepto.
- Si la aprobación no cumple, el estado `concepto-de-aprobacion` queda en rojo en la caja de fusión del final del PR y dice qué falta.
