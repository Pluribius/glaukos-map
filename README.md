# Glaukos Map

A 3D space map built with React, Vite, Three.js, and React Three Fiber. The app renders a set of celestial POIs from `public/data/pois.json` and displays procedural planetary meshes, tilt, ring systems, orbit tracks, and interactive HUD controls.

## What this map does

- Loads POIs from `public/data/pois.json` at runtime
- Renders each object in 3D space using `@react-three/fiber`
- Generates procedural surface rings for planets and moons
- Applies custom tilt and rotation speed per object
- Displays orbit tracks for planets around gas giants and moons around planets
- Shows selectable info panels for each selected object
- Includes HUD toggles for grid, axis helpers, and VHS effect

## Tech stack

- `React` for the UI and app shell
- `Vite` for fast development and build
- `Three.js` for 3D rendering
- `@react-three/fiber` for React bindings to Three.js
- `@react-three/drei` for helpers like `Line`
- `@react-three/postprocessing` and `postprocessing` for visual effects
- `js-cookie` for HUD preference persistence
- `react-markdown` and `marked` for text rendering

## Project structure

- `src/App.jsx` - entry point, loads POI JSON, manages HUD state, and mounts the 3D scene
- `src/components/scene/Spacecanvas.jsx` - main canvas and scene assembly
- `src/components/scene/SpaceObject.jsx` - renders planets, moons, gas giants, and structures
- `src/components/scene/SystemOrbitRings.jsx` - builds orbit tracks using vector geometry
- `src/components/scene/RangeRings.jsx` - visual range rings around selected objects
- `src/components/scene/BackgroundStars.jsx` - starfield background
- `src/components/ui/HUDOverlay.jsx` - HUD controls, filters, and object list
- `src/components/ui/InfoPanel.jsx` - selected object detail panel
- `src/hooks/useCookieState.js` - saves HUD toggle state in cookies
- `public/data/pois.json` - the JSON database of map objects

## How POI data is structured

The map reads an array of objects from `public/data/pois.json`. Each entry should include:

- `id` - unique string identifier
- `name` - display name
- `type` - object type (`gas_giant`, `planet`, `moon`, `ground_base`, etc.)
- `radius` - numeric radius used for visual scaling
- `tilt` - degrees of axial tilt
- `rotationSpeed` - rotation velocity used by the object frame hook
- `coordinates` - `{ x, y, z }` position in 3D space
- `has_rings` - boolean to enable visible ring disks for the object
- `details` - optional metadata displayed in the info panel

Example entry:

```json
{
  "id": "aulden",
  "name": "Aulden",
  "type": "planet",
  "radius": 45,
  "tilt": 12.0,
  "rotationSpeed": 0.18,
  "coordinates": { "x": -527, "y": -14779, "z": -1442 },
  "has_rings": false,
  "details": {
    "classification": "Terrestrial Planet",
    "system": "Luma",
    "diameter": "90.00 km",
    "gravity": "0.82 g",
    "primaryColor": "#3b82f6",
    "secondaryColor": "#1e3a8a"
  }
}
```

## Adding a new coordinate file

To add or replace the POI data:

1. Open `public/data/pois.json`
2. Add a new object to the array following the existing field names
3. Save the file
4. Reload the app in the browser

If deployed to GitHub Pages, make sure the file is included in the published `public/data` path.

## Procedural rings, tilt, and rotation speed

### Planet and moon rings

- `SpaceObject.jsx` generates a procedural surface grid for every planet and moon.
- The code creates:
  - `meridian` rings: a set of vertical torus loops around the body
  - `equator` rings: horizontal torus loops at different latitudes
- `has_rings: true` enables extra disk rings around the object.
- The ring disk appearance is defined by `ringDisks` inside `SpaceObject.jsx`, with inner/outer radii and opacity.

### Tilt

- The `tilt` value is read from the POI entry and converted from degrees to radians.
- Objects are rotated around the Z axis using this tilt value before being rendered.
- Example: `"tilt": 12.0` applies a 12° axial tilt to the planet mesh.

### Rotation speed

- `rotationSpeed` controls the live rotation of the planet's ring grid in `PlanetObject`
- It is parsed from either the root object or `details.rotationSpeed`
- Example: `"rotationSpeed": 0.18` spins the object faster than `0.02`

## Orbit tracking rules

- `SystemOrbitRings.jsx` creates orbit tracks for:
  - planets that reference a gas giant by `details.system`
  - moons that use `details.system` with a `(Parent: PlanetName)` tag
- The track radius is computed from the 3D distance between parent and child coordinates.
- Tilt is applied to the orbit plane using the parent's or moon's `tilt` value.

## Running the project

Install dependencies and start the dev server:

```bash
npm install
npm run dev
```

Build for production:

```bash
npm run build
```

Preview the build locally:

```bash
npm run preview
```
