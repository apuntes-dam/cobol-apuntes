# 3.2-3.5 Mapas, conjuntos, JSON y XML

En los demás lenguajes, estos apartados tratan mapas (diccionarios), conjuntos, JSON y XML. En COBOL, ninguno de los cuatro existe de forma nativa y fiable, y no es un descuido de esta web: así es COBOL de verdad.

## ¿Por qué no?

COBOL es de los años 60 y está pensado para **registros de tamaño fijo** (como una ficha de papel con casillas numeradas), no para estructuras que crecen o cambian de forma. Un "mapa clave → valor" o un documento JSON son estructuras **flexibles**: su forma depende de los datos. Eso no encaja con cómo COBOL describe los datos (`PIC`, niveles, `OCCURS` de tamaño fijo).

* **Mapas y conjuntos**: lo más parecido es una **tabla** (ver [3.1](01-tablas.md)) con `SEARCH` por una clave, como en el ejemplo de abajo. No hay operaciones de conjunto (unión, intersección) integradas: se programarían a mano, recorriendo tablas.
* **JSON**: `nlohmann::json` en C++, `System.Text.Json` en C#... COBOL no tiene nada parecido en una biblioteca estándar. Algunos compiladores comerciales (IBM Enterprise COBOL, desde su versión 6.2) añaden `JSON PARSE`/`JSON GENERATE`, pero **GnuCOBOL, el compilador libre que se usa en esta web, no los soporta** (se ha comprobado directamente: el compilador rechaza esas instrucciones con "compiler is not configured to support JSON"). No se puede enseñar aquí algo que no se puede ejecutar de verdad.
* **XML**: mismo caso. Sin biblioteca estándar ni soporte del compilador libre.

## Lo más parecido a un mapa: una tabla con búsqueda por clave

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. PRECIOS.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-FRUTAS.
           05 WS-FRUTA OCCURS 3 TIMES INDEXED BY WS-IDX.
               10 WS-NOMBRE-FRUTA PIC X(10).
               10 WS-PRECIO PIC 9V99.
       01 WS-BUSCA PIC X(10) VALUE "Pera".
       PROCEDURE DIVISION.
           MOVE "Manzana" TO WS-NOMBRE-FRUTA(1).
           MOVE 0.80 TO WS-PRECIO(1).
           MOVE "Pera" TO WS-NOMBRE-FRUTA(2).
           MOVE 0.85 TO WS-PRECIO(2).
           MOVE "Platano" TO WS-NOMBRE-FRUTA(3).
           MOVE 1.35 TO WS-PRECIO(3).
           SET WS-IDX TO 1.
           SEARCH WS-FRUTA
               AT END
                   DISPLAY "Fruta no encontrada"
               WHEN WS-NOMBRE-FRUTA(WS-IDX) = WS-BUSCA
                   DISPLAY FUNCTION TRIM(WS-BUSCA) ": "
                       WS-PRECIO(WS-IDX) " euros/kg"
           END-SEARCH.
           STOP RUN.
```

**Salida:**

```text
Pera: 0.85 euros/kg
```

Aquí cada posición de la tabla es un **grupo** (`WS-FRUTA`, con subcampos `WS-NOMBRE-FRUTA` y `WS-PRECIO`), así que cada "fila" guarda la clave y el valor juntos: es la forma COBOL de tener algo parecido a un mapa, con la limitación de que el número de filas es fijo desde que se declara la tabla.

!!! info "Lo que realmente usa COBOL en producción"
    Programas COBOL reales que necesitan estructuras flexibles (como leer un pedido con un número variable de líneas) usan **registros con una parte fija y una parte repetida** (`OCCURS ... DEPENDING ON`), o delegan el formato de intercambio a otro programa (escrito en otro lenguaje) que traduce JSON/XML a los registros fijos que COBOL sabe leer. No se enseña aquí porque se sale del nivel de esta unidad, pero merece la pena saber que existe.
