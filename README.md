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
