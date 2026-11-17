# 1.2 Práctica con el lenguaje

## Variables: `WORKING-STORAGE SECTION`

En COBOL las variables no se declaran donde se usan: se declaran todas juntas, antes del código, en la `WORKING-STORAGE SECTION` de la `DATA DIVISION`. Cada una lleva un **nivel** (`01`), un nombre y una imagen (`PIC`, de *picture*) que fija su tipo y tamaño (se explica a fondo en [1.3](03-tipos.md)):

```cobol
       WORKING-STORAGE SECTION.
       01 WS-NOMBRE PIC X(20).
       01 WS-EDAD PIC 9(3) VALUE 0.
```

`VALUE` fija el valor inicial; sin ella, el contenido de la variable al arrancar no está definido (por convención, en esta web se nombran con el prefijo `WS-`, de *working storage*, para distinguirlas a simple vista).

## Comentarios

```cobol
      *> Comentario de toda la línea (asterisco en la columna 7)
       DISPLAY "Hola".   *> También vale al final de una línea de código
```

No existe un comentario "de varias líneas": cada línea de comentario necesita su propio `*>` (o, en el formato fijo clásico más antiguo, su propio `*` en columna 7).

## Asignar y mostrar

```cobol
       MOVE 5 TO WS-EDAD.                  *> asignación directa
       COMPUTE WS-EDAD = WS-EDAD + 1.      *> con una expresión
       DISPLAY "Edad: " WS-EDAD.
```

`MOVE` copia un valor tal cual; `COMPUTE` evalúa una expresión aritmética. `DISPLAY` admite varios valores seguidos (se concatenan, sin espacios automáticos: añádelos tú en los literales).

## Entrada con `ACCEPT`

```cobol
       DISPLAY "Escribe tu nombre: " WITH NO ADVANCING.
       ACCEPT WS-NOMBRE.
       DISPLAY "Hola, " FUNCTION TRIM(WS-NOMBRE) ".".
```

`WITH NO ADVANCING` evita el salto de línea después del `DISPLAY`, para que la respuesta quede en la misma línea que la pregunta. `WS-NOMBRE` es `PIC X(20)`: si escribes `Juan`, el resto se rellena con espacios hasta los 20 caracteres, así que `FUNCTION TRIM(...)` los quita antes de mostrarlo (si no, saldría `Hola, Juan                .`, con los espacios de relleno antes del punto).

## Operadores

```cobol
       COMPUTE WS-RES = 7 + 2.         *> 9
       COMPUTE WS-RES = 7 - 2.         *> 5
       COMPUTE WS-RES = 7 * 2.         *> 14
       COMPUTE WS-RES = 7 / 2.         *> 3  (si WS-RES no tiene decimales, se trunca)
       DIVIDE 7 BY 2 GIVING WS-COC REMAINDER WS-RES.   *> cociente y resto, por separado
       COMPUTE WS-RES = 2 ** 3.        *> 8   (potencia)
```

Relacionales: `=`, `>`, `<`, `>=`, `<=`, `<>` (distinto). Lógicos: `AND`, `OR`, `NOT`. Se usan igual en `IF` y en `PERFORM UNTIL`:

```cobol
       IF WS-EDAD >= 18 AND WS-EDAD < 65
           DISPLAY "Adulto en edad de trabajar"
       END-IF.
```

!!! tip "Comparar texto"
    `IF WS-NOMBRE = "ANA"` compara los `20` caracteres completos de `WS-NOMBRE` (con el relleno de espacios incluido), así que `"ANA"` se compara contra `"ANA                 "`: si lo escribes con las comillas ajustadas al tamaño del campo, funciona; si no, compara con `FUNCTION TRIM` de por medio.

## Literales

`"texto"` (alfanumérico, también con `'texto'`), `42` (entero), `3.14` (decimal, con **punto**, nunca coma, dentro del código fuente) y `TRUE`/`FALSE` solo existen para variables declaradas `PIC 1` o condiciones con nombre (`01 WS-OK PIC X VALUE "S". 88 ES-VALIDO VALUE "S".`), algo que no hace falta todavía en esta unidad.
