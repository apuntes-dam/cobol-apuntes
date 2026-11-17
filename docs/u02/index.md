# U2 · Estructuras de control

Las estructuras que controlan el flujo del programa: decidir (`IF`, selección múltiple con `EVALUATE`) y repetir (`PERFORM`, en [1.6](../u01/06-bucles.md)). Como COBOL no tiene excepciones, se ve cómo controla los errores de verdad: validando antes y con `ON SIZE ERROR`. Incluye cómo **depurar** un programa.

## Teoría

* [2.1 Condicionales](01-condicionales.md): `IF`/`ELSE` y selección múltiple con `EVALUATE`.
* **2.2 Bucles**: se explican en el apartado [1.6 de la unidad 1](../u01/06-bucles.md).
* [2.3 Controlar errores](02-errores.md): COBOL no tiene excepciones; validar antes con `IS NUMERIC` y `ON SIZE ERROR`.
* [2.4 Depurar](03-depurar.md): `DISPLAY` de traza, y los avisos de `cobc -Wall`/`-debug`.

## Antes de empezar: qué debes dominar

* Condicionales: `IF`/`ELSE` y selección múltiple con `EVALUATE`/`EVALUATE TRUE`.
* Bucles: las variantes de `PERFORM` (ver la unidad 1).
* Controlar errores: COBOL no tiene excepciones; validar con `IS NUMERIC` y `ON SIZE ERROR`.
* Depuración: `DISPLAY` de traza y los avisos de `cobc -Wall`/`-debug`.

## Ejercicios de la unidad

| Bloque | Ejercicios |
|---|---|
| [P2.1 · Sentencias condicionales](condicionales.md) | 10 |
| [P2.2 · Sentencias iterativas y saltos](bucles.md) | 25 |
| [P2.3 · Captura de excepciones](excepciones.md) | 5 |
| [P2.4 · Depurar programas](depurar.md) | 1 |

## Antes de pasar a los ejercicios

Cuando hayas leído y practicado la teoría, marca la casilla para **desbloquear** los ejercicios de esta unidad:

<div class="ej-check" data-unit="u02"></div>
