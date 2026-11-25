# 10.3 · Ejercicios de búsquedas y ordenación

<div class="ej-gate" data-unit="u10" data-nombre="U10 · Búsquedas y ordenación"></div>

Para el ejercicio 10.1.3 (solo explicación), entrega la respuesta razonada, no código.

## Ejercicio 10.1.1

Escribe `BUSCAR-POR-NOMBRE`: una tabla de 5 productos (código y nombre) y, con `SEARCH` (secuencial, sin `ASCENDING KEY`), busca el producto cuyo nombre sea "Cable" y muestra su código.

<details class="sol" data-key="p10/10.1.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>       IDENTIFICATION DIVISION.
       PROGRAM-ID. BUSCAR-POR-NOMBRE.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01  TABLA-PROD.
*&gt; ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 10.1.2

Escribe `BUSCAR-BINARIA`: la misma tabla que 10.1.1 pero con `ASCENDING KEY IS` el código, cargada ya ordenada, y usa `SEARCH ALL` para buscar el código 40.

<details class="sol" data-key="p10/10.1.2">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>       IDENTIFICATION DIVISION.
       PROGRAM-ID. BUSCAR-BINARIA.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01  TABLA-PROD.
*&gt; ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 10.1.3

Sin escribir código: tienes una tabla con `ASCENDING KEY IS P-COD`, pero la cargas en el orden 50, 10, 30, 20, 40 (sin ordenarla primero) y haces `SEARCH ALL` buscando el código 20. ¿Qué puede pasar? Explica por qué `SEARCH ALL` no detecta por sí sola que la tabla está desordenada.

## Ejercicio 10.2.1

Cambia la ordenación de burbuja del apartado 10.2 para que quede **ascendente** (de menor a mayor código) en vez de descendente.

<details class="sol" data-key="p10/10.2.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>       IDENTIFICATION DIVISION.
       PROGRAM-ID. ORDENAR-ASCENDENTE.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01  TABLA-PROD.
*&gt; ... (resto de la solución bloqueado)</code></pre></div>
</details>
