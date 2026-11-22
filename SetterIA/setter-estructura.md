# SetterIA - Estructura de Archivos Recomendada

## Archivos Principales

### `README.md`
- Descripción general del setter
- Estructura de fases
- Costes de infraestructura
- Instrucciones de uso

### `setter-prompt.md`
- Prompt aprobado de la IA (Fase 1)
- Rellenado con respuestas de la entrevista
- Versión final aprobada por el usuario

### `setter-progreso.md`
- Estado de cada fase completada/pendiente
- Fecha de inicio y seguimiento
- Proyectos y URLs configurados
- Palabras clave creadas

### `setter-problemas-frecuentes.md`
- Problemas más comunes y soluciones
- Guía de debugging para cada fase
- Contactos de ayuda emergencia

## Flujo de Trabajo

1. **Fase 0**: Presentar plan, verificar herramientas
2. **Fase 1**: Aplicar 10 preguntas de entrevista → Redactar prompt → Guardar en `setter-prompt.md` → Usuario dice OK
3. **Fase 2**: Crear cuentas Instagram, ManyChat, Supabase, Google AI Studio, Agenda → Anotar en `setter-progreso.md`
4. **Fase 3**: Crear Edge Function `setter-ia` en Supabase con código Anexo B → Agregar secretos → Desplegar → Probar 401
5. **Fase 4**: Configurar ManyChat: Campos de usuario, etiquetas, respuesta predeterminada, solicitud externa → Probar solicitud → Publicar automatización
6. **Fase 5**: Probar con DM real → Ver ficha de contacto → Probar 5 casos → Ajustar prompt si es necesario → Desplegar función
7. **Fase 6**: Definir palabras clave en reels → Configurar automatizaciones → Seguimiento 24h → Redactar mensajes de seguimiento → Usuario dice OK

## Checklist de Finalización

- [ ] Fase 0 completada y plan presentado
- [ ] Fase 1: Entrevista realizada, prompt aprobado y guardado
- [ ] Fase 2: Todas las cuentas creadas y anotadas
- [ ] Fase 3: Edge Function desplegable, probando 401, URL anotada
- [ ] Fase 4: ManyChat configurado, solicitud externa probando correctamente, automatización publicada
- [ ] Fase 5: Pruebas con DM real exitosas, 5 casos probados, prompt ajustado
- [ ] Fase 6: Palabras clave configuradas, seguimiento 24h listo
- [ ] Resumen de una página entregado al usuario
- [ ] Política de privacidad actualizada (asistente automático mencionado)

---
*Estructura generada como parte de la documentación del repositorio Documenta*