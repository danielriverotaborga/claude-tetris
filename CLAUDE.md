# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Proyecto

Tetris en JavaScript vanilla + Canvas 2D. Sin dependencias, `package.json`, build, lint ni tests. Mantener así salvo petición expresa. Idioma de UI, README y comentarios: español (etiquetas del panel en inglés).

## Comandos

```bash
start index.html               # abrir directo (Windows)
python -m http.server 8000     # o servidor local: http://localhost:8000
npx serve .                    # alternativa con Node
node --check game.js           # única verificación automática: sintaxis
```

Cambios de comportamiento se verifican jugando en el navegador.

## Arquitectura

`game.js` contiene toda la lógica: script clásico (sin módulos) cargado al final de `<body>`, estado en variables globales, se arranca con `init()` (también reinicia). Acoplado a `index.html` por IDs del DOM.

**Dos vías que mutan el juego:** `loop` (gravedad vía `requestAnimationFrame` + acumulador `dropAccum`) y el listener `keydown` (movimiento, rotación, drops, pausa). Ambas acaban en `lockPiece()` → `merge()` → `clearLines()` → `spawn()` → posible `endGame()`. Detener o reanudar el bucle se hace con `cancelAnimationFrame(animId)` / llamada a `loop`, desde `init`, `togglePause` y `endGame`; cualquier cambio de estados debe revisar ambas vías.

**HUD manual:** modificar `score`/`lines`/`level` no repinta; hay que llamar `updateHUD()`. `drawNext()` solo se ejecuta en `spawn()`; `draw()` en cada frame.

**Overlay único** (`#overlay`) compartido entre PAUSA y GAME OVER.

## Invariantes

- Índice de tipo de pieza (1–7) = índice en `PIECES` = índice en `COLORS` = valor de celda en `board` (0 = vacía). Los números dentro de cada matriz de `PIECES` son ese índice. `randomPiece()` tiene el `7` hardcodeado: añadir piezas exige cambiarlo.
- Piezas son matrices cuadradas; `rotateCW` + wall kicks horizontales en `tryRotate` (no es SRS).
- `COLS`/`ROWS`/`BLOCK` deben coincidir con `width`/`height` de `<canvas id="board">`. La vista previa usa su propio tamaño (`NB = 30`, rejilla 4×4, canvas 120×120).
- Velocidad inicial en `init()`, curva por nivel en `clearLines()`: cambiar ambas coherentemente.
- Si cambian controles, constantes o puntuación, actualizar `README.md` (y la lista de controles en `index.html`).
