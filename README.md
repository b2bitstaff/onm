# ONM staging

Copia estática de [Operación Niños de México](https://operacionninosdemexico.com). El sitio está en `public/` y Vercel lo publica con `outputDirectory: public` (`vercel.json`).

Sigue siendo **staging**, así que no debe indexarse:

- `<meta name="robots" content="noindex,nofollow">` en cada HTML
- `public/robots.txt` con `Disallow: /`
- cabecera `X-Robots-Tag: noindex, nofollow` en `vercel.json`

## Ramas

- `staging` — sitio de staging. Es el branch que debe desplegar Vercel.
- `main` — producción más adelante. Hoy tiene el mismo snapshot, incluido el noindex. Antes de usarla en producción hay que quitar esas tres protecciones.

## Vista previa local

```bash
node server.mjs
```

Abre http://localhost:3000. Para otro puerto: `node server.mjs 4101`.

## Regenerar con tools/

Hace falta Node.js 18 o superior. En `tools/` están `mirror.mjs`, `fill.mjs`, `verify.mjs` y `finalize.mjs` (dependencias: `cheerio` y `playwright-core`).

```bash
cd tools
npm install
node mirror.mjs https://operacionninosdemexico.com .. html
node fill.mjs https://operacionninosdemexico.com ..
node finalize.mjs .. onm-staging html
```

`finalize.mjs` vuelve a escribir `vercel.json`, `package.json`, `server.mjs`, `public/robots.txt` y el noindex. El nombre del paquete tiene que quedar `onm-staging`.

Si `node server.mjs` registra 404 de archivos que la página pide tarde, guarda ese log y completa la copia:

```bash
bash fill-from-log.sh https://operacionninosdemexico.com .. server.log
```

`verify.mjs` abre las páginas en Chrome (`/usr/bin/google-chrome`) y anota las peticiones que fallan.
