# 12.1 Proyecto: clientes y pedidos

## Qué se entrega

Una aplicación de **consola**, construida **por etapas**. Esta página es la **especificación y los criterios de aceptación**, no una aplicación ya hecha: a propósito, no hay un "programa oficial" que copiar. Cada etapa reutiliza algo de una unidad anterior; al llegar aquí ya has visto todo lo que hace falta.

!!! warning "No es un ejercicio con solución para copiar"
    Las demás páginas de esta web dan una solución modelo verificada para cada ejercicio. Esta no: un proyecto final se evalúa por si **cumple las reglas y pasa las pruebas**, no por si coincide con un código concreto. Compilar no demuestra que las reglas estén bien hechas — tus propias pruebas sí.

## Datos y reglas

* **Cliente**: identificador de 4 dígitos, nombre de hasta 30 caracteres, estado activo/inactivo.
* **Producto**: identificador de 4 dígitos, descripción de 30 caracteres, precio de 0,00 a 9999,99, existencias de 0 a 9999.
* **Pedido**: identificador, cliente, hasta 10 líneas, estado borrador/confirmado/cancelado.
* **Línea**: producto, cantidad de 1 a 999, **precio unitario copiado al crearla** (el precio histórico no cambia aunque cambie el catálogo después).

Reglas generales: rechaza claves repetidas; un cliente inexistente o inactivo no puede confirmar pedidos; límite de 100 clientes y 100 productos en memoria (etapas 1-3); los importes se muestran con dos decimales; sin impuestos en esta versión.

## Etapa 1 · Catálogo en memoria

Alta, consulta y listado de productos. Distingue "ya está lleno" de "ya existe esa clave". Separa **entrada**, **validación** y **operación** en párrafos con nombres que digan qué hacen (lo que ya pide la unidad 1 para cualquier programa, aquí con más piezas).

**Aceptación**: crea dos productos, consulta los dos, y que un tercero con clave repetida se rechace. Una búsqueda que falla no debe dejar en pantalla los datos del último que sí encontraste (una `WORKING-STORAGE` mal reiniciada es un fallo real y común aquí).

## Etapa 2 · Pedido con varias líneas

Añadir, quitar y listar líneas de un pedido (tabla `OCCURS`, como en [U3](../u03/01-tablas.md)). Para simplificar, no se repite un mismo producto en el mismo pedido. El total se **calcula desde las líneas que hay ahora mismo**, nunca con un acumulador que vas sumando y restando a mano (eso acaba desincronizado en cuanto borras una línea).

**Datos de prueba**: producto 1001 a 12,50, producto 1002 a 3,20. Cantidades 2 y 3 → importes 25,00 y 9,60 → total 34,60. Quitas la segunda línea → total 25,00. Un pedido sin líneas no se puede confirmar.

## Etapa 3 · Confirmación

Primero se valida **todo** (líneas y existencias suficientes); solo si todo es válido se descuenta stock y cambia el estado. Una segunda confirmación del mismo pedido debe rechazarse **sin volver a descontar**.

**Prueba**: existencias 5 y 2; pedir 2 y 3 debe rechazar el pedido **completo** (no vale confirmar media orden) y dejar las dos existencias intactas. Cambias la segunda cantidad a 2 → total 31,40; existencias finales 3 y 0.

## Etapa 4 · Persistencia

Antes de escribir una sola línea de código: decide formato, codificación, tamaños de campo, una versión del formato y qué pasa si algo falla al leer. Empieza por guardar y recuperar un catálogo de prueba (archivos de texto, [U7](../u07/01-archivos.md)). Comprueba qué pasa con un registro incompleto, uno duplicado y el archivo que no existe — los mismos casos límite que ya viste en U7 con `FILE STATUS`.

!!! warning "Nunca sustituyas el único archivo válido a medias"
    Escribe la nueva versión en un archivo temporal, **en el mismo directorio** que el definitivo (para que el cambio de nombre sea atómico). Comprueba el `FILE STATUS` de `OPEN`, de cada `WRITE` y de `CLOSE` del temporal; solo si todo ha ido bien, llama a la rutina del *runtime* de GnuCOBOL `CBL_RENAME_FILE` para sustituir el definitivo por el temporal (sin borrar antes el definitivo) y comprueba el código que devuelve antes de dar el guardado por bueno.

**Aceptación**: interrumpe el programa a propósito justo después de escribir el primer registro del archivo temporal, antes de llegar a `CBL_RENAME_FILE`. Al reiniciar, el archivo definitivo debe conservar exactamente su contenido anterior: quien lo abra ve la versión vieja completa o la nueva completa, nunca una mezcla. Esto demuestra que un fallo a medias no corrompe el archivo; no demuestra qué pasa si se va la luz a mitad del propio renombrado (es una garantía del sistema de archivos, no de tu programa), y no se aplica igual si usas archivos indexados en la Etapa 5 (un solo `CBL_RENAME_FILE` no sustituye a la vez todos los componentes físicos que use ese motor).

## Etapa 5 · Altas, cambios y bajas

Modificar nombre y precio. Un cliente con pedidos históricos **no se borra**: se da de **baja lógica** (un campo de estado, como en [U5](../u05/01-sin-herencia.md)), para no perder la referencia desde sus pedidos antiguos.

**Camino obligatorio (archivo de texto):** al cargar el archivo, construye un **índice en memoria por identificador** (una tabla `OCCURS`, como en la Etapa 2) para no releer todo el archivo en cada búsqueda. Se admite una sola instancia del programa accediendo a la vez al archivo de datos: con eso basta, no hace falta tratar bloqueos entre procesos en este camino.

**Aceptación**: un alta con clave repetida se rechaza; dar de baja un cliente concreto deja de baja **ese** cliente y ningún otro; los cambios (altas, bajas, modificaciones) se conservan al cerrar y reabrir el programa. Cambiar el precio de un producto **no** cambia el importe de los pedidos ya hechos (el precio copiado en la etapa 2 es el que manda). Desactivar un cliente le impide hacer pedidos nuevos, pero se puede seguir consultando su historial.

!!! tip "Ampliación: archivos indexados y bloqueos"
    Si quieres, sustituye el archivo de texto por `ORGANIZATION IS INDEXED` ([U9](../u09/01-archivos-indexados.md)) — **requiere una compilación de GnuCOBOL con soporte para archivos indexados** (no toda instalación de GnuCOBOL lo trae de serie; hay varios motores posibles, no solo VISAM). Documenta qué motor usas, cuál es la clave principal y el modo de acceso.

    Para probar bloqueos hace falta que **dos procesos compitan de verdad** por el archivo: abre el programa en dos terminales a la vez, deja uno con un registro bloqueado y que el otro intente modificar **ese mismo registro**. Anota qué pasa con tu configuración concreta y qué pasa al liberar el bloqueo. Si el segundo falla ya en el `OPEN` del archivo, eso demuestra un conflicto a nivel de **archivo**, no de **registro**: son pruebas distintas, no las confundas.

## Etapa 6 · Informe y lotes

Un listado por cliente con el número de pedidos y el total confirmado (decide tú qué hacer con los empates al ordenar — mira [U10](../u10/01-busquedas.md)); **borradores y cancelados no cuentan** en ese total.

Importar un lote de pedidos de golpe: cada registro se acepta o se rechaza, con un **motivo** por cada rechazo, y al final **leídos = aceptados + rechazados** (si no cuadra, algo del proceso se comió un registro). Volver a importar el mismo lote **no debe duplicar** los pedidos ya confirmados.

## Etapa 7 · Separar en subprogramas

Saca el cálculo de una línea (cantidad × precio) a un **subprograma** ([U4](../u04/01-subprogramas.md)): documenta sus parámetros, los rangos que acepta, la escala decimal, qué devuelve como estado y si toca algún dato fuera de lo que recibe. Ejecuta los **mismos casos de prueba** antes y después de extraerlo: si algún resultado cambia, el problema está en esa extracción y en ningún otro sitio.

!!! info "No extraigas todo a la vez"
    Una refactorización pequeña, probada ella sola, es la única forma de saber con seguridad qué la rompió si algo deja de cuadrar.

## Entrega y evaluación

Entrega: fuentes, copybooks, datos de prueba (nunca datos de clientes reales), instrucciones para compilar, las pruebas que has hecho con sus resultados reales, y las limitaciones que sepas que tiene.

| Criterio | Peso |
|---|---|
| Reglas de negocio | 30 % |
| Persistencia y recuperación | 20 % |
| Pruebas | 20 % |
| Estructura y contratos (subprogramas, copybooks) | 15 % |
| Explicación y uso | 15 % |

Que compile **no** es prueba de que las reglas estén bien hechas — solo de que no hay errores de sintaxis. Eso lo demuestran tus propias pruebas, de la lista de aceptación de cada etapa hacia arriba.
