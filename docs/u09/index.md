# U9 · Archivos indexados (sin SQL real)

COBOL no tiene SQL ni clases de conexión. Lo más parecido que tiene de forma nativa a una base de datos es un archivo **`ORGANIZATION IS INDEXED`**: registros con una clave, para llegar a uno directamente en vez de leerlos todos. Incluye cómo se relaciona esto de verdad con VSAM y Db2 en un mainframe (teoría, no algo que se pueda ejecutar aquí).

## Teoría

* [9.1 Archivos indexados](01-archivos-indexados.md): `ORGANIZATION IS INDEXED`, `RECORD KEY`, `START`/`READ`/`REWRITE`/`DELETE` y claves alternativas.
* [9.2 VSAM y Db2 en mainframe real](02-vsam-db2.md): solo teoría, no ejecutable aquí.

## Antes de empezar: qué debes dominar

* Archivos `INDEXED`: `RECORD KEY`, `WRITE`/`READ`/`REWRITE`/`DELETE` y sus códigos `FILE STATUS`.
* Recorrer en orden: `START` y `READ ... NEXT RECORD`.
* Clave alternativa (`ALTERNATE RECORD KEY`) para buscar por un segundo campo.
* En mainframe real: VSAM (KSDS) y Db2 con SQL embebido coexisten; no es que uno sustituya al otro.

## Ejercicios de la unidad

| Bloque | Ejercicios |
|---|---|
| [9.3 · Ejercicios de archivos indexados](ejercicios-archivos-indexados.md) | 4 |

## Antes de pasar a los ejercicios

Cuando hayas leído y practicado la teoría, marca la casilla para **desbloquear** los ejercicios de esta unidad:

<div class="ej-check" data-unit="u09"></div>
