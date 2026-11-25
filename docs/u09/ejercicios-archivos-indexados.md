# 9.3 · Ejercicios de archivos indexados

<div class="ej-gate" data-unit="u09" data-nombre="U9 · Archivos indexados (sin SQL real)"></div>

Para el ejercicio 9.1.3 (solo explicación), entrega la respuesta razonada, no código.

## Ejercicio 9.1.1

Escribe `ALTA-PRODUCTOS`: un archivo `INDEXED` de productos (`COD-PROD` numérico de 4 dígitos como `RECORD KEY`, `NOM-PROD` de texto). Da de alta tres productos con códigos distintos, comprobando `FILE STATUS` después de cada `WRITE` y mostrando un mensaje si alguno fallara.

<details class="sol" data-key="p9/9.1.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>       IDENTIFICATION DIVISION.
       PROGRAM-ID. ALTA-PRODUCTOS.
       ENVIRONMENT DIVISION.
       INPUT-OUTPUT SECTION.
       FILE-CONTROL.
*&gt; ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 9.1.2

Escribe `BUSCAR-PRODUCTO`: abre el archivo de `9.1.1` y pide un código por teclado (`ACCEPT`). Si existe, muestra su nombre; si no, muestra `Producto no encontrado` sin tratarlo como un error del programa (recuerda qué `FILE STATUS` da un `READ` por una clave que no está).

<details class="sol" data-key="p9/9.1.2">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>       IDENTIFICATION DIVISION.
       PROGRAM-ID. BUSCAR-PRODUCTO.
       ENVIRONMENT DIVISION.
       INPUT-OUTPUT SECTION.
       FILE-CONTROL.
*&gt; ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 9.1.3

Sin escribir código: un programa hace `OPEN I-O` sobre el archivo de productos y, sin leer antes, ejecuta `MOVE 20 TO COD-PROD` seguido de `REWRITE R-PROD`. ¿Funciona? Explica qué exige `REWRITE` que no exige `DELETE`, y por qué tiene sentido esa diferencia.

## Ejercicio 9.1.4

Cambia el archivo de `9.1.1` para que tenga además una clave alternativa `CAT-PROD` (categoría, con duplicados permitidos). Escribe `LISTAR-CATEGORIA`: pide una categoría por teclado y muestra todos los productos de esa categoría, parando cuando `CAT-PROD` deje de coincidir (no cuando se acabe el archivo).
