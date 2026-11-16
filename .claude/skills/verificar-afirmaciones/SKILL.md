---
name: verificar-afirmaciones
description: Verifica afirmaciones factuales de un político (cifras, fechas, hechos, citas) con búsqueda web y fuentes fiables, y asigna a cada una un veredicto con su fuente. Úsala dentro de analizar-entrevista o cuando el usuario pida comprobar si una declaración es cierta.
---

# Verificar afirmaciones

Para cada elemento de `afirmaciones[]`:

1. **Normaliza** la afirmación: qué se afirma exactamente, sobre qué periodo y territorio, y con qué unidad (por ejemplo, "tasa de paro EPA, España, 3T 2026, ha bajado 2 puntos interanuales").
2. **Busca** con WebSearch y lee las fuentes con WebFetch. Orden de preferencia:
   1. Fuentes primarias oficiales: institutos de estadística (INE, Eurostat…), boletines oficiales (BOE…), diarios de sesiones, presupuestos, bancos centrales, sentencias.
   2. Organismos independientes (AIReF, Tribunal de Cuentas, OCDE, FMI…).
   3. Verificadores con metodología pública (miembros de IFCN, como Newtral, Maldita o EFE Verifica en España).
   4. Prensa de referencia, preferiblemente de líneas editoriales distintas para un mismo dato.
   Evita usar como única fuente los medios de partido, las redes sociales o las webs sin autoría.
3. **Fecha:** compara con el dato disponible *en la fecha de la entrevista*. Si el dato se ha revisado después, dilo.
4. **Veredicto:**
   - `verdadero`: coincide con las fuentes, con un margen razonable de redondeo.
   - `mayormente_verdadero`: correcto en lo esencial, con una imprecisión menor.
   - `enganoso`: literalmente defendible pero induce a error (falta contexto, periodo elegido a conveniencia, comparación que no se sostiene).
   - `falso`: contradicho por fuentes fiables.
   - `no_verificable`: no hay datos públicos fiables, o es una previsión u opinión.
5. **Registra** `explicacion` (1–3 frases con el dato real y la diferencia) y `fuentes` (título, URL y fecha). Nunca inventes una URL: cita solo páginas que hayas abierto o que hayan salido en los resultados de búsqueda.

## Contrastar tesis

Para cada tesis con componente empírico (ver `../analizar-entrevista/criterios.md`):

1. **Reformula la tesis como pregunta investigable**, neutral y sin presuponer la respuesta.
   Ejemplo: «la polarización es asimétrica» se convierte en «¿qué dice la investigación sobre qué actores generan más polarización afectiva o descalificaciones en España?».
2. **Busca en este orden:**
   1. Literatura académica: revisiones y artículos revisados por pares (REIS, *Revista Española de Ciencia Política*, *South European Society and Politics*…), con búsquedas tipo `site:scholar.google.com`, `site:dialnet.unirioja.es` o el nombre del indicador más «estudio».
   2. Datos oficiales y comparativos: CIS, INE, Eurostat, V-Dem, OCDE, Banco de España.
   3. Think tanks y centros de estudios de **orientaciones distintas**, y verificadores.
3. **Busca expresamente evidencia en contra** de la tesis, con al menos una búsqueda formulada en sentido opuesto.
4. Resume la evidencia con sus límites (periodo, método, alcance) y asigna `valoracion` y `confianza` según `criterios.md`.
5. Escribe `argumentos_a_favor` y `argumentos_en_contra` en su versión más sólida, cada uno con su base si la tiene.
6. Marca el `tipo` de cada fuente (`academica | oficial | think_tank | verificador | prensa`).

## Límites
- Si hay muchas afirmaciones, prioriza las centrales de la entrevista y las de severidad potencial alta. Marca las demás como `no_verificable` con la explicación "no verificada por alcance".
- Si dos fuentes fiables discrepan, recoge ambas y baja la confianza en lugar de elegir una.
