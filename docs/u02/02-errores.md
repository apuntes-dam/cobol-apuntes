# 2.3 Controlar errores

COBOL **no tiene excepciones** como `try`/`catch`/`throw`: no hay un mecanismo para "lanzar" un error y que otra parte del programa lo "atrape". En su lugar, un programa COBOL controla los errores **comprobando antes** de que ocurran, con condiciones normales.

## Validar un dato antes de usarlo: `IS NUMERIC`

El error más frecuente con `ACCEPT` es que el usuario escriba algo que no es un número. Se comprueba con la condición de clase `IS NUMERIC` (o `IS NOT NUMERIC`) sobre un campo `PIC X`:

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. VALIDAR.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-TXT PIC X(10).
       01 WS-EDAD PIC 9(3).
       PROCEDURE DIVISION.
           DISPLAY "Edad: " WITH NO ADVANCING.
           ACCEPT WS-TXT.
           IF FUNCTION TRIM(WS-TXT) IS NOT NUMERIC
               DISPLAY "Error: escribe solo digitos"
           ELSE
               COMPUTE WS-EDAD = FUNCTION NUMVAL(WS-TXT)
               DISPLAY "Edad registrada: " WS-EDAD
           END-IF.
           STOP RUN.
```

**Salida:**

```text
Edad: Error: escribe solo digitos
```

!!! note "`RETURN-CODE`: el código de salida del programa"
    Cuando un programa termina, puede avisar a quien lo ejecutó (el sistema operativo, o un script) de si algo fue mal, con `MOVE n TO RETURN-CODE` antes de `STOP RUN` (0 = todo bien, por convención). Es lo más parecido que tiene COBOL a "lanzar" un error hacia fuera: no interrumpe el programa por sí solo, pero cualquiera que lo ejecute (por ejemplo, `echo $?` en la terminal, justo después) puede comprobarlo. Esta web no lo usa en los ejemplos para poder mostrar su salida con normalidad, pero en un programa real de validación es habitual terminar así tras un error grave.

## Desbordamientos aritméticos: `ON SIZE ERROR`

Un campo `PIC 9(2)V99` solo tiene sitio hasta `99.99`. Si una cuenta se pasa de ahí, COMPUTE lo detecta con `ON SIZE ERROR` (y con `NOT ON SIZE ERROR`, si además quieres hacer algo cuando **no** hay error):

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. DESBORDAR.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-A PIC 9(2) VALUE 90.
       01 WS-B PIC 9(2) VALUE 90.
       01 WS-R PIC 9(2)V99.
       PROCEDURE DIVISION.
           COMPUTE WS-R ROUNDED = WS-A * WS-B
               ON SIZE ERROR
                   DISPLAY "Error: el resultado no cabe en el campo"
               NOT ON SIZE ERROR
                   DISPLAY "Resultado: " WS-R
           END-COMPUTE.
           STOP RUN.
```

**Salida:**

```text
Error: el resultado no cabe en el campo
```

`90 * 90 = 8100`, que necesita 4 dígitos enteros y `WS-R` solo tiene 2: sin `ON SIZE ERROR`, COBOL dejaría el campo con un valor truncado (silencioso y erróneo) en vez de avisar.

## Ficheros: `FILE STATUS`

Cuando se trabaja con archivos (unidad 7), el equivalente a "capturar un error" es comprobar un código de dos caracteres después de cada operación (`OPEN`, `READ`, `WRITE`...), guardado en una variable que tú declaras con la cláusula `FILE STATUS IS`. Por ejemplo, `"00"` significa éxito y `"35"`, que el archivo no existe. Se verá con ejemplos reales en la unidad 7.

!!! info "Resumen: tres formas de controlar errores en COBOL"
    * **Validar antes**: condiciones de clase (`IS NUMERIC`) sobre el dato, antes de usarlo.
    * **Desbordamiento**: `ON SIZE ERROR` en operaciones aritméticas.
    * **Códigos de estado**: un campo de dos caracteres que se consulta después de la operación (ficheros, y algunas sentencias más avanzadas).

    Ninguna "interrumpe" el flujo como un `throw`: el programa sigue por la siguiente instrucción salvo que tú decidas lo contrario (por ejemplo, con un `IF` que salte el resto del bloque).
