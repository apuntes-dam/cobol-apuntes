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
    Mientras escribes la nueva versión del archivo, el anterior tiene que seguir intacto por si algo falla a mitad de la escritura. Escribe a un archivo temporal y solo al final sustitúyelo; qué garantías da exactamente tu sistema operativo en ese paso hay que comprobarlo, no suponerlo.

## Etapa 5 · Altas, cambios y bajas

Modificar nombre y precio. Un cliente con pedidos históricos **no se borra**: se da de **baja lógica** (un campo de estado, como en [U5](../u05/01-sin-herencia.md)), para no perder la referencia desde sus pedidos antiguos. Si usas archivos `INDEXED` ([U9](../u09/01-archivos-indexados.md)), documenta qué backend usas, cuál es la clave principal, el modo de acceso y cómo tratas los bloqueos.

**Aceptación**: cambiar el precio de un producto **no** cambia el importe de los pedidos ya hechos (el precio copiado en la etapa 2 es el que manda). Desactivar un cliente le impide hacer pedidos nuevos, pero se puede seguir consultando su historial.

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
