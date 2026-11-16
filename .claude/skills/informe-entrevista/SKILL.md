---
name: informe-entrevista
description: Genera el informe HTML autocontenido de una entrevista analizada a partir de hallazgos.json y transcripcion.md, con resumen, transcripción anotada y tabla de verificaciones. Úsala dentro de analizar-entrevista o para regenerar un informe.
---

# Informe de entrevista

Entradas: `hallazgos.json` y `transcripcion.md` de la carpeta de la entrevista. Plantilla: `plantilla.html` (en esta carpeta).

1. Lee la plantilla y sustituye cada marcador `{{...}}` según los comentarios `<!-- -->` que lo acompañan.
2. Conserva el CSS de la plantilla. No añadas librerías externas: el informe debe abrirse sin conexión.
3. **Escapa el HTML** de todo texto que venga de la transcripción o de fuentes (`&`, `<`, `>`, `"`).
4. **Transcripción anotada:** copia cada intervención de `transcripcion.md` como un `<p class="turno">`. Envuelve la cita de cada hallazgo en
   `<mark class="cat-<categoria>" id="H<n>" tabindex="0">…<span class="nota"><b>etiqueta</b> · severidad · explicación</span></mark>`.
   Si la cita no aparece literal (por diferencias de espacios o puntuación), localiza el fragmento equivalente. Nunca alteres el texto citado.
5. **Contraste de tesis:** rellena `{{CONTRASTES}}` con una tarjeta `<article class="tesis" id="T<n>">` por cada elemento de `contrastes[]`, en el orden de la entrevista y con el resumen de `resumen.contrastes` delante. En la tabla de preguntas, la columna «Contraste» enlaza a `#T<n>` si la pregunta tiene `contraste_id`. Usa las insignias `val-<valoracion>`, que tienen colores propios para no confundirlas con las categorías de hallazgo. Los textos de valoración son: Respaldada, Parcialmente respaldada, Contradicha, Evidencia dividida y «Juicio de valor · sin veredicto» para `no_aplica`.
6. **Verificación por pregunta:** cada fila de la tabla de afirmaciones lleva `id="A<n>"`. En la tabla de preguntas, la columna «Verificación» enlaza a `#A<n>` solo para las afirmaciones con veredicto `falso` o `enganoso` cuyo `pregunta_id` es esa pregunta. Si a una afirmación le falta `pregunta_id`, se asigna a la última pregunta anterior por tiempo.
7. Las marcas de tiempo enlazan al vídeo cuando la fuente es YouTube: `https://www.youtube.com/watch?v=<id>&t=<segundos>s`.
8. Guarda el resultado como `informe.html` en la carpeta de la entrevista. Ábrelo con `start "" "<ruta>"` solo si el usuario lo pide.
9. Si el usuario quiere compartirlo, ofrécete a publicarlo como Artifact privado.

## Lenguaje del informe
Neutral y descriptivo. Las etiquetas son las de `criterios.md`. Nada de adjetivos sobre la persona ("mentiroso", "manipulador").
La sección de metodología y limitaciones es obligatoria.
