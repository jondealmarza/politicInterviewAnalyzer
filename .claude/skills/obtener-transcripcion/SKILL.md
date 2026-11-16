---
name: obtener-transcripcion
description: Obtiene y limpia la transcripción de una entrevista desde un enlace de YouTube (yt-dlp), un audio/vídeo local (whisper) o un texto, con hablantes identificados y marcas de tiempo. Úsala dentro de analizar-entrevista o cuando el usuario solo quiera la transcripción.
---

# Obtener transcripción

Trabaja dentro de la carpeta de la entrevista (`entrevistas/<...>/`). Los archivos temporales van a `tmp/`, dentro de esa carpeta, y se borran al terminar.

## YouTube

1. Metadatos:
   `yt-dlp --skip-download --print "%(title)s|%(uploader)s|%(upload_date)s|%(duration_string)s" "<URL>"`
2. Subtítulos, preferiblemente los del autor:
   `yt-dlp --skip-download --write-subs --write-auto-subs --sub-langs "es.*,es" --sub-format vtt -o "tmp/subs.%(ext)s" "<URL>"`
   - Si hay subtítulos manuales (sin `auto` en el nombre), úsalos (`calidad_transcripcion: subtitulos_autor`). Si no, usa los automáticos (`subtitulos_automaticos`).
   - Si la entrevista no está en español, ajusta `--sub-langs` al idioma real.
3. Si no hay subtítulos, descarga el audio y sigue el apartado **Audio/vídeo**:
   `yt-dlp -x --audio-format mp3 -o "tmp/audio.%(ext)s" "<URL>"`

## Audio/vídeo local

`whisper "<archivo>" --language es --model small --output_format vtt --output_dir tmp`
- `--model medium` es más preciso pero más lento. Usa `small` salvo que el usuario pida más calidad.
- Para audios largos (más de 30 min), avisa al usuario del tiempo estimado antes de lanzar el comando y ejecútalo en segundo plano.
- `calidad_transcripcion: whisper`.

## Texto

Léelo tal cual (archivo o texto pegado). Si es .srt o .vtt, trátalo como subtítulos. Si no tiene marcas de tiempo,
usa referencias de párrafo `[§12]` en lugar de `[hh:mm:ss]`. `calidad_transcripcion: texto_usuario` (o `manual`).

## Limpieza (la hace Claude, sin scripts)

Los subtítulos automáticos de YouTube repiten cada línea 2–3 veces en bloques que se solapan. Lee el VTT y:
1. Elimina las etiquetas `<c>`, las marcas internas `<00:00:01.234>` y las cabeceras.
2. Fusiona el texto repetido en frases continuas, conservando la marca de tiempo del inicio de cada intervención.
3. Identifica a los hablantes por el contexto: preguntas, turnos, "señor/señora…", nombres. Usa `**Entrevistador (Nombre):**` y `**Nombre Político:**`. Si una atribución es dudosa, márcala con `(?)` y añádela a las limitaciones.
4. No corrijas lo que se dijo. Como mucho, marca `[inaudible]` o `[¿cifra?]` donde los subtítulos estén claramente rotos.

## Salida

- `transcripcion.md`: cabecera con los metadatos y después las intervenciones:
  ```
  [00:01:12] **Entrevistadora (Ana Pérez):** ¿Va a subir los impuestos?
  [00:01:18] **Juan García:** Mire, lo importante es...
  ```
- `metadatos.json`: los campos de `metadatos` del esquema de `../analizar-entrevista/criterios.md`.
- Borra `tmp/` al terminar.
