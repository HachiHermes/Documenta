# TEC-2026-001 · Registro de fuentes y evidencia breve

Fecha de consulta: **2026-11-07**. Documentación oficial y referencias privadas de contexto. Los números son los del registro único del expediente. El informe incorpora su propia lista de las fuentes que cita.

## Método y alcance

Se contrastaron páginas oficiales de producto, documentación técnica, licencias y precios. Dos líneas de investigación especializadas se consolidaron con un registro único de fuentes. Las afirmaciones críticas de permisos, operación y costes se volvieron a consultar en la consolidación. Los precios de Metabase y el dimensionamiento se contrastaron además en navegador por omisiones de las extracciones estáticas.

No se han convertido páginas comerciales en benchmarks. Se guardan solo extractos breves, URLs y análisis propio en el repositorio; no se copian documentos completos ni datos personales de los repositorios consultados. Las cachés locales de extracción no forman parte de la entrega.

Las fuentes `latest`, los precios y las ramas `main` son móviles; se recoge fecha de consulta, no se atribuye una versión fija a todo el conjunto. El navegador de Metabase mostraba documentación v0.65 al consultar la página de conexiones; esto no acredita una versión instalada en nuestro sistema.

Algunas rutas antiguas de instalación/permisos devolvieron error; se sustituyeron por las rutas oficiales incluidas abajo. Los errores no demuestran que una función no exista.

## Extractos breves de control

### RAM recomendada

Fuente: https://clickhouse.com/docs/guides/oss/best-practices/tips

> The recommended amount of RAM is 32 GB or more. If your system has less than 16 GB of RAM, you may experience various memory exceptions because default settings do not match this amount of memory. You can use ClickHouse in a system with a small amount of RAM (as low as 2 GB), but these setups require additional tuning and can only ingest at a low rate. When using ClickHouse with less than 16GB of RAM, we recommend the following:

### Edición para permisos por filas/columnas

Fuente: https://www.metabase.com/docs/latest/permissions/row-and-column-security

> Row and column security is only available on [Pro](https://www.metabase.com/product/pro) and [Enterprise](https://www.metabase.com/product/enterprise) plans (both self-hosted and on Metabase Cloud).

### H2 y producción

Fuente: https://www.metabase.com/learn/metabase-basics/administration/administration-and-operation/metabase-in-production

> That embedded database is intended for local use only.

### Memoria de vectores

Fuente: https://clickhouse.com/docs/reference/engines/table-engines/mergetree-family/annindexes

> The vector similarity index must be fully loaded from disk into main memory to perform searches.

### Tarifa de carga CDC

Fuente: https://clickhouse.com/docs/products/cloud/reference/billing/clickpipes/clickpipes-for-cdc.md

> Initial load / resync | $0.10 / GB

### Licencia Metabase

Fuente: https://www.metabase.com/license

> The Metabase Open Source Edition is offered under the AGPL.

### Borrado físico diferido

Fuente: https://clickhouse.com/docs/concepts/features/operations/delete/lightweight-delete

> In both modes, deleted rows remain in storage until cleanup. Background merges normally remove them from the affected data parts.

### OSS y View data

Fuente: https://www.metabase.com/docs/latest/permissions/data

> In the free, open-source version of Metabase, the **View data** setting defaults to “Can view”. Since the setting’s options aren’t available in the OSS version, Metabase will only display this **View data** setting in the Pro/Enterprise version.

## Relación de fuentes consultadas

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
