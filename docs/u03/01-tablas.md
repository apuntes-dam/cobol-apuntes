# 3.1 Tablas

COBOL no tiene listas que crezcan o se encojan: tiene **tablas** de tamaño fijo, declaradas con `OCCURS`. Es lo más parecido que hay a un array.

## Declarar y rellenar una tabla

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. NOTAS.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-NOTAS.
           05 WS-NOTA PIC 9V9 OCCURS 5 TIMES.
       01 WS-I PIC 9.
       01 WS-SUMA PIC 9(2)V9.
       01 WS-MEDIA PIC 9V99.
       PROCEDURE DIVISION.
           MOVE 5.5 TO WS-NOTA(1).
           MOVE 7.0 TO WS-NOTA(2).
           MOVE 3.5 TO WS-NOTA(3).
           MOVE 8.0 TO WS-NOTA(4).
           MOVE 6.0 TO WS-NOTA(5).
           PERFORM VARYING WS-I FROM 1 BY 1 UNTIL WS-I > 5
               ADD WS-NOTA(WS-I) TO WS-SUMA
           END-PERFORM.
           COMPUTE WS-MEDIA ROUNDED = WS-SUMA / 5.
           DISPLAY "Media: " WS-MEDIA.
           STOP RUN.
```

**Salida:**

```text
Media: 6.00
```

`05 WS-NOTA PIC 9V9 OCCURS 5 TIMES` crea 5 copias seguidas de `WS-NOTA`, numeradas de **1** a 5 (no desde 0); `WS-NOTA(WS-I)` accede a la copia número `WS-I`. El tamaño es fijo: no existe un `OCCURS` que "crezca" en tiempo de ejecución (para eso haría falta `OCCURS ... DEPENDING ON`, una variante que no se usa en esta unidad).

## Buscar un valor: `SEARCH`

Recorrer la tabla a mano con `PERFORM VARYING` funciona siempre, pero para **buscar** un valor concreto hay una instrucción dedicada, `SEARCH`, que necesita que la tabla declare un **índice** con `INDEXED BY`:

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. BUSCAR.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-ALUMNOS.
           05 WS-ALUMNO PIC X(10) OCCURS 3 TIMES INDEXED BY WS-IDX.
       01 WS-BUSCA PIC X(10) VALUE "Luis".
       01 WS-POSICION PIC 9.
       PROCEDURE DIVISION.
           MOVE "Ana" TO WS-ALUMNO(1).
           MOVE "Luis" TO WS-ALUMNO(2).
           MOVE "Eva" TO WS-ALUMNO(3).
           SET WS-IDX TO 1.
           SEARCH WS-ALUMNO
               AT END
                   DISPLAY "No encontrado"
               WHEN WS-ALUMNO(WS-IDX) = WS-BUSCA
                   MOVE WS-IDX TO WS-POSICION
                   DISPLAY "Encontrado en la posicion " WS-POSICION
           END-SEARCH.
           STOP RUN.
```

**Salida:**

```text
Encontrado en la posicion 2
```

`SET WS-IDX TO 1` coloca el índice al principio antes de buscar (obligatorio: `SEARCH` empieza donde esté el índice, no siempre desde el 1); `AT END` es lo que se ejecuta si se llega al final sin encontrar nada.

!!! warning "No muestres un índice directamente con `DISPLAY`"
    `WS-IDX` no es una variable `PIC` normal: es un tipo interno (`USAGE INDEX`) pensado solo para moverse por la tabla. Si lo muestras con `DISPLAY WS-IDX` sale algo como `+000000002`, no `2`. Por eso el ejemplo lo copia antes a `WS-POSICION` (un `PIC 9` normal) para mostrarlo.

`SEARCH ALL` existe también, y es mucho más rápida (búsqueda binaria en vez de ir una por una), pero exige que la tabla esté ordenada y declarada con `ASCENDING KEY`; no hace falta para las tablas pequeñas de esta unidad.
