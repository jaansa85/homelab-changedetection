# Convenciones del Servicio Changedetection

> [!WARNING] ESTADO ACTUAL: APAGADO / PENDIENTE DE REVISIÓN INTEGRAL
> Este proyecto **nunca ha funcionado bien** en la práctica (problemas con los checks, bloqueos anti-bot/Playwright y notificaciones). Por ese motivo se mantiene **apagado (`stopped`, `onboot: 0`) en el Bloque 6 Sandbox (`LXC 190`) y sin uso activo**. Queda pendiente **revisarlo y rediseñarlo todo de arriba a abajo en un futuro** antes de reactivarlo o promocionarlo.

## Contexto del Entorno

- **Contenedor:** LXC Debian 12 en Proxmox (`proxmox`).
- **VMID:** `190` *(Bloque 6 - Sandbox / Incubadora en VLAN 190 Servidores — actualmente `stopped` con `onboot: 0`)*.
- **IP Estática:** `172.16.190.190/24` (Gateway: `172.16.190.1`, DNS: `172.16.190.254`, `1.1.1.1`, `tag=190`).
- **Tailscale:** Habilitado con `--tun=userspace-networking` bajo el hostname `changedetection` (`100.124.243.90`).
- **Ruta de Despliegue en el Host:** `/opt/changedetection/docker-compose.yml`.
- **Datos Persistentes:** `/opt/changedetection/datastore`.

## Operaciones Habituales

- **Reiniciar el stack:**
  ```bash
  pct exec 190 -- bash -c 'cd /opt/changedetection && docker compose restart'
  ```
- **Revisar logs:**
  ```bash
  pct exec 190 -- bash -c 'cd /opt/changedetection && docker compose logs -f'
  ```
- **Estado de procesos:**
  ```bash
  pct exec 190 -- ps aux
  ```

## Reglas de Arquitectura

1. **Agnosticismo:** Nunca acoplar el backend o las notificaciones a un producto o tienda concreta. Todo se gestiona mediante tokens dinámicos (`{{watch_title}}`, `{{watch_url}}`, `{{diff}}`).
2. **Reutilización de Playwright:** El puerto `3000` de `sockpuppetbrowser` debe mantenerse expuesto (`0.0.0.0:3000:3000`) para permitir conexiones de scraping desde n8n (`172.16.190.111`) u otros scripts internos.

## Repositorio y Versionado

- **Repositorio independiente:** `https://github.com/jaansa85/homelab-changedetection.git`
- Gestionado y sincronizado automáticamente mediante `sync.sh` desde el directorio raíz del Homelab.

