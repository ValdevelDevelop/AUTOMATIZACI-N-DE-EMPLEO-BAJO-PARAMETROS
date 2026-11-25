# Integración n8n + WhatsApp para Búsqueda de Empleo

Este flujo conecta los reportes diarios generados por tu automatización en `C:\Users\Fabian\.gemini\antigravity\scratch\busqueda-empleo\postulaciones\` con un chat informativo en **WhatsApp**.

## Arquitectura del Flujo (`whatsapp_empleos_workflow.json`)

El workflow tiene **dos ramas**:

1. **Envío diario automático (12:15 PM):**
   - `Disparador Diario (12:15 PM)` se ejecuta 15 minutos después de tu automatización de búsqueda (12:00 PM).
   - `Calcular Ruta del Resumen` arma la ruta `YYYY-MM-DD_resumen.md`.
   - `Leer Resumen Diario (.md)` + `Extraer Texto Markdown` leen el archivo local.
   - `Formatear Mensaje WhatsApp` adapta negritas y encabezados al formato de WhatsApp (límite de ~4096 caracteres).
   - `Enviar Resumen a WhatsApp` te envía el resumen a ``.

2. **Chat interactivo de consultas (WhatsApp Trigger):**
   - `WhatsApp Trigger (Consultas)` escucha mensajes entrantes.
   - `Interpretar Comando de Chat` detecta si escribiste `RESUMEN` / `HOY` / `VACANTES`, o si necesitas el menú de ayuda.
   - Responde en el mismo hilo con las vacantes segmentadas del día o las instrucciones.

## Cómo importarlo en n8n

1. Abre tu instancia de **n8n** (local o en la nube; si n8n corre en Docker, asegúrate de montar la carpeta `C:\Users\Fabian\.gemini\antigravity\scratch\busqueda-empleo` como volumen).
2. Ve a **Workflows → Import from File** y selecciona [whatsapp_empleos_workflow.json]
3. Configura tus credenciales de **WhatsApp Business Cloud API** (Meta for Developers) en los nodos de WhatsApp.
   - *Alternativa:* Si prefieres no usar Meta Cloud API, puedes reemplazar los nodos de WhatsApp por **HTTP Request** (para **Evolution API** o **Waha** corriendo local) o **Twilio**.
4. Activa el workflow.

## Siguiente mejora opcional (IA Conversacional)
Si quieres hacer preguntas abiertas por WhatsApp (ej. *"¿qué requisitos me faltan para la vacante de ALO Credit?"* o *"envíame el CV adaptado de Colmédica"*), puedes reemplazar la rama `Responder Menú de Ayuda` por un nodo **AI Agent** de n8n conectado a Gemini u OpenAI y pasarle el contenido de `cv_base.md` + `YYYY-MM-DD_resumen.md` como contexto.
