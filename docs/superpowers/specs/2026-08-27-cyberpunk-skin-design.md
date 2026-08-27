# Skin Cyberpunk — Diseño

## Contexto

El proyecto ya tiene infraestructura de skins visuales (`retro`, `neon`, `pastel`, `pixel`) implementada en `game.js` (`drawBlock()` switch) y `style.css` (`body.skin-*` reglas). Este diseño agrega un quinto skin: **Cyberpunk**.

## Objetivo

Skin con bloques cuadrados de estética cibernética y colores brillantes tipo neón, que cambia la apariencia completa del tablero (fondo, grid, bloques), persistido en `localStorage` igual que los demás skins.

## Componentes

### 1. Paleta de colores

Nuevo array `CYBERPUNK_COLORS` en `game.js`, paralelo a `COLORS` (mismo índice 1–8, un color por tipo de pieza):

| Índice | Pieza | Color |
|---|---|---|
| 1 | I | cian eléctrico `#00fff9` |
| 2 | O | amarillo neón `#faff00` |
| 3 | T | magenta `#ff00ea` |
| 4 | S | verde neón `#39ff14` |
| 5 | Z | rojo/rosa neón `#ff073a` |
| 6 | J | azul eléctrico `#00b8ff` |
| 7 | L | naranja neón `#ff8c00` |
| 8 | N | violeta `#b026ff` |

Nueva función `getColor(colorIndex)`:
```js
function getColor(colorIndex) {
  return currentSkin === 'cyberpunk' ? CYBERPUNK_COLORS[colorIndex] : COLORS[colorIndex];
}
```
`drawBlock()` usa `getColor(colorIndex)` en vez de `COLORS[colorIndex]` directamente (todos los `case`, incluido el nuevo).

### 2. Estilo de bloque

Nuevo `case 'cyberpunk'` en `drawBlock()`:
- Relleno oscuro casi negro (`rgba(0,0,0,0.85)`) en todo el área del bloque.
- Borde grueso (2–3px) del color de la pieza, esquinas duras (sin `roundRect`).
- `shadowColor`/`shadowBlur` con el color de la pieza para efecto glow, igual que el patrón usado en `case 'neon'`.
- `shadowBlur = 0` al final (ya lo hace el código común tras el switch).

### 3. Tablero (fondo, grid, borde)

CSS nuevo en `style.css`:
```css
body.skin-cyberpunk #board,
body.skin-cyberpunk #next-canvas {
  background: #000;
  border-color: #ff00ea;
  box-shadow: 0 0 30px rgba(255, 0, 234, 0.35), 0 0 30px rgba(0, 255, 249, 0.25);
}

body.skin-cyberpunk {
  --grid-line: rgba(0, 255, 249, 0.15);
}
```
`drawGrid()` ya lee `--grid-line` vía `getComputedStyle`, no requiere cambios en JS.

### 4. Selector de skin

En `index.html`, agregar opción al `<select id="skin-select">`:
```html
<option value="cyberpunk">Cyberpunk</option>
```

En `game.js`, `applySkinClass()` agrega `'skin-cyberpunk'` a la lista de clases removidas antes de aplicar la nueva:
```js
document.body.classList.remove('skin-retro', 'skin-neon', 'skin-pastel', 'skin-pixel', 'skin-cyberpunk');
```

### 5. Persistencia

Sin cambios: `currentSkin` ya se guarda/lee de `localStorage['tetris-skin']` y el flujo existente (`skinSelect` `change` listener → `applySkinClass` + `draw()` + `drawNext()`) aplica el cambio sin recargar la página.

## Fuera de alcance

- No se toca lógica de juego, colisión, scoring, ni el resto de skins existentes.
- No se agregan nuevas piezas ni cambia `COLS`/`ROWS`/`BLOCK`.

## Testing

Manual: abrir `index.html`, seleccionar "Cyberpunk" en el dropdown, verificar:
- Bloques con borde neón y relleno oscuro, esquinas duras, en tablero, ghost piece y preview "next".
- Fondo canvas negro, grid tenue cian, borde/glow magenta.
- Cambio aplica sin recargar página.
- Recargar página mantiene skin elegido (localStorage).
- Ghost piece (`alpha=0.2`) sigue viéndose correctamente atenuado.
