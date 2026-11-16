---
name: analizar-entrevista
description: Analiza una entrevista a un político (enlace de YouTube, audio/vídeo local o texto) y detecta afirmaciones falsas, incoherencias, evasivas, desvíos de atención y errores factuales, con un informe HTML. Úsala cuando el usuario pida analizar, revisar o verificar una entrevista, un debate o unas declaraciones políticas.
argument-hint: <URL de YouTube | ruta a audio/vídeo | ruta a texto | texto pegado>
---

# Analizar entrevista

Entrada: `$ARGUMENTS`. Si está vacía, pide la entrevista al usuario.

## 1. Preparar

1. Detecta el tipo de entrada:
   - `youtube.com` / `youtu.be`: **youtube**
   - ruta existente con extensión de audio/vídeo (mp3, wav, m4a, ogg, flac, mp4, mkv, webm, mov): **audio**
   - ruta existente .txt / .md / .srt / .vtt: **texto-archivo**
   - cualquier otra cosa: **texto-pegado**
2. Crea `entrevistas/<AAAA-MM-DD>-<slug>/`, con la fecha de hoy y un slug corto con el político y el medio si ya se conocen (si no, se usa `entrevista`).
3. Sigue la skill **obtener-transcripcion** para generar `transcripcion.md` y `metadatos.json`.
   Si no se conoce el político o la fecha de la entrevista, pregúntalo, porque afecta a la verificación.

## 2. Analizar (dos pasadas)

Lee `criterios.md` (en esta carpeta) antes de empezar y aplícalo literalmente.

**Pasada A: estructura.** Recorre la transcripción completa y:
- Segmenta en pares pregunta–respuesta (`preguntas[]`), cada uno con su marca de tiempo.
- Para cada pregunta, resume lo que se pedía y valora la respuesta: `completa | parcial | ninguna`.
- Extrae las afirmaciones verificables (cifras, fechas, hechos, citas atribuidas, comparaciones) en `afirmaciones[]`, todavía sin veredicto.
- Anota las afirmaciones que puedan chocar con otras de la misma entrevista.

**Pasada B: hallazgos.** Con la estructura delante, identifica los hallazgos por categoría según `criterios.md`.
Cada hallazgo exige cita literal copiada tal cual de la transcripción y su marca de tiempo.

## 3. Verificar

Sigue la skill **verificar-afirmaciones** sobre `afirmaciones[]`. Las que salgan `falso` o `enganoso` pasan a ser
hallazgos de categoría `falsedad` o `error_factual`, según `criterios.md`.

## 3b. Pasada C: contraste de tesis

Con la sección «Contraste de tesis» de `criterios.md` delante:
1. De las respuestas `completa` y `parcial`, extrae las tesis de fondo que defiende el entrevistado. Pueden ser causales («X provoca Y»), comparativas («estamos mejor que…»), de eficacia («la medida funciona») o de reparto de responsabilidad («la crispación la generan otros»).
2. Elige las 5–8 más centrales y clasifica cada una (`empirica | empirica_disputada | valorativa | mixta`).
3. Para las que tengan parte empírica, sigue la sección **«Contrastar tesis»** de la skill **verificar-afirmaciones**.
4. Para las valorativas, no investigues un veredicto: recoge los mejores argumentos de cada lado.
5. Rellena `contrastes[]` y `resumen.contrastes`, y añade a `preguntas[]` el campo `"contraste_id": "T<n>"` cuando corresponda.

## 4. Guardar y revisar

Escribe `hallazgos.json` con el esquema de `criterios.md`, **siempre con la herramienta Write** (no con Bash), porque al escribirlo así salta un hook de revisión de calidad.
Si lo rechaza, corrige lo indicado y vuelve a escribir el archivo. No sigas hasta que pase.

## 5. Informe

Sigue la skill **informe-entrevista**. Al terminar, da al usuario:
- la ruta absoluta de `informe.html`
- un resumen de 3–5 líneas: recuento por categoría, índice de respuestas claras y los 2–3 hallazgos más graves
- las limitaciones relevantes (subtítulos automáticos, afirmaciones no verificables, hablantes inferidos)
