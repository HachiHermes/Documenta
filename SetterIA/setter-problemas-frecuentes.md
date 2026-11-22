# SetterIA - Problemas Frecuentes

## Sin respuestas a nadie
- Conexión de Instagram en ManyChat: Configuración → Instagram → Actualizar permisos
- «Permitir acceso a los mensajes» activado en la app

## Sin respuestas a una persona
- Tiene la etiqueta `humano` o su automatización está pausada

## IA siempre responde la frase de reserva
- Registros de la función: clave de Gemini, modelo o tiempo de respuesta
- Verificar modelo Flash más reciente y `thinkingConfig`

## Error 401
- `x-setter-key` no coincide con `MANYCHAT_SECRET`
- Reingresar el secreto en ManyChat y função

## Olvida lo hablado
- Mapeo `$.historial` → `ia_historial` incorrecto
- Verificar configuración de variables en ManyChat

## Mensaje vacío
- El bloque Enviar mensaje no contiene `ia_respuesta`
- Fallo en el mapeo `$.respuesta`

## Palabra clave no dispara
- Palabra repetida en otra automatización
- Reel anterior a la automatización (hay que asignarlo a mano)

## Configuración Supabase
- Verificar que `verify_jwt` está en `false` en la Edge Function
- Comprobar que los secretos `GEMINI_API_KEY` y `MANYCHAT_SECRET` están configurados
- Asegurarse de que el prompt no contiene acentos graves `` ` `` ni `${`

## Problemas de tiempo (8 segundos límite)
- MuchChat corta a los 10 segundos
- La función tiene 8,5 segundos máximo
- Si la IA no llega, se envía la pregunta de `RESERVA`
- Considerar cambiar a modelo más optimizado o reducir historial