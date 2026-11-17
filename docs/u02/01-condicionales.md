# 2.1 Condicionales

## `IF` / `ELSE`

Ya se vio una condición simple en [1.2](../u01/02-lenguaje.md). Con `ELSE` se añade la alternativa, y se puede encadenar con más condiciones:

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. MAYORIA.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-EDAD PIC 9(3) VALUE 20.
       PROCEDURE DIVISION.
           IF WS-EDAD >= 18
               DISPLAY "Mayor de edad"
           ELSE
               DISPLAY "Menor de edad"
           END-IF.
           STOP RUN.
```

**Salida:**

```text
Mayor de edad
```

`END-IF` cierra el `IF` de forma explícita; en el formato fijo clásico más antiguo se usaba un punto `.` al final de todo el bloque en su lugar, pero `END-IF` es más claro y evita errores cuando hay varios `IF` anidados.

## Selección múltiple: `EVALUATE`

`EVALUATE` es el `switch` de COBOL. La forma más simple compara un valor contra varios casos:

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. DIA.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-DIA PIC 9 VALUE 3.
       PROCEDURE DIVISION.
           EVALUATE WS-DIA
               WHEN 1
                   DISPLAY "Lunes"
               WHEN 2
                   DISPLAY "Martes"
               WHEN 3
                   DISPLAY "Miercoles"
               WHEN OTHER
                   DISPLAY "Otro dia"
           END-EVALUATE.
           STOP RUN.
```

**Salida:**

```text
Miercoles
```

## `EVALUATE TRUE`: varias condiciones independientes

Cuando lo que varía no es un único valor sino varias condiciones distintas (como una cadena de `if`/`else if`), se evalúa `TRUE` y cada `WHEN` lleva su propia condición completa:

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. TRAMO.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-EDAD PIC 9(3) VALUE 10.
       PROCEDURE DIVISION.
           EVALUATE TRUE
               WHEN WS-EDAD < 4
                   DISPLAY "Entrada gratis"
               WHEN WS-EDAD <= 18
                   DISPLAY "Entrada infantil"
               WHEN OTHER
                   DISPLAY "Entrada de adulto"
           END-EVALUATE.
           STOP RUN.
```

**Salida:**

```text
Entrada infantil
```

Los `WHEN` se comprueban en orden y se ejecuta el **primero** que se cumple (como una cadena de `else if`), nunca varios a la vez.

!!! tip "Comparar varios valores a la vez"
    `EVALUATE WS-A WS-B` compara simultáneamente dos valores contra pares de `WHEN valor-a valor-b`; no hace falta en esta unidad, pero existe para tablas de decisión con varias entradas.

## Operadores lógicos

Ya vistos en [1.2](../u01/02-lenguaje.md) (`AND`, `OR`, `NOT`); en condiciones largas se pueden agrupar entre paréntesis igual que en cualquier otro lenguaje:

```cobol
       IF (WS-EDAD >= 18 AND WS-EDAD < 65) OR WS-ES-PENSIONISTA = "S"
           DISPLAY "Puede jubilarse o ya trabaja"
       END-IF.
```

!!! note "Bucles"
    Las distintas formas de repetir código (`PERFORM`) se explican en el apartado [1.6](../u01/06-bucles.md) de la unidad 1.
