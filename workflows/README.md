# Eight Academy — Admisiones IA v2 · Setup Guide

## 🔗 Links

| Recurso | URL |
|---------|-----|
| Caso de Éxito Live | https://rufoiot.github.io/eightacademy-admisiones-ia/ |
| Google Sheet (CRM) | https://docs.google.com/spreadsheets/d/1CdGDhEDUpHaO8Puzmw1DyCB-U34xQkoSkI_r2LQRAN4/edit |
| n8n Cloud | https://cyberdog87.app.n8n.cloud/home/workflows |
| Repo GitHub | https://github.com/RUFOIOT/eightacademy-admisiones-ia |

---

## ⚙️ Setup en 5 pasos

### Paso 1 — Variables en n8n
En n8n Cloud: **Settings → Variables → Add**

| Variable | Valor |
|----------|-------|
| `GOOGLE_MAPS_API_KEY` | Tu API key de Google Cloud Console |
| `ANTHROPIC_API_KEY` | Tu API key de Anthropic (console.anthropic.com) |
| `BRAVE_API_KEY` | Ver openclaw.json en workspace |
| `LEADS_SHEET_ID` | `1CdGDhEDUpHaO8Puzmw1DyCB-U34xQkoSkI_r2LQRAN4` |
| `OPENCLAW_WEBHOOK_URL` | URL del webhook de OpenClaw (ver OpenClaw → Integrations) |

### Paso 2 — Importar los 6 workflows
En n8n Cloud: **Workflows → Import** (uno por uno)

```
workflow-01-geo-families-v2.json    → WF1 Geo Family Discovery
workflow-02-intent-keywords-v2.json → WF2 Intent Keywords Engine
workflow-03-outreach-v2.json        → WF3 Smart Outreach
                                      (WF4 OpenClaw Bridge — incluido en WF3)
workflow-05-nse-zone-mapper.json    → WF5 NSE Zone Mapper
workflow-06-competitor-gap.json     → WF6 Competitor Gap Intelligence
```

### Paso 3 — Google Sheet ✅ (YA LISTO)
Sheet creado con 5 tabs:
- `Leads Familias` · `Leads Colegios` · `Trends Weekly` · `Intent Signals` · `Competitor Intel`

Conectar OAuth2 Google Sheets en n8n: **Credentials → New → Google Sheets OAuth2**

### Paso 4 — Verificar coordenadas de sedes
En WF1 (Code node "⚙️ Config: 4 Sedes x 4 Queries = 16 Jobs") confirmar:

| Sede | Coords actuales |
|------|----------------|
| Eight Kids — Los Alhelíes E15-44 | -0.2180, -78.4890 |
| Primaria — Av. 6 Dic. & M. Zambrano | -0.1950, -78.4860 |
| Secundaria — Av. La Prensa & N. López | -0.1267, -78.5003 |
| Zoo Botánica — Guayllabamba | -0.0639, -78.3618 |

### Paso 5 — Test manual WF1
En n8n: abrir WF1 → **Test Workflow** (botón ▶️)
Resultado esperado: leads guardados en Google Sheet tab "Leads Familias"

---

## 🗺️ Arquitectura de los 6 Workflows

```
WF1: 4 sedes × 4 tipos de lugar = 16 queries Google Places
     → Claude scoring → Sheet "Leads Familias" → Hot Lead → OpenClaw alert

WF2: 8 keywords HOT/WARM/DISCOVERY via Brave Search
     → Claude análisis → Sheet "Intent Signals" → Intent≥7 → OpenClaw alert

WF3: Lead entrante → NSE routing → Claude redacta (3 estilos)
     → Draft WhatsApp a OpenClaw + Draft email a Gmail

WF5: GPS input → 3 indicadores NSE → zona_nse + promo_recomendada (webhook)

WF6: 8 queries familias switching → Claude score oportunidad
     → Sheet "Competitor Intel" → Hot gap → OpenClaw alert
```

---

## 📊 Scoring de Leads

Solo pasan al CRM: `poder_adquisitivo_score ≥ 5` AND `fit_score ≥ 5`

| Score | Variable | Peso |
|-------|----------|------|
| 0-10 | poder_adquisitivo_score | ¿Puede pagar $350/mes? |
| 0-10 | intent_score | ¿Busca colegio activamente? |
| A/B+/B/C+ | zona_nse | NSE del sector |
| km | distancia_sede_km | Cercanía al campus |
| 0-10 | tech_affinity | Afinidad tecnológica |
| 0-10 | fit_score | Encaje con Eight Academy |

---

## ⚠️ Pendientes para activación completa

1. **WhatsApp admisiones real**: reemplazar `593999793094` (test) con el número oficial
2. **Google Maps API key**: crear en Google Cloud Console (habilitar Places API)
3. **OpenClaw Webhook URL**: configurar en OpenClaw → Integrations
4. **Gmail OAuth en n8n**: conectar para WF3 email drafts
5. **Verificar coords exactas** de las 4 sedes en Google Maps

