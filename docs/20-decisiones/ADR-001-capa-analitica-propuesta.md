# ADR-001 · Separar operación, analítica y visualización

- Fecha: 2026-11-07.
- Estado: **propuesta, pendiente de aprobación**.
- Expediente: [TEC-2026-001](../10-investigaciones/clickhouse-metabase/01-investigacion.md).
- Alcance: criterio compartido para Arte Stampa, MapStories y lacajaambar; no es una migración.

## Contexto
No hay volúmenes, presupuesto, concurrencia ni benchmark confirmados para justificar un almacén analítico adicional. Documenta es biblioteca y no debe convertirse en una base de datos de clientes o visitantes.

## Propuesta
1. Diseñar necesidades y fuentes antes de seleccionar infraestructura.
2. Mantener datos operativos en una base transaccional; PostgreSQL es candidato recomendado, no elección ya aprobada ni base existente verificada.
3. Evaluar Metabase para análisis interno de datos tabulares con permisos mínimos.
4. Reservar ClickHouse para una carga analítica demostrada y mantenerlo fuera del camino crítico del producto.
5. Conservar documentos y medios en sus almacenes adecuados; enlazarlos por metadatos, no cargar todos los archivos en una tabla.
6. No habilitar BI multiempresa externo sin prueba específica de aislamiento y revisión de edición/licencia.

## Alternativas consideradas
- Solo informes/consultas sobre PostgreSQL: menor complejidad, punto de partida preferido.
- PostgreSQL + Metabase: mejora de autonomía analítica, añade operación y copias de su base de aplicación.
- PostgreSQL + ClickHouse + Metabase: reserva para analítica exigente; añade ingesta, consistencia eventual, costes y recuperación coordinada.
- ClickHouse como base única de todos los productos: no recomendado para este alcance, por desajuste entre operación transaccional y analítica.
- Metabase como recolector universal o gestor editorial: no recomendado; necesita una capa de ingesta y revisión con responsabilidades explícitas.

## Consecuencias
Menor coste inicial y menos duplicación, a cambio de posponer capacidades avanzadas no justificadas. La futura capa analítica puede fallar sin detener la aplicación. Esta propuesta no aprueba proveedores Cloud ni cambia repositorios de los productos.

## Reconsiderar cuando
Mediciones muestren consultas agregadas que perjudican el servicio, histórico analítico creciente, necesidad de actualización más frecuente o carga de observabilidad suficiente. Evaluar primero índices, consultas, preagregación y réplica de lectura.

## Pendientes
Aprobación de Luis; validación funcional por proyecto; responsable de datos; presupuesto; política de retención; región y contratos; revisión profesional de seguridad y privacidad; piloto medido.
