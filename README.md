# 3D World Explorer

An infinite procedurally generated 3D world you can fly through in a browser. No server, no dependencies beyond a single HTML file — just open `world.html`.

## Running

Open `world.html` directly in a browser. For audio (wind, rain, birds) to work, serve it over HTTP:

```bash
python3 -m http.server 8000
# then open http://localhost:8000/world.html
```

Audio won't play over `file://` due to browser security restrictions on media elements.

## Controls

| Key | Action |
|-----|--------|
| `W A S D` | Move |
| `Mouse` | Look |
| `Space` | Jump / fly up |
| `Ctrl` | Fly down |
| `Shift` | Sprint |
| `Tab` | Open/close settings panel |
| `P` | Pause day/night cycle |

Click anywhere on the canvas to lock the mouse pointer. Press `Tab` or `Escape` to release it.

## Sharing a World

Every world has a unique seed. After generating a world, hit **🔗 Copy URL** in the settings panel — the URL encodes the seed and all terrain settings. Paste it to anyone and they'll load the exact same world.

URL parameters: `?seed=…&h=…&r=…&hi=…&b=…&e=…`

## World Settings

Open the settings panel with `Tab`.

### Terrain (requires Rebuild)

| Setting | What it does |
|---------|-------------|
| **Seed** | Deterministic world seed (0 – 999999999) |
| **Height Scale** | Overall vertical exaggeration |
| **Mountain Ridge** | Sharpness and prominence of mountain ridges |
| **Hill Amplitude** | Rolling hills in lowland biomes |
| **Biome Size** | How large each biome region is |
| **Erosion** | Gully depth, talus, and ridge weathering |

### Live (no rebuild)

- **Sea Level** — raise/lower the ocean surface in real time
- **Move Speed** — player movement speed
- **Day Speed** — how fast the day/night cycle runs
- **Time of Day** — scrub directly to any time

### Weather

Click a weather button to transition: ☀ Clear → ☁ Cloudy → 🌧 Rain → ⛈ Storm. Clouds, rain particles, lightning, fog, and ambient sound all blend in/out.

## Biomes

| Biome | Character |
|-------|-----------|
| **Flatland** | Gentle rolling terrain, good for villages and roads |
| **Forest** | Denser elevation, tall trees, bird sounds |
| **Jungle** | Lush green, humid feel |
| **Desert** | Sandy dunes, sparse |
| **Mountains** | Sharp ridges, high peaks, wind sounds |

## Features

### Terrain
- Infinite chunk-based world with 6 LOD levels — high detail up close, lower detail at distance
- Domain-warped simplex noise with per-biome ridge, hill, and river weighting
- Hydraulic erosion: gullies, talus slopes, ridge wear
- Procedural vertex coloring blended by biome and slope

### Water
- GPU shader water at sea level with animated wave normals, Fresnel reflection, sun specular, and depth-based color
- Inland lakes in flat areas

### Villages
- Villages spawn on land away from water and mountains
- A guaranteed starting village is placed near the player's spawn point
- Villagers (red figures) idle, hunt nearby animals, and reproduce
- When a village reaches 7 residents, two villagers migrate together to found a new settlement
- Roads connect villages with instanced tile segments; water crossings become bridges

### Wildlife
- Rabbits, deer, and wolves with a food chain — wolves hunt herbivores, villagers hunt herbivores
- Animals avoid obstacles (trees, buildings)
- Herbivores reproduce when near a mate; wolves reproduce more slowly
- All flee from threats

### Ruins
- Ancient stone ruins scattered through the world

### Day / Night
- Full sky color cycle from midnight blue through dawn orange to noon white
- Sun and moon arc across the sky
- Directional lighting shifts color and angle throughout the day

### Weather
- Four states: clear, cloudy, rain, storm
- Storm has lightning flashes and stronger wind/rain particles

### Ambient Sound
- Rain and storm: filtered noise that fades in with weather blend
- Mountain wind: loops `assets/wind.mp3` when in the mountains biome
- Forest birds: synthetic chirps when clear weather in forest/jungle biomes

### Minimap
- Top-right corner overview showing terrain, roads, village and ruin locations, and player position

## File Structure

```
world.html        — entire app (Three.js r134 from CDN, everything else inline)
assets/
  wind.mp3        — mountain wind ambient loop
```

## Technical Notes

- Web Workers handle chunk geometry generation off the main thread; stale results are discarded via a generation counter
- Terrain noise runs on both main thread (height queries for gameplay) and workers (mesh generation) using the same SimplexNoise seed
- Chunk size: 420 world units, render radius: 10 chunks
