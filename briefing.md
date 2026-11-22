# Briefing — Demo portada web Construcciones Furelos

## Qué es
Demo de **una sola página** (portada) para **Construcciones Furelos Louzao, S.L.**, constructora de Medina de Pomar (Burgos). Es una **propuesta interna** para enseñársela al dueño. NO es la web oficial.

## Stack obligatorio
- `index.html` + `styles.css` + `script.js` (JS mínimo, sin librerías, sin frameworks, sin CDN de JS).
- **Cero dependencias**: nada de Tailwind, Bootstrap, jQuery ni fuentes por CDN de pago. Google Fonts sí se permite (`<link>`), o `@font-face` local si no hay red.
- Responsive real: 390 px, 768 px, 1024 px, 1440 px. Sin scroll horizontal en ningún ancho.
- Accesible: un solo `<h1>`, jerarquía correcta, `alt` en todas las imágenes, foco visible, `prefers-reduced-motion` respetado, contraste AA.

## Identidad (sale del logo y de su web, NO inventar)
- **Gris carbón `#383838`** (color principal del logo, 84 % de sus píxeles)
- **Rojo `#b81818`** (color del símbolo, 9 % del logo)
- **Blanco roto `#f8f8f8`** (versión inversa del logo)
- Neutros de apoyo derivados del carbón: `#1e1e1e`, `#6b6b6b`, `#e6e3de`
- Tipografía del sitio actual: **Open Sans**. Para los titulares usar una grotesca industrial con más carácter: **Archivo** (700/800) o **Barlow Condensed** (600/700). Cuerpo: Open Sans 400/600. Máximo 2 familias.
- **Símbolo**: un círculo fino rojo que encierra el tejado a dos aguas de una casa.
- **Elemento firma (obligatorio, aquí se gasta el riesgo)**: el ángulo del tejado (~35°) se convierte en el módulo del layout. Una línea roja a 35° que cruza la página en el hero y en la banda de obras, y **recortes de foto con ese ángulo** (clip-path) en la rejilla de obras. Nada de tarjetas con border-radius 16 px y sombras difusas: aquí las esquinas son rectas y las diagonales mandan.
- Fondo general blanco roto `#f8f8f8`; el carbón se usa en bloques completos (hero, cierre), no en trocitos.

## Imágenes disponibles (ya en `assets/opt/`, usarlas por ruta relativa)
- `hero1.jpg` — **Plaza Mayor de Medina de Pomar pavimentada** (obra suya). ES LA FOTO DEL HERO. Necesita velo oscuro y texto legible encima.
- `hero2.jpg` — retroexcavadora amarilla cargando escombros en obra urbana. Usar en la banda de "obra civil", recortada.
- `IMG_9088.jpg` — cubierta C- Agreda, Medina de Pomar (vertical)
- `IMG_7300-1.jpg` — piscinas Puente Villanueva, Medina de Pomar (horizontal)
- `IMG_7615.jpg` — nave en polígono, Medina de Pomar (horizontal)
- `IMG_4406.jpg` — estructura Larrabizker, Munguía (vertical)
- `IMG_3360.jpg` — estructura Sopeñano, Valle de Mena (vertical)
- `IMG_3108.jpg` — estructura Los Llamares, Villasana de Mena (vertical)
- `../assets/logo.png` (logo sobre claro, 1920×886) y `../assets/logo_inv2.png` (logo sobre oscuro) — copiar a `assets/` y referenciar desde ahí.

## Estructura de la página (en este orden)
1. **Cinta superior fina** (no un banner enorme): «Propuesta de diseño · demo no oficial» + enlace a `https://construccionesfurelos.com`.
2. **Cabecera**: logo a la izquierda, nav (Empresa · Actividad · Obras · Contacto), teléfono `947 147 242` como botón. En móvil, menú desplegable con JS mínimo y accesible (`aria-expanded`).
3. **Hero a sangre** con `hero1.jpg` + velo. Titular y subtítulo a la izquierda:
   - H1: «Construimos en Las Merindades desde 1988»
   - Subtítulo: «Obra pública, edificación y rehabilitación. Proyecto, ejecución y entrega con un solo interlocutor.»
   - Dos CTA: «Ver obras» (ancla) y «Llamar 947 147 242» (`tel:`).
   - Franja de datos bajo el hero, con separadores verticales: **+30 años** · **70 % de licitaciones ganadas** · **Medina de Pomar, Burgos**
4. **Servicios** (6 bloques numerados 01–06, sin iconos de stock): Obra civil pública y privada · Edificación y promoción · Rehabilitación y reformas · Accesibilidad y eficiencia energética · Retail e interiorismo comercial · Arquitectura. Una línea de descripción real por bloque (máx. 18 palabras).
5. **Obras** (rejilla de 6, con las fotos verticales y una horizontal; nombre + lugar reales):
   - Cubierta C- Agreda · Medina de Pomar
   - Piscinas Puente Villanueva · Medina de Pomar
   - Nave en polígono · Medina de Pomar
   - Estructura Larrabizker · Munguía
   - Estructura Sopeñano · Valle de Mena
   - Estructura Los Llamares · Villasana de Mena
6. **Banda de confianza** (sin certificados ISO: los que anuncian están retirados). Tres puntos: «Inscrita en el Registro Oficial de Licitadores del Estado» · «Departamento de arquitectura propio» · «Ámbito: Merindades, Burgos, País Vasco, Cantabria y La Rioja».
7. **Cierre / contacto**: bloque carbón a sangre, logo inverso, dirección `Avda. Bilbao, 11 Bajo · 09500 Medina de Pomar (Burgos)`, teléfono y email `furelos@construccionesfurelos.com`. Sin mapa embebido (nada de iframes de terceros).
8. **Pie**: aviso de demo (no oficial), año 2026, y «Diseño propuesto por [pendiente]» sin rellenar con datos falsos.

## Reglas duras
- **Nada de texto de relleno**: ni lorem ipsum, ni «Lorem», ni frases genéricas inventadas.
- **Nada de datos inventados**: no añadir cifras, años, certificaciones ni clientes que no estén en este briefing.
- **No usar más fotos que las 9 listadas** (no descargar stock).
- No usar emojis en la página.
- No animaciones que bloqueen la lectura; transiciones ≤ 200 ms; reveal sutil con IntersectionObserver y desactivado con `prefers-reduced-motion`.
- El HTML tiene que estar **balanceado** (mismo número de `<div>` abiertos y cerrados) y validar sin errores graves.
- Comentario `<!-- -->` al inicio de cada sección para poder localizarlas.
