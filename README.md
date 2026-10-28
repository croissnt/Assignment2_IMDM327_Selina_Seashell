# Merillia Interactive Seashells

An interactive Unity piece where your hands keep a tilted ring of glowing "ocean" alive. Seashells orbit the ring, rise and glow when you reach in, and sink back to rest when you let go.

![Reference still from the film](images/film-reference.png)

*Reference still from* Barbie in A Mermaid Tale 

---

## Inspiration

The piece is inspired by the scene in *Barbie in A Mermaid Tale* where the queen spins **Merillia**, the flowing lines of light, to keep the ocean alive. In the film, the seashells around her power up and down and shift in color as she works, as if the whole sea is breathing with her gestures.

This project translates that moment into an interaction:

| In the film | In this project |
| --- | --- |
| The queen spins Merillia | Your hand gestures stir a ring of 120 glowing particle trails |
| Seashells power up as she casts | Seashells stand upright and brighten when a hand is tracked |
| The ocean calms when she rests | Shells lie flat and dim, and the trails fade back to a quiet, pale ring |
| The water changes color as it comes alive | Trails shift from near-white to saturated blue-teal with hand energy |

---

## How the interaction works

### 1. Hand tracking
A `MediaPipeBodyTracker` component supplies each hand's position, whether it is tracked, and whether it is pinching. `InteractiveBody` reads those values every physics step.

### 2. Pinch to conjure, move to stir
- A **pinch** places an invisible interaction point on the ring's plane, shown as a small glowing sphere.
- How fast the hand moves is turned into an **actuation** value between 0 and 1. A still pinch barely does anything, and a quick shake does a lot.
- Actuation pulls the particles toward your hand with a gravity-like force, so the ring gets swept and swirled by your gesture. It decays smoothly when you slow down.

### 3. The ocean ring (the Merillia lines)
- 120 hidden particles orbit a tilted ring around a central mass, each leaving a **trail**. The trails are the flowing lines.
- Simple **boids rules** (separation, alignment, cohesion) keep the particles flowing together like a current.
- Gentle sine-wave ripples keep the ring alive even when nothing is touching it.
- When your hand lets go, a **ring recovery** force pulls everything back to a calm, circular orbit.

### 4. Color follows energy
A smoothed `handInfluence` value rises quickly when you stir and fades slowly when you stop. It drives the trail **saturation**, so the lines go from pale and ghostly to vivid blue-teal as the ocean "powers up".

### 5. Seashells power up and down
Three seashells orbit the ring, evenly spaced, drifting with the nearby particle flow.

- **Rise:** while a hand is tracked, a `shellRise` value eases toward 1, tilting each shell from lying flat to standing upright. When the hand is gone, it eases back to 0.
- **Glow:** the same value blends each shell's **emission** from dim to bright.
- **Speed scale:** faster shells swell slightly, then relax to normal size when they slow down.
- **Bloom:** a Bloom post-processing effect turns the bright emission into a soft glow.

---

## Code overview (vaguely)

Everything lives in `InteractiveBody.cs`.

| Part | What it does |
| --- | --- |
| `Start()` | Builds the ring particles, trails, hand markers, and shells |
| `FixedUpdate()` | Runs the particle physics, boids, hand forces, and ring recovery |
| `UpdateSeashells()` | Moves the shells along the ring, spaces them out, and applies tilt and scale |
| `Update()` | Smooths hand influence and refreshes visuals every frame |
| `UpdateTrailColors()` | Maps hand influence to trail saturation and opacity |
| `UpdateSeashellVisuals()` | Eases the rise value and sets shell emission |
| `CalculateGravity()` | The attraction force used between particles and toward your hand |

---

## Setup

1. Use **Unity 6 with URP** (built with 6000.4).
2. Add `InteractiveBody` to an empty GameObject and assign a **Seashell Prefab**.
3. Make sure a `MediaPipeBodyTracker` exists in the scene.
4. Enable bloom:
   - Turn on **Post-processing** in the URP Renderer Data asset.
   - Tick **Post Processing** on the Main Camera.
   - Add a **Global Volume** with a **Bloom** override.
   - Turn on **HDR** in the URP Asset.
5. Give the shell material a **Lit** shader (so it has an emission color), and use HDR-capable materials for trails or particles that should glow.

## Tuning

| Setting | Effect |
| --- | --- |
| `shellFlatEuler` | Tilt that lays the shells flat from the prefab's upright pose |
| `shellRiseSpeed` | How quickly shells rise and settle |
| `shellIdleEmission` / `shellActiveEmission` | Dim and bright glow levels |
| `shakeSensitivity` | How easily hand motion becomes energy |
| `idleSaturation` / `activeSaturation` | How pale or vivid the trails get |
| `ringTilt` | Angle of the ocean ring |

---

## Credits

Inspired by *Barbie in A Mermaid Tale*. Hand tracking via MediaPipe. All film imagery belongs to its respective owners.
