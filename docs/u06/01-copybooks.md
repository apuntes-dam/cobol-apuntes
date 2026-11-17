# 6.1 Copybooks y diseño modular

Sin clases, COBOL organiza programas grandes con dos piezas: **subprogramas** (unidad 4, operaciones reutilizables) y **copybooks** (descripciones de datos reutilizables). Esta página es sobre los copybooks y sobre qué significa "diseñar bien" cuando no hay jerarquías de clases de por medio.

## `COPY`: compartir la descripción de un dato

Un copybook es un fichero con una descripción de datos (o de código) que varios programas necesitan. `COPY "nombre.cpy".` incorpora su texto **durante la compilación**: no es una lectura en tiempo de ejecución, es como si hubieras pegado ese texto ahí a mano.

```cobol
*> cliente.cpy
       01 CLIENTE.
           05 CODIGO-CLIENTE PIC 9(4).
           05 NOMBRE-CLIENTE PIC X(30).
```

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. USARCOPY.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       COPY "cliente.cpy".
       PROCEDURE DIVISION.
           INITIALIZE CLIENTE.
           MOVE 17 TO CODIGO-CLIENTE.
           MOVE "Ana" TO NOMBRE-CLIENTE.
           DISPLAY CODIGO-CLIENTE " " FUNCTION TRIM(NOMBRE-CLIENTE).
           STOP RUN.
```

**Salida:**

```text
0017 Ana
```

Compilado así: `cobc -x -I <carpeta-de-copybooks> usarcopy.cob -o usarcopy` (`-I` le dice al compilador dónde buscar los `.cpy`). `INITIALIZE CLIENTE` deja todo el grupo en su valor por defecto (los `PIC 9` a cero, los `PIC X` a espacios) antes de rellenarlo: útil para no arrastrar basura de un uso anterior de la misma variable.

!!! warning "Un copybook es un contrato: cámbialo con cuidado"
    Si dos programas distintos usan `cliente.cpy` y tú le añades un campo, **tienes que volver a compilar los dos**: `COPY` no es un enlace en tiempo de ejecución, así que un programa compilado con la versión antigua del copybook no se entera del cambio hasta que se recompila. Si cambias el **tamaño** de un campo existente (no solo añades uno nuevo al final), cualquier programa que no se recompile puede leer o escribir en la posición equivocada sin que el compilador avise: versiona los copybooks compartidos y documenta qué cambia.

## `MOVE CORRESPONDING` y `FILLER`

Dos herramientas más que reducen repetición al trabajar con grupos, con su propia trampa:

* **`MOVE CORRESPONDING grupo-a TO grupo-b`** copia solo los subcampos que tienen **el mismo nombre** en los dos grupos, no todos los bytes. Si `grupo-a` tiene un campo que `grupo-b` no tiene (o con otro nombre), ese campo simplemente no se copia, sin avisar.
* **`FILLER`** (o un nombre de campo omitido, según el dialecto) describe una zona del registro sin un nombre de dato utilizable: hueco reservado, o relleno para que el registro cuadre en un tamaño concreto.

Las dos ahorran líneas, pero las dos pueden ocultar un cambio real si alguien modifica el registro sin mirar quién depende de qué nombre.

## ¿Y los cinco principios SOLID?

Dicho con honestidad: forzar un paralelismo con SOLID (pensado para clases con herencia e interfaces) no encaja bien en COBOL, y esta web no va a fingir que sí. Lo que sí es real y aplicable en un programa COBOL grande:

* **Contratos claros**: cada subprograma (unidad 4) y cada copybook documentan qué reciben, qué devuelven y qué forma tienen los datos — el equivalente COBOL a una interfaz pequeña y bien definida.
* **Una responsabilidad por subprograma**: igual que una función no debería hacer dos cosas a la vez en cualquier lenguaje, un subprograma de cálculo no debería además leer de consola o decidir terminar el programa (ya se vio en [4.1](../u04/01-subprogramas.md)).
* **Copybooks versionados y documentados**: en vez de "sustituir una clase base por otra" (que no existe), la forma de evolucionar con seguridad es controlar quién usa cada copybook y recompilar todo lo que depende de él cuando cambia.

No es SOLID, pero el objetivo de fondo —que un cambio en un sitio no rompa silenciosamente otro— es el mismo.
