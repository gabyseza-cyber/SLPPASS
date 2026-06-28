---
name: crear-contenido
description: Crear contenido nuevo para SLP PASS: artículos del blog, módulos del curso, secciones de la homepage, preguntas FAQ. Genera el HTML correcto siguiendo el design system del proyecto y lo inserta en el lugar adecuado.
---

# Skill: crear-contenido

SLP PASS es una web estática HTML/CSS/JS en `/home/user/SLPPASS`. No hay framework ni build step — el contenido se edita directamente en los archivos HTML. Para previsualizar cambios se levanta un servidor local y se toma un screenshot con Playwright.

## Servidor local + screenshot (verificación obligatoria)

```bash
# 1. Levantar servidor
cd /home/user/SLPPASS
python3 -m http.server 8743 --directory . &
sleep 2

# 2. Screenshot de cualquier página
NODE_PATH=/opt/node22/lib/node_modules node -e "
const { chromium } = require('playwright');
(async () => {
  const browser = await chromium.launch({
    executablePath: '/opt/pw-browsers/chromium-1194/chrome-linux/chrome',
    args: ['--no-sandbox','--disable-gpu']
  });
  const page = await browser.newPage();
  await page.setViewportSize({ width: 1280, height: 900 });
  await page.goto('http://localhost:8743/RUTA/');
  await page.screenshot({ path: '/tmp/preview.png' });
  await browser.close();
  console.log('OK');
})().catch(e => console.error(e));
"
# 3. Verificar visualmente con Read tool: Read('/tmp/preview.png')
```

Ambos comandos fueron verificados en este contenedor y funcionan. `NODE_PATH` es obligatorio — sin él `require('playwright')` falla aunque esté instalado globalmente.

## Design system

```css
--bg:#f2ede2    /* beige, fondo principal */
--lt:#f8f5ef    /* beige claro, fondo alternativo */
--bdr:#d8d2c6   /* borde */
--tx:#1a2630    /* texto principal */
--txd:#6a7a88   /* texto secundario */
--txb:#0e1820   /* texto fuerte / casi negro */
--ol:#4d6b3a    /* verde oliva — color primario */
--old:#3a5229   /* verde oscuro — hover */
--gd:#b8920a    /* dorado — accent */
```

Fuentes (ya importadas en todas las páginas):
- `Rajdhani` — títulos, nav, labels uppercase
- `Lato` — cuerpo de texto
- `Share Tech Mono` — tags, monospace, metadatos

## Tipos de contenido

### 1. Artículo del blog

**Crear:** `blog/<slug>/index.html`  
**Copiar CSS de:** `blog/como-aprobar-slp-2222/index.html` (bloque `<style>` completo)  
**Añadir card al inicio de `.blog-list` en:** `blog/index.html`  
**Añadir entrada en:** `sitemap.xml` con `<priority>0.8</priority>`

```html
<!-- Estructura del artículo -->
<div class="breadcrumb">
  <a href="/">Inicio</a> · <a href="/blog/">Blog</a> · <span>Título corto</span>
</div>
<div class="article-tags">
  <span class="article-tag">Tag</span>
</div>
<h1 class="article-h1">TÍTULO EN MAYÚSCULAS</h1>
<div class="article-byline">
  Evaluador Acreditado SLP PASS · Mes Año · X min de lectura
</div>
<div class="article-body">
  <p>Intro...</p>
  <h2>Sección</h2>
  <div class="highlight">
    <p><strong>Lo que penaliza:</strong> texto concreto.</p>
  </div>
  <div class="cta-box">
    <div class="cta-pretitle">Siguiente paso</div>
    <h3>TÍTULO CTA</h3>
    <p>Texto breve.</p>
    <a href="https://calendly.com/slppass/30min" class="cta-btn">RESERVAR DIAGNÓSTICO — 35€</a>
  </div>
</div>
```

Card para `blog/index.html`:
```html
<article class="blog-card">
  <div class="blog-card-tag">Tag</div>
  <div class="blog-card-body">
    <div class="blog-card-meta">Evaluador STANAG 6001 · Mes Año · X min</div>
    <h2 class="blog-card-title">Título del artículo</h2>
    <p class="blog-card-excerpt">Descripción de 2 líneas.</p>
    <a href="/blog/<slug>/" class="blog-card-link">LEER ARTÍCULO →</a>
  </div>
</article>
```

---

### 2. Módulo del curso

**Crear:** `curso/modulo-N/index.html` cuando el vídeo esté listo en Vimeo.  
**Copiar cabecera CSS de:** `curso/index.html`

```html
<main style="max-width:800px;margin:0 auto;padding:80px 5vw">
  <div style="font-family:'Share Tech Mono',monospace;font-size:9px;
    color:var(--ol);letter-spacing:3px;text-transform:uppercase;margin-bottom:8px">
    MÓDULO 0N
  </div>
  <h1 style="font-family:'Rajdhani',sans-serif;font-size:clamp(28px,5vw,44px);
    font-weight:700;color:var(--txb);text-transform:uppercase;margin-bottom:24px">
    Título del módulo
  </h1>
  <!-- Vídeo embed Vimeo -->
  <div style="position:relative;padding-bottom:56.25%;height:0;overflow:hidden;margin-bottom:32px">
    <iframe src="https://player.vimeo.com/video/ID_VIMEO"
      style="position:absolute;top:0;left:0;width:100%;height:100%"
      frameborder="0" allowfullscreen></iframe>
  </div>
  <!-- Contenido y ejercicios -->
  <div class="article-body">
    <p>...</p>
    <div class="highlight"><p><strong>Ejercicio:</strong> ...</p></div>
  </div>
</main>
```

---

### 3. Sección nueva en homepage

Insertar en `index.html` entre los comentarios `<!-- ── NOMBRE ──`. Patrón:

```html
<!-- ── NOMBRE SECCIÓN ─────────────────────────── -->
<section style="background:var(--lt);padding:72px 5vw">
  <div style="max-width:960px;margin:0 auto">
    <div style="font-family:'Share Tech Mono',monospace;font-size:10px;
      letter-spacing:4px;color:var(--ol);text-transform:uppercase;
      margin-bottom:12px;display:flex;align-items:center;gap:10px">
      <span style="width:24px;height:2px;background:var(--ol);display:inline-block"></span>
      Label de sección
    </div>
    <h2 style="font-family:'Rajdhani',sans-serif;font-size:clamp(26px,4vw,40px);
      font-weight:700;color:var(--txb);letter-spacing:2px;text-transform:uppercase;
      margin-bottom:10px;line-height:1.1">
      Título
    </h2>
    <!-- contenido -->
  </div>
</section>
```

---

### 4. Pregunta FAQ

Insertar dentro de `<section class="faq">` en `index.html`:

```html
<div class="faq-item">
  <button class="faq-toggle" aria-expanded="false">
    <span class="faq-q">¿Pregunta concreta?</span>
    <span class="faq-icon">+</span>
  </button>
  <div class="faq-a">Respuesta directa. Sin rodeos.</div>
</div>
```

---

## Checklist tras crear contenido

1. Screenshot y verificación visual (ver script arriba)
2. Validar HTML: `python3 -c "from html.parser import HTMLParser; HTMLParser().feed(open('ARCHIVO').read()); print('OK')"`
3. Si es página nueva: añadir a `sitemap.xml`
4. Commit y push:
   ```bash
   git add <archivos>
   git commit -m "Contenido: descripción breve"
   git push origin main
   ```
   Vercel despliega en ~1 minuto.

## Tono y copy

- Audiencia: militares españoles (suboficiales, oficiales) con examen STANAG 6001 próximo
- Objetivo: obtener **SLP 2** (determina destinos en el extranjero y ascensos)
- Tono: directo, técnico, sin adornos. Como hablaría el evaluador en una sesión real.
- Evitar: promesas vagas ("mejora tu inglés"), anglicismos innecesarios, texto genérico
- CTA principal: `https://calendly.com/slppass/consulta-previa-de-nivel` (5 min gratis)
- CTA secundario: `https://calendly.com/slppass/30min` (diagnóstico 35€)
- WhatsApp: `https://wa.me/34620581881`

## Gotchas

- **`source` no definido en páginas nuevas**: los botones con `onclick="gtag(...,{source:source})"` necesitan el snippet de analytics al final del `<body>`. Copiar de `curso/index.html`.
- **Nav `active`**: en cada página el enlace correspondiente lleva `class="nav-link active"`.
- **`display:none` + `display:flex` en el mismo `style=""`**: el segundo gana y anula al primero. Evitar duplicados en estilos inline.
- **Puerto 8743**: usar este puerto para el servidor local — el 8080 puede estar ocupado.
- **`NODE_PATH` obligatorio**: sin `NODE_PATH=/opt/node22/lib/node_modules`, `require('playwright')` falla con MODULE_NOT_FOUND aunque playwright esté instalado globalmente.
