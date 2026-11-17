# 3.0 Cadenas

Ya se usó `FUNCTION TRIM` en la unidad 1. Aquí se ven las instrucciones propias de COBOL para unir, partir y contar dentro de un texto.

## Unir textos: `STRING`

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. UNIR.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-NOMBRE PIC X(10) VALUE "Ana".
       01 WS-APELLIDO PIC X(10) VALUE "Ruiz".
       01 WS-COMPLETO PIC X(25).
       PROCEDURE DIVISION.
           STRING FUNCTION TRIM(WS-NOMBRE) DELIMITED BY SIZE
                  " " DELIMITED BY SIZE
                  FUNCTION TRIM(WS-APELLIDO) DELIMITED BY SIZE
                  INTO WS-COMPLETO.
           DISPLAY "Completo: [" FUNCTION TRIM(WS-COMPLETO) "]".
           STOP RUN.
```

**Salida:**

```text
Completo: [Ana Ruiz]
```

Cada trozo que se une necesita su propio `DELIMITED BY SIZE` (coge el trozo entero, tal cual); con `DELIMITED BY "x"` cogería solo hasta el primer carácter `x` que encontrara.

## Partir un texto: `UNSTRING`

Es lo contrario de `STRING`: reparte un texto en varias variables según un separador.

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. PARTIR.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-CSV PIC X(20) VALUE "Ana,23,Madrid".
       01 WS-NOMBRE PIC X(10).
       01 WS-EDAD PIC X(10).
       01 WS-CIUDAD PIC X(10).
       PROCEDURE DIVISION.
           UNSTRING WS-CSV DELIMITED BY ","
               INTO WS-NOMBRE WS-EDAD WS-CIUDAD.
           DISPLAY "Nombre: " FUNCTION TRIM(WS-NOMBRE).
           DISPLAY "Edad: " FUNCTION TRIM(WS-EDAD).
           DISPLAY "Ciudad: " FUNCTION TRIM(WS-CIUDAD).
           STOP RUN.
```

**Salida:**

```text
Nombre: Ana
Edad: 23
Ciudad: Madrid
```

## Contar apariciones: `INSPECT ... TALLYING`

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. CONTAR.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-CSV PIC X(20) VALUE "Ana,23,Madrid".
       01 WS-COMAS PIC 9(2) VALUE 0.
       PROCEDURE DIVISION.
           INSPECT WS-CSV TALLYING WS-COMAS FOR ALL ",".
           DISPLAY "Comas: " WS-COMAS.
           STOP RUN.
```

**Salida:**

```text
Comas: 02
```

`INSPECT` también sirve para **reemplazar** caracteres (`INSPECT campo REPLACING ALL "a" BY "@"`), aunque no hace falta en esta unidad.

## Acceder a una parte del texto: modificación por referencia

Sin llamar a ninguna función, se puede leer (o escribir) un trozo de un campo con `campo(posicion:longitud)`:

```cobol
       01 WS-TXT PIC X(10) VALUE "Programar".
       PROCEDURE DIVISION.
           DISPLAY WS-TXT(1:4).       *> "Prog"
           DISPLAY WS-TXT(5:4).       *> "rama"
           MOVE "T" TO WS-TXT(1:1).   *> cambia solo la primera letra
           DISPLAY FUNCTION TRIM(WS-TXT).  *> "Trogramar"
```

Esto es lo que usan por dentro muchas comprobaciones carácter a carácter, como la del palíndromo de los ejercicios.

!!! warning "Cuidado con el relleno de espacios"
    Un campo `PIC X(n)` que guarda un texto más corto se rellena con espacios hasta `n`. Una instrucción como `FUNCTION REVERSE(campo)` invierte **todo el campo tal cual está**, espacios de relleno incluidos, así que el resultado no coincide con invertir solo la palabra: por eso los ejercicios que comparan un texto con su inversa (como el palíndromo) calculan antes su longitud real con `FUNCTION LENGTH(FUNCTION TRIM(campo))` y comparan carácter a carácter dentro de esa longitud, en vez de usar `FUNCTION REVERSE` directamente sobre el campo completo.
