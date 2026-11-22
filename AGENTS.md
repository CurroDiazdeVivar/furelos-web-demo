# AGENTS.md — instrucciones para agentes que toquen este repo

## Qué es esto

Demo de **una sola página** (portada) para **Construcciones Furelos Louzao, S.L.**, constructora de Medina de Pomar (Burgos). Es una **propuesta de diseño no oficial** hecha para enseñársela al cliente. Nada de aquí es la web en producción.

- Cliente: **Luis Furelos**, gerente y dueño. Su hija **Vanesa** es la arquitecta de la empresa.
- Empresa: Avda. Bilbao 11 Bajo · 09500 Medina de Pomar (Burgos) · 947 147 242 · furelos@construccionesfurelos.com
- Web actual (WordPress, de un proveedor externo): https://construccionesfurelos.com

## Reglas duras

1. **No inventar datos.** Ni cifras, ni años, ni certificaciones, ni clientes, ni premios. Si falta un dato, se pregunta.
2. **No anunciar certificados retirados.** La web actual anuncia ISO 9001:2008 y OHSAS 18001; hoy las vigentes son ISO 9001:2015 e ISO 45001. Aquí no aparecen.
3. **Mantener el aviso de demo** («Propuesta de diseño — no es la web oficial») mientras siga siendo una propuesta.
4. **No añadir dependencias.** HTML + CSS + JS vanilla. Sin frameworks, sin CDN de JS, sin cookies ni analítica de terceros.
5. **Identidad cerrada**: carbón `#383838`, rojo `#b81818`, blanco roto `#f8f8f8`, tinta `#1e1e1e`, gris `#6b6b6b`, arena `#e6e3de`. Titulares Archivo, cuerpo Open Sans.
6. **Las fotos son de la empresa** (`assets/img/`): Plaza Mayor de Medina, obras en Mungía, Villasana de Mena, Puente Villanueva. Nada de stock ajeno.
7. **Un solo `<h1>`**. Jerarquía de encabezados sin saltos.

## Antes de tocar UI: consultar las guías de Chrome (local, sin red)

Corpus instalado en este equipo:

```sh
mwg search "responsive navigation menu"   # query en inglés, 2-5 palabras
mwg retrieve "navbar"                     # devuelve la guía completa
mwg legacy-scan . --json                  # patrones antiguos (cubre ~20% del corpus)
```

CLI en `~/.local/bin/mwg`. **No usar `npx modern-web-guidance@latest`**: descarga de red y telemetría.

## Verificación obligatoria (no vale «se ve bien»)

- Render real con Brave headless a 390, 768, 1024 y 1440 px:
  `brave-browser --headless=new --window-size=W,H --screenshot=out.png index.html`
- Sin scroll horizontal: `document.documentElement.scrollWidth === clientWidth`
  (inyectar un script que lo escriba en el DOM y leerlo con `--dump-dom`).
- Alineaciones medidas con `getBoundingClientRect().top`, nunca a ojo.
- Contraste sobre foto: si el velo no da AA, se sube el velo; no se cambia el texto.
- Tras editar HTML: contar `<div>` abiertos/cerrados, un solo `<h1>`, `alt` en todas las `<img>`.

## Decisiones de diseño ya tomadas (no revertir sin motivo)

- **Sin diagonales decorativas.** Se probaron dos rayones rojos y un corte diagonal de sección: en render se leían como errores. El ángulo del tejado se descartó como motivo.
- **El hero es la Plaza Mayor de Medina**, obra suya. No sustituir por otra foto ni por stock.
- **Descripciones de servicios alineadas por fila** con flex + `margin-top: auto` (se probó `subgrid`, no alineaba en este grid).
- **Navegación visible sin JS**: clase `no-js` en `<html>` que el script retira. No romper ese fallback.

## Estado

Publicado en **https://currodiazdevivar.github.io/furelos-web-demo/** (GitHub Pages, rama `main`, raíz). Cada push a `main` reconstruye.

Pendiente antes de producción: confirmar datos con el cliente, páginas internas, migrar las 23 obras reales del WordPress y montar los 301 desde las URLs actuales.
