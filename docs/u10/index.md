# U10 · Búsquedas y ordenación

Buscar un elemento en una tabla sin recorrerla a mano (`SEARCH`, `SEARCH ALL`) y **ordenar**: a mano con un algoritmo clásico (burbuja) sobre una tabla en memoria, y con el verbo `SORT` sobre un archivo. Incluye cómo se relaciona esto con DFSORT y JCL en un mainframe real.

## Teoría

* [10.1 Buscar en una tabla](01-busquedas.md): `SEARCH` (secuencial) y `SEARCH ALL` (binaria, con `ASCENDING KEY`).
* [10.2 Ordenar](02-ordenar.md): burbuja a mano sobre una tabla, el verbo `SORT` sobre archivos, y DFSORT/JCL en mainframe real.

## Antes de empezar: qué debes dominar

* `SEARCH`: búsqueda secuencial sobre una tabla `OCCURS`.
* `SEARCH ALL`: búsqueda binaria; exige `ASCENDING KEY` y una tabla realmente ordenada.
* Ordenar una tabla en memoria a mano: el algoritmo de la burbuja.
* `SORT`: ordenar un archivo completo con una sola instrucción (`USING`/`GIVING`).
* En mainframe real: DFSORT por JCL y el `SORT` de COBOL pueden convivir y hasta compartir motor.

## Ejercicios de la unidad

| Bloque | Ejercicios |
|---|---|
| [10.3 · Ejercicios de búsquedas y ordenación](ejercicios-busquedas-ordenacion.md) | 4 |

## Antes de pasar a los ejercicios

Cuando hayas leído y practicado la teoría, marca la casilla para **desbloquear** los ejercicios de esta unidad:

<div class="ej-check" data-unit="u10"></div>
