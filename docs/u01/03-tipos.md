# 1.3 Tipos de datos

COBOL es de **tipado estático**, pero no tiene palabras como `int` o `string`: el tipo y el tamaño exacto de cada variable se describen con una **imagen** (`PIC`, de *picture*).

## Símbolos de `PIC`

| Símbolo | Significa | Ejemplo | Guarda |
|---|---|---|---|
| `9` | Un dígito | `PIC 9(3)` | números de 0 a 999 |
| `X` | Un carácter cualquiera | `PIC X(10)` | hasta 10 caracteres |
| `A` | Una letra o espacio | `PIC A(10)` | texto sin dígitos |
| `V` | El punto decimal **implícito** (no ocupa espacio) | `PIC 9(3)V99` | de `000.00` a `999.99` |
| `S` | Con signo (sin ella, el valor siempre es positivo) | `PIC S9(3)` | de `-999` a `999` |

`9(3)` es una forma corta de escribir `999` (tres nueves); funciona igual con `X` y `A`.

## Mostrar un valor numérico

```cobol
       01 WS-EDAD PIC 9(3) VALUE 7.
       01 WS-SALDO PIC S9(5)V99 VALUE -12.5.
       PROCEDURE DIVISION.
           DISPLAY WS-EDAD.     *> 007
           DISPLAY WS-SALDO.    *> -00012.50
```

**Salida:**

```text
007
-00012.50
```

Esto no es un error: `DISPLAY` muestra el contenido **tal cual ocupa en memoria**, con el tamaño exacto que marca el `PIC` (relleno de ceros a la izquierda, y el signo delante si la variable es `S`). Para un número "bonito" sin ceros de más hace falta una imagen de edición (`PIC ZZ9` o similar), que se verá más adelante.

## Niveles y grupos

Las variables se numeran (`01`, `05`, `10`...) para formar jerarquías: un **grupo** (una variable sin `PIC`, que contiene otras) y sus elementos **elementales** (los que sí llevan `PIC`):

```cobol
       01 WS-PERSONA.
           05 WS-NOMBRE PIC A(10) VALUE "Ana".
           05 WS-EDAD   PIC 9(3)  VALUE 20.
       PROCEDURE DIVISION.
           DISPLAY WS-PERSONA.          *> el grupo entero
           DISPLAY WS-NOMBRE " " WS-EDAD.
```

**Salida:**

```text
Ana       020
Ana        020
```

`DISPLAY WS-PERSONA` muestra los bytes de **todo el grupo** seguidos (el nombre, con su relleno de espacios hasta 10 caracteres, pegado a la edad); `DISPLAY WS-NOMBRE " " WS-EDAD` los muestra por separado, con el espacio literal entre medias. El número de nivel solo indica **quién está dentro de quién**: `05` está dentro del `01` anterior porque es un número más alto, no porque sea exactamente "5"; es habitual subir de 5 en 5 (`05`, `10`, `15`...) para poder meter más niveles en medio si hace falta.

## Convertir texto a número: `FUNCTION NUMVAL`

```cobol
       01 WS-TEXTO PIC X(10) VALUE "3.14".
       01 WS-NUM PIC 9(3)V99.
       PROCEDURE DIVISION.
           COMPUTE WS-NUM = FUNCTION NUMVAL(WS-TEXTO).
           DISPLAY WS-NUM.   *> 003.14
```

`FUNCTION NUMVAL` es la forma fiable de convertir un texto escrito por el usuario (con su punto decimal) a un valor numérico; ya se usó en [1.1](01-programa.md) para leer por teclado. También existe `FUNCTION NUMVAL-C` para textos con símbolo de moneda.

!!! note "No hay `double` ni redondeos raros"
    Al no usar coma flotante, `PIC 9(3)V99` guarda **exactamente** `3.14`, sin el error de redondeo binario que tienen `double`/`float` en otros lenguajes (`0.1 + 0.2` ahí puede dar `0.30000000000000004`). Es la razón por la que la banca lleva décadas confiando los cálculos de dinero a COBOL.
