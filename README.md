# DOCUMENTA · Biblioteca tecnológica

Infraestructura documental para descubrir y evaluar herramientas antes de incorporarlas a los proyectos. **No es una aplicación independiente ni una base de datos de negocio.**

## Empezar aquí

- [Catálogo documental](docs/00-biblioteca/catalogo.md): inventario, clasificación y estados.
- [Investigación: AI Factory con agentes](docs/10-investigaciones/ai-factory-agentes/01-investigacion.md): análisis crítico del vídeo, evaluación del resumen, contraste de afirmaciones y piloto recomendado.
- [Fuentes del análisis AI Factory](docs/10-investigaciones/ai-factory-agentes/02-fuentes.md): evidencia, limitaciones y matriz de afirmaciones.
- [Investigación: ClickHouse y Metabase](docs/10-investigaciones/clickhouse-metabase/01-investigacion.md): capacidades, viabilidad, costes, privacidad y arquitectura propuesta.
- [Resumen visual](docs/10-investigaciones/clickhouse-metabase/04-resumen-visual.html): descargar y abrir en un navegador; GitHub muestra el código, no lo ejecuta.
- [Piloto y criterios de aceptación](docs/10-investigaciones/clickhouse-metabase/02-piloto-y-aceptacion.md): propuesta, no prueba ejecutada.
- [Fuentes y evidencia breve](docs/10-investigaciones/clickhouse-metabase/03-fuentes.md).
- [ADR-001 · Capa analítica propuesta](docs/20-decisiones/ADR-001-capa-analitica-propuesta.md): pendiente de aprobación.
- [Referencias de los proyectos](docs/30-proyectos/referencias.md).

## Flujo de incorporación conservado

**Descubrir** enlaces e ideas → **Evaluar** seguridad, privacidad y dependencia → **Probar** en piloto aislado → **Medir** coste, calidad y utilidad → **Integrar** con aprobación.

El flujo evita instalar por entusiasmo: cada incorporación debe superar seguridad, privacidad, coste y utilidad. Una investigación no autoriza instalar ni contratar nada.

## Organización de la biblioteca

| Sección | Contenido |
|---|---|
| `docs/00-biblioteca/` | Catálogo, normas y plantilla |
| `docs/10-investigaciones/` | Expedientes tecnológicos y fuentes |
| `docs/20-decisiones/` | Propuestas, decisiones y condiciones de revisión |
| `docs/30-proyectos/` | Enlaces al contexto canónico, sin duplicación completa |
| `ProyectosGitHub/` y archivos históricos de raíz | Fondo anterior conservado; no auditado por este trabajo |

[Normas de organización y conservación](docs/00-biblioteca/organizacion.md) · [Plantilla](docs/00-biblioteca/plantilla-investigacion.md) · [Historial](CHANGELOG.md).

**Privacidad:** no incorporar secretos, datos personales, volcados ni respaldos. Mantener la visibilidad privada. Los proyectos conservan su código y documentación canónica en sus propios repositorios.
