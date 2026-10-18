# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Comandos

No hay build, linter, gestor de paquetes ni suite de tests. Para ejecutar el juego:

- Abrir `index.html` directamente en el navegador, o
- Servir la carpeta con un servidor estático, p. ej. `python3 -m http.server 8000` y abrir `http://localhost:8000`.

No hay forma de ejecutar un test individual: no existen tests. Para verificar cambios hay que probarlos manualmente en el navegador.

## Arquitectura

Juego Tetris en JavaScript vanilla (HTML5 Canvas + CSS), sin dependencias. Son tres archivos que colaboran:

- `index.html` define el canvas del tablero (`#board`, 300×600), el canvas de la siguiente pieza (`#next-canvas`), el panel de marcadores (`#score`, `#lines`, `#level`) y el overlay de pausa/game over (`#overlay`). Carga `game.js` al final del `<body>`, así que el script se ejecuta con el DOM ya disponible.
- `style.css` aplica el tema oscuro y los overlays. No afecta a la lógica.
- `game.js` contiene toda la lógica, en el ámbito global (`'use strict'`), sin módulos. Llama a `init()` al cargar.

### Estado en `game.js`

Variables globales de partida: `board` (matriz `ROWS × COLS`, 0 = vacío, 1–7 = índice de color/pieza), `current` y `next` (objetos `{type, shape, x, y}`), además de `score`, `lines`, `level`, `dropInterval`, `dropAccum`, `paused`, `gameOver` y `animId`. `init()` reinicia todo; el botón de reinicio lo vuelve a llamar.

### Bucle y ciclo de una pieza

- `loop(ts)` (requestAnimationFrame) acumula el tiempo y, al superar `dropInterval`, baja la pieza o llama a `lockPiece()`. Después dibuja con `draw()`.
- Los eventos de teclado (`keydown`) actúan directamente sobre `current` y luego llaman a `updateHUD()`. `P` pausa tanto si el juego está en pausa como si no.
- `lockPiece()` encadena `merge()` → `clearLines()` → `spawn()`. Si la pieza nueva colisiona al aparecer, `spawn()` llama a `endGame()`.

### Reglas clave

- Colisiones: `collide(shape, ox, oy)` es la única comprobación de validez; la usan movimiento, caída, rotación, ghost y spawn.
- Rotación: `rotateCW` gira la matriz y `tryRotate` prueba desplazamientos `[0, -1, 1, -2, 2]` (wall kicks).
- Puntuación: `LINE_SCORES[n] * level`. Hard drop suma 2 por celda; soft drop suma 1 por fila. Ojo: el nivel se recalcula en `clearLines()` después de sumar la puntuación de esas líneas.
- Velocidad: `dropInterval = max(100, 1000 − (level − 1) × 90)`, con nivel = `floor(lines / 10) + 1`.
- Ghost: `ghostY()` proyecta la posición de caída y se dibuja con `globalAlpha = 0.2`.

### Piezas y colores

Las formas están en `PIECES` (matrices cuadradas con el número de color de la pieza) y los colores en `COLORS`, indexados por el mismo número. Si cambias `COLS` o `ROWS`, actualiza también el tamaño del canvas en `index.html` (`COLS × BLOCK` por `ROWS × BLOCK`).
