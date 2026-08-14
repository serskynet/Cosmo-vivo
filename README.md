# Cosmos Vivo — Galería NASA APOD

Galería astronómica con la imagen/video del día de la NASA, comentarios
públicos en tiempo real y descarga de imágenes. HTML/CSS/JS puro — sin build,
sin dependencias, sin `npm install`.

## 🚀 Publicar en GitHub Pages

1. Crea un repositorio en GitHub (o usa uno existente).
2. Sube **todo el contenido de esta carpeta** a la raíz del repo:
   ```
   index.html
   css/main.css
   js/main.js
   .nojekyll
   ```
3. Ve a **Settings → Pages** en el repositorio.
4. En "Source" elige la rama (`main`) y carpeta `/root`. Guarda.
5. En 1-2 minutos el sitio estará en `https://tu-usuario.github.io/tu-repo/`.

## ⚠️ Los videos no cargan si abres el archivo con doble clic

Si abres `index.html` directamente (protocolo `file://`), YouTube bloquea los
videos por seguridad — no es un error del sitio. Las imágenes y comentarios
sí funcionan en `file://`. Los videos funcionan correctamente en:

- GitHub Pages (`https://`)
- Un servidor local: `python3 -m http.server 8000` → abre `localhost:8000`

## 🔑 Claves configuradas

- **NASA API Key** propia — 1,000 peticiones/hora (en vez de 30/hora de la
  clave demo pública).
- **JSONBin.io** (Master Key + Access Key) — comentarios públicos. Al primer
  comentario de cada imagen se crea un "bin" público automáticamente,
  visible para cualquier visitante del sitio.

Para cambiarlas, edita el bloque `CONFIG` al inicio de `js/main.js`.

## 🏗️ Arquitectura y decisiones técnicas

```
index.html      → estructura completa
css/main.css     → tokens de diseño + todos los estilos
js/main.js       → toda la lógica (NASA API, JSONBin, galería, modal, chat)
.nojekyll        → evita que GitHub procese el sitio con Jekyll
```

**Carga rápida — 1 petición para 9 imágenes**: en vez de pedir cada fecha por
separado, se usa el endpoint de rango de NASA (`start_date` + `end_date`),
que trae 9 imágenes en una sola llamada.

**Imágenes sin `crossOrigin` en el modal**: NASA no envía cabeceras CORS
correctas; ponerlas bloquea la carga. El modal las carga sin ese atributo, y
usa una imagen auxiliar aparte (con `crossOrigin`) solo para leer los
colores dominantes — si eso falla, simplemente no se muestra la paleta.

**Cards usan `url` (versión web ligera), nunca `hdurl`**: la versión HD puede
pesar 10-20MB. Las miniaturas usan la versión web (~200-500KB); el modal
carga `hdurl` solo cuando el usuario abre la imagen en detalle.

**Shimmer con z-index correcto**: el brillo de "cargando" es un
pseudo-elemento `::before` con `z-index:1`; la imagen real tiene
`z-index:2` y nunca queda tapada por el shimmer.

**Descarga real de imágenes**: un `<a download>` simple no funciona con
imágenes de otro dominio. El botón hace `fetch()` → `blob()` →
`URL.createObjectURL()` para forzar la descarga real del archivo. Si falla
(sin conexión, por ejemplo), abre la imagen en pestaña nueva como respaldo.

**Comentarios públicos vía JSONBin.io**: cada imagen tiene un bin JSON propio
(creado al primer comentario), marcado como público — la lectura no requiere
autenticación, así que cualquier visitante ve los mismos comentarios.

## 📋 Límites

- NASA API: 1,000 peticiones/hora.
- JSONBin.io (plan gratuito): 10,000 peticiones/mes, 100,000 registros —
  suficiente para un sitio personal o comunitario pequeño/mediano.

## 🎨 Personalizar colores y tipografía

Todo está centralizado en las variables `:root` al inicio de
`css/main.css` (`--gold`, `--ink1`, `--f-d`, etc.). Cambiar esos valores
actualiza el sitio completo de forma consistente.
