# P2.3 · Captura de excepciones

<div class="ej-gate" data-unit="u02" data-nombre="U2 · Estructuras de control"></div>

Salvo que se diga lo contrario, el objetivo es **que ninguna excepción llegue al programa principal y lo aborte**. Controla cualquier dato incorrecto que pueda introducir el usuario.

## Ejercicio 2.3.1

Pide la edad y muestra todos los años cumplidos (de 1 hasta su edad). Controla que lo escrito sea un número.

<details class="sol" data-key="p2-3/2.3.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>       IDENTIFICATION DIVISION.
       PROGRAM-ID. ANIOSCUMPLIDOS.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-TXT PIC X(10).
*&gt; ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 2.3.2

Pide un entero positivo y muestra los impares de 1 hasta ese número, separados por comas. Controla las entradas incorrectas.

## Ejercicio 2.3.3

Pide un entero positivo y muestra la cuenta atrás hasta cero. Repite la petición hasta que el número sea correcto.

<details class="sol" data-key="p2-3/2.3.3">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>       IDENTIFICATION DIVISION.
       PROGRAM-ID. CUENTAATRAS.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-TXT PIC X(10).
*&gt; ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 2.3.4

Pide un entero. Si la entrada no es correcta, muestra `La entrada no es correcta` y termina el programa marcando el fallo (en COBOL no hay una excepción que «relanzar»: simplemente no sigas y deja constancia del error).

## Ejercicio 2.3.5

Pide una contraseña y, si no coincide con la guardada, muestra el mensaje `Incorrect Password!!` y termina el programa marcando el fallo (en COBOL no se «lanza» nada: se comprueba y se actúa).
