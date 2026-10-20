# Blockhollow

A cozy, blocky open-world game that runs in the browser. Explore a randomly generated valley of villages, caves, monster lairs and islands, take on quests from villagers, buy gear with coins, grow wings and fly, and eventually brave the storm to reach Demon Island and its dragon.

The whole game is a single file, `index.html`: HTML, CSS and JavaScript with no build step.

## Setup

### Requirements

- A modern desktop or mobile browser with WebGL (Chrome, Firefox, Safari, Edge).
- An internet connection on first load. The page pulls two things from CDNs:
  - [three.js r128](https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js) from cdnjs
  - The *Pixelify Sans* font from Google Fonts (falls back to a monospace font if unavailable)

### Run it

Open `index.html` directly in your browser, or serve the folder locally (recommended, since some browsers restrict pointer lock on `file://` pages):

```bash
# from the project folder
python3 -m http.server 8000
# then open http://localhost:8000
```

Any static file server works (`npx serve`, VS Code Live Server, etc.).

### Talking to characters (optional)

When you press **T** to talk to someone, the game asks Claude to answer in that character's voice if the page is running somewhere that provides `window.claude` (for example, as a published Claude artifact). Opened locally, characters answer from a small built-in offline phrasebook instead. Nothing needs configuring either way.

## Controls

| Key | Action |
| --- | --- |
| W A S D / arrows | Walk |
| Space | Jump, swim up |
| Shift | Sprint |
| Left click | Mine a block or attack |
| Right click | Place a block, open doors, talk, shop, pet animals, open chests, sleep |
| Middle click | Copy the targeted block into your hand |
| 1–0 / mouse wheel | Choose hotbar item |
| T | Say something to the person in front of you |
| F | Grow wings and fly for a few seconds |
| M | Read the map to Demon Island (once bought) |
| R | Ride your dragon (once you have one) |
| N | Skip ahead to dusk or dawn |
| Esc | Pause |

Touch devices get an on-screen stick and buttons automatically.

Each page load generates a new world from a random seed. There is no save system, so reloading starts over.

## Project layout

```
index.html   # the entire game
README.md
```

Inside `index.html`:

- **Lines ~1–250:** CSS and HUD/overlay markup (status, quests, hotbar, pause menu, shop, chat, map).
- **The `<script>` block:** split into sections marked with banner comments like `/* ---------------- world generation ---------------- */`. Search for `/* ----` to jump between them. In order, roughly:
  - *World data:* constants & blocks, noise & random, biomes, ocean, people (names, jobs, dialog), villages & landmarks
  - *Generation:* terrain, caves, villages, pirate ship, circus/pyramid/igloo/witch's hut, monster lairs, islands, Demon Island, trees & plants
  - *Rendering:* texture atlas, renderer & scene, chunk meshing, sky
  - *Gameplay:* physics, doors, models & looks, mobs, villagers, player, coins, quests, talking, shops, chat, map, sleeping
  - *Runtime:* input, targeting, HUD, day/night, wings, main loop, boot

## Contributing

1. **Make your change in `index.html`.** Keep it a single self-contained file: no bundler, no extra local scripts or assets. If you need a library, load a pinned version from a CDN (like three.js r128) and make sure the game still degrades gracefully if it's missing.
2. **Match the existing style.**
   - Put code in the matching `/* ---- section ---- */`, or add a new section with the same banner format.
   - Short comments that explain *why* or describe the thing in plain words (e.g. `// A 7x7 ruined stone tower: ...`).
   - Compact modern JavaScript: `const`/`let`, arrow functions, template strings, semicolons.
   - Colors and fonts for the UI go through the CSS variables in `:root`.
3. **Keep it kid-friendly.** Dialog, quest text and the character persona prompt are written for children. New content should be too.
4. **Keep things in sync.** If you add a key binding, update both the desktop help list (`#keyhelp`) and the touch help text in the input section, plus the Controls table above. If you add a place or character people should know about, add it to the world description in `persona()` so characters can talk about it, and to `localReply()` if the offline phrasebook should understand it.
5. **Test by playing.** There is no automated test suite. Before sending a change, reload the page a few times (worlds are random) and check:
   - The browser console has no errors.
   - The thing you changed works by day and by night.
   - The frame rate still feels smooth; chunk rebuilds and model counts are the usual culprits.
   - If you touched input or the HUD, try it on a narrow/touch screen too (browser dev tools device mode is fine).
6. **Describe the change.** Say what you changed, how to see it in game (where to go, what to press), and include a screenshot or clip for anything visual.
