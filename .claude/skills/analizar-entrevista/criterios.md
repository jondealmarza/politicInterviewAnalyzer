# Criterios de análisis

## Reglas generales

1. **Sin cita no hay hallazgo.** La cita se copia literal de `transcripcion.md` con su marca `[hh:mm:ss]`.
2. **Solo se verifican hechos.** "Es la peor ley de la historia" es una opinión. "El paro ha bajado un 20 %" es verificable.
3. **Mismo umbral para todos**, sea cual sea el partido o la ideología. Las retóricas parecidas se clasifican igual.
4. **Interpretación caritativa:** si una frase admite una lectura razonable que no es un error, esa es la que se toma. Las frases ambiguas se marcan con confianza baja.
5. **La transcripción puede fallar.** Si una cifra "falsa" puede deberse a un error de los subtítulos automáticos, se indica en la explicación y se baja la confianza.
6. Las interrupciones del entrevistador o la falta de tiempo no cuentan como evasiva del entrevistado.

## Categorías

### `falsedad`: afirmación falsa o engañosa
Afirmación verificable que contradicen fuentes fiables (veredicto `falso`), o que siendo literalmente cierta
induce a error por omitir contexto, elegir mal el periodo o comparar cosas que no son comparables (veredicto `enganoso`).
- En `etiqueta` se usa "Afirmación falsa" o "Afirmación engañosa".
- Solo se usa **"Mentira"** si hay pruebas documentadas, con fuente, de que sabía que era falso (por ejemplo, rectificó antes o tenía el dato oficial delante). Esas pruebas van en `explicacion`.

### `error_factual`
Dato incorrecto que parece un desliz sin intención: una fecha, un nombre, un orden de magnitud o un cargo
equivocado, sin que la conclusión del argumento dependa de él. Si el argumento descansa en el dato, es `falsedad`.

### `incoherencia`
- **Interna:** dos afirmaciones de la misma entrevista que no pueden ser ciertas a la vez. Se citan ambas (`cita` y `cita_relacionada`).
- **Externa:** contradice declaraciones previas documentadas de la misma persona. Requiere fuente con fecha.
- Cambiar de postura con el tiempo no es incoherencia si se reconoce el cambio. Sí lo es negar haber dicho algo que consta.

### `evasiva`: ausencia de respuesta clara
La respuesta no contesta a lo que se preguntó. Señales:
- responde a otra pregunta parecida pero más cómoda;
- generalidades sin compromiso ("trabajaremos para…", "lo importante es…") ante una pregunta concreta (sí/no, cifra, fecha);
- remite a otro momento ("ya lo explicaremos") sin dar ningún dato;
- el entrevistador repite la pregunta y sigue sin respuesta, lo que agrava la severidad.
Se registra en `preguntas[].respuesta = "ninguna" | "parcial"` y, si es significativa, también como hallazgo.

### `desvio`: desvío de atención
Se cambia el foco para no tratar el asunto:
- **"Y tú más" (whataboutism):** responde señalando faltas ajenas.
- **Cambio de tema:** salta a un asunto no preguntado.
- **Ataque personal (ad hominem):** al entrevistador, al medio o al rival en lugar del argumento.
- **Hombre de paja:** reformula la pregunta o la postura contraria de forma distorsionada y responde a esa versión.
- **Apelación emocional** que sustituye a la respuesta (no la que la acompaña).
El subtipo concreto se indica en `subtipo`.

## Severidad
- `alta`: afecta al núcleo del asunto preguntado o es un dato con mucho impacto público, o es una evasiva reiterada a una pregunta central.
- `media`: relevante, pero no central.
- `baja`: menor o periférica.

## Confianza
`alta | media | baja`: grado de seguridad en el hallazgo, teniendo en cuenta la calidad de la transcripción, la ambigüedad y la solidez de las fuentes.

## Índice de respuestas claras
`completas / total de preguntas` (las parciales cuentan 0,5). Solo cuentan preguntas genuinas, no saludos ni réplicas breves.

## Esquema de `hallazgos.json`

```json
{
  "metadatos": { "titulo": "", "politico": "", "partido_cargo": "", "entrevistador": "", "medio": "",
                 "fecha_entrevista": "AAAA-MM-DD", "fuente": "URL o ruta", "duracion": "hh:mm:ss",
                 "calidad_transcripcion": "manual | subtitulos_autor | subtitulos_automaticos | whisper | texto_usuario" },
  "preguntas": [
    { "id": "P1", "tiempo": "00:01:12", "pregunta": "resumen", "respuesta": "completa | parcial | ninguna",
      "comentario": "por qué" }
  ],
  "afirmaciones": [
    { "id": "A1", "tiempo": "00:03:40", "cita": "literal", "afirmacion": "lo que se afirma, normalizado",
      "veredicto": "verdadero | mayormente_verdadero | enganoso | falso | no_verificable",
      "explicacion": "", "fuentes": [ { "titulo": "", "url": "", "fecha": "AAAA-MM-DD" } ] }
  ],
  "hallazgos": [
    { "id": "H1", "categoria": "falsedad | error_factual | incoherencia | evasiva | desvio",
      "subtipo": "opcional", "etiqueta": "texto corto para el informe",
      "tiempo": "00:03:40", "cita": "literal", "cita_relacionada": "opcional (incoherencia)",
      "pregunta_id": "opcional", "afirmacion_id": "opcional",
      "explicacion": "neutral y concreta", "severidad": "alta | media | baja", "confianza": "alta | media | baja",
      "fuentes": [ { "titulo": "", "url": "", "fecha": "" } ] }
  ],
  "resumen": { "por_categoria": { "falsedad": 0, "error_factual": 0, "incoherencia": 0, "evasiva": 0, "desvio": 0 },
               "indice_respuestas_claras": 0.0, "valoracion_general": "2–4 frases neutrales" },
  "limitaciones": [ "" ]
}
```
`fuentes` es obligatorio (al menos una) en `falsedad`, `error_factual`, en las `incoherencia` externas y en toda afirmación con veredicto distinto de `no_verificable`.
