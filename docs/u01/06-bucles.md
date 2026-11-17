# 1.6 Bucles: `PERFORM`

En COBOL todas las formas de repetir código usan la misma instrucción, `PERFORM`, con distintas variantes.

## `PERFORM UNTIL`: cuando no sabes cuántas veces

```cobol
       01 WS-I PIC 9 VALUE 1.
       PROCEDURE DIVISION.
           PERFORM UNTIL WS-I > 5
               DISPLAY "Numero: " WS-I
               ADD 1 TO WS-I
           END-PERFORM.
           STOP RUN.
```

**Salida:**

```text
Numero: 1
Numero: 2
Numero: 3
Numero: 4
Numero: 5
```

La condición se comprueba **antes** de cada vuelta (como un `while`); si quieres que se compruebe después (como un `do-while`), añade `WITH TEST AFTER`.

## `PERFORM VARYING`: cuando sabes cuántas veces

Es la forma más parecida a un `for` clásico: controla ella sola el inicio, el avance y la condición de parada.

```cobol
       01 WS-I PIC 9.
       PROCEDURE DIVISION.
           PERFORM VARYING WS-I FROM 1 BY 1 UNTIL WS-I > 5
               DISPLAY "Numero: " WS-I
           END-PERFORM.
           STOP RUN.
```

| Parte | Qué hace |
|---|---|
| `FROM 1` | Valor inicial de `WS-I` |
| `BY 1` | Cuánto avanza cada vuelta (puede ser negativo, para contar hacia atrás) |
| `UNTIL WS-I > 5` | Se repite mientras **no** se cumpla; se comprueba antes de cada vuelta |

## `PERFORM n TIMES`: un número fijo de veces

```cobol
       PROCEDURE DIVISION.
           PERFORM 3 TIMES
               DISPLAY "Hola"
           END-PERFORM.
           STOP RUN.
```

## `PERFORM nombre-de-párrafo`: repetir un bloque con nombre

Un **párrafo** es un bloque de código con nombre, fuera del flujo normal, que solo se ejecuta cuando algo lo llama con `PERFORM`. Es la forma que tiene COBOL de organizar el código en partes, antes de que existieran las funciones tal como se conocen hoy:

```cobol
       PROCEDURE DIVISION.
           PERFORM SALUDAR 3 TIMES.
           PERFORM SALUDAR.
           STOP RUN.

       SALUDAR.
           DISPLAY "Hola".
```

**Salida:**

```text
Hola
Hola
Hola
Hola
```

Las tres primeras vienen de `PERFORM SALUDAR 3 TIMES`, y la última, de la llamada suelta de abajo. Un párrafo también se puede combinar con `UNTIL` o `VARYING` (`PERFORM SALUDAR UNTIL ...`) en vez de escribir el bloque entre `PERFORM` y `END-PERFORM`.

!!! warning "Cuidado al mezclar `PERFORM` de párrafo con el flujo normal"
    Si el código sigue **después** de un párrafo sin que nada lo llame con `PERFORM`, el programa lo ejecuta igualmente al llegar ahí abajo (los párrafos no están "aislados" como las funciones de otros lenguajes). Por eso en los ejemplos de esta unidad, `STOP RUN` va siempre antes del primer párrafo con nombre.
