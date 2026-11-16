# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Qué es este proyecto

Analizador de entrevistas a políticos que detecta afirmaciones falsas o engañosas, incoherencias, evasivas
(preguntas sin respuesta clara), desvíos de atención y errores factuales, y genera un informe HTML.

**No hay código propio.** El proyecto está construido solo con skills, hooks y este archivo. Claude Code
es el motor de análisis. Si hace falta lógica nueva, se escribe como instrucciones en una skill y no como
scripts.

## Uso

```
/analizar-entrevista <URL de YouTube | ruta a audio/vídeo | ruta a .txt/.md | texto pegado>
```

Cada análisis crea `entrevistas/<AAAA-MM-DD>-<slug>/` con:
- `metadatos.json`: título, medio, fecha, político, entrevistador y fuente.
- `transcripcion.md`: transcripción limpia con hablantes y marcas de tiempo `[hh:mm:ss]`.
- `hallazgos.json`: resultado estructurado del análisis; es la fuente de verdad del informe. Contiene `preguntas`, `afirmaciones`, `hallazgos`, `contrastes` (tesis contrastadas con evidencia), `resumen` y `limitaciones`.
- `informe.html`: informe autocontenido.

## Flujo (skills en `.claude/skills/`)

1. `analizar-entrevista`: orquestador. Detecta el tipo de entrada, crea la carpeta y encadena el resto. La taxonomía de hallazgos y el esquema de `hallazgos.json` están en `criterios.md`, dentro de esa skill.
2. `obtener-transcripcion`: YouTube (yt-dlp), audio/vídeo (whisper) o texto, y escribe `transcripcion.md`.
3. `verificar-afirmaciones`: verificación con WebSearch/WebFetch y veredicto con fuente para cada afirmación.
4. `informe-entrevista`: rellena `plantilla.html` con `hallazgos.json` y genera `informe.html`.

## Hooks (`.claude/settings.json`)

- `SessionStart`: avisa si faltan `yt-dlp`, `ffmpeg` o `whisper`.
- `PostToolUse` (prompt) sobre la escritura de `hallazgos.json`: revisa con un LLM que cada hallazgo tenga
  cita literal y marca de tiempo, que cada veredicto factual tenga fuente, que no se hable de "mentira" sin
  pruebas de intención y que el tono sea neutral. Si lo rechaza, hay que corregir `hallazgos.json` y volver a
  escribirlo antes de generar el informe.

## Principios editoriales (obligatorios)

- **Falso ≠ mentira.** Se usa "afirmación falsa". "Mentira" solo vale si hay pruebas documentadas de que el político sabía que era falso.
- **Opiniones ≠ hechos.** Solo se verifican afirmaciones comprobables. Los juicios de valor no son falsos ni verdaderos.
- **Tesis ≠ dato ≠ valor.** Las tesis de fondo («la polarización es asimétrica») se contrastan con evidencia en `contrastes[]`: solo su parte empírica recibe valoración, con al menos dos fuentes independientes. Los juicios de valor no se juzgan; se exponen los mejores argumentos de cada lado. El listón es el mismo aunque la tesis coincida con la opinión del usuario.
- **Todo hallazgo lleva cita literal y marca de tiempo**, y todo veredicto factual lleva URL de una fuente fiable.
- **Neutralidad:** el mismo umbral para cualquier partido o ideología. Sin adjetivos valorativos sobre la persona.
- **Contexto temporal:** los datos se verifican en la fecha de la entrevista, no en la actual.
- Ante la duda, `no_verificable` o confianza baja. Es mejor no incluir un hallazgo que inventarlo.

## Requisitos (solo para YouTube/audio; el texto funciona sin nada)

```
winget install yt-dlp.yt-dlp Gyan.FFmpeg
pip install openai-whisper
```
