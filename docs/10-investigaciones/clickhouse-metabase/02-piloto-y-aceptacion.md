# TEC-2026-001 · Piloto propuesto y criterios de aceptación

2026-11-07 · v1.0 · **No ejecutado**. Esta investigación no instala servicios, contrata Cloud ni prueba rendimiento.

## Pregunta
¿Un panel interno resuelve decisiones reales con un coste y riesgo aceptables? Solo después: ¿añadir ClickHouse mejora una carga analítica que PostgreSQL no resuelve adecuadamente?

## Etapa A: necesidades antes de infraestructura
El responsable funcional selecciona un caso por proyecto, define quién puede ver cada dato y confirma las fuentes autorizadas. Documentar volumen actual, incremento diario, concurrencia, retención y presupuesto; hoy no están medidos. Elegir indicadores con numerador, denominador, zona horaria y tratamiento de duplicados.

Propuestas de indicadores, no métricas existentes:
- Arte Stampa: obras con relato autorizado / obras publicables; audioguías con transcripción; uso agregado por evento, sin compradores identificados.
- MapStories: cobertura editorial por punto, reproducciones y finalización por ruta cuando haya consentimiento; no interpretar aperturas como visitas físicas demostradas.
- lacajaambar: registros con fuente, licencia y fecha / registros publicados; antigüedad de revisión y errores de ingesta. Validar primero si existe un conjunto tabular que requiera BI.

## Etapa B: PostgreSQL y Metabase en aislamiento
1. Autorizar un entorno separado y fijar versiones exactas de Metabase, PostgreSQL, JVM/imagen y driver. No usar `latest` en producción.
2. Trabajar primero con un conjunto sintético explícitamente etiquetado, sin presentarlo como evidencia del negocio. Probar luego con una muestra autorizada minimizada.
3. Mantener base de aplicación de Metabase separada lógicamente de los datos de negocio; usuarios distintos y restauraciones diferenciadas.
4. Conectar solo vistas analíticas permitidas con una cuenta SELECT. Desactivar uploads, acciones y enlaces públicos en este piloto.
5. Construir los indicadores seleccionados y reconciliarlos con consultas de control independientes.
6. Medir memoria total, CPU, disco, latencia, errores y efecto sobre otros servicios; no asumir capacidad sobrante del VPS.
7. Probar recuperación de la base de aplicación, permisos con cuentas de prueba y retirada del acceso.

## Etapa C: ClickHouse solo si existe una limitación demostrada
Replicar una muestra equivalente mediante lotes antes de añadir CDC; comparar mismos indicadores, filtros y tamaño de datos. Registrar versiones, hardware, estado de caché y número de consultas. Comparar mediana y p95 mediante una herramienta de medición; no usar una sola consulta caliente como benchmark.

Si se usa CDC, probar inserción, actualización, borrado, duplicados, desorden de eventos, reintentos y reconstrucción tras caída. Validar retraso, consistencia eventual y permisos de la cuenta de replicación separada de la de lectura.

## Umbrales propuestos para aprobación, no resultados
| Dimensión | Criterio propuesto |
|---|---|
| Utilidad | Responsable identifica una decisión concreta que el panel permite tomar |
| Integridad | Conteos y sumas reconciliados con la fuente; diferencias documentadas y aceptadas |
| Rendimiento | Acordar objetivo antes de medir; referencia inicial propuesta: p95 de panel ≤ 3 s con concurrencia representativa |
| Actualización | SLA elegido por caso: lote diario inicialmente, no tiempo real por defecto |
| Aislamiento | Ninguna cuenta de prueba puede leer datos de otro ámbito, ni mediante SQL, API, exportación o enlace |
| Recuperación | Restauración comprobada en entorno vacío y cotejo del contenido recuperado |
| Privacidad | No hay coordenadas precisas ni identificadores de personas innecesarios en BI |
| Coste | Coste total medido o presupuestado por debajo del límite aprobado por Luis |
| ClickHouse | Mejora útil frente al baseline con coste operativo aceptado; no basta con que sea más rápido |

## Registro obligatorio de resultados
Fecha; responsable; commit del dataset sintético; versiones; configuración; carga; latencias; consumo; errores; coste; pruebas negativas de permisos; recuperación; resultado aprobado/no aprobado. No hay casillas marcadas como superadas en este expediente.

## Reversión
Cerrar conexiones y retirar credenciales del piloto. Conservar informe y resultados minimizados. No borrar bases ni volúmenes sin autorización. Si ClickHouse se retira, la aplicación sigue funcionando con su fuente primaria: por eso el piloto no debe hacer escrituras dobles desde el producto.
