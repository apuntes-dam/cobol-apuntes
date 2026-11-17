# U3.0 · Cadenas

<div class="ej-gate" data-unit="u03" data-nombre="U3 · Estructuras de datos"></div>

Recorrido y búsqueda en cadenas de texto.

## Ejercicio 3.0.1

Escribe un bucle `while` que empiece en el **último** carácter de una cadena y avance hacia atrás hasta el primero, mostrando cada letra en una línea distinta.

## Ejercicio 3.0.2

En Python, si `fruta` es una cadena, ¿qué significa `fruta[:]`? Explícalo y escribe cómo obtendrías el mismo resultado (una copia de toda la cadena) en tu lenguaje.

## Ejercicio 3.0.3

Escribe una función `cuenta(texto, letra)` que reciba una cadena y una letra y devuelva cuántas veces aparece la letra (por ejemplo, las `a` de `banana`):

```text
cuenta("consuelo", "o")  ->  2
```

Partida: recorre la palabra y suma 1 cada vez que la letra coincide.

<details class="sol" data-key="u345/u3-0/3.0.3">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>       IDENTIFICATION DIVISION.
       PROGRAM-ID. CUENTALETRA.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-TEXTO PIC X(20) VALUE "consuelo".
*&gt; ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 3.0.4

Las cadenas tienen un método de búsqueda (`find`, `indexOf`...) parecido a contar. Lee su documentación y escribe el código que use ese método **repetidamente** para contar cuántas veces aparece una letra en `"banana"`.
