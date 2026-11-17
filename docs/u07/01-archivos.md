# 7.1 Archivos de texto

## La consola, ya vista

La entrada y salida por consola (`DISPLAY`, `ACCEPT`) se vio a fondo en la unidad 1 ([1.1](../u01/01-programa.md), [1.2](../u01/02-lenguaje.md)), y cómo validar lo que escribe el usuario, en [2.3](../u02/02-errores.md). Esta unidad es sobre **ficheros**: la otra forma de que un programa COBOL hable con el exterior, y la que de verdad importa en programas de negocio (nóminas, movimientos, lotes de pedidos).

## `FILE STATUS`: ya lo adelantó la unidad 2

En [2.3](../u02/02-errores.md) ya se dijo que, al trabajar con archivos, el equivalente a "capturar un error" es comprobar un código de dos caracteres (`FILE STATUS`) después de cada operación. Aquí se ve con ejemplos reales.

## Escribir en un fichero

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. ESCRIBIR-TAREAS.
       ENVIRONMENT DIVISION.
       INPUT-OUTPUT SECTION.
       FILE-CONTROL.
           SELECT ARCHIVO-TAREAS ASSIGN TO "tareas.txt"
               ORGANIZATION IS LINE SEQUENTIAL
               FILE STATUS IS ESTADO-ARCHIVO.
       DATA DIVISION.
       FILE SECTION.
       FD ARCHIVO-TAREAS.
       01 LINEA-TAREA PIC X(80).
       WORKING-STORAGE SECTION.
       01 ESTADO-ARCHIVO PIC XX.
       PROCEDURE DIVISION.
           OPEN OUTPUT ARCHIVO-TAREAS.
           IF ESTADO-ARCHIVO NOT = "00"
               DISPLAY "Error al abrir: " ESTADO-ARCHIVO
               MOVE 1 TO RETURN-CODE
               STOP RUN
           END-IF.
           MOVE "Leer el tema de COBOL" TO LINEA-TAREA.
           WRITE LINEA-TAREA.
           IF ESTADO-ARCHIVO NOT = "00"
               DISPLAY "Error al escribir: " ESTADO-ARCHIVO
               CLOSE ARCHIVO-TAREAS
               MOVE 1 TO RETURN-CODE
               STOP RUN
           END-IF.
           CLOSE ARCHIVO-TAREAS.
           IF ESTADO-ARCHIVO NOT = "00"
               DISPLAY "Error al cerrar: " ESTADO-ARCHIVO
               MOVE 1 TO RETURN-CODE
           ELSE
               DISPLAY "Archivo guardado"
           END-IF.
           STOP RUN.
```

**Salida:**

```text
Archivo guardado
```

`ORGANIZATION IS LINE SEQUENTIAL` es la organización más sencilla: cada registro es una línea de texto, igual que un `.txt` normal. `SELECT` le da un nombre lógico (`ARCHIVO-TAREAS`) y lo asocia a una ruta; `FD` (*file description*) describe cómo es cada registro (`LINEA-TAREA`, hasta 80 caracteres); `FILE STATUS IS ESTADO-ARCHIVO` dice dónde dejar el resultado de cada operación (`OPEN`, `WRITE`, `CLOSE`...). Se comprueba **después de cada una**, no solo al final: un `CLOSE` también puede fallar.

!!! warning "`OPEN OUTPUT` reemplaza el contenido"
    Si `tareas.txt` ya existe, `OPEN OUTPUT` lo vacía antes de escribir. Para añadir registros sin perder los que ya había, se abre con `EXTEND` en vez de `OUTPUT` (cuando la organización y el entorno lo permiten).

## Leer hasta el final

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. LEER-TAREAS.
       ENVIRONMENT DIVISION.
       INPUT-OUTPUT SECTION.
       FILE-CONTROL.
           SELECT ARCHIVO-TAREAS ASSIGN TO "tareas.txt"
               ORGANIZATION IS LINE SEQUENTIAL
               FILE STATUS IS ESTADO-ARCHIVO.
       DATA DIVISION.
       FILE SECTION.
       FD ARCHIVO-TAREAS.
       01 LINEA-TAREA PIC X(80).
       WORKING-STORAGE SECTION.
       01 ESTADO-ARCHIVO PIC XX.
       01 WS-FIN PIC X VALUE "N".
           88 FIN-ARCHIVO VALUE "S".
       PROCEDURE DIVISION.
           OPEN INPUT ARCHIVO-TAREAS.
           IF ESTADO-ARCHIVO NOT = "00"
               DISPLAY "Error al abrir: " ESTADO-ARCHIVO
               MOVE 1 TO RETURN-CODE
               STOP RUN
           END-IF.
           PERFORM UNTIL FIN-ARCHIVO
               READ ARCHIVO-TAREAS
               EVALUATE ESTADO-ARCHIVO
                   WHEN "00"
                       DISPLAY FUNCTION TRIM(LINEA-TAREA)
                   WHEN "10"
                       SET FIN-ARCHIVO TO TRUE
                   WHEN OTHER
                       DISPLAY "Error al leer: " ESTADO-ARCHIVO
                       MOVE 1 TO RETURN-CODE
                       SET FIN-ARCHIVO TO TRUE
               END-EVALUATE
           END-PERFORM.
           CLOSE ARCHIVO-TAREAS.
           STOP RUN.
```

**Salida** (leyendo el `tareas.txt` que acaba de crear el programa anterior):

```text
Leer el tema de COBOL
```

`WS-FIN`/`FIN-ARCHIVO` es una **condición de nivel 88**: no es una variable nueva, es un nombre para un valor concreto de `WS-FIN` (ya se vio una de pasada en la unidad 5). El bucle `PERFORM UNTIL FIN-ARCHIVO` lee un registro, mira el estado y decide: `"00"` lo muestra y sigue, `"10"` marca el final, cualquier otra cosa es un error de verdad (y también para el bucle, para no repetirlo sin fin).

| Estado | Significa |
|---|---|
| `"00"` | Operación correcta |
| `"10"` | Fin de archivo (al leer) |
| `"35"` | El archivo no existe (al abrir para entrada) |

!!! info "Un archivo vacío no es un error"
    Si `tareas.txt` existe pero está vacío, `OPEN INPUT` tiene éxito (`"00"`) y el primer `READ` da directamente `"10"` (fin de archivo): el bucle no muestra nada y el programa termina sin más, sin mensaje de error. Si el archivo **no existe**, en cambio, el fallo ya aparece en el propio `OPEN` (`"35"`). Son dos situaciones distintas y el programa no debería confundirlas.

## Modos de apertura

| Modo | Para qué |
|---|---|
| `INPUT` | Leer |
| `OUTPUT` | Crear o reemplazar, para escribir |
| `EXTEND` | Añadir al final, sin borrar lo que ya había |
| `I-O` | Leer y modificar registros existentes |

Abrir, operar y cerrar es un ciclo: no se puede leer un archivo abierto solo para `OUTPUT`, y si el `OPEN` falla, no hay que seguir como si `LINEA-TAREA` tuviera un dato recién leído.

## Más allá de `LINE SEQUENTIAL`: archivos indexados

COBOL también tiene organizaciones más avanzadas (`RELATIVE`, `INDEXED`) para acceder a un registro por una clave (`READ`/`REWRITE`/`DELETE`/`START`), típicas de procesos de negocio reales (un fichero de clientes por código, por ejemplo). **No se enseñan aquí con un ejemplo ejecutado**: `ORGANIZATION IS INDEXED` no compila con el GnuCOBOL que se usa en esta web (necesita un soporte de indexado que este compilador no trae configurado). Si algún día trabajas con un COBOL de mainframe (IBM Enterprise COBOL con VSAM, por ejemplo), es donde más se nota: claves, duplicados y bloqueo de registros frente a otros procesos son temas con matices propios del entorno, no algo que se pueda resumir en un ejemplo suelto.

## Interfaces gráficas: no aplica

Los demás lenguajes de esta web cierran esta unidad con una interfaz gráfica (una ventana con botones y eventos). COBOL no tiene nada parecido disponible con GnuCOBOL: no hay una biblioteca de ventanas que compile y se ejecute aquí, como sí la hay para C++ (FLTK) o Rust (egui). Esta web no va a fingir que existe. Lo que de verdad hace un programa COBOL cuando necesita mostrar resultados a gran escala no es una ventana: son **informes** (listados, resúmenes, archivos de salida que otro programa o persona revisa) y **procesos por lotes** (leer un fichero de entrada entero, procesarlo sin intervención del usuario y dejar un resultado) — justo lo que se ha practicado en esta unidad.
