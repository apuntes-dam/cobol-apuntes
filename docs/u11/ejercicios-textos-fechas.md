# 11.3 · Ejercicios de textos y fechas

<div class="ej-gate" data-unit="u11" data-nombre="U11 · Textos y fechas"></div>

Para el ejercicio 11.1.2 (solo explicación), entrega la respuesta razonada, no código.

## Ejercicio 11.1.1

Escribe `DIAS-HASTA-NAVIDAD`: calcula cuántos días faltan desde una fecha dada (`PIC 9(8)`, valor `20261201`) hasta el 25 de diciembre de ese mismo año (si la fecha dada ya fuera posterior, el resultado saldría negativo; no hace falta tratar ese caso aparte).

<details class="sol" data-key="p11/11.1.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>       IDENTIFICATION DIVISION.
       PROGRAM-ID. DIAS-HASTA-NAVIDAD.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01  WS-FECHA  PIC 9(8) VALUE 20261201.
*&gt; ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 11.1.2

Sin escribir código: un archivo antiguo guarda la fecha con `PIC 9(6)` (`AAMMDD`, dos dígitos de año). Para pasarla a `FUNCTION INTEGER-OF-DATE` (que exige año de 4 dígitos), hay que decidir el siglo. Si el campo vale `260315`, ¿qué fecha completa es? ¿Y si valiera `990315`?

## Ejercicio 11.2.1

Escribe `NOMBRE-COMPLETO`: dados un nombre y un apellido en dos campos `PIC X(15)`, muestra el nombre completo en mayúsculas, sin los espacios de relleno de ninguno de los dos campos.

<details class="sol" data-key="p11/11.2.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>       IDENTIFICATION DIVISION.
       PROGRAM-ID. NOMBRE-COMPLETO.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01  WS-NOMBRE   PIC X(15) VALUE "Ana".
*&gt; ... (resto de la solución bloqueado)</code></pre></div>
</details>
