# Changelog

All notable changes to NavCube are documented here. This project follows [Semantic Versioning](https://semver.org/).

---

## v1.0.1

**DPI and adaptive sizing fix.**

### Fixed

- **NavCube size now stable across all display scale factors.**
  The previous implementation used `logicalDotsPerInch() / 96` as a *multiplier*, causing Qt 6's automatic HiDPI scaling to double-scale the widget — at 150 % Windows zoom the cube appeared ~2.25× larger than intended.
  The new formula uses `physicalDotsPerInch()` and `devicePixelRatio()` to target a consistent physical size (≈ 26.5 mm) on every display at every OS scale factor: 100 %, 125 %, 150 %, 200 %, 4K, and Retina.

### Added

- `NavCubeStyle.size_fraction` — when > 0, sizes the cube as a percentage of the parent widget's shorter side with automatic parent-resize tracking via an event filter.
- `NavCubeStyle.size_min` / `size_max` — clamp bounds (in 96-dpi-equivalent pixels) used with `size_fraction`.
- `NavCubeOverlay` now installs a parent event filter when `size_fraction > 0`, so the cube resizes synchronously before the host's `resizeEvent` handler repositions it — no layout jitter.

---

## v1.0.0

**Initial release.**

This is the first public version of NavCube — extracted from Osdag and polished into a standalone library.

### What's included

**NavCubeOverlay** — a pure PySide6 widget that draws a FreeCAD-style NaviCube with `QPainter`:
- 6 main faces, 12 edge faces, 8 corner faces with FreeCAD-style chamfered geometry
- Orbit buttons (North/South/East/West), roll arrows (Left/Right), home button, and backside dot
- Smooth quaternion SLERP animations with antipodal handling — no NaN crashes on 180° flips
- Quintic ease-in-out animation curve for natural-feeling motion
- Per-face Lambertian shading with configurable light direction
- Back-face culling with a configurable visibility threshold
- DPI-aware rendering that recalculates automatically when moved between monitors
- Overlay mode (transparent floating window) and inline mode (standard QWidget)
- Light and dark theme support with automatic detection from `QPalette`

**NavCubeStyle** — full visual control through a Python dataclass:
- 60+ configurable fields covering geometry, animation, colors, fonts, labels, borders, and shadows
- Runtime style swapping via `set_style()` with a full rebuild and repaint
- Separate light-theme and dark-theme color palettes
- Customizable face labels — any language, any script
- Configurable font family, weight, and size constraints
- Optional XYZ axis gizmo with configurable colors

**Coordinate system support:**
- Z-up by default (OCC, FreeCAD, Blender)
- Y-up via `_WORLD_ROT` class attribute override (Three.js, Unity, Unreal)
- Clear sign convention: `push_camera()` takes the inward direction, `viewOrientationRequested` emits the outward direction

**OCCNavCubeSync** — OCC V3d_View connector:
- Adaptive poll rate: every tick during interaction, every 4 ticks at idle
- Atomic camera updates via `Camera.SetEye()` + `Camera.SetUp()` to eliminate animation flicker
- Fallback to `SetProj`/`SetUp` for older pythonocc builds
- Clean teardown with no dangling references

**VTKNavCubeSync** — VTK renderer connector:
- Same architecture and API as the OCC connector

**SLERP smoothing during live camera interaction:**
- Quaternion-based smoothing (alpha 0.65) absorbs momentary renderer instabilities
- ~8 ms effective visual lag — imperceptible in use
- Automatically turns off when interaction ends

### Dependencies

- Python 3.10+
- PySide6
- NumPy
- Optional: `pythonocc-core` (OCC connector)
- Optional: `vtk` (VTK connector)

### License

LGPL-2.1
