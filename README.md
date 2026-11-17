# ONM staging

Copia estática de [Operación Niños de México](https://operacionninosdemexico.com). El sitio está en `public/` y Node lo publica con `npm start` (`server.mjs`).

Sigue siendo **staging**, así que no debe indexarse:

- `<meta name="robots" content="noindex,nofollow">` en cada HTML
- `public/robots.txt` con `Disallow: /`
- cabecera `X-Robots-Tag: noindex, nofollow` en `server.mjs`

## Ramas

- `staging` — sitio de staging. Es el branch que debe desplegar el hosting (auto-deploy al hacer push).
- `main` — producción más adelante. Hoy tiene el mismo snapshot, incluido el noindex. Antes de usarla en producción hay que quitar esas tres protecciones.

## Arranque

Hace falta Node.js 18 o superior. En SiteGround elige Node.js 22.

```bash
npm install
npm start
```

`npm start` ejecuta `node server.mjs`. El HTML ya está en `public/`. El proceso escucha en `PORT` (por defecto `3000`) en todas las interfaces. Abre http://localhost:3000. Para otro puerto: `PORT=4101 npm start` o `node server.mjs 4101`.

Las rutas limpias (`/contacto` en lugar de `/contacto.html`, sin barra final) las resuelve `server.mjs`.

En SiteGround (Node.js, despliegue desde GitHub): comando de inicio `npm start`, versión de Node 22.

## Regenerar con tools/

Hace falta Node.js 18 o superior. En `tools/` están `mirror.mjs`, `fill.mjs`, `verify.mjs` y `finalize.mjs` (dependencias: `cheerio` y `playwright-core`).

```bash
cd tools
npm install
node mirror.mjs https://operacionninosdemexico.com .. html
node fill.mjs https://operacionninosdemexico.com ..
node finalize.mjs .. onm-staging html
```

`finalize.mjs` vuelve a escribir `package.json`, `server.mjs`, `public/robots.txt` y el noindex. El nombre del paquete tiene que quedar `onm-staging`.

Si `npm start` registra 404 de archivos que la página pide tarde, guarda ese log y completa la copia:

```bash
bash fill-from-log.sh https://operacionninosdemexico.com .. server.log
```

`verify.mjs` abre las páginas en Chrome (`/usr/bin/google-chrome`) y anota las peticiones que fallan.
