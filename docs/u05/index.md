# U5 · Sin herencia ni polimorfismo

COBOL no tiene herencia ni polimorfismo en tiempo de ejecución. La alternativa procedural: `REDEFINES` para reinterpretar los mismos bytes, y un campo discriminador con `EVALUATE`.

## Teoría

* [5.1 Sin herencia ni polimorfismo](01-sin-herencia.md): `REDEFINES` y un campo discriminador con `EVALUATE`, la alternativa procedural.

## Antes de empezar: qué debes dominar

* `REDEFINES`: otra vista de los mismos bytes, sin convertir nada.
* Un campo discriminador (`WS-TIPO`) y `EVALUATE` para decidir el comportamiento según el caso.
* Por qué esto no es polimorfismo de verdad, y qué significa diseñar bien sin herencia.

## Ejercicios de la unidad

| Bloque | Ejercicios |
|---|---|
| [5.2 · Ejercicios sin herencia ni polimorfismo](ejercicios-sin-herencia.md) | 4 |

## Antes de pasar a los ejercicios

Cuando hayas leído y practicado la teoría, marca la casilla para **desbloquear** los ejercicios de esta unidad:

<div class="ej-check" data-unit="u05"></div>
