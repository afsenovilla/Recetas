# 🥘 Mi Recetario

Recetario web personal, ligero y sin dependencias. Una sola página estática
(HTML + CSS + un `recipes.json`) pensada para usar desde el móvil mientras
cocinas. Funciona en GitHub Pages sin backend.

## ✨ Características

- **Tarjetas de recetas** con emoji, categoría, tiempos, raciones y dificultad.
- **Buscador** que mira nombre, descripción, categoría, etiquetas e **ingredientes**.
- **Filtros** por dieta (🌿 vegetariano / 🌱 vegano) y por exclusión de alérgenos (sin gluten, sin lácteos, etc.).
- **Sección de alérgenos** y etiqueta de dieta en cada receta.
- **Modo cocina** a pantalla completa: un paso grande cada vez, barra de progreso, navegación por teclado (← →), gestos de swipe y salida con Esc.
- **Enlace a la receta original** cuando existe.
- **Importar desde un enlace**: pega la URL de una receta y rellena el formulario automáticamente (ver más abajo).
- **Traducción al inglés** del detalle de la receta.
- **Añadir / editar / borrar** recetas desde la propia interfaz (protegido por contraseña).
- **Modo noche** con detección automática de la preferencia del sistema.
- **Mantener pantalla encendida** (wake lock) para que no se apague mientras cocinas.
- **Escala bien** de móvil a escritorio.

## 📁 Estructura

```
.
├── index.html      # Toda la lógica de la app (HTML + JS en un único fichero)
├── styles.css      # Estilos y temas (claro / oscuro)
├── recipes.json    # Recetas "de fábrica" — la fuente de verdad
└── README.md
```

## 🗃️ Cómo se guardan los datos (importante)

Esta es la parte que conviene entender antes de tocar nada. Hay **dos capas
separadas** y se fusionan al cargar:

1. **Recetas de fábrica** → `recipes.json`. Es la fuente de verdad. Se lee
   **siempre** al arrancar la app.
1. **Cambios del usuario** → `localStorage` (clave `recetario_user`). Guarda
   **solo** lo que haces tú, en tres apartados:
- `added`: recetas nuevas que creas o importas.
- `edited`: ediciones que haces sobre recetas de fábrica (guarda tu versión).
- `hidden`: ids de recetas de fábrica que has borrado.

Al iniciar, la app parte del JSON, oculta lo borrado, aplica tus ediciones
encima y añade tus recetas propias. El resultado es lo que ves.

**Consecuencias prácticas:**

- Si actualizas `recipes.json` en el repo (añades o cambias recetas), esos
  cambios aparecen solos la próxima vez que se cargue la app. No hay que borrar
  caché ni tocar la consola.
- Tus recetas creadas a mano **nunca se pierden** aunque cambie el repo.
- Las recetas nuevas usan un id basado en `Date.now()`, por lo que nunca chocan
  con los ids pequeños del JSON de fábrica.

> **Migración automática:** la primera vez que se carga esta versión, se
> recuperan del esquema antiguo (`recetario_v4`) las recetas que hubieras
> añadido a mano y se borra esa clave. No se pierde nada.

## 📝 Formato de una receta

Cada entrada de `recipes.json` sigue esta forma:

```json
{
  "id": 1,
  "name": "Galletas de Mantequilla",
  "category": "Repostería",
  "description": "Texto breve para la tarjeta y el detalle.",
  "emoji": "🍪",
  "color": "#f0d9b5",
  "accent": "#b07030",
  "prepTime": "20 min",
  "cookTime": "12 min",
  "totalTime": "2 h 30 min",
  "servings": "~600 g",
  "difficulty": "Fácil",
  "diet": null,
  "sourceUrl": "",
  "tags": ["galletas", "repostería"],
  "allergens": ["gluten", "lacteos", "huevos"],
  "ingredients": [
    { "amount": "285 g", "name": "harina de repostería" }
  ],
  "steps": [
    "Primer paso de la elaboración."
  ],
  "tips": "Consejos, variaciones o conservación (opcional)."
}
```

Notas sobre los campos:

- **`diet`**: `null`, `"vegetariano"` o `"vegano"`. El filtro de vegetariano
  también muestra las veganas.
- **`allergens`**: lista de ids. Disponibles: `gluten`, `lacteos`, `huevos`,
  `frutos_secos`, `soja`, `pescado`, `marisco`, `sesamo`, `apio`, `mostaza`,
  `sulfitos`, `altramuces`, `moluscos`, `cacahuetes`. Lista vacía = sin alérgenos.
- **`sourceUrl`**: enlace a la receta original. Vacío = no se muestra.
- **`amount`** con valor `"—"` sirve para escribir subtítulos dentro de la lista
  de ingredientes (p. ej. `"PREFERMENTO:"`).

## 🔗 Importar desde un enlace

El formulario de nueva receta permite pegar la URL de una web de cocina y
rellenar los campos automáticamente. Por debajo:

1. Descarga el HTML a través de un proxy CORS público (necesario porque la app
   es estática y no puede leer otras webs directamente desde el navegador).
1. Busca el bloque de datos estructurados [`schema.org/Recipe`](https://schema.org/Recipe)
   (JSON-LD) que incluyen casi todas las webs de recetas serias.
1. Extrae nombre, descripción, tiempos, raciones, ingredientes y pasos, y
   rellena el formulario para que **revises antes de guardar**.

Limitaciones: la dieta y los alérgenos no se autocompletan (se marcan a mano),
el contenido llega en el idioma de la fuente, y si una web no tiene JSON-LD o el
proxy falla, conviene probar otro enlace.

## 🍳 Recetas incluidas

Galletas de Mantequilla · Vegan Cinnamon Rolls · Chocolate Chip Cookies ·
Pan de Masa Madre · Roscón de Reyes · Naranjas Confitadas · Bánh Mì · Crumpets

## 🚀 Uso y desarrollo

Al ser estático, basta con servir los ficheros. En local:

```bash
# Cualquier servidor estático vale; con Python:
python3 -m http.server 8000
# y abre http://localhost:8000
```

> Conviene servirlo por HTTP (no abrir el `index.html` con `file://`) para que
> el `fetch` de `recipes.json` funcione sin problemas.

Para publicar, sube los ficheros a la rama que use **GitHub Pages**.

## 🔒 Sobre la contraseña

Añadir, editar y borrar recetas está protegido por una contraseña sencilla en el
cliente. Es solo un freno para evitar cambios accidentales: al ser una app
estática, **no es seguridad real**. No guardes nada sensible asumiendo que esa
contraseña protege algo.

## 🛠️ Tecnología

HTML, CSS y JavaScript puro (vanilla). Sin frameworks, sin build, sin
dependencias. Tipografías Playfair Display y DM Sans vía Google Fonts.
