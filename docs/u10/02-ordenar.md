# 10.2 Ordenar: a mano y con `SORT`

`SEARCH ALL` de la página anterior exige una tabla ya ordenada. ¿Cómo se ordena? Hay dos escenarios completamente distintos en COBOL, con dos soluciones distintas: una **tabla en memoria** (`OCCURS`, como la de productos) y un **archivo** (como el de la unidad 7 o el 9). COBOL no tiene una función `SORT()` que ordene una tabla en memoria: eso se escribe a mano. Para archivos, en cambio, hay un verbo dedicado.

## Ordenar una tabla a mano: burbuja

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. ORDENAR-TABLA.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01  TABLA-PROD.
           05 PROD OCCURS 5 TIMES.
               10 P-COD  PIC 9(4).
               10 P-NOM  PIC X(10).
       01  I PIC 9(2).
       01  J PIC 9(2).
       01  WS-TEMP.
           05 T-COD  PIC 9(4).
           05 T-NOM  PIC X(10).
       PROCEDURE DIVISION.
           MOVE 10 TO P-COD(1). MOVE "Teclado" TO P-NOM(1).
           MOVE 20 TO P-COD(2). MOVE "Raton" TO P-NOM(2).
           MOVE 30 TO P-COD(3). MOVE "Monitor" TO P-NOM(3).
           MOVE 40 TO P-COD(4). MOVE "Cable" TO P-NOM(4).
           MOVE 50 TO P-COD(5). MOVE "Altavoz" TO P-NOM(5).

           PERFORM VARYING I FROM 1 BY 1 UNTIL I > 4
               PERFORM VARYING J FROM 1 BY 1 UNTIL J > 5 - I
                   IF P-COD(J) < P-COD(J + 1)
                       MOVE P-COD(J) TO T-COD
                       MOVE P-NOM(J) TO T-NOM
                       MOVE P-COD(J + 1) TO P-COD(J)
                       MOVE P-NOM(J + 1) TO P-NOM(J)
                       MOVE T-COD TO P-COD(J + 1)
                       MOVE T-NOM TO P-NOM(J + 1)
                   END-IF
               END-PERFORM
           END-PERFORM.
           PERFORM VARYING I FROM 1 BY 1 UNTIL I > 5
               DISPLAY P-COD(I) " " P-NOM(I)
           END-PERFORM.
           STOP RUN.
```

**Salida** (ordenado de mayor a menor código):

```text
0050 Altavoz
0040 Cable
0030 Monitor
0020 Raton
0010 Teclado
```

Es la burbuja de toda la vida: un bucle exterior (`I`) y uno interior (`J`) que compara cada elemento con el siguiente y los intercambia si están en el orden equivocado. El intercambio necesita una variable auxiliar (`WS-TEMP`) con la misma forma que un elemento de la tabla, porque hay que guardar uno de los dos valores en algún sitio mientras se mueve el otro. Para una tabla que ya viene de un `SEARCH ALL` con `ASCENDING KEY`, cambiar `<` por `>` (y recorrer en el sentido contrario) ordena de forma ascendente.

## Ordenar un archivo: el verbo `SORT`

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. ORDENAR-ARCHIVO.
       ENVIRONMENT DIVISION.
       INPUT-OUTPUT SECTION.
       FILE-CONTROL.
           SELECT ENTRADA ASSIGN TO "entrada.txt"
               ORGANIZATION IS LINE SEQUENTIAL.
           SELECT SALIDA ASSIGN TO "salida.txt"
               ORGANIZATION IS LINE SEQUENTIAL.
           SELECT TRABAJO ASSIGN TO "trabajo.tmp".
       DATA DIVISION.
       FILE SECTION.
       FD  ENTRADA.
       01  LINEA-E PIC X(30).
       FD  SALIDA.
       01  LINEA-S PIC X(30).
       SD  TRABAJO.
       01  LINEA-T.
           05 T-COD PIC 9(4).
           05 T-NOM PIC X(20).
       PROCEDURE DIVISION.
           SORT TRABAJO ON ASCENDING KEY T-COD
               USING ENTRADA
               GIVING SALIDA.
           OPEN INPUT SALIDA.
           PERFORM UNTIL 1 = 2
               READ SALIDA AT END EXIT PERFORM END-READ
               DISPLAY FUNCTION TRIM(LINEA-S)
           END-PERFORM.
           CLOSE SALIDA.
           STOP RUN.
```

**Salida** (con `entrada.txt` desordenado: 0030, 0010, 0050, 0020, 0040):

```text
0010Teclado
0020Raton
0030Monitor
0040Cable
0050Altavoz
```

`SD` (*sort description*) describe la forma de cada registro durante la ordenación, igual que `FD` la describe en un archivo normal; `TRABAJO` es un archivo de trabajo interno que el propio `SORT` crea y borra, nunca se abre ni se cierra a mano. `USING ENTRADA` lee todo `ENTRADA` como entrada y `GIVING SALIDA` escribe el resultado ya ordenado; ninguno de los dos archivos se abre explícitamente tampoco, el `SORT` se encarga. Una sola instrucción sustituye lo que, en memoria, serían varias decenas de líneas de burbuja — y para archivos grandes, es la única opción razonable: la burbuja de la página anterior necesita tener toda la tabla en memoria a la vez.

!!! info "`SORT` también puede calcular antes de ordenar"
    En vez de `USING`/`GIVING`, `SORT` admite una `INPUT PROCEDURE` (un párrafo propio que decide, registro a registro, cuáles entran a ordenar y con qué forma, entregándolos con `RELEASE`) y una `OUTPUT PROCEDURE` (que los recoge ya ordenados con `RETURN` y hace algo con ellos, sin pasar por un archivo de salida intermedio). Es más código, pero evita escribir y releer un archivo cuando el resultado se va a procesar enseguida.

## En un mainframe real

En COBOL de producción (IBM z/OS), ordenar un archivo grande se hace de dos formas, y **las dos conviven**:

* como un **paso independiente de JCL**, ejecutando una utilidad de ordenación (típicamente **DFSORT**) antes de que el programa COBOL lea el resultado — natural cuando el archivo ya existe tal cual y varios pasos posteriores van a leerlo ordenado;
* con el propio **verbo `SORT`** dentro del programa COBOL — natural cuando es el programa quien calcula los registros que hay que ordenar (con una `INPUT PROCEDURE`) o quien va a procesar la secuencia ordenada directamente (con una `OUTPUT PROCEDURE`), sin un archivo intermedio de por medio.

Las dos formas pueden incluso usar el mismo motor por debajo: el `SORT` de COBOL, bien compilado, puede delegar en DFSORT. No es "JCL mejor que COBOL"; es qué parte del trabajo (leer/escribir el archivo, o calcular/consumir los registros) le conviene hacer a cada pieza.
