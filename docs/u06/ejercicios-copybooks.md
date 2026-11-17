# 6.2 · Ejercicios de copybooks y diseño modular

<div class="ej-gate" data-unit="u06" data-nombre="U6 · Diseño de programas con POO"></div>

Para los ejercicios de solo explicación, entrega la respuesta razonada, no solo código.

## Ejercicio 6.1.1

Crea un copybook `producto.cpy` con código, nombre y precio, y un programa que lo incorpore con `COPY`, rellene los tres campos y los muestre.

<details class="sol" data-key="p6/6.1.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>       IDENTIFICATION DIVISION.
       PROGRAM-ID. USARPRODUCTO.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       COPY "producto.cpy".
*&gt; ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 6.1.2

Añade `CIUDAD PIC X(20)` al copybook `cliente.cpy` de la teoría y recompila `usar-copy.cob`. ¿Qué otros programas tendrías que revisar si `cliente.cpy` lo usaran varios programas distintos, y por qué?

## Ejercicio 6.1.3

Declara dos grupos con un subcampo de **igual nombre** y otro de **distinto nombre**, y usa `MOVE CORRESPONDING` entre ellos. Comprueba con `DISPLAY` qué se copió y qué no.

## Ejercicio 6.1.4

¿Qué diferencia práctica hay entre inicializar un grupo campo a campo con `MOVE` y hacerlo de una vez con `INITIALIZE`? Pruébalo sobre un grupo con varios `PIC 9` y `PIC X`.
