# 1.1 Un programa

Un **programa** es una secuencia de instrucciones que resuelve un problema, y el **algoritmo** son los pasos, en orden, que van de los datos de entrada al resultado.

!!! info "La teoría general está aparte"
    Qué es un algoritmo, el ciclo de desarrollo y el **pseudocódigo** son iguales en todos los lenguajes, así que están en una sola página: [Fundamentos: algoritmos y pseudocódigo](https://apuntes-dam.github.io/apuntes-lenguajes/fundamentos/). Aquí solo ves cómo se traduce a COBOL.

## Del pseudocódigo a COBOL

El algoritmo del área de un rectángulo, en pseudocódigo:

```text
ALGORITMO areaRectangulo
  LEER base
  LEER altura
  area <- base * altura
  ESCRIBIR "Área:", area
FIN
```

y su traducción a COBOL:

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. AREA.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-BASE-TXT PIC X(10).
       01 WS-ALTURA-TXT PIC X(10).
       01 WS-BASE PIC 9(3)V99.
       01 WS-ALTURA PIC 9(3)V99.
       01 WS-AREA PIC 9(5)V99.
       PROCEDURE DIVISION.
           DISPLAY "Base: " WITH NO ADVANCING.
           ACCEPT WS-BASE-TXT.
           DISPLAY "Altura: " WITH NO ADVANCING.
           ACCEPT WS-ALTURA-TXT.
           COMPUTE WS-BASE = FUNCTION NUMVAL(WS-BASE-TXT).
           COMPUTE WS-ALTURA = FUNCTION NUMVAL(WS-ALTURA-TXT).
           COMPUTE WS-AREA = WS-BASE * WS-ALTURA.
           DISPLAY "Area: " WS-AREA.
           STOP RUN.
```

| Pseudocódigo | COBOL |
|---|---|
| `LEER x` | `ACCEPT` (ver la nota de abajo sobre los decimales) |
| `x <- expresión` | `COMPUTE x = expresión` o `MOVE valor TO x` |
| `ESCRIBIR x` | `DISPLAY x` |
| `SI ... SINO ... FIN SI` | `IF` / `ELSE` / `END-IF` (ver [1.2](02-lenguaje.md)) |
| `MIENTRAS` | `PERFORM UNTIL` (ver [1.6](06-bucles.md)) |

!!! warning "`ACCEPT` con decimales: el truco de `NUMVAL`"
    Si el campo numérico tiene decimales (`PIC 9(3)V99`), `ACCEPT` directamente sobre él es poco fiable: según lo que escribas, el compilador puede interpretarlo como un número **entero** y recortarlo de forma inesperada. La forma segura es leer el texto tal cual en un campo `PIC X` y convertirlo con la función `FUNCTION NUMVAL(...)`, como arriba. Para números sin decimales (edades, cantidades) `ACCEPT` directo funciona bien.

## Las cuatro divisiones

Un programa COBOL se organiza en **divisiones**, siempre en este orden (las dos primeras son obligatorias en el nombre, no siempre en el contenido):

| División | Para qué |
|---|---|
| `IDENTIFICATION DIVISION` | El nombre del programa (`PROGRAM-ID`). Obligatoria. |
| `ENVIRONMENT DIVISION` | El entorno: ficheros externos, el sistema operativo. Se omite si no hace falta. |
| `DATA DIVISION` | Las variables (`WORKING-STORAGE SECTION`). Se omite si el programa no usa ninguna. |
| `PROCEDURE DIVISION` | El código: las instrucciones que se ejecutan. |

El programa más pequeño posible no necesita variables:

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. HOLA.
       PROCEDURE DIVISION.
           DISPLAY "Hola, COBOL".
           STOP RUN.
```

`STOP RUN` termina el programa; sin ella, seguiría leyendo instrucciones que no existen.

## Las columnas: el formato fijo

COBOL nació para tarjetas perforadas, y todavía se nota: en el **formato fijo** (el clásico, y el que se usa en esta web) cada línea tiene zonas con un significado concreto:

| Columnas | Zona | Para qué |
|---|---|---|
| 1-6 | Número de secuencia | Histórico (numerar las tarjetas); hoy se deja en blanco |
| 7 | Indicador | `*` para un comentario de toda la línea; en blanco, código normal |
| 8-11 | Área A | Encabezados de división/sección, nombres de párrafo, los niveles `01` |
| 12-72 | Área B | El resto del código |
| 73-80 | (ignoradas) | El compilador no las lee |

En la práctica: deja **7 espacios** antes de cualquier instrucción y 6 antes de un comentario con `*>`:

```cobol
      *> Esto es un comentario de toda la línea (columna 7)
       IDENTIFICATION DIVISION.
```

GnuCOBOL (el compilador que se usa aquí) también admite un **formato libre** sin columnas (con la opción `cobc -free`), pero el formato fijo es la seña de identidad histórica del lenguaje y el que verás en el código heredado real, así que es el que se usa en los ejemplos de esta web (más en [1.5 Entorno](05-entorno.md)).

!!! warning "Errores típicos en COBOL"
    Escribir código en la columna 7 (el compilador lo toma por un indicador de comentario o lo rechaza), olvidar el punto `.` al final de una frase, y una línea que se pasa de la columna 72 (el texto después se ignora, y si corta un literal a mitad, el compilador se queja de "continuation character expected"). Los errores generales de diseño están en [Fundamentos](https://apuntes-dam.github.io/apuntes-lenguajes/fundamentos/).
