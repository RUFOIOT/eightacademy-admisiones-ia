# 🎓 Eight Academy — Admisiones IA v2.0

Sistema avanzado de generación de leads para admisiones. 6 workflows n8n interconectados con scoring NSE, intención de compra y outreach hiper-personalizado.

## Campuses

| Campus | Nivel | Dirección | Coords |
|--------|-------|-----------|--------|
| Eight Kids | Inicial | Los Alhelíes E15-44, Quito | -0.2180,-78.4890 |
| Primaria | EGB | Av. 6 de Diciembre y M. Zambrano | -0.1950,-78.4860 |
| Secundaria | Bachillerato | Av. La Prensa y Nicolás López | -0.1267,-78.5003 |
| Zoo Botánica | Todos | 15 min de Guayllabamba | -0.0639,-78.3618 |

## Los 6 Workflows

| # | Archivo | Función | Trigger |
|---|---------|---------|---------|
| WF1 | `workflow-01-geo-families-v2.json` | Geo Family Discovery (4 sedes × 4 queries = 16 búsquedas) | Lun/Mié/Vie 8AM |
| WF2 | `workflow-02-intent-keywords-v2.json` | Intent Keywords (8 queries: hot/warm/discovery) | Mar/Jue 7AM |
| WF3 | `workflow-03-outreach-v2.json` | Outreach NSE-aware (A/B+/B routing) | Webhook |
| WF4 | `workflow-04-openclaw-bridge.json` | Bridge OpenClaw↔n8n | Webhook |
| WF5 | `workflow-05-nse-zone-mapper.json` | NSE Zone Mapper (poder adquisitivo por zona) | Webhook |
| WF6 | `workflow-06-competitor-gap.json` | Competitor Gap Intelligence | Miércoles 8AM |

## Google Sheet — 5 Tabs requeridas

```
Tab 1: Leads Familias       ← WF1 (geo family discovery)
Tab 2: Leads Colegios       ← WF1 (escuelas y aliados)
Tab 3: Trends Weekly        ← WF2 (tendencias semanales)
Tab 4: Intent Signals       ← WF2 (señales de intención)
Tab 5: Competitor Intel     ← WF6 (inteligencia competitiva)
```

## Variables de Entorno (n8n Settings → Variables)

```
GOOGLE_MAPS_API_KEY     → Maps Platform con Places API habilitado
ANTHROPIC_API_KEY       → Claude (Haiku para scoring, Sonnet para outreach)
BRAVE_API_KEY           → Ya configurado en openclaw.json
LEADS_SHEET_ID          → ID del Google Sheet CRM
OPENCLAW_WEBHOOK_URL    → Webhook de notificaciones OpenClaw
```

## Scoring Variables (WF1)

| Variable | Rango | Descripción |
|----------|-------|-------------|
| `poder_adquisitivo_score` | 0-10 | ¿Puede pagar $350/mes? |
| `intent_score` | 0-10 | ¿Está buscando colegio activamente? |
| `zona_nse` | A/B+/B/C+ | Nivel socioeconómico del sector |
| `distancia_sede_km` | km | Al campus más cercano |
| `tech_affinity` | 0-10 | Valores tecnológicos del hogar |
| `fit_score` | 0-10 | Encaje con Eight Academy |

## Outreach Routing por NSE (WF3)

| NSE | Ingreso Est. | Ángulo Principal |
|-----|-------------|-----------------|
| A | $7,000+/mes | Exclusividad · Metaverso · Líder Ecuador |
| B+ | $3,500-7,000 | Innovación · Tech · Sin matrícula como extra |
| B | $2,000-3,500 | **SIN MATRÍCULA** + **BECAS** como gancho |

## Setup Rápido (30 min)

1. **n8n** → Settings → Variables → Añadir las 5 keys
2. **Importar** los 6 JSONs en orden (WF1→WF6)
3. **Google Sheet** → Crear con las 5 tabs
4. **Credentials** → Google Sheets OAuth2 + Gmail OAuth2
5. **Coordenadas** → Verificar/actualizar coords exactas en WF1 Code node
6. **Test manual** → Ejecutar WF1, revisar primeros leads en Sheet
7. **Activar** todos los workflows

## Comandos OpenClaw → n8n (WF4)

```json
{"command": "get_stats"}      → Stats del pipeline en vivo
{"command": "get_trends"}     → Último reporte de tendencias
{"command": "buscar_leads"}   → Disparar búsqueda manual
{"command": "nse_query"}      → Consultar NSE de una zona
```

## Mejoras v2 vs v1

- ✅ Busca **familias** (B2C) además de colegios (B2B)
- ✅ **4 sedes** georeferenciadas (vs 1 punto central)
- ✅ **16 búsquedas** por ejecución (vs 1)
- ✅ Scoring con **6 variables** (vs 1 score simple)
- ✅ Outreach **NSE-aware**: A/B+/B con mensajes diferentes
- ✅ **Intent Keywords** con 8 queries específicas EA
- ✅ **Competitor Gap** para detectar familias switching
- ✅ **NSE Zone Mapper** standalone
- ✅ Contexto completo Eight Academy en todos los prompts Claude
