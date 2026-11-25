# 11.1 Fechas

COBOL no tiene un tipo `DATE`: una fecha es, casi siempre, un número de 8 dígitos (`PIC 9(8)`) con la forma `AAAAMMDD`. Para trabajar con ella de verdad (sumar días, restar dos fechas, saber qué día es hoy) hay un conjunto de `FUNCTION` pensadas exactamente para eso.

## La fecha de hoy

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. FECHA-HOY.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01  WS-FECHA-NUM PIC 9(8).
       01  WS-HOY.
           05 H-ANIO PIC 9(4).
           05 H-MES  PIC 9(2).
           05 H-DIA  PIC 9(2).
       PROCEDURE DIVISION.
           MOVE FUNCTION CURRENT-DATE(1:8) TO WS-FECHA-NUM.
           DISPLAY "hoy (AAAAMMDD): " WS-FECHA-NUM.
           MOVE WS-FECHA-NUM(1:4) TO H-ANIO.
           MOVE WS-FECHA-NUM(5:2) TO H-MES.
           MOVE WS-FECHA-NUM(7:2) TO H-DIA.
           DISPLAY "formateada: " H-DIA "/" H-MES "/" H-ANIO.
           STOP RUN.
```

**Salida** (ejecutado el 25 de noviembre de 2026):

```text
hoy (AAAAMMDD): 20261125
formateada: 25/11/2026
```

`FUNCTION CURRENT-DATE` devuelve una cadena de 21 caracteres (fecha, hora con fracción de segundo y desplazamiento horario); `(1:8)` se queda solo con los 8 primeros (año, mes, día). A partir de ahí, `WS-FECHA-NUM(1:4)` etc. son **referencias de subcadena** (`campo(posición:longitud)`, ya vistas con `STRING`/`UNSTRING` en la unidad 3): no hace falta separar el año, el mes y el día con `UNSTRING`, basta con recortar por posición porque el formato `AAAAMMDD` siempre mide igual.

## Sumar días y restar dos fechas

```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. CALCULAR-FECHAS.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01  WS-FECHA1 PIC 9(8) VALUE 20261125.
       01  WS-FECHA2 PIC 9(8) VALUE 20261225.
       01  WS-DIAS   PIC S9(9).
       01  WS-FECHA3 PIC 9(8).
       PROCEDURE DIVISION.
           COMPUTE WS-DIAS =
               FUNCTION INTEGER-OF-DATE(WS-FECHA2)
               - FUNCTION INTEGER-OF-DATE(WS-FECHA1).
           DISPLAY "dias entre 25/11 y 25/12: " WS-DIAS.

           COMPUTE WS-FECHA3 =
               FUNCTION DATE-OF-INTEGER(
                   FUNCTION INTEGER-OF-DATE(WS-FECHA1) + 10).
           DISPLAY "25/11/2026 + 10 dias: " WS-FECHA3.
           STOP RUN.
```

**Salida:**

```text
dias entre 25/11 y 25/12: +000000030
25/11/2026 + 10 dias: 20261205
```

`FUNCTION INTEGER-OF-DATE` convierte una fecha `AAAAMMDD` en un número entero (los días transcurridos desde una fecha de referencia fija); con eso, restar dos fechas o sumarle días a una es una resta o una suma normal, sin preocuparse de meses de 28, 30 o 31 días ni de años bisiestos (lo resuelve la propia función). `FUNCTION DATE-OF-INTEGER` hace el camino contrario: de ese número entero, de vuelta a `AAAAMMDD`. El patrón siempre es el mismo: **para calcular con fechas, se pasa a entero, se calcula, y se vuelve a fecha**; nunca se suma ni se resta directamente sobre el `PIC 9(8)` (sumarle 10 a `20261125` a pelo daría `20261135`, un 35 de noviembre que no existe).

!!! warning "Dos dígitos de año: el problema del año 2000, todavía vivo en código antiguo"
    Programas COBOL muy antiguos guardaban el año con solo dos dígitos (`AAMMDD`, `PIC 9(6)`) para ahorrar espacio en los años 60-70, cuando cada byte en una cinta o una tarjeta perforada costaba. Eso es exactamente el problema del año 2000 (Y2K): con dos dígitos, 00 podía significar 1900 o 2000, y buena parte del esfuerzo mundial de los años 90 fue revisar COBOL de producción para arreglarlo. `FUNCTION INTEGER-OF-DATE` exige los 4 dígitos completos; si alguna vez trabajas con un archivo heredado de solo 2, hay que decidir explícitamente el siglo antes de convertir.
