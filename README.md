# Changedetection.io & Playwright Stealth (Price & Stock Tracker)

Plataforma agnóstica de monitorización diferencial de precios, stock y cambios web para el Homelab.

## Información del Entorno

- **Tipo:** Contenedor LXC (VMID 119 en Proxmox VE) sobre almacenamiento `ssd1tb` (NVMe).
- **IP Local:** `192.168.1.119`
- **Tailscale IP:** `100.124.243.90` (`changedetection`)
- **Puertos:**
  - `5000`: Interfaz Web de Changedetection.io (`http://192.168.1.119:5000` / `http://100.124.243.90:5000`).
  - `3000`: Endpoint WebSocket de Playwright stealth (`ws://192.168.1.119:3000`).

## Componentes del Stack

1. **Changedetection.io:**
   - Motor de comprobación periódica, filtrado visual/XPath/CSS y registro de cambios históricos.
   - Volumen de datos persistente en `/opt/changedetection/datastore`.
2. **Sockpuppetbrowser (Playwright Stealth Chromium):**
   - Instancia headless con evasión de fingerprints para sitios con protecciones WAF, Cloudflare y Turnstile (Amazon, PcComponentes, etc.).
   - Granja reutilizable desde otros servicios del Homelab (n8n, scripts de Python) a través de WebSocket.

## Integración con Home Assistant

Changedetection está configurado con una notificación global genérica:
- **URL de Notificación:** `post://192.168.1.117:8123/api/webhook/price_tracker_alert`
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
