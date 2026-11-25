# 9.1 Archivos indexados

## La "base de datos" nativa de COBOL

Los demás lenguajes de esta web usan esta unidad para conectarse a una base de datos relacional de verdad (SQLite, consultas, transacciones). COBOL no tiene nada de eso de forma nativa: ni driver, ni SQL, ni clases. Lo que sí tiene, desde siempre, es una forma de guardar registros y **recuperarlos por una clave** sin leer el archivo entero: `ORGANIZATION IS INDEXED`. La unidad 7 ya avisó de que esto no se podía mostrar aquí con un ejemplo ejecutado, porque el GnuCOBOL de esta web no traía soporte de indexado. Para esta unidad se ha compilado un GnuCOBOL aparte, solo para esto, con esa pieza activada.

!!! info "Qué se ha probado exactamente"
    Los programas de esta página se compilaron y ejecutaron con **GnuCOBOL 3.2.0** y la biblioteca de indexado **VISAM 2.2-3** (el paquete `libvisam4` de Ubuntu). Los códigos `FILE STATUS` que se muestran son los que devuelve GnuCOBOL con esa configuración concreta: son los códigos estándar de COBOL (iguales en cualquier compilador serio), pero si alguna vez usas otro motor de indexado (el VBISAM original, o uno de IBM/Micro Focus en un mainframe) y algo no coincide exactamente, no sería la primera rareza de interoperabilidad entre implementaciones de ISAM.

## Declarar un archivo con clave

`ORGANIZATION IS INDEXED` dice que no es un archivo de texto línea a línea (como el de la unidad 7): es un archivo con **clave**. `RECORD KEY IS CLI-ID` dice cuál es esa clave (un campo de la `FD`, no una variable aparte). `ACCESS MODE IS DYNAMIC` permite las dos formas de leer que se usan en esta página: por clave suelta (`READ`) y en orden, desde donde se deje (`START` + `READ NEXT`).

## Un programa con todo: escribir, leer, modificar, borrar y recorrer

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. GESTION-CLIENTES.
       ENVIRONMENT DIVISION.
       INPUT-OUTPUT SECTION.
       FILE-CONTROL.
           SELECT F-CLI ASSIGN TO "clientes2.dat"
               ORGANIZATION IS INDEXED
               ACCESS MODE IS DYNAMIC
               RECORD KEY IS CLI-ID
               FILE STATUS IS WS-STATUS.
       DATA DIVISION.
       FILE SECTION.
       FD  F-CLI.
       01  R-CLI.
           05 CLI-ID    PIC 9(4).
           05 CLI-NOM   PIC X(20).
       WORKING-STORAGE SECTION.
       01  WS-STATUS    PIC XX.
       PROCEDURE DIVISION.
           OPEN OUTPUT F-CLI.
           MOVE 10 TO CLI-ID. MOVE "Ana" TO CLI-NOM.
           WRITE R-CLI.
           DISPLAY "1 escribir 10 nuevo: " WS-STATUS.
           MOVE 20 TO CLI-ID. MOVE "Luis" TO CLI-NOM.
           WRITE R-CLI.
           DISPLAY "2 escribir 20 nuevo: " WS-STATUS.
           MOVE 10 TO CLI-ID. MOVE "Otra" TO CLI-NOM.
           WRITE R-CLI.
           DISPLAY "3 escribir 10 duplicado: " WS-STATUS.
           CLOSE F-CLI.

           OPEN I-O F-CLI.
           MOVE 99 TO CLI-ID.
           READ F-CLI.
           DISPLAY "4 leer 99 no existe: " WS-STATUS.

           MOVE 10 TO CLI-ID.
           READ F-CLI.
           DISPLAY "5 leer 10 existe: " WS-STATUS " nom=" CLI-NOM.
           MOVE "Ana Maria" TO CLI-NOM.
           REWRITE R-CLI.
           DISPLAY "6 rewrite 10: " WS-STATUS.

           MOVE 20 TO CLI-ID.
           DELETE F-CLI.
           DISPLAY "7 delete 20: " WS-STATUS.
           READ F-CLI.
           DISPLAY "8 leer 20 tras borrar: " WS-STATUS.

           MOVE 30 TO CLI-ID. MOVE "Eva" TO CLI-NOM.
           WRITE R-CLI.
           DISPLAY "9 escribir 30: " WS-STATUS.

           DISPLAY "recorrido secuencial".
           MOVE 0 TO CLI-ID.
           START F-CLI KEY IS GREATER THAN CLI-ID.
           DISPLAY "10 start mayor que 0: " WS-STATUS.
           PERFORM 3 TIMES
               READ F-CLI NEXT RECORD
               DISPLAY "next status=" WS-STATUS
                   " id=" CLI-ID " nom=" CLI-NOM
           END-PERFORM.
           CLOSE F-CLI.
           STOP RUN.
```

**Salida:**

```text
1 escribir 10 nuevo: 00
2 escribir 20 nuevo: 00
3 escribir 10 duplicado: 22
4 leer 99 no existe: 23
5 leer 10 existe: 00 nom=Ana
6 rewrite 10: 00
7 delete 20: 00
8 leer 20 tras borrar: 23
9 escribir 30: 00
recorrido secuencial
10 start mayor que 0: 00
next status=00 id=0010 nom=Ana Maria
next status=00 id=0030 nom=Eva
next status=10 id=0030 nom=Eva
```

Van diez operaciones, cada una pensada para enseñar un código de estado distinto:

* **`WRITE`** (pasos 1-3) añade un registro nuevo; no vale para modificar uno que ya existe (para eso está `REWRITE`). Si la clave ya está ocupada, falla con **`"22"`** y el registro no se toca: la segunda "Ana" nunca llega a sustituir a la primera.
* **`READ`** por una clave (pasos 4-5): se pone la clave que se busca **en el propio campo** (`MOVE 99 TO CLI-ID`) y luego `READ`; no hay una instrucción tipo "buscar(99)" con el valor como argumento. Si no existe, **`"23"`** (registro no encontrado) — un resultado normal, no un error del programa (igual que el `"10"` de fin de archivo en la unidad 7 no lo era).
* **`REWRITE`** (paso 6) exige haber **leído antes** el registro con esa misma clave en este `OPEN` (no se puede reescribir a ciegas).
* **`DELETE`** (pasos 7-8) solo necesita la clave puesta en el campo. Borrado un registro, un `READ` por esa clave vuelve a dar `"23"`: no es que esté vacío, es que ya no está.
* **`START` + `READ NEXT`** (pasos 10 en adelante) recorren el archivo en orden de clave. `START` no lee nada: solo coloca un cursor interno en la primera clave que cumpla la condición (`GREATER THAN 0`, cualquiera, desde el principio). El cliente 20 no aparece (está borrado) y, al no quedar más registros, el tercer `READ NEXT` da `"10"` (fin de archivo): el campo no se actualiza, por eso se repite "0030 Eva".

!!! warning "`OPEN OUTPUT` sigue vaciando el archivo"
    Igual que en la unidad 7, si se quiere leer y modificar lo que ya hay (no empezar de cero), se abre con `OPEN I-O`, no con `OUTPUT`.

## Una segunda forma de buscar: clave alternativa

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. BUSCAR-POR-CIUDAD.
       ENVIRONMENT DIVISION.
       INPUT-OUTPUT SECTION.
       FILE-CONTROL.
           SELECT F-CLI ASSIGN TO "clientes3.dat"
               ORGANIZATION IS INDEXED
               ACCESS MODE IS DYNAMIC
               RECORD KEY IS CLI-ID
               ALTERNATE RECORD KEY IS CLI-CIUDAD
                   WITH DUPLICATES
               FILE STATUS IS WS-STATUS.
       DATA DIVISION.
       FILE SECTION.
       FD  F-CLI.
       01  R-CLI.
           05 CLI-ID       PIC 9(4).
           05 CLI-NOM      PIC X(15).
           05 CLI-CIUDAD   PIC X(10).
       WORKING-STORAGE SECTION.
       01  WS-STATUS    PIC XX.
       PROCEDURE DIVISION.
           OPEN OUTPUT F-CLI.
           MOVE 10 TO CLI-ID.
           MOVE "Ana" TO CLI-NOM.
           MOVE "Cadiz" TO CLI-CIUDAD.
           WRITE R-CLI.
           MOVE 20 TO CLI-ID.
           MOVE "Luis" TO CLI-NOM.
           MOVE "Cadiz" TO CLI-CIUDAD.
           WRITE R-CLI.
           MOVE 30 TO CLI-ID.
           MOVE "Eva" TO CLI-NOM.
           MOVE "Madrid" TO CLI-CIUDAD.
           WRITE R-CLI.
           CLOSE F-CLI.

           OPEN INPUT F-CLI.
           MOVE "Cadiz" TO CLI-CIUDAD.
           START F-CLI KEY IS EQUAL TO CLI-CIUDAD.
           DISPLAY "start ciudad=Cadiz: " WS-STATUS.
           PERFORM 3 TIMES
               READ F-CLI NEXT RECORD
               DISPLAY "status=" WS-STATUS
                   " id=" CLI-ID " nom=" CLI-NOM
                   " ciudad=" CLI-CIUDAD
           END-PERFORM.
           CLOSE F-CLI.
           STOP RUN.
```

**Salida** (dos clientes de Cadiz, uno de Madrid):

```text
start ciudad=Cadiz: 00
status=00 id=0010 nom=Ana             ciudad=Cadiz
status=00 id=0020 nom=Luis            ciudad=Cadiz
status=00 id=0030 nom=Eva             ciudad=Madrid
```

Un archivo `INDEXED` puede tener, además de su clave principal (`RECORD KEY`, siempre única), una o varias **claves alternativas**: otra forma de llegar a los mismos registros, por otro campo. `WITH DUPLICATES` permite que varios clientes compartan ciudad; sin esa palabra, la clave alternativa también tendría que ser única. Importante: `READ NEXT` **no para sola** al acabarse Cadiz; sigue en el orden de esa clave hasta el final del archivo (por eso aparece también Eva, de Madrid). Si solo interesan los de Cadiz, el programa tiene que comprobar él mismo si `CLI-CIUDAD` sigue valiendo "Cadiz" después de cada lectura y parar cuando cambie.

## Por qué esto (y no más) es la "base de datos" de COBOL

Un archivo `INDEXED` con una clave principal y alguna alternativa cubre lo mismo que cubriría una tabla sencilla con un índice: **llegar directo a un registro por un dato conocido**, en vez de leerlos todos. Lo que no cubre es lo que de verdad distingue a una base de datos **relacional**: expresar qué datos quieres y cómo se relacionan (un `JOIN` entre dos tablas) y que sea el motor quien decida cómo buscarlos. Eso, en COBOL de producción real, lo hace **SQL embebido contra Db2** — que no se puede mostrar aquí porque hace falta un Db2 de verdad. La siguiente página lo explica como lo que es: teoría, no un ejemplo ejecutado.
