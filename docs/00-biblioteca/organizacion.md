# Política de biblioteca documental

Versión: 1.0 · 2026-11-07 · Estado: organización documental aplicada; adopciones tecnológicas pendientes.

## Misión y límites
Documenta es infraestructura documental compartida: conserva investigaciones, referencias y criterios para incorporar herramientas. No es un proyecto de producto ni el almacén de datos de las aplicaciones. Cada proyecto mantiene su código y documentos canónicos en su repositorio. Aquí se enlazan, no se duplican completos.

Se conserva el flujo original: **Descubrir → Evaluar seguridad, privacidad y dependencia → Probar en piloto aislado → Medir coste, calidad y utilidad → Integrar con aprobación**. Una investigación no autoriza un despliegue ni acredita revisión profesional.

## Clasificación
- `docs/00-biblioteca/`: catálogo, normas y plantillas.
- `docs/10-investigaciones/<tema>/`: expedientes tecnológicos; cada expediente reúne informe, fuentes, prueba propuesta y resumen visual.
- `docs/10-investigaciones/videos-youtube/`: videoteca e índice único de análisis cuyo material de partida es un vídeo de YouTube; cada registro incluye autor/canal y URL.
- `docs/20-decisiones/`: decisiones propuestas y posteriormente aceptadas/rechazadas con responsable y motivo.
- `docs/30-proyectos/`: referencias al contexto canónico y aplicaciones posibles, sin copiar proyectos.
- `ProyectosGitHub/` y documentos de raíz anteriores: fondo existente conservado en su ubicación original. La pertenencia al fondo no acredita vigencia ni seguridad de su contenido.

No se crean carpetas vacías para aparentar contenido. No se renombra el fondo existente: preservar enlaces tiene prioridad sobre la estética.

## Descripción de cada registro
Identificador estable; título; fecha de consulta; versión; tipo; estado; ámbito; palabras clave; fuente canónica; hechos verificados; hipótesis; decisión recomendada; dependencias; revisión pendiente y desencadenante para revisar.

Estados: `referencia` / `investigado` / `piloto propuesto` / `piloto medido` / `aprobado` / `descartado` / `sustituido`. Solo el responsable autorizado cambia una recomendación a aprobado. Una prueba documental no pasa a piloto medido.

## Conservación y revisión
- Nombres de archivo descriptivos en minúsculas y con guiones; IDs estables en el catálogo.
- No copiar páginas web completas: conservar URL, fecha, extracto breve y análisis propio.
- Usar enlaces relativos entre documentos. No crear copias “final-final”. Git conserva versiones.
- Ante sustitución, enlazar el sucesor y conservar el original salvo autorización explícita de retirada.
- Revisar precios, licencias, ediciones y seguridad antes de contratar o desplegar, aunque la investigación sea reciente.
- Actualizar catálogo y CHANGELOG en cada cambio material.
- No publicar secretos, cuentas personales, datos de visitantes, ubicaciones precisas, volcados de producción ni respaldos. La privacidad del repo no hace seguros esos contenidos.

## Revisión de una investigación
Comprobar alcance, fuentes oficiales, separación hecho/hipótesis, contexto real del proyecto, alternativas simples, coste total, permisos, recuperación, criterios de aceptación y reversibilidad. Registrar qué no se ha probado.

## Fondo previo
Este trabajo no audita ni sanea el fondo histórico. No se ha extraído su contenido completo ni se ha alterado; no atribuirle revisión de seguridad por estar catalogado.
