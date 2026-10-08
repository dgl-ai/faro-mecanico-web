# Faro Mecánico — distribución web

Repositorio **sólo** para la distribución web e infraestructura de despliegue del piloto P01: https://faro-mecanico.dglabs.cloud

- El **código fuente** del juego vive en https://github.com/dgl-ai/faro-mecanico (privado).
- Aquí NO se suben docs internas, secretos ni assets finales.
- El export web de Godot (`index.html`, `index.pck`, `index.wasm`, `index.js`, audio) se copia a `public/` cuando exista.

## Estado

**Piloto P01 en preparación — todavía no jugable.** La página publicada es un placeholder de bootstrap; no hay build del juego.

## Publicar una nueva build (Forge / Maestro, sin tocar infraestructura)

1. Exportar desde Godot 4.7.2 el preset **Web (Compatibility, sin threads)** del repo `faro-mecanico`.
2. Copiar el contenido del export a `public/` en este repo (sobrescribir `index.html`, `index.pck`, `index.wasm`, `index.js` y assets) y regenerar `public/BUILD_INFO.txt` con el SHA real del commit del repo fuente y la fecha.
3. Commit y push a `main`.
4. En el VPS, Ferra (o quien tenga acceso) sincroniza: `git -C /opt/faro-mecanico-web/code pull` y recarga del contenedor (`docker service update --force faro-mecanico-web` — sólo si el bind no refleja los ficheros al instante; los ficheros estáticos se sirven al momento por bind mount).

No hay auto-deploy desde GitHub activado; la publicación al host es manual vía el paso 4.

## Infraestructura (Ferra)

Servicio swarm `faro-mecanico-web` (nginx) con bind mounts `/opt/faro-mecanico-web/code` → `/usr/share/nginx/html` y `/opt/faro-mecanico-web/nginx.conf`, router Traefik en `/etc/dokploy/traefik/dynamic/faro-mecanico.yml` con TLS Let's Encrypt. MIME correcto para wasm/pck/ogg y cache coherente en la nginx conf.
