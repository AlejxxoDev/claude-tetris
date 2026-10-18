---
name: clima
description: Consulta el clima actual y el pronóstico de una ciudad usando wttr.in desde la terminal (sin API key). Úsala cuando el usuario pida el tiempo, la temperatura, si va a llover o el pronóstico de un lugar.
---

# Clima

Obtiene el clima de una ubicación con `wttr.in`, un servicio público sin API key. Las peticiones salen desde la máquina local con `curl`.

## Cuándo usarla

- El usuario pregunta por el clima, la temperatura, el viento, la humedad o si va a llover.
- Pide el pronóstico de una ciudad o región.

## Procedimiento

1. Determina la ubicación. Si el usuario no la indica, pregúntala. Si no hay forma de saberla, usa la ciudad por defecto `Yopal,Casanare,Colombia`.
2. Ejecuta **una** de estas consultas con Bash (ajusta `CIUDAD`, con espacios reemplazados por `+`):

   Resumen actual en una línea:
   ```bash
   curl -s "https://wttr.in/CIUDAD?format=%l:+%C+%t+(sensacion+%f),+viento+%w,+humedad+%h,+lluvia+%p&lang=es"
   ```

   Pronóstico de 3 días en texto, para más detalle:
   ```bash
   curl -s "https://wttr.in/CIUDAD?lang=es&T"
   ```

   Datos completos en JSON, si hace falta procesarlos:
   ```bash
   curl -s "https://wttr.in/CIUDAD?format=j1"
   ```

3. Responde al usuario con los datos relevantes de forma breve: temperatura, sensación térmica, estado del cielo y si hay lluvia prevista. Sigue las preferencias de brevedad de `CLAUDE.md`.

## Notas

- Si `curl` devuelve un error o una respuesta vacía, reintenta una vez. Si sigue fallando, informa al usuario de que el servicio no responde.
- Para ciudades con nombres ambiguos, añade el país: `Cordoba,Spain`.
- En PowerShell, usa `curl.exe` en lugar de `curl`, porque `curl` es un alias de `Invoke-WebRequest`.
- Los datos de `wttr.in` son de terceros y pueden tener retraso; indícalo si la precisión importa.
