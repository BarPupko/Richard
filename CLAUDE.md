# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Cute Richard - Corgi Adventures Game Suite** is a browser-based collection of mini-games featuring a cute corgi character named "Richard." The project is built entirely with vanilla HTML5, CSS3, and JavaScript with no frameworks, build tools, or dependencies.

## Technology Stack

- **Pure HTML5** - No template engines
- **Vanilla JavaScript** - No frameworks (React, Vue, etc.)
- **CSS3** - No preprocessors
- **HTML5 Canvas API** - For game rendering
- **No build system** - Files run directly in browser
- **No backend** - Pure client-side application

## Running the Project

Since this is a static HTML project with no build step:

1. **Using a browser directly:**
   ```
   Open richard-index.html in a web browser
   ```

2. **Using a local server (recommended for development):**
   ```bash
   # Python 3
   python -m http.server 8000

   # Python 2
   python -m SimpleHTTPServer 8000

   # Node.js (if http-server is installed)
   npx http-server -p 8000
   ```
   Then navigate to `http://localhost:8000/richard-index.html`

## Project Structure

The project has a flat file structure with only 3 HTML files:

- **richard-index.html** - Main menu/hub page with a 3x3 grid of game options
- **fetch-game.html** - Implemented game where Richard catches tennis balls and avoids bones
- **index.html** - Empty placeholder file

All CSS and JavaScript are embedded inline within each HTML file (no separate .css or .js files).

## Architecture

### Game Hub Pattern

The project uses a hub-and-spoke pattern:
- **richard-index.html** serves as the central hub
- Each game is a separate HTML file with its own inline CSS/JS
- Games return to the hub via a `goBack()` function that navigates back to richard-index.html

### Game Implementation Pattern (fetch-game.html)

Each game follows this structure:
1. **Canvas Setup** - Full-window responsive canvas with resize handling
2. **Game State** - Object tracking score, lives, running state, etc.
3. **Drawing Functions** - Custom canvas drawing code for game entities (corgi, balls, bones)
4. **Game Loop** - Uses `requestAnimationFrame` for animation
5. **Input Handling** - Mouse and touch event handlers for cross-platform support
6. **Collision Detection** - Manual bounding box checks
7. **Modal System** - Start screen and game over screen overlays

### Canvas Rendering Approach

Games use **immediate mode rendering**:
- Canvas is cleared each frame with `clearRect()`
- All entities are redrawn from scratch using Canvas API primitives (arc, ellipse, fillRect, quadraticCurveTo)
- No canvas libraries or sprite systems
- All graphics are procedurally drawn (no image assets)

### Input Handling

Both mouse and touch events are handled:
- Mouse: `mousemove` for desktop
- Touch: `touchmove`, `touchstart`, `touchend` for mobile
- Smooth following behavior using linear interpolation (`dx * 0.15`)

## Game Status

Currently implemented:
- **Fetch Game** - Fully functional catch/avoid game

Planned (show "coming soon" alerts):
- Bone Collector
- Ball Chase
- Corgi Run
- Park Adventure
- Treat Hunt
- Nap Time
- Party Tricks
- Cuddle Time

## Responsive Design

All pages include:
- `<meta name="viewport" content="width=device-width, initial-scale=1.0">`
- Media query breakpoint at `@media (max-width: 480px)` for mobile
- Touch event handlers for mobile interaction
- Responsive canvas sizing that adapts to window dimensions

## Code Style Notes

- All code is vanilla JS (ES5/ES6 compatible)
- No TypeScript, no transpilation
- Inline styles and scripts within HTML
- Functions are defined globally within script tags
- Uses modern canvas features but maintains broad browser compatibility
- Color schemes: Purple/pink gradients for menu, brown/tan for fetch game

## Adding New Games

To add a new game:

1. Create a new HTML file (e.g., `bone-collector-game.html`)
2. Copy the structure from [fetch-game.html](fetch-game.html) as a template
3. Update the game-specific logic (canvas drawing, collision detection, scoring)
4. Link from [richard-index.html](richard-index.html) by updating the onclick handler for the corresponding game tile
5. Ensure the `goBack()` function navigates to `richard-index.html`
6. Make it responsive with proper touch/mouse handling

## Key Files to Understand

- **richard-index.html:44-169** - Game grid layout and navigation structure
- **fetch-game.html:123-162** - Corgi character drawing function (demonstrates canvas usage pattern)
- **fetch-game.html:164-234** - Game object classes (Ball and Bone)
- **fetch-game.html:236-299** - Main game loop and collision detection
- **fetch-game.html:301-324** - Input handling for cross-platform support
