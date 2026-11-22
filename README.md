# Construcciones Furelos — demo de portada

Propuesta de diseño de **una sola página** (portada) para **Construcciones Furelos Louzao, S.L.**, constructora de Medina de Pomar (Burgos).

> **Aviso**: es una **demo no oficial**, hecha para enseñar una propuesta al cliente. No sustituye a la web en producción ni está afiliada a la empresa.

## Qué hay

- `index.html` — portada completa (hero, servicios, banda de licitación, obras, confianza, contacto).
- `styles.css` — identidad tomada del logo real: carbón `#383838` y rojo `#b81818`.
- `script.js` — JS mínimo: menú móvil accesible (Escape y clic fuera) y reveal con `IntersectionObserver` respetando `prefers-reduced-motion`.
- `assets/` — logo, favicon y fotos de obras **de la propia web actual**, optimizadas (WebP + JPG de respaldo, `width`/`height` para evitar CLS).
- `briefing.md` — el encargo original: tokens, estructura y reglas.
- `guia-tecnica.md` — extracto de las guías de Chrome (Modern Web Guidance) aplicadas: navbar, prioridad de imágenes y diseño responsive.

## Stack

HTML + CSS + JS vanilla. Sin frameworks, sin dependencias, sin cookies de terceros. Servible desde cualquier hosting estático (GitHub Pages, Cloudflare Pages, Caddy).

## Verificado

- Un solo `<h1>`, jerarquía correcta, `alt` en las 10 imágenes.
- Sin scroll horizontal a 390, 768 y 1024 px (`scrollWidth == clientWidth`).
- Descripciones de servicios alineadas por fila (medido en el DOM).
- Contraste del hero reforzado con velo; botón fantasma con fondo propio.

## Pendiente antes de producción

- Confirmar con el cliente los datos y las certificaciones (la web actual anuncia ISO 9001:2008 y OHSAS 18001, ambas retiradas).
- Sustituir el aviso de demo por el contenido definitivo y enlazar las páginas internas.
- Migrar el contenido de las 23 obras reales y montar los 301 desde las URLs actuales.

## Reglas para quien toque este repo

1. **No inventar datos**: ni cifras, ni años, ni certificaciones, ni clientes. Si falta un dato, se pregunta.
2. **No anunciar ISO 9001:2008 ni OHSAS 18001**: están retiradas (hoy son ISO 9001:2015 e ISO 45001).
3. **Mantener el aviso de demo** mientras siga siendo una propuesta.
4. **Sin dependencias**: HTML + CSS + JS vanilla. Nada de frameworks ni CDN de JS.
5. **Identidad cerrada**: carbón `#383838`, rojo `#b81818`, blanco roto `#f8f8f8`, tinta `#1e1e1e`, gris `#6b6b6b`, arena `#e6e3de`. Titulares Archivo, cuerpo Open Sans.
6. **Las fotos son de la empresa** (`assets/img/`). Nada de stock ajeno.

### Guías de Chrome antes de tocar UI (local, sin red)

```sh
mwg search "responsive navigation menu"   # query en inglés, 2-5 palabras
mwg retrieve "navbar"                     # guía completa
mwg legacy-scan . --json                  # patrones antiguos (~20% del corpus)
```

CLI en `~/.local/bin/mwg`. **No usar `npx modern-web-guidance@latest`**: descarga de red y telemetría.

### Verificación obligatoria (no vale «se ve bien»)

- Render con Brave headless a 390, 768, 1024 y 1440 px.
- Sin scroll horizontal: `document.documentElement.scrollWidth === clientWidth`.
- Alineaciones medidas con `getBoundingClientRect().top`, no a ojo.
- Tras editar HTML: contar `<div>` abiertos/cerrados, un solo `<h1>`, `alt` en todas las `<img>`.

Publicado en **https://currodiazdevivar.github.io/furelos-web-demo/** (Pages, rama `main`, raíz).

