# 7.2 · Ejercicios de archivos de texto

<div class="ej-gate" data-unit="u07" data-nombre="U7 · Archivos (sin GUI)"></div>

Para el ejercicio 7.1.4 (solo explicación), entrega la respuesta razonada, no código.

## Ejercicio 7.1.1

Cambia `ESCRIBIR-TAREAS` para que escriba tres líneas (tres tareas distintas), con un `MOVE` y un `WRITE` para cada una, comprobando `ESTADO-ARCHIVO` después de cada `WRITE`.

<details class="sol" data-key="p7/7.1.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>       IDENTIFICATION DIVISION.
       PROGRAM-ID. ESCRIBIR-TAREAS.
       ENVIRONMENT DIVISION.
       INPUT-OUTPUT SECTION.
       FILE-CONTROL.
*&gt; ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 7.1.2

Cambia `LEER-TAREAS` para que, además de mostrar cada línea, cuente cuántas ha leído y muestre el total al final (`Total: 3`, por ejemplo).

## Ejercicio 7.1.3

Proyecto: escribe `CONTAR-LINEAS`, que abra `entrada.txt` para lectura y cuente cuántas líneas están vacías y cuántas no (una línea con solo espacios cuenta como vacía), mostrando los dos totales al final. Compara éxito, fin de archivo y error como tipos de resultado distintos.

<details class="sol" data-key="p7/7.1.3">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>       IDENTIFICATION DIVISION.
       PROGRAM-ID. CONTAR-LINEAS.
       ENVIRONMENT DIVISION.
       INPUT-OUTPUT SECTION.
       FILE-CONTROL.
*&gt; ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 7.1.4

Sin escribir código: un programa abre un archivo con `OPEN INPUT` y el primer `READ` da estado `"10"`. ¿El archivo existía o no? ¿Y si el estado del propio `OPEN` hubiera sido `"35"`? Explica la diferencia.

## Ejercicio 7.1.5

Cambia `ESCRIBIR-TAREAS` para que abra con `EXTEND` en vez de `OUTPUT`. Ejecútalo dos veces seguidas sobre el mismo archivo y explica la diferencia de contenido final frente al original.
