# 11.2 Formatear texto y números

## Funciones de texto que faltaban

La unidad 3 ya vio `STRING`, `UNSTRING`, `INSPECT` y `FUNCTION TRIM`. Quedan unas pocas funciones de texto cortas, pero de uso constante:

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. FUNCIONES-TEXTO.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01  WS-NOM PIC X(15) VALUE "Ana Garcia".
       PROCEDURE DIVISION.
           DISPLAY "mayus: " FUNCTION UPPER-CASE(WS-NOM).
           DISPLAY "minus: " FUNCTION LOWER-CASE(WS-NOM).
           DISPLAY "reves: "
               FUNCTION REVERSE(FUNCTION TRIM(WS-NOM)).
           DISPLAY "longitud: "
               FUNCTION LENGTH(FUNCTION TRIM(WS-NOM)).
           STOP RUN.
```

**Salida:**

```text
mayus: ANA GARCIA
minus: ana garcia
reves: aicraG anA
longitud: 10
```

`FUNCTION UPPER-CASE`/`LOWER-CASE` cambian mayúsculas y minúsculas (útil para comparar dos textos sin que importe cómo los escribió quien los tecleó). `FUNCTION REVERSE` invierte el orden de los caracteres. `FUNCTION LENGTH` da el número de caracteres; nótese que se aplica sobre `FUNCTION TRIM(WS-NOM)`, no sobre `WS-NOM` directamente: sin el `TRIM`, `LENGTH` daría 15 (el tamaño declarado del campo, `PIC X(15)`, relleno de espacios), no los 10 caracteres reales de "Ana Garcia". Las funciones se pueden **anidar** así, unas dentro de otras.

## `PICTURE` de edición: la otra forma de "formatear"

Hasta ahora, formatear un número o una fecha para mostrarlos (separador de miles, punto decimal, barras entre año/mes/día) se ha hecho recortando con subcadenas o concatenando con `STRING`. Hay una forma más directa, propia de COBOL: una **`PICTURE` de edición**, declarada en un campo aparte, donde un simple `MOVE` hace el formateo.

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. FORMATEAR-NUMEROS.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01  WS-IMPORTE   PIC 9(6)V99 VALUE 1234.5.
       01  WS-EDITADO   PIC Z,ZZZ,ZZ9.99.
       01  WS-FECHA     PIC 9(8) VALUE 20261225.
       01  WS-FECHA-ED  PIC 9999/99/99.
       PROCEDURE DIVISION.
           MOVE WS-IMPORTE TO WS-EDITADO.
           DISPLAY "importe editado: " WS-EDITADO.
           MOVE WS-FECHA TO WS-FECHA-ED.
           DISPLAY "fecha editada: " WS-FECHA-ED.
           STOP RUN.
```

**Salida:**

```text
importe editado:     1,234.50
fecha editada: 2026/12/25
```

`PIC Z,ZZZ,ZZ9.99`: cada `Z` es un dígito que, si está a la izquierda y vale cero, se muestra como espacio en blanco (zero-suppression) en vez de un cero inútil; la coma y el punto se insertan solos, en las posiciones exactas donde están escritos en la `PICTURE`. `PIC 9999/99/99` hace lo mismo con una fecha `AAAAMMDD`: inserta las barras donde toca, sin tocar los dígitos. El `MOVE` de un campo numérico normal a uno con `PICTURE` de edición hace la conversión automáticamente; no se puede operar (sumar, restar) directamente sobre un campo editado, solo mostrarlo o imprimirlo — por eso siempre hay dos campos: uno "de trabajo" (`WS-IMPORTE`, `WS-FECHA`) con el que se calcula, y uno "de edición" (`WS-EDITADO`, `WS-FECHA-ED`) solo para enseñarlo.
