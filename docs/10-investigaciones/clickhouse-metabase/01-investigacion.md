# TEC-2026-001 · ClickHouse y Metabase: viabilidad para nuestras arquitecturas

**Fecha:** 2026-11-07 · **Versión:** 1.0 · **Estado:** investigado; piloto propuesto, no ejecutado.
**Ámbito:** Arte Stampa / ArteEstampa / EventosDeArte, MapStories y lacajaambar.
**Etiquetas:** datos, OLAP, BI, ingesta, privacidad, arquitectura, PostgreSQL.
**Aprobación de adopción:** pendiente de Luis y de revisión profesional cuando corresponda.

## 1. Dictamen ejecutivo

Punto de partida: web oficial de ClickHouse y su documentación técnica, contrastadas con el índice de configuración de Metabase solicitado.[1][2][4]

**Sí merece la pena incorporarlas al catálogo de herramientas; no desplegarlas automáticamente juntas ni utilizarlas indistintamente como “bases de datos”.** La recomendación es empezar por los casos de uso, después definir la recogida de información y solo entonces seleccionar infraestructura.

- **ClickHouse** es una base de datos orientada a analítica: útil para agregar muchos eventos e históricos. Su documentación distingue ese uso OLAP del transaccional y no declara soporte transaccional completo equivalente al de una base OLTP.[13]
- **Metabase** es una capa de consulta y visualización sobre fuentes de datos. Su documentación incluye conexiones tanto a PostgreSQL como a ClickHouse; los sincronizados de metadatos no son un crawler de Internet.[24][29]
- **Recomendación:** evaluar primero PostgreSQL como candidato a base operativa y Metabase para informes internos. Añadir ClickHouse solo si hay un problema analítico medido que compense duplicación, ingesta, coste y mantenimiento. Es una propuesta, no una arquitectura ya instalada o aprobada.
- **Documenta** debe guardar esta investigación y sus decisiones, no los datos de visitantes, compradores, audio ni respaldos de las aplicaciones.

| Necesidad | Encaje propuesto | Límite que no debemos confundir |
|---|---|---|
| Guardar y editar catálogo, permisos y estados de negocio | Base transaccional; PostgreSQL como candidato | ClickHouse no es nuestra elección automática para todas las escrituras |
| Analizar reproducciones, cobertura e históricos | PostgreSQL al principio; ClickHouse si las mediciones lo justifican | Capacidad analítica no equivale a necesidad actual |
| Construir paneles de consulta | Metabase | No sustituye al backend del producto |
| Recoger datos desde APIs, formularios o fuentes públicas | Conectores y procesos explícitos de ingesta | Ninguna de las dos resuelve por sí sola permisos editoriales y procedencia |
| Conservar documentos y medios | Repositorio documental y almacenamiento de objetos, según tipo | No cargar archivos completos en ClickHouse por defecto |

Estas asignaciones son recomendaciones de diseño, no afirmaciones de servicios existentes.

## 2. Contexto real revisado: qué sabemos y qué no

Se consultaron las referencias de los repositorios, sin modificarlos ni copiar su contenido completo:

- **ArteEstampa / EventosDeArte:** su README describe cámara o QR → identificación de obra → relato autorizado del artista → audioguía con transcripción, además de catálogo, accesibilidad, permisos y estado comercial verificable. Se presenta como diseño funcional/documentación, no como aplicación propia implementada.[10]
- **MapStories:** su README describe una demo Expo/React Native con mapas, GPS voluntario en primer plano, audio, transcripción y progreso local. Distingue la demo de la propuesta documental PWA y no confirma una integración implementada con LIDERA360.[11]
- **lacajaambar:** el inventario de herramientas exige descubrir, evaluar, probar, medir e integrar con aprobación; mantiene restricciones de privacidad y referencia sin duplicación completa.[12]

No están confirmados volumen de datos, crecimiento diario, concurrencia, presupuesto, SLA de frescura, clientes externos del BI ni requisitos de alta disponibilidad. Tampoco se ha inspeccionado aquí el código completo ni comprobado capacidad libre del servidor. Por tanto, no se afirma que exista una base PostgreSQL compartida o capacidad sobrante para desplegar nada.

**Convención de nombres:** conservamos “Arte Stampa” como denominación usada en la petición y enlazamos al identificador técnico existente `ArteEstampa`. No se ha renombrado ningún repositorio.

## 3. ClickHouse: capacidades, límites y utilidad

### 3.1 Papel analítico y transacciones

El almacenamiento columnar y el enfoque OLAP favorecen consultas agregadas; la FAQ indica que no dispone de soporte transaccional completo. Existe además documentación de transacciones experimentales: no debe leerse como garantía general de equivalencia OLTP.[13][7]

**Propuesta:** utilizarlo, si hace falta, para eventos de uso, series temporales, históricos de estados y agregaciones. Mantener edición editorial, autorizaciones y operaciones que requieran consistencia transaccional en la base operativa elegida. No justificarlo por el prestigio del producto ni por las cifras promocionales de rendimiento.

### 3.2 Ingesta desde PostgreSQL y consistencia eventual

ClickPipes para PostgreSQL replica cambios a ClickHouse Cloud. Las actualizaciones se convierten en nuevas versiones y los borrados incorporan marcadores como `_peerdb_is_deleted`; la deduplicación es asíncrona y pueden verse duplicados transitorios. Las tablas requieren clave primaria o configuración adecuada de `REPLICA IDENTITY`.[15]

Esto exige consultas que seleccionen correctamente la versión vigente y excluyan los borrados. Un conteo ingenuo de filas puede inflar indicadores. Tampoco se debe asumir que una modificación de clave primaria funciona sin limitaciones.[15]

**Propuesta:** si basta un informe diario, empezar por lotes controlados, con identificador de importación, registros rechazados y reconciliación. Activar CDC solo por una necesidad de frescura explícita. No hacer escrituras dobles independientes desde la aplicación hacia PostgreSQL y ClickHouse: complican recuperación e integridad.

### 3.3 Actualizar y borrar: sí se puede, pero hay consecuencias

La documentación actual incluye `UPDATE` ligero mediante patch parts, para la familia MergeTree y con condiciones de configuración. Lo orienta a cambios pequeños —aproximadamente hasta el 10 % de la tabla— y no permite modificar columnas de las claves primaria o de partición. Actualizaciones frecuentes pueden añadir coste y partes.[25]

El borrado ligero no garantiza retirada física inmediata: puede ocultar las filas hasta que los merges materialicen el borrado. Deben distinguirse desaparición de resultados y eliminación de datos del almacenamiento.[16]

**Implicación de privacidad:** una política de supresión debe cubrir origen, réplica analítica, tablas derivadas, exports, cachés y backups. Probar y documentar los plazos; no considerar que un dashboard vacío demuestra una eliminación completa. Seudonimización no significa anonimización automática.

### 3.4 Geografía y vectores

ClickHouse ofrece funciones geográficas, pero `pointInPolygon` no da un resultado único garantizado en el borde de un polígono. No es una prueba de que resuelva todas las reglas geográficas de un producto.[32]

Para MapStories proponemos considerar PostgreSQL/PostGIS en el núcleo geográfico y reservar ClickHouse para agregaciones espaciales. No sustituye al mapa móvil, enrutamiento, GPS, geofencing o modo offline. La documentación oficial de PostGIS queda como alternativa para un estudio específico, no como evaluación exhaustiva realizada aquí.[9]

También hay búsqueda vectorial exacta y ANN con HNSW. El índice vectorial debe cargarse completo en memoria para buscar, y su construcción también consume memoria.[26]

**Propuesta para lacajaambar:** no añadir ClickHouse solo por una futura búsqueda semántica. Definir primero corpus, permisos, filtros, frecuencia de actualización y necesidad de recuperación semántica. Un índice vectorial no es una base de conocimiento completa ni un sistema RAG validado.

### 3.5 Recursos, operación y licencia

La guía operativa recomienda **32 GB de RAM o más**. Describe configuraciones de hasta 2 GB con ajustes y poca ingesta, y avisa de excepciones de memoria con menos de 16 GB si se mantienen valores por defecto.[14]

No debe publicitarse “funciona con 2 GB” como dimensionamiento de producción recomendado. La capacidad de un piloto pequeño y la convivencia con otros servicios requieren medición. Autohospedar supone actualizaciones, copias, recuperación, protección de red, observabilidad y, si se requiere, alta disponibilidad.

El servidor utiliza licencia Apache 2.0; eso no elimina el coste operativo ni hace gratuito el servicio Cloud.[31]

## 4. Metabase: capacidades, ediciones y precauciones

### 4.1 Qué aporta y qué no

Permite conectar fuentes, explorar datos mediante herramientas visuales o SQL y construir informes. ClickHouse y PostgreSQL aparecen en la lista actual de drivers oficiales: no corresponde describir la conexión ClickHouse simplemente como un plugin comunitario sin soporte oficial. El soporte oficial a clientes indicado en esa página corresponde a Pro/Enterprise: driver oficial no equivale a soporte comercial incluido en OSS. Debe comprobarse compatibilidad de las versiones elegidas y el alcance del soporte contratado.[24][5]

Los procesos de sync, scan y fingerprinting descubren esquemas y valores de bases conectadas. No recogen por sí solos páginas culturales, audios, ventas ni consentimientos; además generan carga que hay que programar y medir.[29]

La configuración tiene opciones por interfaz, variables y otras funciones según edición. El fichero de configuración documentado para autohospedado corresponde a Pro/Enterprise; no diseñar una automatización OSS suponiendo paridad de edición.[4]

### 4.2 Dos bases distintas: negocio y aplicación de Metabase

La base interna de Metabase guarda preguntas, dashboards, cuentas y configuración; no debe confundirse con las fuentes que se analizan. La guía de producción recomienda una base PostgreSQL dedicada y advierte que H2 **no es para producción**.[19][18]

**Propuesta:** bases y usuarios separados para negocio, app DB de Metabase e ingesta. Pueden estudiarse distintos alojamientos, pero “base dedicada” no implica por sí sola que cada una necesite un servidor físico. Guardar datos persistentes fuera de la capa efímera del contenedor y probar restauraciones de cada base.

La guía oficial orienta a **1 núcleo y 1 GB de RAM** como base del servidor Metabase, añadiendo **1 CPU y 2 GB de RAM por cada 20 personas concurrentes**. Para el servidor de su app DB indica como punto de partida **1 CPU y 2 GB de RAM**. Son recomendaciones orientativas y separadas; no incluyen las bases de negocio o ClickHouse que ejecutan las consultas.[19]

El dimensionamiento debe contemplar todos esos componentes. No se ofrece un presupuesto de RAM de este sistema sin medir carga y revisar la versión elegida. Tampoco se extrapola el benchmark del proveedor a nuestras aplicaciones.

### 4.3 Permisos: la limitación decisiva para varios proyectos o clientes

La documentación actual restringe la seguridad por filas y columnas a Pro/Enterprise. También indica que en OSS el ajuste `View data` queda en `Can view` y no expone los controles avanzados de esa opción.[20][28]

**No basta con poner cada proyecto en una colección y prometer aislamiento multiempresa.** Los permisos de colecciones organizan el acceso a objetos; no sustituyen una política de datos verificada. Proponemos empezar con un equipo interno autorizado y fuentes analíticas minimizadas; si hay grupos incompatibles, evaluar separación de instancias/fuentes o edición comercial y comprobar la matriz efectiva de permisos.

PostgreSQL dispone de RLS, pero el propietario suele eludirla y los superusuarios o roles `BYPASSRLS` la evitan. Además, si Metabase conecta con una sola identidad compartida, no aparece mágicamente una identidad SQL distinta por visitante.[8]

Propuesta: cuentas de BI sin propiedad, sin superusuario y con lectura de vistas permitidas; probar SQL nativo, exportaciones, API, enlaces públicos y acceso indirecto. Los filtros de un dashboard no son controles de seguridad.

### 4.4 Embedding y escritura: no asumir que todo es solo lectura

El guest embedding firmado usa recursos y parámetros autorizados; no equivale por sí solo a una sesión individual con SSO. Se deben generar las firmas en servidor y evaluar sus límites antes de incrustar paneles en una aplicación.[33]

Metabase admite cargas CSV para análisis puntual, que requieren escritura en una base compatible y crean tablas/modelos. La propia guía recomienda cargar directamente a una base cuando el volumen es grande o las actualizaciones son frecuentes.[21]

Existen data actions para escribir datos; por tanto, sería incorrecto afirmar que Metabase nunca inserta, actualiza o borra. La documentación actual limita estas acciones a PostgreSQL y MySQL, y su creación a administradores. Advierte además que los formularios públicos pueden ejecutar acciones sin iniciar sesión y no comprueban la seguridad por filas y columnas. Las compatibilidades y permisos deben revisarse por función y versión: no extender estas capacidades a ClickHouse.[27]

**Propuesta inicial:** desactivar uploads, acciones y publicaciones abiertas. Si se necesita escritura, crear una conexión separada con permisos acotados y validar lógica de negocio. Metabase no sustituye a la aplicación editorial ni a sus autorizaciones.

### 4.5 Licencia y cifrado de credenciales

La edición OSS se ofrece bajo AGPL. Hay condiciones comerciales y de embedding, incluidas opciones para retirar la marca; uso interno, modificación, redistribución y embedding requieren revisar su caso concreto. No se ofrece aquí una conclusión jurídica.[22]

`MB_ENCRYPTION_SECRET_KEY` protege detalles de conexión almacenados; no cifra por ello toda la información del negocio ni reemplaza la protección de la app DB. Debe custodiarse y respaldarse por un canal seguro; la migración de una instalación existente requiere seguir su procedimiento específico.[30]

## 5. Viabilidad por proyecto: empezar por la decisión útil

Las siguientes son **propuestas de casos de uso**, no funcionalidades o métricas ya implementadas.

| Proyecto | Caso inicial propuesto | Papel de Metabase | Papel eventual de ClickHouse | Dictamen |
|---|---|---|---|---|
| Arte Stampa | Cobertura de obras con relato autorizado y transcripción; uso agregado por evento | Panel interno editorial y de actividad | Histórico de eventos de interacción si su volumen lo justifica | Metabase: candidato a piloto; ClickHouse: posponer |
| MapStories | Cobertura de puntos/rutas, errores de ingesta y reproducciones consentidas | Panel editorial y de uso agregado | Eventos de escucha e históricos geográficos minimizados | Mayor encaje potencial de ClickHouse, sin necesidad demostrada todavía |
| lacajaambar | Inventario de fuentes, permisos, revisiones y calidad de metadatos | Útil solo si existe una tabla que permita decisiones reales | Poco justificado sin carga analítica concreta | Biblioteca/documentos primero; BI condicional |

En Arte Stampa no se presupone una tienda con inventario y pagos. En MapStories no se transforman reproducciones en “visitas físicas verificadas” ni se añaden trayectorias precisas por defecto. En lacajaambar no se afirma una aplicación operativa a partir de carpetas de persistencia.[10][11][12]

## 6. Recoger información: la pieza que falta entre las fuentes y los paneles

### 6.1 Flujo funcional propuesto

**Fuentes autorizadas → recepción → validación y cuarentena → normalización y procedencia → revisión humana cuando corresponda → publicación en la base operativa → vistas/tabla analítica → Metabase.**

ClickHouse, si se aprueba, entraría después de la publicación operativa como destino analítico. Los archivos originales y audios permanecerían en un almacén adecuado, enlazados mediante referencias. Esta es arquitectura propuesta.

### 6.2 Fuentes posibles, pendientes de autorización

- Arte Stampa: fichas aportadas por artistas/organizadores, catálogo aprobado, relatos y transcripciones autorizadas, eventos de interacción minimizados.
- MapStories: fuentes institucionales y editoriales autorizadas, puntos culturales y metadatos de audio, telemetría opcional con propósito explícito.
- lacajaambar: documentos y enlaces seleccionados, inventario de herramientas, registro de revisiones y fuentes estructuradas.

Un API público no concede automáticamente permiso para reutilizar cada audio o texto. No eludir autenticación ni restricciones de acceso. Confirmar licencias, atribución, condiciones de uso, frecuencia y base jurídica antes de automatizar.

### 6.3 Contrato de procedencia propuesto

Cada registro debería incluir `source_id`, `source_url`, `retrieved_at`, `source_updated_at` si se conoce, versión del conector, licencia/permiso, atribución, estado de revisión y regla de retención. Añadir identificador estable, versión de esquema e identificador de lote para deduplicación. Si hay un hash, calcularlo sobre el contenido real: no inventarlo ni usarlo como prueba de veracidad editorial.

Distinguir metadatos públicos, contenido con derechos, datos personales y secretos. No almacenar tokens de acceso en campos de procedencia. No copiar al BI datos que no necesita.

### 6.4 Calidad y errores

Propuesta: validar campos obligatorios, fechas, coordenadas, URLs y estado de derechos; registrar rechazos; reintentar con límites; no sobrescribir contenido aprobado sin revisión; conservar trazabilidad de correcciones. La falta de fecha/licencia debe ser un estado explícito, no completarse por inferencia de un modelo.

## 7. Arquitecturas candidatas, de menor a mayor complejidad

### A. Empezar sin almacén analítico adicional — preferida para el piloto

```text
Aplicación / conectores autorizados
                │
                ▼
      PostgreSQL de negocio (candidato)
                │ vistas analíticas de lectura
                ▼
             Metabase ─── PostgreSQL app DB independiente

Documentos / audio → almacén de objetos o repositorio adecuado
Documenta → investigaciones, fuentes, enlaces y decisiones
```

Ventajas propuestas: menos componentes, menos duplicación y reconciliación más simple. Riesgo: consultas de BI pueden competir con el producto; limitar complejidad, horarios, concurrencia y, si se demuestra necesario, estudiar réplica o preagregación.

### B. Añadir ClickHouse cuando haya evidencia

```text
PostgreSQL de negocio → lotes o CDC aprobado → ClickHouse → Metabase
        │                                                   │
        └─ sigue siendo fuente operativa          app DB separada
```

Ventajas esperables, pendientes de prueba: descargar agregaciones y crecer en histórico. Costes: consistencia eventual, monitorización de ingesta, reconciliación, supresión coordinada y nuevas copias. El fallo del panel o de la réplica no debería bloquear la aplicación.

### C. Servicios gestionados

Reducen tareas propias de operación, pero añaden facturación, dependencia contractual, región, transferencia y requisitos de acceso. No se recomienda contratar por defecto ni usar créditos de prueba como presupuesto permanente.

La web incluye también ClickHouse Managed Postgres, presentado en beta en la página de precios. No confundir ese producto distinto con el motor analítico ClickHouse ni convertir su promoción en recomendación de proveedor para la base primaria.[3]

## 8. Coste y sostenibilidad

### 8.1 Metabase

Tarifas mensuales observadas en la página pública, comprobadas también en navegador porque la extracción estática omitía parte de la tabla; USD, sin impuestos, a fecha de consulta:[6]

| Modalidad | Precio publicado | Usuarios |
|---|---|---|
| OSS autogestionado | Licencia sin cuota; infraestructura aparte | Sin cuota comercial por usuario de esta modalidad |
| Starter Cloud | 100 USD/mes | Primeros 5 incluidos; 6 USD/mes por adicional |
| Pro | 575 USD/mes | Primeros 10 incluidos; 12 USD/mes por adicional |
| Enterprise | Precio personalizado, desde 20.000 USD/año | Confirmar oferta |

Hay extras opcionales por IA, transforms y almacenamiento. Autohospedar Pro no elimina la licencia ni paga nuestra infraestructura. No extrapolar el coste de usuarios internos a un embedding masivo sin consultar condiciones.[6]

### 8.2 ClickHouse Cloud

La página observada, con AWS `us-east-1` seleccionado, muestra estas tarifas. No son una oferta para Europa ni una factura mínima garantizada:[3]

| Concepto | Tarifa observada |
|---|---|
| Basic | 0,2181 USD por unidad-hora |
| Scale | 0,2985 USD por unidad-hora |
| Enterprise | 0,3903 USD por unidad-hora |
| Almacenamiento | 25,30 USD por TB-mes |
| Unidad de cómputo | 8 GiB RAM y 2 vCPU |

La prueba ofrece 300 USD durante 30 días, no gratuidad permanente. El almacenamiento y backups siguen importando aunque el cómputo se suspenda por inactividad; ingesta y transferencia pueden facturarse aparte.[3]

**Ejemplo aritmético ilustrativo**, calculado con herramienta: una unidad Basic, 100 GB comprimidos tratados como 0,1 TB, 30 días. No confirma el tamaño provisionable para una región ni incluye backups adicionales, CDC, egress o impuestos:

| Actividad supuesta | Cómputo | Almacenamiento | Subtotal ilustrativo |
|---|---:|---:|---:|
| 2 horas/día | 13,09 USD | 2,53 USD | 15,62 USD |
| 24 horas/día | 157,03 USD | 2,53 USD | 159,56 USD |

Fórmula: tarifa por unidad-hora × unidades × horas activas; más TB comprimidos × tarifa de almacenamiento. Los escaneos y cargas pueden mantener activo el servicio: las 2 horas/día son una hipótesis, no un ahorro garantizado.

**CDC añade su propia factura.** La documentación publica 0,10 USD/GB para carga inicial/resync y 0,20 USD/GB para cambios continuos sin comprimir; cómputo CDC Basic 0,10 USD/h y Scale/Enterprise 0,20 USD/h. Pausar un pipe no elimina por sí solo el coste del cómputo provisionado.[17]

Por tanto, evaluar coste total: licencia BI + servidores/app DB + almacenamiento + ingesta/CDC + transferencia + backups + mantenimiento + recuperación + revisión de seguridad. No se ha elaborado una factura total del sistema porque faltan carga, región y presupuesto.

## 9. Seguridad, privacidad y continuidad: condiciones de adopción

La documentación de Metabase recomienda usuario dedicado y privilegios mínimos de lectura. Algunas funciones opcionales requieren escritura: no ampliar la cuenta de BI silenciosamente para habilitarlas.[23]

Controles propuestos, pendientes de implementación y prueba:

1. Separar identidades de aplicación, ingesta, BI y administración; no usar superusuario en paneles.
2. Acceso privado o restringido; TLS verificado; ninguna base expuesta por comodidad.
3. No activar enlaces públicos ni compartir exports sin revisar datos y destinatarios.
4. Revisar permisos efectivos de todos los grupos; probar escenarios negativos y accesos cruzados.
5. Minimizar datos: zonas agregadas antes que coordenadas precisas; no identificar compradores privados.
6. Definir retención, supresión y tratamiento de backups antes de recoger telemetría.
7. Fijar versiones y probar actualizaciones/restauración; custodiar por separado secretos y claves de cifrado.
8. Limitar consultas, tiempo de ejecución y concurrencia; monitorizar efecto de sync/scan, lag y errores.
9. En Cloud: validar región, contrato de tratamiento, subencargados, exportación y salida. Una afirmación comercial de cumplimiento no acredita el cumplimiento del proyecto.
10. Revisar específicamente cualquier envío de datos a funciones de IA/MCP y sus proveedores; no activarlas por defecto en el piloto.

## 10. Plan de decisión y límites de esta investigación

El [piloto detallado](02-piloto-y-aceptacion.md) propone necesidades, baseline PostgreSQL + Metabase y una comparación con ClickHouse solo si existe un cuello de botella. Incluye pruebas de integridad, permisos, latencia, recursos, recuperación, coste y reversión.

**Comprobado:** documentación oficial, tarifas visibles y referencias puntuales de contexto de los repositorios. Las URLs `latest` y las páginas comerciales son móviles: revisar antes de comprar o desplegar. Algunas rutas antiguas fallaron; se localizaron alternativas y no se usaron los errores como evidencia de ausencia de capacidades.

**No comprobado:** conexión real PostgreSQL/ClickHouse/Metabase, benchmark, compatibilidad de versiones instaladas, coste del proyecto, seguridad efectiva, capacidad del VPS ni restauraciones. No se ha instalado, migrado ni contratado nada. La investigación no debe etiquetarse como piloto medido.

**Decisión solicitada posteriormente:** confirmar un indicador útil por proyecto, quién lo consulta y su frecuencia; aprobar o no un piloto acotado. El [ADR-001](../../20-decisiones/ADR-001-capa-analitica-propuesta.md) conserva la propuesta y sus alternativas sin atribuir aprobación inexistente.

## Sources

[1] https://clickhouse.com/es — SGBD OLAP rápido y de código abierto | ClickHouse
[2] https://clickhouse.com/docs — Home - ClickHouse Documentation
[3] https://clickhouse.com/pricing — Pricing | ClickHouse
[4] https://www.metabase.com/docs/latest/configuring-metabase — Configuration overview | Metabase Documentation
[5] https://www.metabase.com/docs/latest/databases/connections/clickhouse — ClickHouse | Metabase Documentation
[6] https://www.metabase.com/pricing — Metabase Pricing
[7] https://clickhouse.com/docs/guides/developer/transactional — Case 1: INSERT into one partition, of one table, of the MergeTree* family​
[8] https://www.postgresql.org/docs/current/ddl-rowsecurity.html — PostgreSQL: Documentation: 18: 5.9. Row Security Policies
[9] https://postgis.net/documentation — Documentation | PostGIS
[10] https://github.com/HachiHermes/ArteEstampa/blob/main/README.md
[11] https://github.com/HachiHermes/MapStories/blob/main/README.md
[12] https://github.com/HachiHermes/lacajaambar/blob/main/herramientas/inventario.md
[13] https://clickhouse.com/docs/resources/support-center/knowledge-base/general-faqs/olap — What is OLAP?
[14] https://clickhouse.com/docs/guides/oss/best-practices/tips — OSS usage recommendations
[15] https://clickhouse.com/docs/integrations/clickpipes/postgres/faq — ClickPipes for Postgres FAQ
[16] https://clickhouse.com/docs/concepts/features/operations/delete/lightweight-delete — Lightweight delete
[17] https://clickhouse.com/docs/products/cloud/reference/billing/clickpipes/clickpipes-for-cdc.md — ClickPipes for CDC
[18] https://www.metabase.com/docs/latest/installation-and-operation/configuring-application-database — Configuring the Metabase application database | Metabase Documentation
[19] https://www.metabase.com/learn/metabase-basics/administration/administration-and-operation/metabase-in-production — How to run Metabase in production | Metabase Learn
[20] https://www.metabase.com/docs/latest/permissions/row-and-column-security — Row and column security | Metabase Documentation
[21] https://www.metabase.com/docs/latest/databases/uploads — Setting up data uploads | Metabase Documentation
[22] https://www.metabase.com/license — Metabase licenses and terms of service
[23] https://www.metabase.com/docs/latest/databases/users-roles-privileges — Database users, roles, and privileges | Metabase Documentation
[24] https://www.metabase.com/docs/latest/databases/connecting — Adding and managing databases | Metabase Documentation
[25] https://clickhouse.com/docs/reference/statements/update — UPDATE - ClickHouse Documentation
[26] https://clickhouse.com/docs/reference/engines/table-engines/mergetree-family/annindexes — Exact and Approximate Vector Search - ClickHouse Documentation
[27] https://www.metabase.com/docs/latest/data-modeling/data-actions — Data actions | Metabase Documentation
[28] https://www.metabase.com/docs/latest/permissions/data — Data permissions | Metabase Documentation
[29] https://www.metabase.com/docs/latest/databases/sync-scan — Syncing and scanning databases | Metabase Documentation
[30] https://www.metabase.com/docs/latest/databases/encrypting-details-at-rest — Encrypting your database connection | Metabase Documentation
[31] https://github.com/ClickHouse/ClickHouse/blob/master/LICENSE — ClickHouse/LICENSE at master Â· ClickHouse/ClickHouse Â· GitHub
[32] https://clickhouse.com/docs/reference/functions/regular-functions/geo/coordinates — Functions for Working with Geographical Coordinates
[33] https://www.metabase.com/docs/latest/embedding/guest-embedding — Guest embeds | Metabase Documentation
