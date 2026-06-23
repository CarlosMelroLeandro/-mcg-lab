# mcg-lab

An interactive Python environment for learning **Mathematics for Computer Graphics**.

Students type Python commands into a shell and see the results drawn on a canvas in real time. The environment covers 2D drawing primitives, affine geometry, 3D transformations, and multiple projection models — all explored through code, without a compile step.

Designed for Multimedia students studying affine geometry and computer graphics mathematics.

---

## Overview

mcg-lab provides two things:

- A **drawing shell** — an interactive Python 2.7 interpreter wired to a 2D canvas. Commands like `DrawPolygon`, `SetPen`, and `DrawCirclePoint` draw immediately on screen.
- **MCGLib** — a 3D mathematics library (available inside the shell) covering vectors, points, lines, planes, affine transformation matrices (4×4 homogeneous), projection models (orthographic, cavalier, cabinet, 1/2/3-point perspective), and a simple 3D scene system.

The canvas origin is at the centre of the window; the Y axis points **upward** (standard mathematical convention).

---

## Screenshots / Help screens

The application ships with four illustrated help pages (accessible via **Help → F1** or the "Teach Me" button) that introduce the drawing commands visually.

---

## Quick start

```python
# Draw a red point at the origin
SetPen("red", 10, SOLID)
DrawPointPoint((0, 0))

# Draw a blue polygon
SetPen("blue", 1, SOLID)
SetBrush("blue", TRANSPARENT)
DrawPolygon([(30,40), (80,40), (80,80), (30,80)])

# Rotate it by 60°
import math
def rotate(angle, pts):
    return [(x*math.cos(angle)-y*math.sin(angle),
             x*math.sin(angle)+y*math.cos(angle)) for x,y in pts]
DrawPolygon(rotate(math.pi/3, [(30,40),(80,40),(80,80),(30,80)]))
```

The first block draws a thick red dot at the canvas centre (coordinates `(0, 0)` map to the middle of the window). It then draws a 50×40 blue rectangle with a transparent fill, placing its bottom-left corner 30 units right and 40 units up from the centre. Finally, it applies a 2D rotation matrix for 60° to the same four vertices and draws the rotated rectangle alongside the original — illustrating how a standard rotation formula transforms a polygon in place.

```python
# 3D triangle — orthographic projection
from MCGLib import *
f = FaceTriang(ponto(0,0,0), ponto(30,30,30), ponto(100,0,10))
c = Cena()
c.addObj(f)
actualizaIG(c, True)
```

This block works in 3D. It creates a triangular face whose three vertices are at the world-space coordinates `(0,0,0)`, `(30,30,30)`, and `(100,0,10)`. The face is added to a scene (`Cena`) and rendered onto the canvas using `actualizaIG`, which applies an orthographic projection — all depth (Z) information is discarded and the X/Y coordinates are drawn directly. The result is the triangle as seen straight-on along the Z axis.

See [`MANUAL.md`](MANUAL.md) for the full reference.

---

## Installation

### Linux

```bash
sudo apt-get install python-wxgtk2.8 git -y
git clone https://github.com/CarlosMelroLeandro/-mcg-lab.git
cd -mcg-lab/src
python2 pythonmcg.py
```

### Windows

A pre-built executable is available in `win_dist/`. Run `pythonmcg.exe` — no Python installation required.

### Dependencies

| Dependency | Version |
|---|---|
| Python | 2.7 |
| wxPython | 2.8 or 3.0 |

Tested on Windows and Ubuntu.

---

## Repository structure

```
src/
  pythonmcg.py          # entry point — ApplicationWindow
  MCGprocess.py         # user's Python subprocess + drawing command definitions
  MCGwidget.py          # canvas widget (polls drawing commands, renders bitmap)
  MCGLib.py             # 3D math library (vectors, points, transforms, scenes)
  my_mcg.py             # MCG drawing state object
  shelltoprocess/       # wxPython shell ↔ subprocess bridge (queue-based)
  vector.py             # 2D/3D Vector class
  misc/                 # angle utils, queue helpers, resource path helper
  resources/            # icons and help images
  samples/              # example scripts
packaging_scripts/      # py2exe (Windows) and py2app (macOS) build scripts
win_dist/               # pre-built Windows executable
MANUAL.md               # full user manual and API reference
```

---

## Architecture

mcg-lab runs the user's Python code in a **separate process** (via `multiprocessing`) so that a long-running loop or a crash in user code cannot freeze the GUI.

The GUI process hosts the canvas (`MCGWidget`) and the shell widget. The subprocess runs the user's commands and sends drawing state updates back via a `multiprocessing.Queue`. The shell and subprocess are connected by four queues (input, output, runcode-finished, runsource-return).

---

## Credits

Inspired by and using portions of [PythonTurtle](https://github.com/PythonTurtle/PythonTurtle) by Ram Rachum.

---

## License

MIT License — © 2012 Ram Rachum and Carlos Leandro. See [`LICENSE`](LICENSE).
