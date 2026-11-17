# 4.2 · Ejercicios de subprogramas

<div class="ej-gate" data-unit="u04" data-nombre="U4 · Programación orientada a objetos"></div>

Para cada ejercicio, define primero el contrato (parámetros, modo de paso, qué significa cada estado) antes de escribir el código.

## Ejercicio 4.1.1

Escribe un subprograma SALUDAR que reciba un nombre (`PIC X(15)`, `BY REFERENCE`) desde el programa principal y muestre `Hola, <nombre>` él mismo (no el programa principal).

## Ejercicio 4.1.2

Cambia el ejemplo de `DOBLE` de la teoría para que triplique en vez de duplicar. ¿Hasta qué valor de `WS-NUMERO` sigue cabiendo el resultado en `PIC 9(3)`?

<details class="sol" data-key="p4/4.1.2">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>       IDENTIFICATION DIVISION.
       PROGRAM-ID. PRINCIPAL.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-NUMERO PIC 9(3) VALUE 21.
*&gt; ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 4.1.3

Añade al ejemplo de `CONTAR` una tercera variable, pero declarada en `LOCAL-STORAGE SECTION` con `VALUE 100`. ¿Qué muestra en cada una de las tres llamadas?

## Ejercicio 4.1.4

Escribe un subprograma `DIVIDIR` que reciba dos números y un estado por parámetro, y que marque el estado como error (sin dividir) si el divisor es 0.

<details class="sol" data-key="p4/4.1.4">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>       IDENTIFICATION DIVISION.
       PROGRAM-ID. PRINCIPAL.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-A PIC 9(3) VALUE 17.
*&gt; ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 4.1.5

En el ejemplo de `DOBLE`, cambia `BY REFERENCE` por `BY CONTENT` en la llamada y añade un `DISPLAY WS-NUMERO` después de la llamada, en el programa principal. ¿Qué cambia, y por qué?
