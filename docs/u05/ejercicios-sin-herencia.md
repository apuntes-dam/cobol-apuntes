# 5.2 · Ejercicios sin herencia ni polimorfismo

<div class="ej-gate" data-unit="u05" data-nombre="U5 · Herencia, clases abstractas e interfaces"></div>

Antes de programar, decide qué campo es el discriminador y qué vistas (`REDEFINES`) tienen sentido con cada valor.

## Ejercicio 5.1.1

Escribe un programa `FIGURA` que, según un campo `WS-TIPO` (`C` círculo con `WS-DATOS` como radio, `R` rectángulo con `WS-DATOS`/`WS-DATOS2` como base y altura), calcule el área con un `EVALUATE` — como en la teoría, pero para dos figuras en vez de persona/empresa.

<details class="sol" data-key="p5/5.1.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>       IDENTIFICATION DIVISION.
       PROGRAM-ID. FIGURA.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-TIPO PIC X VALUE "C".
*&gt; ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 5.1.2

Amplía el ejemplo de `CLIENTE` (persona/empresa) de la teoría para añadir un tercer tipo `A` (autónomo), con su propia vista `REDEFINES` (nombre y NIF) y su propio `WHEN`.

## Ejercicio 5.1.3

¿Qué muestra el programa de `CLIENTE` de la teoría si cambias `WS-TIPO` a `X` (un valor no previsto)? ¿Por qué es importante tener siempre un `WHEN OTHER`?

## Ejercicio 5.1.4

Añade al ejemplo de `CLIENTE` un contador `WS-DESCONOCIDOS` que sume uno cada vez que se entra por `WHEN OTHER`, a lo largo de varias comprobaciones con distintos valores de `WS-TIPO`.
