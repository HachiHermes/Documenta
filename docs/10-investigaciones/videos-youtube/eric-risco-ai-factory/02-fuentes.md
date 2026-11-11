# YT-2026-001-F / TEC-2026-002-F · Fuentes y trazabilidad

- **Tipo de entrada:** vídeo de YouTube analizado
- **ID de videoteca:** YT-2026-001
- **Vídeo:** *Programar con agentes ya no basta: monta tu Software Factory*
- **Autor / canal:** Eric Risco · Eric Risco | Inteligencia Artificial
- **URL de acceso:** <https://www.youtube.com/watch?v=p6nnqerea7w&t=51s>
- **Fecha de consulta:** 2026-11-11
- **Estado:** fuentes primarias consultadas; sin instalación ni auditoría del sistema del autor

## Criterio de evidencia

Se priorizaron fuentes originales: el vídeo y descripción del autor, su repositorio público, y las publicaciones de OpenAI y Stripe a las que remiten las afirmaciones cuantitativas. Los fragmentos se resumen de forma transformativa; no se reproduce la transcripción completa.

## [1] Vídeo principal

- **Título:** Programar con agentes ya no basta: monta tu Software Factory
- **Autor/canal:** Eric Risco | Inteligencia Artificial
- **URL:** <https://www.youtube.com/watch?v=p6nnqerea7w&t=51s>
- **Duración declarada:** 17:21
- **Fecha indicada por YouTube:** 2026-10-23
- **Tipo:** fuente primaria del planteamiento y de la demostración narrada

### Capítulos declarados

- 00:00 · Programar con agentes ya no basta
- 01:06 · Hay trabajo que ya no te necesita
- 01:38 · Qué es una AI Factory
- 04:00 · El brain
- 05:30 · Mi software factory
- 08:12 · Los cuatro revisores
- 10:14 · El aprobador
- 12:32 · En paralelo
- 13:51 · Content factory
- 15:49 · Por qué ahora y RSC Factory

### Evidencia utilizada

El vídeo define una fábrica como proceso repetible y auditable; separa pasos deterministas, agentes y revisión humana; describe memoria persistente, spec writer, builder con TDD, cuatro revisores, aprobador, clasificación de riesgo, controles mediante hooks, paralelismo y una fábrica de contenidos. También anuncia RSC Factory como trabajo futuro.

### Limitaciones

- La demostración y las métricas propias no se pudieron auditar.
- Los subtítulos automáticos pueden introducir errores en nombres o formulaciones.
- La explicación tiene componente promocional y llamada a la interacción.
- La fuente no publica en el vídeo tasas de fallo, coste, latencia ni comparación controlada.

## [2] Repositorio RSC Harness

- **Título:** `ericrisco/rsc-harness`
- **URL:** <https://github.com/ericrisco/rsc-harness>
- **Tipo:** fuente primaria de software y documentación pública

### Evidencia utilizada

El repositorio se presenta como un meta-arnés para agentes, con memoria/contexto, habilidades, herramientas, hooks y compatibilidad con múltiples asistentes. Sustenta la existencia del arnés que el autor menciona como antecedente de RSC Factory.

### Limitaciones

- La existencia de `rsc-harness` no demuestra que RSC Factory esté completo, publicado o sea adecuado para el entorno de Luis.
- No se instaló, ejecutó ni auditó el paquete.
- La versión, dependencias, licencia y superficie de seguridad deben revisarse de nuevo antes de cualquier piloto.

## [3] OpenAI · Harness engineering

- **Título:** Harness engineering: leveraging Codex in an agent-first world
- **Autor:** Ryan Lopopolo, OpenAI
- **Fecha publicada:** 2026-02-11
- **URL:** <https://openai.com/index/harness-engineering/>
- **Tipo:** fuente primaria corporativa

### Evidencia utilizada

OpenAI declara:

- un producto interno construido con cero líneas de código escritas manualmente;
- aproximadamente un millón de líneas en cinco meses;
- alrededor de 1.500 PR abiertas y fusionadas;
- un equipo inicial pequeño de tres ingenieros dirigiendo Codex;
- código, pruebas, CI, documentación, observabilidad y herramientas generados por agentes;
- estimación de una décima parte del tiempo que habría requerido el trabajo manual.

### Interpretación

La afirmación del vídeo sobre una solución de más de un millón de líneas sin código manual está respaldada en lo esencial. No significa ausencia de trabajo humano: las personas diseñaron el entorno, especificaron intención y construyeron bucles de feedback.

### Limitaciones

Es una publicación de OpenAI sobre su propio producto y herramienta. Las cifras no equivalen a auditoría independiente ni son directamente transferibles a otros equipos.

## [4] Stripe · Minions

- **Título:** Minions: Stripe’s one-shot, end-to-end coding agents
- **URL:** <https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents>
- **Tipo:** fuente primaria corporativa

### Evidencia utilizada

Stripe afirma que:

- sus agentes internos se llaman “minions”;
- producen de extremo a extremo tareas que terminan en PR;
- más de mil PR fusionadas cada semana son producidas completamente por minions;
- esas PR no contienen código escrito por personas;
- las PR siguen siendo revisadas por humanos;
- el sistema aprovecha la infraestructura madura de repositorios, CI y herramientas internas de Stripe.

### Interpretación

La frase del vídeo “más de 1.000 PR en una semana” está respaldada, aunque omite dos datos esenciales: Stripe habla de una cadencia semanal y conserva revisión humana.

### Limitaciones

El volumen de PR no demuestra por sí solo valor, seguridad o calidad. Stripe opera a una escala y con una base de ingeniería que no puede asumirse en otros entornos.

## Matriz de afirmaciones

| Afirmación | Fuente | Evaluación |
|---|---|---|
| Una AI Factory debe ser repetible y auditable | [1] | Tesis explícita del autor |
| Combina código, agentes y humanos | [1] | Descripción explícita |
| Usa memoria persistente antes de especificar y construir | [1] | Descripción explícita |
| Incluye cuatro revisores especializados | [1] | Descripción explícita |
| El riesgo decide la revisión humana | [1] | Descripción explícita; requiere política externa |
| Los hooks/código deben bloquear acciones | [1] | Matiz de seguridad explícito |
| OpenAI generó ~1 M de líneas sin código manual | [3] | Sustancialmente confirmado |
| Stripe supera 1.000 PR producidas por agentes por semana | [4] | Confirmado; con revisión humana |
| RSC Harness existe públicamente | [2] | Confirmado |
| RSC Factory está publicado y listo para adoptar | [1][2] | No confirmado; tratar como anuncio/trabajo en curso |
| La fábrica del vídeo mejora calidad o coste | [1] | No demostrado con métricas |

## Separación entre evidencia e inferencia

### Evidencia

- Componentes y secuencia narrados por el autor.
- Capítulos y ejemplos del vídeo.
- Existencia pública de RSC Harness.
- Declaraciones corporativas de OpenAI y Stripe.

### Inferencia razonada

- El cuello de botella se desplaza hacia revisión, gobierno y excepciones.
- Un clasificador LLM no debe ser la barrera de seguridad.
- La memoria necesita versionado, permisos y caducidad.
- Un piloto documental ofrece menor riesgo que producción.

### No demostrado

- Calidad objetiva de la fábrica del autor.
- Seguridad del sistema.
- Rentabilidad para Luis.
- Disponibilidad completa de RSC Factory.
- Adecuación de modelos concretos para cada rol.

## Revisión futura

Revisar este expediente si ocurre cualquiera de estos cambios:

1. publicación pública de RSC Factory;
2. documentación de arquitectura, seguridad o licencia;
3. métricas reproducibles de coste, calidad y fallos;
4. cambios relevantes en `rsc-harness`;
5. decisión de ejecutar un piloto en Hermes o Documenta.
