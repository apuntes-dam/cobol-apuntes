# 10.1 Buscar en una tabla: `SEARCH` y `SEARCH ALL`

La unidad 3 ya enseñó las tablas (`OCCURS`) y a recorrerlas con `PERFORM VARYING`. Para **buscar** un elemento concreto, COBOL tiene dos instrucciones dedicadas, más rápidas de leer (y a veces de ejecutar) que un bucle con `IF` escrito a mano.

## `SEARCH`: búsqueda secuencial

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. BUSCAR-PRODUCTO-SEC.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01  TABLA-PROD.
           05 PROD OCCURS 5 TIMES
               INDEXED BY IDX.
               10 P-COD  PIC 9(4).
               10 P-NOM  PIC X(10).
       PROCEDURE DIVISION.
           MOVE 10 TO P-COD(1). MOVE "Teclado" TO P-NOM(1).
           MOVE 20 TO P-COD(2). MOVE "Raton" TO P-NOM(2).
           MOVE 30 TO P-COD(3). MOVE "Monitor" TO P-NOM(3).
           MOVE 40 TO P-COD(4). MOVE "Cable" TO P-NOM(4).
           MOVE 50 TO P-COD(5). MOVE "Altavoz" TO P-NOM(5).

           SET IDX TO 1.
           SEARCH PROD
               AT END
                   DISPLAY "no encontrado"
               WHEN P-NOM(IDX) = "Cable"
                   DISPLAY "encontrado idx=" IDX " cod=" P-COD(IDX)
           END-SEARCH.
           STOP RUN.
```

**Salida:**

```text
encontrado idx=+000000004 cod=0040
```

`INDEXED BY IDX` declara un **índice** (no una variable `PIC 9` normal: la propia tabla lo gestiona) que se usa para recorrerla. `SET IDX TO 1` lo coloca al principio; `SEARCH` avanza él solo, comparando cada elemento con la condición del `WHEN`, hasta encontrarla o llegar al final (`AT END`). Es, en esencia, el mismo bucle secuencial de la unidad 3, pero sin escribir el `PERFORM VARYING` a mano.

!!! info "El índice no se muestra como un número normal"
    `DISPLAY IDX` da `+000000004`, no `4`: un índice no es un `PIC 9`, es un tipo binario con signo que COBOL gestiona internamente. Para mostrarlo "limpio" o guardarlo, se pasa primero a un campo normal con `SET campo-normal FROM IDX` (o al revés, `SET IDX FROM campo-normal`).

## `SEARCH ALL`: búsqueda binaria (la tabla tiene que estar ordenada)

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. BUSCAR-PRODUCTO-BIN.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01  TABLA-PROD.
           05 PROD OCCURS 5 TIMES
               ASCENDING KEY IS P-COD
               INDEXED BY IDX.
               10 P-COD  PIC 9(4).
               10 P-NOM  PIC X(10).
       PROCEDURE DIVISION.
           MOVE 10 TO P-COD(1). MOVE "Teclado" TO P-NOM(1).
           MOVE 20 TO P-COD(2). MOVE "Raton" TO P-NOM(2).
           MOVE 30 TO P-COD(3). MOVE "Monitor" TO P-NOM(3).
           MOVE 40 TO P-COD(4). MOVE "Cable" TO P-NOM(4).
           MOVE 50 TO P-COD(5). MOVE "Altavoz" TO P-NOM(5).

           SET IDX TO 1.
           SEARCH ALL PROD
               AT END
                   DISPLAY "no encontrado"
               WHEN P-COD(IDX) = 30
                   DISPLAY "encontrado idx=" IDX " nom=" P-NOM(IDX)
           END-SEARCH.

           SET IDX TO 1.
           SEARCH ALL PROD
               AT END
                   DISPLAY "99 no encontrado (correcto)"
               WHEN P-COD(IDX) = 99
                   DISPLAY "encontrado idx=" IDX
           END-SEARCH.
           STOP RUN.
```

**Salida:**

```text
encontrado idx=+000000003 nom=Monitor
99 no encontrado (correcto)
```

La diferencia clave está en la declaración: `ASCENDING KEY IS P-COD` le dice al compilador por qué campo está ordenada la tabla. Con eso, `SEARCH ALL` no mira elemento a elemento: hace una **búsqueda binaria** (como adivinar un número partiendo siempre por la mitad). Es mucho más rápida en tablas grandes, pero tiene una condición que no se comprueba sola: **si la tabla no está realmente ordenada por esa clave, el resultado no es de fiar** (puede decir "no encontrado" aunque el dato esté ahí, en una posición que la búsqueda binaria no llega a mirar).

!!! warning "`SEARCH ALL` solo admite igualdad"
    La condición del `WHEN` en `SEARCH ALL` tiene que ser de igualdad (`=`) sobre la clave declarada; no vale un `>` o un `<` ahí. Para "el primero mayor que X" en una tabla, se usa `SEARCH` normal o se recorre a mano.

## Cuál usar

| | `SEARCH` | `SEARCH ALL` |
|---|---|---|
| Orden de la tabla | Cualquiera | Tiene que estar ordenada por la clave |
| Qué compara el `WHEN` | Cualquier condición | Solo igualdad, sobre la clave declarada |
| Coste | Recorre hasta encontrarlo (o todo, si no está) | Mucho más rápido en tablas grandes |

En una tabla pequeña (unas pocas decenas de elementos, cargada una vez) la diferencia no se nota. Donde importa de verdad es en tablas grandes que se consultan muchas veces: un catálogo de productos, una tabla de códigos postales.
