# 2.4 Depurar

## La herramienta real: `DISPLAY`

La forma más fiable de depurar un programa COBOL (la que se usa en esta web, porque se puede comprobar de verdad) es la más antigua de todas: añadir `DISPLAY` en los puntos clave para ver qué valor tiene una variable en ese momento.

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. TRAZA.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-N PIC 9(3) VALUE 5.
       01 WS-FACTORIAL PIC 9(8) VALUE 1.
       01 WS-I PIC 9(3).
       PROCEDURE DIVISION.
           PERFORM VARYING WS-I FROM 1 BY 1 UNTIL WS-I > WS-N
               COMPUTE WS-FACTORIAL = WS-FACTORIAL * WS-I
               DISPLAY "DEBUG: i=" WS-I " factorial=" WS-FACTORIAL
           END-PERFORM.
           DISPLAY "Factorial de " WS-N " es " WS-FACTORIAL.
           STOP RUN.
```

**Salida:**

```text
DEBUG: i=001 factorial=00000001
DEBUG: i=002 factorial=00000002
DEBUG: i=003 factorial=00000006
DEBUG: i=004 factorial=00000024
DEBUG: i=005 factorial=00000120
Factorial de 005 es 00000120
```

Un `DISPLAY` de depuración así se quita (o se comenta con `*>`) antes de dar el programa por terminado; mientras se depura, conviene que el texto deje claro que es una traza (por ejemplo con el prefijo `DEBUG:`) para no confundirlo con la salida real del programa.

## Comprobaciones del compilador: `-Wall` y `-debug`

`cobc -Wall` (ver [1.5](../u01/05-entorno.md)) avisa de cosas sospechosas **al compilar**, sin ejecutar nada: variables que no se usan, comparaciones que nunca pueden ser verdad, etc. La opción `cobc -debug` añade comprobaciones **en tiempo de ejecución** (por ejemplo, acceder a una posición de una tabla que no existe) y hace que los errores de ese tipo se detecten con un mensaje claro, en vez de corromper otra variable en silencio.

!!! info "Un depurador paso a paso de verdad existe, pero no se usa en esta web"
    GnuCOBOL genera código C por dentro, así que un depurador como `gdb` puede llegar a seguir la ejecución de un programa COBOL compilado con `-g`. Montarlo bien (y que muestre líneas de COBOL en vez de las del C generado) depende de la versión y del entorno, así que no es algo fiable para enseñar aquí con ejemplos ejecutados de verdad; si programas COBOL en serio, un IDE como *Visual Studio Code* con una extensión para COBOL es el camino más práctico para depurar paso a paso con breakpoints.
