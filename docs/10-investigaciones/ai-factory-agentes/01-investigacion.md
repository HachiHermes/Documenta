# TEC-2026-002 · AI Factory con agentes: análisis crítico del vídeo de Eric Risco

- **Fecha y versión:** 2026-11-11 · v1.0
- **Estado:** investigado; no autoriza instalación ni automatización de producción
- **Responsable de aprobación:** pendiente de designación
- **Ámbito:** Hermes, Documenta y posibles flujos de los proyectos; no modifica ningún producto
- **Etiquetas:** agentes, AI Factory, software factory, trazabilidad, revisión, memoria, CI/CD, supervisión humana
- **Pregunta de decisión:** ¿qué ideas del vídeo son sólidas, qué omite el resumen inicial y qué haría falta para convertir el uso actual de agentes en procesos repetibles, auditables y seguros?

## 1. Resumen ejecutivo

El vídeo defiende una transición desde el uso conversacional de agentes —una persona que abre varias sesiones y dirige cada paso— hacia una **fábrica de IA**: un proceso con entradas y salidas definidas, combinación de código determinista, agentes y aprobaciones humanas, memoria persistente, trazas por etapa y ejecución paralela. Su ejemplo principal es una fábrica de software que recibe errores de Sentry, incidencias de GitHub o tareas, redacta una especificación, construye mediante TDD, lanza cuatro revisiones especializadas, itera correcciones y decide por riesgo si puede integrar o requiere aprobación humana [1].

El resumen proporcionado por Luis es **correcto en dirección, pero demasiado breve para representar el contenido del vídeo**. Conserva tres ideas reales —automatización, arquitectura con memoria y especialistas, y control humano según riesgo—, pero omite el mecanismo operativo, los criterios de una fábrica, el ciclo de corrección, la paralelización, el ejemplo de contenidos y varios riesgos. No es engañoso; sí es **insuficiente** para evaluar o implementar lo propuesto.

La contribución más valiosa del vídeo no es “usar muchos agentes”, sino convertir el trabajo en un **sistema verificable**. La tesis útil puede reformularse así:

> Un agente aislado acelera tareas; una fábrica solo existe cuando la organización define contratos de entrada/salida, estado persistente, controles deterministas, evidencia, recuperación y autoridad humana proporcional al riesgo.

La propuesta es plausible y coincide parcialmente con prácticas publicadas por OpenAI y Stripe. OpenAI documenta un producto interno con aproximadamente un millón de líneas generadas por Codex, cero líneas escritas manualmente, unas 1.500 PR y una estimación de desarrollo en una décima parte del tiempo manual [3]. Stripe afirma que sus “minions” producen más de mil PR fusionadas por semana, sin código escrito por personas, pero **con revisión humana** [4]. Por tanto, las referencias cuantitativas del vídeo tienen respaldo, aunque el contexto humano, técnico y organizativo es esencial.

**Recomendación:** adoptar los principios, no copiar la automatización completa. El entorno actual de Luis ya tiene memoria, herramientas, especialistas, Git y verificación, pero todavía no constituye una fábrica autónoma. Conviene ensayar primero un flujo de bajo riesgo —por ejemplo, “fuente → investigación Documenta → borrador de PR → validación → aprobación humana”— antes de permitir cambios automáticos en aplicaciones o producción.

## 2. Método, alcance y límites

### 2.1 Material analizado

- Vídeo público de Eric Risco, duración declarada 17:21, con capítulos y subtítulos accesibles [1].
- Descripción y capítulos del propio vídeo [1].
- Repositorio público `ericrisco/rsc-harness`, enlazado por el autor [2].
- Publicaciones originales de OpenAI y Stripe usadas para contrastar las dos cifras empresariales citadas [3][4].
- Resumen breve aportado por Luis, evaluado como texto independiente.

### 2.2 Qué se ha verificado

- La estructura argumental, los componentes y ejemplos descritos por el vídeo.
- La correspondencia entre el resumen breve y el contenido.
- La existencia pública de `rsc-harness` y su orientación como arnés para agentes [2].
- La base documental de las afirmaciones sobre OpenAI y Stripe [3][4].
- La correspondencia conceptual con capacidades ya observadas en el entorno Hermes de Luis.

### 2.3 Qué no se ha verificado

- No se ha inspeccionado el código privado de la fábrica del autor ni se ha reproducido su demostración.
- No se ha confirmado que “RSC Factory” esté publicado como producto completo; el vídeo lo presenta como trabajo en curso.
- No se ha medido calidad, coste, tasa de fallos, latencia ni seguridad de la fábrica del autor.
- No se ha instalado `rsc-harness` ni se ha autorizado su adopción.
- No se ha ejecutado un piloto de AI Factory en los proyectos de Luis.
- Los nombres y capacidades de modelos mencionados en el vídeo se recogen como explicación del autor, no como benchmark independiente.

## 3. Tesis y recorrido del vídeo

### 3.1 00:00–01:38 · El humano como cuello de botella

El punto de partida es que abrir varias ventanas de agentes no elimina la dependencia del operador. Aunque la generación de código sea rápida, cada conversación sigue esperando que una persona formule la siguiente tarea, transfiera contexto, revise el resultado y decida el paso posterior. El vídeo sostiene que determinados trabajos —errores de producción, pruebas y desarrollos acotados— ya pueden delegarse sin supervisión continua [1].

La observación es válida, pero requiere una precisión: el cuello de botella no desaparece, **cambia de sitio**. Pasa de escribir código a definir objetivos, diseñar controles, resolver excepciones y aprobar riesgos. Si una fábrica genera más cambios de los que una organización puede revisar, el cuello de botella reaparece en seguridad, producto, pruebas o despliegue.

### 3.2 01:38–04:00 · Definición de AI Factory

El autor usa una analogía industrial: entra una materia prima, atraviesa máquinas o puestos con funciones concretas y sale transformada. Para él, una AI Factory debe cumplir dos condiciones:

1. **Repetibilidad:** la tarea sigue un proceso reconocible.
2. **Auditabilidad:** cada etapa deja rastro y permite localizar un fallo.

Distingue tres clases de pasos:

- **Código determinista:** adecuado para operaciones repetibles y verificables.
- **Agentes:** adecuados cuando la entrada exige interpretar, razonar o elegir una estrategia.
- **Revisión humana:** necesaria cuando el riesgo o la ambigüedad superan el umbral admitido.

Esta separación es uno de los puntos más sólidos. Evita pedir razonamiento probabilístico para aquello que puede resolverse con validadores, scripts, políticas, pruebas o controles de acceso. Sin embargo, la frase del vídeo “el código funciona o no funciona” es demasiado binaria: un programa puede compilar y aun así ser inseguro, incorrecto en casos límite, lento, inaccesible o contrario al negocio.

### 3.3 04:00–05:30 · El “brain” o memoria persistente

El vídeo considera imprescindible una memoria continua con decisiones del proyecto. El ejemplo es una elección previa de PostgreSQL: si el agente no conoce esa decisión, puede proponer otra base o producir una solución incoherente. Antes de especificar o construir, los agentes consultan ese “brain” [1].

La idea es correcta, pero “memoria” no debe confundirse con una única bolsa de texto. Una implementación robusta separaría:

- hechos vigentes y decisiones de arquitectura;
- requisitos y contratos verificables;
- historial y evidencia de ejecuciones;
- preferencias del usuario;
- secretos, que **no deben entrar en memoria textual del modelo**;
- información caducada, sustituida o pendiente de validación.

También hacen falta procedencia, versión, fecha, propietario y reglas de invalidación. Una memoria persistente sin gobierno puede propagar un error antiguo con más eficacia que una sesión sin memoria.

### 3.4 05:30–08:12 · Entrada, especificación y construcción

La fábrica de software presentada acepta tres entradas principales:

- errores de producción registrados en Sentry;
- incidencias de GitHub;
- tareas solicitadas por una persona.

Todas convergen en un punto común. Un agente “spec writer” consulta la memoria y redacta una especificación. Después, un “builder” crea una rama y desarrolla la especificación. El autor afirma que el builder usa TDD: primero crea una prueba que falla y luego implementa hasta que pasa [1].

El diseño es razonable porque separa **qué hay que hacer** de **cómo implementarlo**. Aun así, faltan controles esenciales:

- normalizar y validar la entrada;
- detectar duplicados e incidentes relacionados;
- delimitar alcance, presupuesto y tiempo;
- rechazar tareas que no tengan criterios de aceptación;
- evitar que texto externo se convierta en instrucciones privilegiadas;
- aislar credenciales y herramientas;
- comprobar que una prueba nueva representa el requisito y no solo confirma la solución propuesta.

TDD ayuda a reducir regresiones, pero una prueba escrita por el mismo sistema puede compartir su error conceptual. Son necesarias pruebas preexistentes, invariantes independientes, análisis estático y, cuando proceda, pruebas de integración o aceptación humanas.

### 3.5 08:12–10:14 · Cuatro revisores especializados

El builder lanza cuatro revisiones en paralelo con contexto limpio y modelos/tareas diferenciados:

1. **Seguridad:** busca vulnerabilidades y riesgos.
2. **UX:** evalúa flujo, pasos y adecuación al usuario objetivo.
3. **UI:** revisa la interfaz mediante capacidades visuales o de uso del ordenador.
4. **Código/arquitectura:** contrasta convenciones y decisiones del proyecto.

La separación reduce la tendencia de un único agente a validar su propio trabajo. Usar familias de modelos distintas puede añadir diversidad. No obstante, “otro modelo” no equivale automáticamente a independencia: pueden compartir datos de entrenamiento, sesgos, instrucciones o puntos ciegos. La independencia real exige también herramientas distintas, reglas deterministas, evidencia reproducible y criterios explícitos.

Cuatro revisores no garantizan calidad. Cada uno debe producir:

- hallazgo identificable;
- severidad y confianza;
- evidencia (archivo, línea, prueba, captura o log);
- recomendación concreta;
- estado de resolución;
- regla para bloquear o permitir el avance.

Sin ese contrato, la revisión se convierte en cuatro opiniones difíciles de comparar.

### 3.6 10:14–12:32 · Aprobador, bucle de corrección y riesgo

Un aprobador agrega los resultados. Si encuentra feedback negativo, devuelve el trabajo al builder y repite el ciclo. Después, otro agente clasifica el cambio por riesgo: tareas pequeñas podrían integrarse automáticamente; cambios de backend, base de datos o lógica central exigirían un PR y aprobación humana. El autor subraya que deben existir **hooks o código** que bloqueen acciones; la decisión no puede descansar solo en el modelo [1].

Este es el principio de seguridad más importante del vídeo: **la política debe aplicarse fuera del modelo**. Un clasificador puede recomendar, pero las protecciones deterministas deben controlar ramas, permisos, secretos, despliegues y bases de datos.

La clasificación propuesta es demasiado simple si solo distingue “pequeño” y “grande”. Un cambio visual puede afectar accesibilidad, consentimiento, precios o cumplimiento legal. Una edición de texto puede introducir una promesa contractual. El riesgo debe evaluarse al menos por:

- impacto potencial;
- reversibilidad;
- exposición de datos;
- privilegios requeridos;
- alcance de usuarios;
- criticidad del dominio;
- novedad o incertidumbre;
- cobertura y calidad de pruebas;
- capacidad de observación y rollback.

### 3.7 12:32–13:51 · Paralelismo y acceso desde equipos no técnicos

Cada tarea tiene identificador, rama y pruebas, por lo que varias tareas pueden avanzar a la vez. El autor extiende la entrada a Slack o voz y propone que marketing o ventas soliciten landings, textos o avisos legales sin programar [1].

El paralelismo multiplica capacidad, pero también conflictos y costes. Hace falta gestionar:

- dos tareas que modifican el mismo archivo o decisión;
- versiones de memoria distintas;
- orden de migraciones;
- límites de concurrencia y presupuesto;
- cancelación, reintentos e idempotencia;
- colas de revisión humana;
- responsabilidad del solicitante.

Permitir cambios desde Slack o voz exige autenticación fuerte, autorización por rol, confirmación de intención y defensa frente a instrucciones maliciosas. El canal de entrada no debe heredar permisos de producción.

### 3.8 13:51–15:49 · Content Factory

El segundo ejemplo recibe audio o vídeo bruto, elimina silencios y errores, une fragmentos, añade transiciones/animaciones, genera miniaturas y prepara el contenido para YouTube. El autor conserva la publicación final manual [1].

Este ejemplo demuestra que el patrón no se limita al código. También revela por qué la supervisión humana sigue siendo útil: estilo, reputación, derechos, privacidad y mensaje editorial no se reducen a pruebas automáticas. Una fábrica de contenidos necesitaría controles de licencia, consentimiento, atribución, datos personales, desinformación, accesibilidad y conservación de originales.

### 3.9 15:49–17:21 · Escala, referencias y RSC Factory

El vídeo propone automatizar procesos que se repiten frecuentemente y tienen entrada/salida identificable. Cita OpenAI y Stripe como prueba de escala y anuncia “RSC Factory”, basado en su arnés, como proyecto futuro de código abierto [1].

La regla “si se hace más de una vez por semana, merece una fábrica” es una heurística, no un criterio económico. Automatizar solo compensa cuando frecuencia, coste manual, estabilidad del proceso y coste del error justifican construir y mantener el sistema.

## 4. Evaluación del resumen aportado

### Texto evaluado

> Automatización: sustituir conversaciones aisladas con agentes por procesos repetibles y auditables.
> Arquitectura: combinar memoria, planificación, construcción y revisores especializados.
> Control: reservar intervención humana según el riesgo.

### Veredicto global

**Clasificación: correcto, pero muy incompleto.** Resume la dirección estratégica, no el contenido suficiente para comprender el sistema ni decidir si conviene adoptarlo.

### Evaluación por afirmación

#### “Automatización”

- **Correcta:** recoge el paso de conversación a proceso repetible/auditable.
- **Falta:** entradas concretas, código determinista, agentes, humanos, trazas por etapa, ramas, pruebas, bucle de corrección y paralelismo.
- **Precisión necesaria:** no toda conversación debe convertirse en fábrica; solo tareas con contrato, frecuencia y control del error.

#### “Arquitectura”

- **Correcta:** memoria, planificación/construcción y especialistas aparecen en el vídeo.
- **Falta:** spec writer, builder con TDD, cuatro revisores, aprobador, clasificador de riesgo y controles externos al modelo.
- **Matiz:** “planificación” es una generalización; el vídeo habla de una especificación consultando el “brain”.

#### “Control”

- **Correcta:** la intervención humana depende del riesgo.
- **Falta:** el vídeo insiste en que hooks/código deben impedir acciones, y conserva revisión manual en cambios críticos y en la publicación de contenidos.
- **Riesgo del resumen:** podría interpretarse que un agente decide soberanamente cuándo no hace falta una persona, cuando el propio vídeo pide barreras deterministas.

### Ideas esenciales omitidas

1. El operador sigue siendo cuello de botella en el modo chat.
2. Una fábrica mezcla código, agentes y humanos; no es solo una cadena de LLM.
3. La memoria es consultada antes de especificar y construir.
4. El trabajo entra desde Sentry, GitHub o una petición.
5. El builder crea rama y trabaja con TDD.
6. Hay cuatro revisores especializados y paralelos.
7. Un aprobador devuelve el trabajo al builder hasta resolver hallazgos.
8. Un clasificador separa vía rápida y revisión humana.
9. Los hooks deterministas, no el modelo, aplican las restricciones.
10. Las tareas se ejecutan en paralelo con identificadores propios.
11. La idea se extiende a equipos no técnicos y a contenido audiovisual.
12. El vídeo anuncia RSC Factory, pero no demuestra un producto completo públicamente disponible.
13. Las cifras de OpenAI y Stripe requieren contexto humano y operativo.

### Resumen mejorado recomendado

> El vídeo propone pasar del uso manual de agentes en conversaciones aisladas a “AI Factories”: flujos repetibles, auditables y paralelizables que combinan código determinista, agentes especializados y aprobación humana. En el ejemplo de software, errores de Sentry, issues de GitHub o tareas entran en un pipeline donde un agente consulta una memoria persistente, redacta una especificación, un builder implementa mediante TDD y cuatro revisores independientes evalúan seguridad, UX, UI y arquitectura. Un aprobador itera las correcciones y un control de riesgo —respaldado por hooks y políticas fuera del modelo— decide si el cambio puede avanzar o requiere revisión humana. El patrón se aplica también a edición de contenidos. La tesis es prometedora, pero el vídeo no aporta métricas propias de calidad, coste o fallos y simplifica riesgos de integración, seguridad y gobierno.

## 5. Contraste de afirmaciones externas

### 5.1 OpenAI: aproximadamente un millón de líneas sin código manual

**Resultado:** sustancialmente confirmado.

OpenAI describe un experimento de cinco meses con un producto interno cuyo código de aplicación, pruebas, CI, documentación, observabilidad y herramientas fue generado por Codex. Declara cero líneas escritas manualmente, alrededor de un millón de líneas, unas 1.500 PR y una estimación de una décima parte del tiempo manual [3].

**Contexto omitido en el vídeo:**

- hubo un equipo humano dirigiendo y diseñando el entorno;
- el resultado incluye infraestructura, herramientas y documentación, no solo lógica de producto;
- líneas de código y número de PR no son métricas directas de valor o calidad;
- se trata de un informe de la propia organización que desarrolla Codex, no de una auditoría independiente.

### 5.2 Stripe: más de mil PR por semana

**Resultado:** confirmado y expresado de forma incluso conservadora.

Stripe afirma que más de mil PR fusionadas **cada semana** son producidas íntegramente por sus agentes “minions”, sin código humano [4]. Sin embargo, la misma fuente especifica que esas PR son revisadas por personas. El vídeo menciona “más de 1.000 PR en una semana”, pero omite que la práctica es semanal y que mantiene revisión humana.

**Contexto necesario:** Stripe dispone de repositorios, CI, entornos, controles y herramientas internas maduras. El número no es transferible directamente a una organización pequeña sin esa base.

### 5.3 RSC Harness y RSC Factory

El repositorio público `rsc-harness` existe y se presenta como un meta-arnés para dar memoria, herramientas, habilidades y protecciones a múltiples asistentes [2]. Esto respalda el antecedente técnico mencionado por el autor.

No se ha encontrado evidencia suficiente en las fuentes consultadas para afirmar que **RSC Factory**, como sistema completo mostrado en el vídeo, esté disponible y validado públicamente. Debe tratarse como anuncio o trabajo en curso, no como dependencia adoptable sin evaluación adicional.

## 6. Fortalezas de la propuesta

1. **Diseño por procesos:** obliga a definir entrada, salida y responsabilidades.
2. **Trazabilidad:** reconoce que localizar el punto de fallo es condición para operar.
3. **Memoria compartida:** reduce decisiones contradictorias entre sesiones.
4. **Separación de funciones:** especificar, construir, revisar y aprobar no recaen en el mismo rol lógico.
5. **Diversidad de revisión:** seguridad, experiencia, interfaz y arquitectura requieren criterios distintos.
6. **Bucles de reparación:** el feedback no termina en informe; vuelve a construcción.
7. **Controles fuera del modelo:** hooks y código imponen límites verificables.
8. **Supervisión proporcional:** evita revisar manualmente cada detalle sin renunciar al control de cambios críticos.
9. **Paralelismo explícito:** permite escalar sin multiplicar conversaciones manuales.
10. **Generalización prudente del patrón:** muestra software y contenido, conservando publicación final humana en el segundo caso.

## 7. Debilidades, riesgos y omisiones

### 7.1 Calidad y métricas

El vídeo no define tasa de éxito, regresiones, tiempo de ciclo, coste por tarea, porcentaje de reintentos, falsos negativos de revisión ni carga humana. Sin estas métricas no puede demostrarse que la fábrica sea mejor que un flujo asistido.

### 7.2 Seguridad

- Riesgo de prompt injection desde issues, logs, Slack o documentos.
- Exceso de privilegios del builder o de los revisores.
- Exposición de secretos en contexto, logs o artefactos.
- Dependencias maliciosas y cadena de suministro.
- Autoaprobación por modelos correlacionados.
- Cambios “pequeños” con impacto legal, reputacional o de accesibilidad.

### 7.3 Operación distribuida

Faltan cola durable, máquina de estados, bloqueos, idempotencia, reintentos, deduplicación, límites de concurrencia, resolución de conflictos y recuperación tras interrupciones.

### 7.4 Gobierno de memoria

El “brain” necesita fuentes canónicas, versionado, caducidad, permisos y resolución de contradicciones. Memorizar todo incrementa ruido y puede consolidar errores.

### 7.5 Despliegue y recuperación

No se detalla promoción entre entornos, canary, feature flags, copia de seguridad, migraciones reversibles, observabilidad posterior, rollback automático ni responsable de incidentes.

### 7.6 Coste y dependencia

Varios modelos grandes, revisiones paralelas y bucles pueden ser costosos. También existe dependencia de proveedores, licencias, límites de uso y cambios de modelo.

### 7.7 Responsabilidad

“Lo decidió el agente” no define responsabilidad. Deben quedar registrados solicitante, propietario, aprobador, política aplicada y evidencia. En dominios regulados, el derecho a ejecutar no se deduce de la capacidad técnica.

### 7.8 Generalización excesiva

No todo proceso con entrada y salida debe automatizarse. Los procesos inestables, infrecuentes, ambiguos o con coste de error alto pueden necesitar primero simplificación, documentación o decisión humana.

## 8. Relación con lo que Luis ya ha construido

Esta sección distingue **capacidades existentes observadas** de una fábrica completa. No presenta como desplegado lo que aún es una propuesta.

### 8.1 Correspondencias ya presentes

- **Memoria y contexto persistente:** Hermes mantiene memoria y habilidades reutilizables.
- **Especialización:** se pueden delegar análisis independientes a agentes especialistas.
- **Herramientas reales:** los agentes pueden leer, editar, ejecutar pruebas, navegar y trabajar con GitHub.
- **Disciplina de verificación:** los cambios se prueban y se contrastan antes de afirmar éxito.
- **Git como trazabilidad:** repositorios privados, commits y remotos permiten auditar cambios.
- **Supervisión humana:** Luis conserva decisiones de producto, seguridad y publicación.
- **Separación documental:** Documenta funciona como biblioteca de investigación, no como producto ni duplicado de los repositorios.
- **Restricciones de seguridad:** no exponer secretos, no subir datos personales y no hacer push automático sin alcance claro.

Estas piezas se parecen al “arnés” del vídeo. Son fundamentos necesarios, pero no bastan para llamar fábrica al sistema.

### 8.2 Lo que todavía falta para ser una AI Factory

- Un punto de entrada normalizado para eventos o tareas.
- Una máquina de estados durable por ejecución.
- Identificador, propietario, prioridad, presupuesto y plazo por trabajo.
- Contratos de salida y criterios de aceptación ejecutables.
- Orquestación automática entre especificación, construcción, revisión y aprobación.
- Políticas de riesgo codificadas fuera del modelo.
- Ramas/worktrees aislados y CI obligatorio por tarea.
- Cola, reintentos, cancelación, deduplicación e idempotencia.
- Telemetría de coste, duración, fallos, correcciones y carga humana.
- Gestión formal de secretos y privilegios mínimos.
- Despliegue progresivo, observación y rollback automatizable.
- Pruebas de recuperación y respuesta ante un agente o dependencia comprometidos.

### 8.3 Nivel de madurez estimado

- **Uso de agentes con herramientas:** presente.
- **Arnés con memoria, habilidades y reglas:** presente en parte.
- **Flujo reproducible dirigido por humano:** presente en varios trabajos.
- **Pipeline durable y disparado por eventos:** no verificado.
- **Aprobación por riesgo codificada:** no verificada.
- **Autonomía de producción con observabilidad y rollback:** no implementada ni recomendada todavía.

Conclusión: el entorno se encuentra entre **arnés asistido** y **flujo reproducible**, no en fábrica autónoma de producción.

## 9. Arquitectura de referencia segura

```text
[Entrada autenticada]
  Sentry / GitHub / formulario interno
            |
            v
[Normalizador + política de admisión]
  esquema, deduplicación, permisos, presupuesto
            |
            v
[Registro durable de ejecución]
  ID, estado, evidencias, propietario, plazos
            |
            v
[Planificador / especificador]
  consulta fuentes canónicas, redacta criterios
            |
        [puerta humana si falta intención]
            v
[Builder aislado]
  worktree/branch, mínimos privilegios, TDD
            |
            v
[Controles deterministas]
  lint, tipos, pruebas, secretos, dependencias, políticas
            |
            v
[Revisores especializados en paralelo]
  seguridad | producto/UX | UI/accesibilidad | arquitectura
            |
            v
[Agregador de evidencia]
  corrige y repite con máximo de ciclos
            |
            v
[Motor de riesgo codificado]
  datos, privilegios, alcance, reversibilidad, dominio
        /                           \
[PR + aprobación humana]       [PR automático de bajo riesgo]
        \                           /
            v
[Despliegue progresivo]
  preview/canary, observabilidad, rollback
            |
            v
[Postvalidación + aprendizaje gobernado]
```

Principios obligatorios:

- Los agentes **proponen**; las políticas externas **permiten o bloquean**.
- Ningún agente debe aprobar un cambio propio como única evidencia.
- Los secretos no se incorporan a prompts, memoria o logs.
- Todo cambio debe ser reversible o requerir aprobación reforzada.
- La ejecución se detiene ante evidencia insuficiente, no “rellena” huecos.
- El aprendizaje solo actualiza memoria tras validación, procedencia y versionado.

## 10. Piloto recomendado

### 10.1 Elección

Comenzar por una **fábrica de investigación documental**, no por producción:

```text
URL o pregunta
→ extracción y fuentes
→ informe según plantilla Documenta
→ revisión de citas, privacidad y contradicciones
→ borrador de PR
→ aprobación humana
→ incorporación al catálogo y changelog
```

### 10.2 Por qué este piloto

- Riesgo operativo bajo: no toca datos de negocio ni aplicaciones.
- Resultado auditable: Markdown, fuentes y diff.
- Reversibilidad inmediata mediante Git.
- Permite medir calidad y carga humana.
- Aprovecha capacidades existentes de Hermes y Documenta.
- Expone problemas reales de memoria, citas, duplicados y revisión sin habilitar despliegues autónomos.

### 10.3 Criterios de aceptación

- 100 % de afirmaciones externas importantes con fuente trazable.
- Cero secretos o datos personales en commits.
- Separación visible entre evidencia, inferencia y recomendación.
- Ningún push o merge sin aprobación humana durante el piloto.
- Reejecución idempotente: no duplica expediente ni catálogo.
- Cada ejecución registra coste, duración, fuentes fallidas y correcciones.
- Rollback probado mediante revert o cierre del PR.
- Al menos diez casos representativos antes de evaluar ampliación.

### 10.4 Métricas

- tiempo humano y total por expediente;
- número de correcciones humanas;
- citas inválidas o débiles;
- duplicados detectados;
- coste de modelos/herramientas;
- tasa de ejecuciones que requieren intervención;
- incidencias de privacidad o política;
- utilidad percibida frente al proceso manual.

### 10.5 Condiciones para ampliar

Solo después de un piloto satisfactorio:

1. pasar a generación de borradores de issues o PR sin merge;
2. automatizar controles deterministas;
3. permitir cambios de documentación de bajo riesgo;
4. considerar código no crítico en preview;
5. mantener bases de datos, autenticación, pagos, datos personales y producción bajo aprobación humana explícita.

## 11. Decisión recomendada

- **Adoptar ahora:** proceso repetible, trazas, memoria gobernada, controles deterministas, especialistas y aprobación proporcional al riesgo.
- **Pilotar:** fábrica documental con PR y aprobación humana.
- **No adoptar todavía:** auto-merge general, auto-deploy de producción, clasificación de riesgo exclusivamente por LLM o instalación de RSC Factory sin código y evaluación verificables.
- **Revisar cuando:** exista publicación estable de RSC Factory, documentación de seguridad, licencia, arquitectura, pruebas, costes y mecanismo de desinstalación/rollback.

## 12. Conclusiones

1. El vídeo presenta una arquitectura coherente para pasar de chats a procesos, pero es una explicación conceptual y promocional, no una validación completa.
2. El resumen de tres puntos no contiene errores materiales; pierde la mayor parte del diseño y de los límites.
3. La idea central útil es la combinación de repetibilidad, auditabilidad, memoria y autoridad humana, no el número de agentes.
4. Las cifras de OpenAI y Stripe están respaldadas por fuentes originales, con matices: equipos humanos diseñan el sistema y Stripe mantiene revisión humana.
5. El sistema actual de Luis ya posee varias piezas de arnés, pero no un orquestador durable de extremo a extremo.
6. El siguiente paso sensato es medir una fábrica documental de bajo riesgo antes de automatizar código o producción.
7. El éxito debe medirse por calidad, recuperación, coste y atención humana ahorrada, no por líneas de código, PR o volumen generado.

## Sources

[1] https://www.youtube.com/watch?v=p6nnqerea7w&t=51s — Programar con agentes ya no basta: monta tu Software Factory
[2] https://github.com/ericrisco/rsc-harness — rsc-harness
[3] https://openai.com/index/harness-engineering — Harness engineering: leveraging Codex in an agent-first world
[4] https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents — Minions: Stripe's one-shot, end-to-end coding agents
