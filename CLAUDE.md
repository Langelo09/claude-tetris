# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Proyecto

Tetris en JavaScript vanilla + HTML5 Canvas + CSS. Sin `package.json`, sin build, sin linter, sin tests. El código, la UI y el README están en español; mantén ese idioma.

## Ejecutar

Abrir `index.html` directamente en el navegador, o servir la carpeta con cualquier servidor estático (`python -m http.server 8000`, `npx serve .`) y abrir `http://localhost:8000`.

## Arquitectura

Tres archivos: `index.html` (DOM + dos canvas: `#board` 300×600 y `#next-canvas` 120×120), `style.css` y `game.js` (toda la lógica, script clásico con `'use strict'`, sin módulos).

Puntos de `game.js` que conviene saber antes de editar:

- **Estado global mutable**: `board, current, next, score, lines, level, paused, gameOver, lastTime, dropAccum, dropInterval, animId` se declaran con un solo `let` y se reinician en `init()` (que también sirve de reinicio desde el botón).
- **Tablero**: matriz `ROWS × COLS` con `0` o índice de color 1–7. El mismo índice indexa `COLORS` y `PIECES` (el índice 0 es `null` en ambos), por lo que añadir una pieza exige tocar los dos arrays y el `Math.random() * 7` en `randomPiece()`.
- **Ciclo de vida de una pieza**: `lockPiece()` = `merge()` → `clearLines()` → `spawn()`. `spawn()` detecta el game over (la pieza nueva ya colisiona) y llama a `endGame()`, pero sigue ejecutando `drawNext()`.
- **Bucle**: `loop(ts)` acumula `dropAccum` con `requestAnimationFrame`; la caída automática está duplicada inline en `loop` (no usa `softDrop`, que además suma puntos). Pausar/terminar cancela el frame con `cancelAnimationFrame(animId)`; reanudar reinicia `lastTime` y llama a `loop` a mano.
- **Entrada**: un único listener `keydown` usa `e.code` (no `e.key`).
- **Puntuación/velocidad**: `LINE_SCORES[cleared] * level`; hard drop +2 por celda, soft drop +1; `dropInterval = max(100, 1000 - (level-1)*90)`, nivel cada 10 líneas.

Si cambias `COLS`, `ROWS` o `BLOCK`, actualiza también `width`/`height` del `<canvas id="board">` en `index.html` (`COLS*BLOCK` × `ROWS*BLOCK`). El tamaño de celda del canvas de vista previa (`NB = 30`) está fijo dentro de `drawNext()`.
