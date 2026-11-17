# 4.1 Subprogramas

COBOL no tiene clases ni objetos. Lo más parecido que tiene a "separar una operación reutilizable con una interfaz definida" es un **subprograma**: otra unidad de programa, compilada aparte (o junto a la que la llama), con un contrato de entrada y salida que tú defines.

## Un ejemplo completo: duplicar un número

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. PRINCIPAL.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-NUMERO PIC 9(3) VALUE 21.
       01 WS-ESTADO PIC 9 VALUE 0.
       PROCEDURE DIVISION.
           CALL "DOBLE" USING BY REFERENCE WS-NUMERO WS-ESTADO.
           IF WS-ESTADO = 0
               DISPLAY "Resultado: " WS-NUMERO
           ELSE
               DISPLAY "Error: el doble no cabe"
               MOVE 1 TO RETURN-CODE
           END-IF.
           STOP RUN.
       END PROGRAM PRINCIPAL.

       IDENTIFICATION DIVISION.
       PROGRAM-ID. DOBLE.
       DATA DIVISION.
       LINKAGE SECTION.
       01 LS-NUMERO PIC 9(3).
       01 LS-ESTADO PIC 9.
       PROCEDURE DIVISION USING LS-NUMERO LS-ESTADO.
           MOVE 0 TO LS-ESTADO.
           IF LS-NUMERO > 499
               MOVE 1 TO LS-ESTADO
           ELSE
               MULTIPLY 2 BY LS-NUMERO
                   ON SIZE ERROR
                       MOVE 1 TO LS-ESTADO
               END-MULTIPLY
           END-IF.
           GOBACK.
       END PROGRAM DOBLE.
```

**Salida:**

```text
Resultado: 042
```

Dos programas en un único fichero fuente, separados con `END PROGRAM`: es la forma más simple de tenerlos juntos para compilar y probar (también podrían estar cada uno en su propio `.cob` y compilarse a la vez: `cobc -x principal.cob doble.cob -o principal`). `DOBLE` no tiene `WORKING-STORAGE` para sus datos de entrada: los recibe en la `LINKAGE SECTION`, los nombres que **el que llama** y **el llamado** acuerdan compartir. `GOBACK` devuelve el control a quien hizo la llamada (en el programa principal, `STOP RUN` sí termina todo el proceso).

!!! warning "La `LINKAGE SECTION` no reserva memoria propia"
    Los campos de `LINKAGE SECTION` describen datos que **vienen de fuera**: no son una copia nueva, son una ventana sobre el dato real del llamador. Si el llamador declara `LS-NUMERO` como `PIC 9(3)` y el llamado lo describe como `PIC 9(5)`, puedes leer o escribir memoria que no te pertenece. El contrato (tamaño, tipo, orden, modo de paso) lo tienes que mantener tú: el compilador no lo comprueba entre dos fuentes distintas.

## Formas de pasar datos

| Forma | Qué hace | Cuidado |
|---|---|---|
| `BY REFERENCE` (la de por defecto) | Comparte el dato del llamador: si el subprograma lo cambia, el llamador lo ve cambiado | Es la única forma de "devolver" un resultado por parámetro, como `WS-ESTADO` en el ejemplo |
| `BY CONTENT` | Pasa una copia: el subprograma puede modificarla, pero el llamador no se entera | No esperes recuperar cambios por ese parámetro |
| `BY VALUE` | Pasa un valor (pensado sobre todo para interoperar con otros lenguajes, como C) | No lo uses como sinónimo de `BY CONTENT`: la representación exacta depende del dialecto y del tipo |

## Estado entre llamadas: `WORKING-STORAGE` frente a `LOCAL-STORAGE`

Una sorpresa habitual: si un subprograma se llama varias veces, su `WORKING-STORAGE` **no se reinicia sola** entre llamadas (conserva lo que tenía la vez anterior). Si quieres que una variable empiece de cero en cada llamada, usa `LOCAL-STORAGE SECTION` en su lugar:

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. PRINCIPAL.
       PROCEDURE DIVISION.
           CALL "CONTAR".
           CALL "CONTAR".
           CALL "CONTAR".
           STOP RUN.
       END PROGRAM PRINCIPAL.

       IDENTIFICATION DIVISION.
       PROGRAM-ID. CONTAR.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 WS-VECES PIC 9 VALUE 0.
       LOCAL-STORAGE SECTION.
       01 LS-VECES PIC 9 VALUE 0.
       PROCEDURE DIVISION.
           ADD 1 TO WS-VECES.
           ADD 1 TO LS-VECES.
           DISPLAY "WS: " WS-VECES " LS: " LS-VECES.
           GOBACK.
       END PROGRAM CONTAR.
```

**Salida:**

```text
WS: 1 LS: 1
WS: 2 LS: 1
WS: 3 LS: 1
```

`WS-VECES` suma uno en cada llamada (1, 2, 3): sigue ahí de la llamada anterior. `LS-VECES` siempre vale 1: `LOCAL-STORAGE` se crea de nuevo, con su `VALUE` inicial, cada vez que se entra al subprograma. Si dependes de que algo persista (o de que no persista) entre llamadas, decláralo donde corresponde en vez de asumirlo.

## Señalar un error: con un parámetro, no con `STOP RUN`

En el primer ejemplo, `DOBLE` nunca para el programa aunque el resultado no quepa: deja el aviso en `LS-ESTADO` y es `PRINCIPAL` quien decide qué hacer (mostrar un mensaje y terminar con un código de error). Un subprograma de cálculo que decide por su cuenta terminar toda la aplicación (o que pide datos por consola cuando su contrato dice que los recibe por parámetro) rompe la idea de tener una interfaz clara: quien lo usa no puede confiar en que solo hace lo que promete.

!!! info "¿Y si quiero llamarlo sin saber su nombre de antemano?"
    `CALL "DOBLE"` (con comillas) es una llamada **estática**: el nombre del subprograma está fijo en el código fuente. COBOL también permite `CALL WS-NOMBRE-VARIABLE` (sin comillas, el nombre en una variable) para decidir en tiempo de ejecución qué subprograma llamar, y carga dinámica de módulos — mecanismos más avanzados que no hacen falta para esta unidad.
