# Revisión de entrevistas

Analizador de entrevistas a políticos. Lee una entrevista y detecta:

- **Afirmaciones falsas o engañosas** y **errores factuales**, verificados con fuentes.
- **Incoherencias** dentro de la entrevista o con declaraciones anteriores.
- **Evasivas**: preguntas que no reciben una respuesta clara.
- **Desvíos de atención**: cambios de tema en lugar de responder.

También contrasta con evidencia las tesis de fondo del entrevistado. El resultado es un informe HTML con citas literales, marcas de tiempo y la fuente de cada veredicto.

La finalidad es ayudar a evaluar el discurso político con criterios iguales para cualquier partido, separando los hechos comprobables de las opiniones.

## Cómo funciona

No hay código propio. Todo el proyecto está hecho con [Claude Code](https://claude.com/claude-code): skills (`.claude/skills/`), hooks (`.claude/settings.json`) y `CLAUDE.md`. Claude hace la transcripción, la verificación en la web y la redacción del informe.

## Uso

Abre Claude Code en la carpeta del proyecto y ejecuta:

```
/analizar-entrevista <entrada>
```

Como `<entrada>` acepta:

| Entrada | Ejemplo |
|---|---|
| Enlace de YouTube | `/analizar-entrevista https://youtu.be/XXXXXXXX` |
| Audio o vídeo local | `/analizar-entrevista C:\grabaciones\entrevista.mp3` |
| Archivo de texto | `/analizar-entrevista doc/sanchez.md` |
| Texto pegado | `/analizar-entrevista <pega aquí la transcripción>` |

Si falta el nombre del político o la fecha de la entrevista, Claude te los pedirá, porque los datos se verifican tal como estaban en esa fecha.

## Resultado

Cada análisis crea `entrevistas/<AAAA-MM-DD>-<slug>/` con:

- `informe.html`: el informe. Ábrelo en el navegador.
- `hallazgos.json`: el análisis estructurado del que sale el informe.
- `transcripcion.md`: la transcripción con hablantes y marcas de tiempo.
- `metadatos.json`: título, medio, fecha, político y entrevistador.

Para regenerar solo el informe a partir de un `hallazgos.json` existente, usa `/informe-entrevista`.

## Requisitos

- [Claude Code](https://claude.com/claude-code).
- Solo para YouTube o audio/vídeo:

  ```
  winget install yt-dlp.yt-dlp Gyan.FFmpeg
  pip install openai-whisper
  ```

  Con texto no hace falta nada más. Al abrir la sesión, un hook avisa si falta alguna herramienta.

## Criterios editoriales

- **Falso no es mentira.** Se habla de "afirmación falsa". "Mentira" solo se usa si hay pruebas de que el político sabía que era falso.
- **Solo se verifican hechos.** Las opiniones y los juicios de valor no son ni verdaderos ni falsos.
- **Todo hallazgo lleva cita literal y marca de tiempo**, y todo veredicto factual lleva la URL de una fuente fiable.
- **El mismo umbral para todos**, sin adjetivos valorativos sobre la persona.
- **Ante la duda, no se afirma.** Se marca como `no_verificable` o con confianza baja.

Un hook revisa `hallazgos.json` antes de generar el informe y lo rechaza si no cumple estos criterios.

Los detalles (taxonomía, severidad y esquema JSON) están en [`CLAUDE.md`](CLAUDE.md) y en [`.claude/skills/analizar-entrevista/criterios.md`](.claude/skills/analizar-entrevista/criterios.md).

> El análisis lo hace un modelo de lenguaje y puede contener errores. Revisa las fuentes enlazadas antes de citar un hallazgo.
