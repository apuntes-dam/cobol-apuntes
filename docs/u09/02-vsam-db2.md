# 9.2 En mainframe real: VSAM y Db2

!!! warning "Teoría, no algo que se pueda ejecutar en esta web"
    Esta página no tiene ejemplos compilados: hace falta un IBM Z con VSAM y Db2 de verdad, que no existe aquí. Es orientación para no quedarte con la idea de que los archivos `INDEXED` de la página anterior son "todo lo que hay"; no sustituye la documentación del producto.

## Dos mecanismos que conviven, no uno que sustituye al otro

Es tentador pensar la historia como "COBOL usaba archivos indexados y luego llegaron las bases de datos relacionales, que los sustituyeron". No es así. En un mainframe IBM, un mismo programa COBOL puede:

* acceder directamente a un archivo **VSAM KSDS** (*Key-Sequenced Data Set*) — el equivalente real, a escala de producción, de los archivos `INDEXED` de la página anterior;
* consultar tablas de **Db2** mediante SQL embebido (`EXEC SQL ... END-EXEC`);
* hacer las dos cosas **en el mismo proceso**.

| Lo que necesita el programa | Con un VSAM KSDS | Con tablas de Db2 |
|---|---|---|
| El cliente 150 | Pone 150 en el campo clave y hace `READ` sobre el archivo de clientes (igual que en 9.1). | Dentro de `EXEC SQL ... END-EXEC`: `SELECT NOMBRE INTO :WS-NOMBRE FROM CLIENTES WHERE ID = :WS-ID`. Las variables con `:` son campos COBOL que intercambian valores con Db2. |
| Cada pedido, con el nombre de su cliente | El programa recorre los pedidos y, para cada uno, hace un `READ` al archivo de clientes por clave: el cruce lo hace el propio programa, registro a registro. | Una consulta con `JOIN` expresa la relación; Db2 decide cómo acceder a los datos y combinarlos. |

La primera fila explica por qué la analogía con un `READ` por clave resulta tan natural. La segunda muestra hasta dónde llega: con un KSDS, el programa dice **qué registro leer, uno a uno**; con Db2, el programa describe **qué conjunto de filas quiere**, aunque salgan de varias tablas, y deja que el motor decida cómo.

## Un detalle que conecta ambos mundos

En z/OS, Db2 no "sustituye" a VSAM: **lo usa por debajo**. Los espacios de tablas de Db2 se guardan en conjuntos de datos lineales de VSAM (LDS, *Linear Data Set*) — una organización de VSAM distinta de un KSDS. Cuando un programa COBOL ejecuta un `SELECT`, está trabajando con una tabla relacional; es Db2 quien gestiona ese almacenamiento VSAM por debajo, no el programa.

## Qué significa esto para lo aprendido en 9.1

El archivo `INDEXED` de GnuCOBOL es una versión real, ejecutable y gratuita del mismo concepto que un VSAM KSDS: clave, `READ`/`REWRITE`/`DELETE`/`START`, claves alternativas. Lo que no se puede practicar aquí es SQL embebido, porque no existe un Db2 para esta web. Si algún día trabajas con COBOL de mainframe, los nombres cambian (VSAM, Db2, `EXEC SQL`) pero la idea central no: hay una forma de llegar directo a un registro por clave, y otra de pedir una combinación de datos y dejar que el motor decida cómo traerla.
