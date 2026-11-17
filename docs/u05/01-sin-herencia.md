# 5.1 Sin herencia ni polimorfismo

Dicho claro, para no generar confusión: **COBOL no tiene herencia ni polimorfismo en tiempo de ejecución**. No hay una clase base, no hay sobrescribir un método, no hay "una misma llamada que se comporta distinto según el objeto". `CLASS-ID` (la extensión orientada a objetos de algunos COBOL) ni siquiera compila con el compilador libre que se usa en esta web (GnuCOBOL).

Lo que sí tiene COBOL, y que resuelve un problema parecido (datos o comportamiento que varían de forma según el caso) con herramientas propias, es **`REDEFINES`** (reinterpretar los mismos bytes con otra estructura) combinado con **`EVALUATE`** sobre un campo que indica de qué caso se trata. No es polimorfismo: es la forma procedural de decir "según el tipo, haz una cosa u otra".

## `REDEFINES`: otra vista de los mismos bytes

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. FECHA.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-FECHA-TEXTO PIC X(8) VALUE "20261117".
       01 WS-FECHA-PARTES REDEFINES WS-FECHA-TEXTO.
           05 WS-ANIO PIC X(4).
           05 WS-MES PIC XX.
           05 WS-DIA PIC XX.
       PROCEDURE DIVISION.
           DISPLAY WS-DIA "/" WS-MES "/" WS-ANIO.
           MOVE "20261201" TO WS-FECHA-TEXTO.
           DISPLAY WS-DIA "/" WS-MES "/" WS-ANIO.
           STOP RUN.
```

**Salida:**

```text
17/11/2026
01/12/2026
```

`WS-FECHA-PARTES` no es una copia de `WS-FECHA-TEXTO`: es **el mismo almacenamiento**, descrito de otra forma. Al cambiar `WS-FECHA-TEXTO`, lo que se ve a través de `WS-ANIO`/`WS-MES`/`WS-DIA` cambia también, porque son los mismos bytes. `REDEFINES` no convierte nada (no transforma binario en texto, por ejemplo): solo cambia cómo se interpreta lo que ya hay.

## Un campo "discriminador" + `EVALUATE`: la alternativa a una llamada polimórfica

Un uso real de `REDEFINES` es describir un registro que puede ser de **varios tipos**, guiado por un campo que dice cuál es el tipo real (el "discriminador"). En vez de que cada tipo "sepa mostrarse a sí mismo" (como haría `toString()` sobrescrito en una clase), un único `EVALUATE` decide qué hacer según ese campo:

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. CLIENTE.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-TIPO PIC X VALUE "P".
       01 WS-DATOS PIC X(20) VALUE "Garcia    Ana       ".
       01 WS-PERSONA REDEFINES WS-DATOS.
           05 WS-APELLIDO PIC X(10).
           05 WS-NOMBRE PIC X(10).
       01 WS-EMPRESA REDEFINES WS-DATOS.
           05 WS-CIF PIC X(9).
           05 WS-RAZON PIC X(11).
       PROCEDURE DIVISION.
           EVALUATE WS-TIPO
               WHEN "P"
                   DISPLAY "Persona: " FUNCTION TRIM(WS-NOMBRE)
                       " " FUNCTION TRIM(WS-APELLIDO)
               WHEN "E"
                   DISPLAY "Empresa: " FUNCTION TRIM(WS-RAZON)
                       " (" FUNCTION TRIM(WS-CIF) ")"
               WHEN OTHER
                   DISPLAY "Tipo desconocido"
           END-EVALUATE.
           STOP RUN.
```

**Salida:**

```text
Persona: Ana Garcia
```

`WS-DATOS` guarda 20 bytes sin más; `WS-PERSONA` y `WS-EMPRESA` son dos formas distintas de leer esos mismos 20 bytes, con campos de tamaño distinto (`WS-APELLIDO`/`WS-NOMBRE` ocupan 10+10; `WS-CIF`/`WS-RAZON` ocupan 9+11). `WS-TIPO` es el discriminador: dice cuál de las dos vistas tiene sentido en cada momento. **Es responsabilidad tuya** comprobar `WS-TIPO` antes de usar una vista: nada impide leer `WS-RAZON` sobre datos que en realidad son de una persona, y el resultado sería basura con sentido aparente (texto, pero el texto equivocado).

!!! warning "No es polimorfismo de verdad"
    En Java, C# o Python, una lista de objetos de distintas subclases puede recorrerse con un único bucle, llamando al mismo método en cada uno, y cada objeto responde a su manera. Aquí no: si tuvieras una tabla de registros con distintos `WS-TIPO`, tendrías que recorrerla a mano y poner el `EVALUATE` (o algo parecido) en cada vuelta. COBOL no "sabe" qué hacer con cada fila por sí solo; el programador lo decide explícitamente, caso a caso.

## ¿Entonces qué significa "diseñar bien" aquí?

Sin clases que reutilizar por herencia, diseñar un programa COBOL grande con variación de comportamiento se parece más a: decidir bien los campos discriminadores, mantener `EVALUATE`/`WHEN` exhaustivos (con un `WHEN OTHER` que avise si aparece un tipo no previsto, en vez de callarse), y documentar qué vista de `REDEFINES` es válida con qué valor del discriminador. La unidad 6 sigue con cómo se organiza el resto de un programa COBOL grande (copybooks, subprogramas) sin clases.
