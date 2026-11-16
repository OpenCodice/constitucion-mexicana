# constitucion-mexicana — guía para agentes

Texto vigente de la Constitución Política de los Estados Unidos Mexicanos, un
archivo por artículo, versionado para que el historial de git sea el registro
de sus reformas. Es un repo de datos: aquí no hay código.

**No edites a mano nada de `articulos/` ni de `metadata/`.** Todo se genera con
[`constitucion-extractor`](https://github.com/OpenCodice/constitucion-extractor)
a partir de `fuente/CPEUM.pdf`. Si un artículo está mal, el arreglo va en el
extractor; la siguiente vigilancia lo trae aquí.

## Estructura

- `articulos/NNN.md` — el texto. Es lo único que cuenta como "la ley".
- `metadata/` — derivada y regenerable: índice de artículos, fechas de reforma,
  bloques (`segmentos/`) y pasajes citables con su página del PDF
  (`pasajes.jsonl`).
- `fuente/CPEUM.pdf` — el PDF oficial del que salió la versión actual.

## Cómo cambia este repo

El workflow `vigilar-*` corre cada lunes 09:00 UTC (y a mano con
`gh workflow run`). Descarga el PDF oficial, regenera con el extractor y, si
cambió texto, lanza un agente (Claude Code) que compara cada unidad cambiada
contra el PDF y repara el extractor si hace falta. Después abre un PR:

- Con la etiqueta `verificado`, `revisar-pr` corre los invariantes (`validar`)
  y lo fusiona solo, con squash.
- Si no quedó verificado, no se abre PR: el texto vigente se queda como está,
  lo no publicado se anota en el resumen de la corrida y la siguiente vigilancia
  lo reintenta. Nada se asigna a una persona.

El título del commit lo declara el agente y distingue una **reforma** (cambió la
ley) de una **corrección de extracción** (misma ley, mejor lectura del PDF). No
lo cambies a mano al fusionar: la bitácora dependerá de él.

Un job `salud` renueva el cron en cada corrida y, si el workflow falla, lo
relanza una vez; si el reintento también falla, deja un issue (sin asignar) como
constancia y la vigilancia de la semana siguiente lo vuelve a intentar.

## Lo único que se edita aquí

Los workflows de `.github/workflows/` y la documentación. Trabaja en `main`,
con `git pull --ff-only` antes; `main` exige el check `validar`.
