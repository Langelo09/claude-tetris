---
name: clima
description: Consulta el clima actual y el pronóstico de Ciudad de México por defecto (o de otra ciudad si el usuario la indica). Úsala cuando pregunte por el clima, la temperatura, si va a llover o el pronóstico de hoy o de los próximos días.
---

# Clima local

Obtiene el clima sin API key usando [wttr.in](https://wttr.in). La ubicación por defecto es **Ciudad de México**.

## Pasos

1. Si el usuario indicó otra ciudad, úsala. Si no, usa siempre `Ciudad+de+Mexico` (no detectes por IP).
2. Ejecuta con Bash (el espacio en nombres de ciudad va como `+`):

   ```bash
   curl -s "https://wttr.in/<CIUDAD>?format=j1&lang=es"
   ```

   Por defecto: `https://wttr.in/Ciudad+de+Mexico?format=j1&lang=es`. Otra ciudad: `https://wttr.in/Bogota?format=j1&lang=es`.

3. Del JSON, usa:
   - `nearest_area[0]` → `areaName[0].value` y `country[0].value` (confirma el lugar consultado).
   - `current_condition[0]` → `temp_C`, `FeelsLikeC`, `humidity`, `windspeedKmph`, `precipMM`, `lang_es[0].value` (descripción).
   - `weather[0..2]` → `date`, `mintempC`, `maxtempC` y `hourly[*].chanceofrain` para el pronóstico de 3 días.

4. Responde en español, breve y directo: primero el clima actual, luego el pronóstico solo si lo pidió o si hay lluvia probable. Indica siempre el lugar consultado.

## Notas

- Unidades: métricas (°C, km/h, mm).
- Si `curl` falla o devuelve algo que no es JSON, reintenta una vez; si persiste, díselo al usuario y sugiere indicar la ciudad manualmente.
- Alternativa si wttr.in no responde: Open-Meteo (`https://api.open-meteo.com/v1/forecast?latitude=<LAT>&longitude=<LON>&current=temperature_2m,precipitation,wind_speed_10m&daily=temperature_2m_max,temperature_2m_min,precipitation_probability_max&timezone=auto`), que requiere coordenadas.
