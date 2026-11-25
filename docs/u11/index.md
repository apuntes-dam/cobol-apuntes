# U11 · Textos y fechas

COBOL no tiene un tipo `DATE`: una fecha es un número `AAAAMMDD` y un conjunto de `FUNCTION` (`CURRENT-DATE`, `INTEGER-OF-DATE`, `DATE-OF-INTEGER`) para calcular con ella sin reinventar el calendario. Incluye las funciones de texto que la unidad 3 no cubrió y el formateo con `PICTURE` de edición (números y fechas con separadores, sin tocar el dato real).

## Teoría

* [11.1 Fechas](01-fechas.md): `FUNCTION CURRENT-DATE`, `INTEGER-OF-DATE`, `DATE-OF-INTEGER`.
* [11.2 Formatear texto y números](02-formatear.md): `UPPER-CASE`/`LOWER-CASE`/`REVERSE`/`LENGTH` y `PICTURE` de edición.

## Antes de empezar: qué debes dominar

* Fechas: `FUNCTION CURRENT-DATE`, sumar días y restar fechas con `INTEGER-OF-DATE`/`DATE-OF-INTEGER`.
* El problema del año con 2 dígitos (Y2K) en archivos heredados, y por qué sigue siendo relevante.
* Funciones de texto: `UPPER-CASE`, `LOWER-CASE`, `REVERSE`, `LENGTH`.
* `PICTURE` de edición: formatear números y fechas con un simple `MOVE`, sin `STRING`.

## Ejercicios de la unidad

| Bloque | Ejercicios |
|---|---|
| [11.3 · Ejercicios de textos y fechas](ejercicios-textos-fechas.md) | 3 |

## Antes de pasar a los ejercicios

Cuando hayas leído y practicado la teoría, marca la casilla para **desbloquear** los ejercicios de esta unidad:

<div class="ej-check" data-unit="u11"></div>
