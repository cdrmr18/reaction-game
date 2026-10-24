# Reaction Timer

A single-page reaction timer game with a 3D background that responds to the game state. Press start, wait for a random delay, then react to the GO signal. The app measures your reaction time in milliseconds and tracks your best time and attempts for the session.

Built with HTML, CSS, vanilla JavaScript, and [Three.js](https://threejs.org/) r128.

## Run it

1. Download `index.html`.
2. Open it in any modern browser with an internet connection.

There is no build step and nothing to install. The internet connection is needed to load Three.js (from cdnjs) and the fonts (from Google Fonts).

## How to play

1. Start a round with the **Start round** button, a tap or click on the pad, or the spacebar.
2. Wait while the pad says **Wait…**. The delay is random, between 2 and 5 seconds.
3. When the pad says **GO!**, press as fast as you can.
4. Pressing before GO is a **false start**. No time is recorded.

Your best time and attempt count are shown below the pad. Attempts counts rounds that produced a time, not false starts. Press again to try another round without reloading. An optional checkbox plays a short sound with the GO signal (off by default).

## Controls

| Input | Action |
| --- | --- |
| Mouse click or touch on the pad | Start a round, or react |
| Start round button | Start a round |
| Spacebar (or Enter) | Start a round, or react |

## Game states

| State | Pad label | Color | 3D scene |
| --- | --- | --- | --- |
| Idle | Ready | Sky `#38BDF8` | Slow rotation |
| Waiting | Wait… | Amber `#F59E0B` | Slower, steadier motion |
| Go | GO! | Emerald `#10B981` | Fast rotation and a quick scale pulse |
| False start | Too early | Rose `#F43F5E` | Brief shake |
| Result | Your time | Sky `#38BDF8` | Settles back to idle |

Every state has a text label, so color is never the only signal.

## Key design decisions

**Timing is isolated from rendering.** The GO time is recorded in `showGo()`, the same code path that switches the UI to the go state, and not in the animation loop. The render loop never reads input and the game never waits on it, so a dropped frame cannot change a measured time. Reaction time is calculated from `event.timeStamp` (when the browser received the press) against `performance.now()` at the signal.

**No stale signals.** The only timer is the random wait. It is cleared on every reset path: false start, result, new round, hidden tab, and page hide. Each round also has an id, and the timeout callback ignores itself if its round is no longer current.

**Hidden tabs cancel the round.** Browsers throttle timers in background tabs, which would skew results, so the round resets to idle.

**Presses are measured on press, not release.** Mouse, touch, and pen use `pointerdown`. A short input lock after each result stops a double-tap from instantly starting a new round.

**The 3D scene is purely visual.** An icosahedron, a torus, and six floating spheres reuse a small set of shared geometries and materials. The app uses a single `requestAnimationFrame` loop, caps the pixel ratio at 2, and disposes of the scene when the page is torn down.

## Accessibility

- All state changes are announced to screen readers through an `aria-live` region.
- Every color state has a matching text label.
- Text and background combinations are chosen for sufficient contrast.
- Keyboard focus is clearly visible.
- If `prefers-reduced-motion` is set, rotation, bobbing, pulsing, and shaking stop, and only static color changes remain.

## Fallbacks

If WebGL or Three.js is unavailable, the canvas is hidden, the layout re-centers, and the timer works the same with the same color states.

## Responsive layout

On desktop, the content sits on the left and the 3D shapes float on the right. On phones, the shapes sit in a band at the top and the content flows below.

## Tech

- HTML, CSS, vanilla JavaScript (no framework, no build step)
- Three.js r128, loaded from cdnjs with a pinned URL. It is the only external JavaScript library.
- Space Grotesk (500, 700) for headings and the timer, Inter (400, 600) for body text, from Google Fonts

## Out of scope

This version has no persistent storage, no statistics beyond best time and attempt count, and no benchmark comparisons.

## Project structure

```
index.html   # markup, styles, and scripts in one file
README.md
```
