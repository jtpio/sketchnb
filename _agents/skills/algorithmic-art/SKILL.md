---
name: algorithmic-art
description: Create generative algorithmic art in Jupyter notebooks using p5.js. Use when asked to create generative art, creative coding, algorithmic visuals, or p5.js sketches in a notebook.
---

# Algorithmic Art Generation in Notebooks

This skill creates generative art in Jupyter notebooks using the [p5.js kernel for JupyterLite](https://github.com/jupyterlite/p5-kernel). It follows a two-phase process: developing a computational aesthetic philosophy, then expressing it through code cells.

## p5.js Kernel Runtime Model

Understanding how the kernel works is essential for writing correct notebooks.

### Code Registration, Not Execution

When you run a code cell, the kernel **registers** the code (variables, functions, classes) in an internal code registry — it does not render anything visually. The registry uses AST-based deduplication, so **later definitions of the same function or variable override earlier ones**. This means you can redefine `setup()` or `draw()` in a later cell and it will replace the previous version.

### `%show` Renders the Sketch

The `%show` magic is the **only** way to display a sketch. When executed, it:

1. Bootstraps a fresh p5.js instance inside an iframe
2. Loads any ES module imports
3. Combines all registered code from previous cells (deduplicated)
4. Starts the p5 sketch (`setup()` then `draw()` loop)

Without `%show`, nothing is displayed — code cells only register definitions.

### Live Updates

After a `%show` cell has been displayed, re-executing any code cell automatically updates all existing `%show` displays with the new code. This enables live tweaking of parameters and functions without re-running `%show`.

### Global Mode

The kernel runs p5.js in **global mode**. All p5.js functions and constants are available as globals — no `p5.` prefix needed:

- **Drawing**: `createCanvas`, `background`, `fill`, `stroke`, `noFill`, `noStroke`, `circle`, `rect`, `ellipse`, `line`, `point`, `triangle`, `quad`, `arc`, `beginShape`, `vertex`, `endShape`
- **Color**: `color`, `colorMode`, `lerpColor`, `red`, `green`, `blue`, `alpha`, `hue`, `saturation`, `brightness`
- **Math**: `random`, `noise`, `map`, `lerp`, `constrain`, `dist`, `abs`, `floor`, `ceil`, `round`, `sin`, `cos`, `atan2`, `pow`, `sqrt`, `log`, `exp`, `min`, `max`
- **Vectors**: `createVector`, `p5.Vector.fromAngle`, `p5.Vector.random2D`
- **Transform**: `translate`, `rotate`, `scale`, `push`, `pop`, `applyMatrix`
- **Constants**: `PI`, `TWO_PI`, `HALF_PI`, `QUARTER_PI`, `TAU`, `CENTER`, `CORNER`, `CORNERS`, `RADIUS`, `CLOSE`
- **System variables**: `width`, `height`, `mouseX`, `mouseY`, `pmouseX`, `pmouseY`, `frameCount`, `deltaTime`, `windowWidth`, `windowHeight`, `innerWidth`, `innerHeight`
- **Seeding**: `randomSeed`, `noiseSeed`
- **Control**: `noLoop`, `loop`, `frameRate`, `redraw`
- **Blend modes**: `blendMode`, `ADD`, `MULTIPLY`, `SCREEN`, `OVERLAY`, `DIFFERENCE`

### ES Module Imports

The kernel supports ES module imports from npm (auto-resolved via jsDelivr), GitHub, or direct URLs:

```javascript
// npm package (default import)
import confetti from 'canvas-confetti';

// Named imports
import { shuffle, debounce } from 'lodash-es';

// Namespace import
import * as d3 from 'd3';

// From GitHub
import something from 'gh/user/repo/file.js';

// Direct URL
import lib from 'https://example.com/lib.js';
```

Imports are tracked across cells and included when `%show` generates the sketch.

### Event Handlers

p5.js event handler functions can be defined in code cells and will be included in the sketch:

- **Mouse**: `mousePressed()`, `mouseReleased()`, `mouseMoved()`, `mouseDragged()`, `mouseClicked()`, `doubleClicked()`, `mouseWheel()`
- **Keyboard**: `keyPressed()`, `keyReleased()`, `keyTyped()`
- **Window**: `windowResized()`

## p5.js Reference

When you need to look up specific p5.js functions, signatures, or behavior, consult the official documentation:

- **Reference (API docs)**: https://p5js.org/reference/
- **Examples**: https://p5js.org/examples/
- **Tutorials**: https://p5js.org/tutorials/
- **WebGL**: https://github.com/processing/p5.js/wiki/Getting-started-with-WebGL-in-p5

The kernel also provides built-in documentation for p5.js functions via Shift+Tab inspect in the notebook.

## Core Process

### Phase 1: Algorithmic Philosophy

Before writing any code, develop a manifesto for a generative aesthetic movement. Write 4-6 paragraphs in a markdown cell covering:

- **Movement name** (1-2 words) as the cell heading
- A vision articulating the computational aesthetic — what mathematical relationships, emergent behaviors, and algorithmic processes define this movement
- The mechanisms at play: noise functions, particle systems, field dynamics, geometric transformations, or mathematical mappings
- Emphasis on craftsmanship: the algorithm should feel meticulously crafted, the product of deep computational expertise refined through countless iterations
- Creative space for interpretation during implementation

**Key principle:** "Beauty lives in the process, not the final frame." Each execution with a different seed reveals unique variations of the same algorithmic vision.

### Phase 2: Notebook Expression

Implement the philosophy through notebook cells using the p5.js kernel.

## Notebook Structure

Create a notebook (`.ipynb`) with the following cell structure:

### Cell 1 — Title & Philosophy (Markdown)

```markdown
# [Movement Name]

[4-6 paragraphs of algorithmic philosophy developed in Phase 1]
```

### Cell 2 — Parameters & Configuration (Code)

Define all tunable parameters at the top so they are easy to adjust:

```javascript
// Seed for reproducibility
let seed = 42;

// Parameters — tunable system properties
let params = {
  particleCount: 500,
  noiseScale: 0.005,
  flowSpeed: 2.0,
  trailLength: 50,
  colorShift: 0.3,
  decayRate: 0.95,
  angleOffset: PI / 6,
  threshold: 0.4
};

// Palette
let palette = ['#264653', '#2a9d8f', '#e9c46a', '#f4a261', '#e76f51'];
```

### Cell 3+ — Helper Classes & Utilities (Code)

If the algorithm requires helper classes or utility functions, define them in intermediate cells before the main sketch:

```javascript
class Particle {
  constructor(x, y) {
    this.pos = createVector(x, y);
    this.vel = createVector(0, 0);
    // ...
  }

  update() { /* ... */ }
  display() { /* ... */ }
}
```

Use markdown cells between code sections to explain the algorithmic approach when helpful.

### Sketch Definition Cell — `setup()` and `draw()` (Code)

The main p5.js sketch with `setup()` and `draw()`. All parameters, classes, and helpers must be defined in earlier cells first.

```javascript
function setup() {
  createCanvas(innerWidth, innerHeight);
  randomSeed(seed);
  noiseSeed(seed);
  // ... initialization using params, palette, and helper classes from earlier cells
}

function draw() {
  // ... generative algorithm
}
```

### Final Cell — Display with `%show` (Code)

The `%show` magic is **required** to render the sketch. Without it, code cells only register definitions — nothing is displayed. This MUST be the last cell.

```javascript
%show
```

`%show` accepts optional width and height arguments (defaults: `100%` width, `400px` height):

```javascript
%show 100% 800
```

## Technical Requirements

### Seeded Randomness

Every sketch MUST use seeded randomness for reproducibility:

```javascript
randomSeed(seed);
noiseSeed(seed);
```

Changing the seed should produce a distinct but aesthetically coherent variation.

### Parameter Design

Design parameters around system qualities, not pattern types:
- **Quantities**: particle counts, layer counts, iteration counts
- **Scales**: noise scale, size multipliers, amplitude
- **Probabilities**: branching chance, mutation rate, spawn probability
- **Ratios**: aspect ratios, golden ratio subdivisions, density ratios
- **Angles**: rotation offsets, angular velocity, spiral tightness
- **Thresholds**: activation levels, distance cutoffs, opacity limits

### Canvas & Rendering

- Use `createCanvas(innerWidth, innerHeight)` so the sketch fills the `%show` output iframe — this ensures proper rendering regardless of the display size
- Use `background()` in `setup()` for static compositions, or with low alpha in `draw()` for trail effects
- Prefer `noStroke()` with filled shapes, or thin strokes with `noFill()`, depending on the aesthetic
- Use `blendMode()` for layering effects when appropriate

### Color

- Define palettes as arrays of hex strings or HSB values
- Use `colorMode(HSB, 360, 100, 100, 100)` for hue-based generative color
- Ensure visual harmony — colors should feel intentional, not random

### Performance

- For particle systems, keep counts reasonable (100-2000 depending on complexity)
- Use `noLoop()` for static compositions that render once
- Avoid unnecessary per-frame allocations

## Algorithmic Approaches

Choose approaches that serve the philosophy. Some possibilities:

- **Flow fields**: Perlin/simplex noise-driven vector fields guiding particles
- **Recursive subdivision**: Space partitioning with varying rules per depth
- **L-systems / turtle graphics**: Rule-based organic growth
- **Voronoi / Delaunay**: Tessellation-based compositions
- **Attractors**: Strange attractors (Lorenz, Clifford, De Jong) as compositional scaffolding
- **Reaction-diffusion**: Turing patterns and biological simulation
- **Wave interference**: Overlapping sinusoidal or circular wave patterns
- **Stochastic geometry**: Randomized geometric constructions with probabilistic rules

## Craftsmanship Standards

- **Avoid redundancy**: Each algorithmic concept should appear once — do not repeat the same noise or particle logic in different forms
- **Balance complexity and clarity**: The code should be readable; the output should be visually rich
- **Visual hierarchy**: Even within randomness, there should be focal points, rhythm, and spatial balance
- **Reproducibility**: Same seed, same output — always
- **Algorithmic essence over static composition**: The beauty is in the process; each seed is a unique expression of the same underlying system

## What NOT to Do

- Do not use external images or assets — everything is generated algorithmically
- Do not hardcode positions or shapes — let the algorithm determine composition
- Do not create static drawings — the system should be parameterized and seed-driven
- Do not over-comment the code — let the philosophy markdown cell and clean code speak for themselves
- Do not use `mouseX`/`mouseY` for the core composition — interactivity is optional and secondary to the algorithmic output
- Do not forget the `%show` cell — without it, the sketch will not render
