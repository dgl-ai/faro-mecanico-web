# Faro Mecánico — distribución web

Repositorio **sólo** para la distribución web e infraestructura de despliegue del piloto P01: https://faro-mecanico.dglabs.cloud

- El **código fuente** del juego vive en https://github.com/dgl-ai/faro-mecanico (privado).
- Aquí NO se suben docs internas, secretos ni assets finales.
- El export web de Godot (`index.html`, `index.pck`, `index.wasm`, `index.js`, audio) se copia a `public/` cuando exista.

## Estado

**Piloto P01 en preparación — todavía no jugable.** La página publicada es un placeholder de bootstrap; no hay build del juego.

## Publicar una nueva build (Forge / Maestro exportan y suben; Ferra sincroniza en el host)

1. Exportar desde Godot 4.7.2 el preset **Web (Compatibility, sin threads)** del repo `faro-mecanico`.
2. Copiar el contenido del export a `public/` en este repo (sobrescribir `index.html`, `index.pck`, `index.wasm`, `index.js` y assets) y regenerar `public/BUILD_INFO.txt` con el SHA real del commit del repo fuente y la fecha.
3. Commit y push a `main`.
4. **Sólo Ferra, en el VPS:** ejecutar `/opt/faro-mecanico-web/sync.sh` — actualiza el clone `/opt/faro-mecanico-web/repo` (main, `--ff-only`, limpio) y copia **sólo `public/`** al document root `/opt/faro-mecanico-web/code`. La config nginx se sincroniza aparte (`nginx.conf` → `/opt/faro-mecanico-web/nginx.conf` + `nginx -s reload` en el contenedor). No hay auto-deploy; push a GitHub NO despliega. No hace falta reiniciar el servicio para copiar ficheros.

El document root nunca contiene `.git`, docs internas ni secretos: sólo el contenido de `public/`.

## Infraestructura (Ferra)

Servicio swarm `faro-mecanico-web` (nginx) con bind mounts `/opt/faro-mecanico-web/code` → `/usr/share/nginx/html` y `/opt/faro-mecanico-web/nginx.conf`, router Traefik en `/etc/dokploy/traefik/dynamic/faro-mecanico.yml` con TLS Let's Encrypt. MIME correcto para wasm/pck/ogg y cache coherente en la nginx conf.
