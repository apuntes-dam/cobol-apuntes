# 1.4 Pruebas

COBOL no tiene un framework de pruebas unitarias estándar incluido, como xUnit en C# o `#[test]` en Rust: GnuCOBOL, el compilador libre que se usa en esta web, no trae ninguno integrado. Existen proyectos externos (por ejemplo **Cobol Check**, de código abierto) que añaden algo parecido, pero no están tan extendidos ni probados aquí, así que esta página no los usa: en su lugar, se plantea una **convención sencilla**, hecha con las mismas instrucciones que ya conoces, para comprobar un programa sin tener que mirar la salida a ojo cada vez.

## La idea: comparar lo esperado contra lo obtenido

Para cada caso de prueba: fija los datos de entrada, calcula el resultado y compara con el valor que **ya sabes** que debería salir (lo calculas tú a mano, antes de programar). Si coincide, `CORRECTO`; si no, `FALLO`, con los dos valores a la vista para ver qué pasó:

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. PRUEBA-DOBLE.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-N PIC 9(3).
       01 WS-DOBLE PIC 9(3).
       01 WS-ESPERADO PIC 9(3).
       PROCEDURE DIVISION.
           MOVE 7 TO WS-N.
           MOVE 14 TO WS-ESPERADO.
           COMPUTE WS-DOBLE = WS-N * 2.
           IF WS-DOBLE = WS-ESPERADO
               DISPLAY "CORRECTO: doble(7) = " WS-DOBLE
           ELSE
               DISPLAY "FALLO: se esperaba " WS-ESPERADO
                   " y salio " WS-DOBLE
           END-IF.

           MOVE 0 TO WS-N.
           MOVE 0 TO WS-ESPERADO.
           COMPUTE WS-DOBLE = WS-N * 2.
           IF WS-DOBLE = WS-ESPERADO
               DISPLAY "CORRECTO: doble(0) = " WS-DOBLE
           ELSE
               DISPLAY "FALLO: se esperaba " WS-ESPERADO
                   " y salio " WS-DOBLE
           END-IF.
           STOP RUN.
```

**Salida:**

```text
CORRECTO: doble(7) = 014
CORRECTO: doble(0) = 000
```

## Por qué sirve, aunque no sea un framework de verdad

- Obliga a decidir **antes** de programar cuál es el resultado correcto (como en cualquier prueba, sea del lenguaje que sea).
- El caso `0` de este ejemplo no está puesto al azar: en cualquier lenguaje conviene probar los **casos límite** (cero, el primero, el último, el que no cabe) además del caso normal.
- Cuando en unidades más adelantadas aparezcan subprogramas (`CALL`), la misma idea se reutiliza: un programa de pruebas que llama al subprograma con varios datos y compara cada resultado.

!!! info "Si quieres ir más allá"
    Busca **Cobol Check**: un proyecto de código abierto que añade una sintaxis de pruebas (estilo *dado/cuando/entonces*) sobre programas COBOL compilados con GnuCOBOL, entre otros compiladores. No se usa en esta web porque la convención de arriba ya cubre lo que hace falta para empezar, pero merece la pena conocerlo si programas COBOL en serio.
