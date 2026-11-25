# Changedetection.io & Playwright Stealth (Price & Stock Tracker)

> [!WARNING]
> **Estado del Proyecto (Sandbox `LXC 190` — Apagado / Sin uso):**
> Este servicio **nunca ha funcionado bien** y por ello permanece **apagado (`stopped`) y con el arranque automático deshabilitado (`onboot: 0`)** en el bloque Sandbox. En un futuro habrá que **revisar todo el proyecto de arriba a abajo** (arquitectura de scraping, evasión de bloqueos con Playwright/Sockpuppetbrowser y flujo de alertas) antes de volver a ponerlo en marcha.

Plataforma agnóstica de monitorización diferencial de precios, stock y cambios web para el Homelab.

## Información del Entorno

- **Tipo:** Contenedor LXC (VMID 190 en Proxmox VE, `stopped` / `onboot: 0`) sobre almacenamiento `ssd1tb` (NVMe).
- **IP Local:** `172.16.190.190`
- **Tailscale IP:** `100.124.243.90` (`changedetection`)
- **Puertos:**
  - `5000`: Interfaz Web de Changedetection.io (`http://172.16.190.190:5000` / `http://100.124.243.90:5000`).
  - `3000`: Endpoint WebSocket de Playwright stealth (`ws://172.16.190.190:3000`).

## Componentes del Stack

1. **Changedetection.io:**
   - Motor de comprobación periódica, filtrado visual/XPath/CSS y registro de cambios históricos.
   - Volumen de datos persistente en `/opt/changedetection/datastore`.
2. **Sockpuppetbrowser (Playwright Stealth Chromium):**
   - Instancia headless con evasión de fingerprints para sitios con protecciones WAF, Cloudflare y Turnstile (Amazon, PcComponentes, etc.).
   - Granja reutilizable desde otros servicios del Homelab (n8n, scripts de Python) a través de WebSocket.

## Integración con Home Assistant

Changedetection está configurado con una notificación global genérica:
- **URL de Notificación:** `post://172.16.190.110:8123/api/webhook/price_tracker_alert`
- **Formato:** JSON
- **Cuerpo:**
  ```json
  {
    "title": "{{watch_title}}",
    "url": "{{watch_url}}",
    "diff": "{{diff}}",
    "tag": "{{watch_tag}}"
  }
  ```

En Home Assistant, la automatización `price_tracker_alerta_universal` recibe este evento y emite una notificación push inmediata con el enlace directo al dispositivo móvil registrado (`notify.poco_f7`).
