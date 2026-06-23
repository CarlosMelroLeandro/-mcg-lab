# PythonMCG — User Manual

PythonMCG is an interactive Python environment for learning **Mathematics for Computer Graphics**. You type Python commands into a shell and see the results drawn on a canvas in real time — no compile step, no file to save.

It was designed for Multimedia students studying affine geometry and 2D/3D transformations.

---

## Table of Contents

1. [Installation](#installation)
2. [Interface](#interface)
3. [Coordinate System](#coordinate-system)
4. [Colours, Pen and Brush](#colours-pen-and-brush)
5. [2D Drawing Commands](#2d-drawing-commands)
6. [MCGLib — 3D Mathematics Library](#mcglib--3d-mathematics-library)
   - [Vectors and Points](#vectors-and-points)
   - [Lines and Planes](#lines-and-planes)
   - [Affine Transformations](#affine-transformations)
   - [3D Scenes and Projection](#3d-scenes-and-projection)
7. [Examples](#examples)
8. [Running a Script File](#running-a-script-file)

---

## Installation

### Linux

```bash
sudo apt-get install python-wxgtk2.8 git -y
git clone https://github.com/MIguelLeandro/PythonMCG.git
cd PythonMCG/src
python2 pythonmcg.py
```

### Windows

A pre-built installer is available in the `win_dist/` folder of this repository. Run `pythonmcg.exe` directly — no Python installation required.

### macOS

Install Python 2.7 and wxPython 2.8 or 3.0, then:

```bash
cd src
python2 pythonmcg.py
```

---

## Interface

The window is divided into two areas:

```
┌─────────────────────────────────────────┐
│                                         │
│           Drawing Canvas                │
│           (MCG Widget)                  │
│                                         │
├──────────────────────────────┬──────────┤
│  Interactive Python Shell    │ Teach Me │
└──────────────────────────────┴──────────┘
```

- **Drawing Canvas** — displays everything you draw. The canvas is 2000 × 1200 pixels with the origin at the centre.
- **Interactive Python Shell** — a standard Python 2.7 interpreter. Type commands here and press Enter to execute them.
- **Teach Me button** — opens the illustrated help screen with a quick introduction to the drawing commands.
- **Help menu → F1** — toggles the help panel.

---

## Coordinate System

The origin `(0, 0)` is at the **centre** of the canvas. The X axis points right and the **Y axis points upward** (standard mathematical convention).

```
              Y
              ↑
              │
    ──────────┼──────────→ X
              │
              │
```

Coordinates are in pixels. Positive X is to the right of centre; positive Y is above centre.

---

## Colours, Pen and Brush

### Setting the pen

The **pen** controls lines and outlines.

```python
SetPen(color, width, style)
```

| Parameter | Type | Example |
|-----------|------|---------|
| `color` | string (name or hex) | `"red"`, `"#00FF88"` |
| `width` | integer (pixels) | `1`, `3` |
| `style` | constant (see below) | `SOLID`, `DOT` |

**Pen style constants:**

| Constant | Appearance |
|---|---|
| `SOLID` | ——————— |
| `TRANSPARENT` | (invisible) |
| `DOT` | · · · · · · |
| `LONG_DASH` | — — — — |
| `SHORT_DASH` | - - - - - |
| `DOT_DASH` | -·-·-·-· |

### Setting the brush

The **brush** controls filled areas.

```python
SetBrush(color, style)
```

Use `SOLID` for filled shapes, `TRANSPARENT` for outlines only.

### Quick shortcuts

```python
color("blue")   # change pen colour only
width(3)        # change pen width only
```

### Resetting

```python
clear()   # clear the canvas (keeps pen/brush settings)
reset()   # clear canvas and reset all settings to defaults
```

---

## 2D Drawing Commands

All coordinates are `(x, y)` tuples or lists of tuples.

### Points

```python
DrawPointPoint((x, y))
DrawPointList([(x1, y1), (x2, y2), ...])
```

### Lines

```python
DrawLinePoint((x1, y1), (x2, y2))
DrawLineList([(x1, y1, x2, y2), (x3, y3, x4, y4), ...])
```

### Polygons

```python
DrawPolygon([(x1, y1), (x2, y2), (x3, y3), ...])
DrawPolygonList([polygon1, polygon2, ...])
```

Where each polygon is a list of `(x, y)` tuples.

**Per-polygon colour variants:**

```python
# Wire frame — each polygon gets its own outline colour
DrawPolygonListC([("red",  [(x1,y1), (x2,y2), (x3,y3)]),
                 ("blue", [(x4,y4), (x5,y5), (x6,y6)])])

# Filled — each polygon gets its own fill colour
DrawPolygonListB([("red",  [(x1,y1), (x2,y2), (x3,y3)]),
                 ("blue", [(x4,y4), (x5,y5), (x6,y6)])])
```

### Circles and Ellipses

```python
DrawCirclePoint((cx, cy), radius)
DrawEllipse(x, y, width, height)          # bounding-box top-left corner
DrawEllipseList([(x, y, w, h), ...])
```

### Arcs

```python
DrawArcPoint(pt1, pt2, center)
```

Draws an arc of a circle centred on `center`, from `pt1` to `pt2`.

### Splines

```python
DrawSpline([(x1,y1), (x2,y2), (x3,y3), ...])
```

### Text

```python
DrawText("Hello", x, y)
DrawRotatedText("Hello", x, y, angle_degrees)
```

### Viewport controls

```python
SetUserScale(s)            # zoom factor (default 1.0)
SetDeviceOrigin(x, y)      # move the canvas origin (pan)
```

---

## MCGLib — 3D Mathematics Library

`MCGLib` is automatically available in the shell (it is loaded when MCGProcess starts). Use `from MCGLib import *` at the top of script files.

It provides the mathematical building blocks taught in affine geometry: vectors, points, lines, planes, affine transformation matrices, and a simple 3D scene system.

> **Note:** `MCGLib` uses Portuguese names for some methods (e.g. `soma`, `normalizar`, `comprimento`). Both Portuguese and the descriptions below are listed.

---

### Vectors and Points

#### `vector(x, y, z)` — a free vector

```python
v = vector(1, 0, 0)
w = vector(0, 1, 0)
```

| Operation | Code | Notes |
|---|---|---|
| Addition | `v + w` | vector + vector = vector; vector + point = point |
| Subtraction | `v - w` | |
| Scalar multiply | `v.mult(3)` | |
| Negate | `-v` | |
| Length (norm) | `v.comprimento()` | √(x²+y²+z²) |
| Normalise | `v.normalizar()` | unit vector |
| Dot product | `v.interno(w)` | scalar |
| Cross product | `v.externo(w)` | vector |
| Parallel test | `v.paralelo(w)` | True / False |
| Is vector? | `v.vector()` | True |

#### `ponto(x, y, z)` — a point (position)

```python
P = ponto(1, 2, 3)
Q = ponto(4, 5, 6)
```

| Operation | Code | Notes |
|---|---|---|
| Translate | `P + v` | point + vector = point |
| Difference | `P - Q` | point − point = vector |
| Position vector | `P.posicao()` | vector from origin |
| Line through P and Q | `P.recta(Q)` | |
| Plane through P, Q, R | `P.plano(Q, R)` | |
| Is point? | `P.ponto()` | True |

---

### Lines and Planes

#### `recta(ponto, vector)` — a line

```python
r = P.recta(Q)         # line through P and Q
r = recta(P, v)        # line through P with direction v
```

| Method | Result |
|---|---|
| `r.tem_ponto(P)` | True if P lies on r |
| `r == s` | True if r and s are the same line |

#### `plano(ponto, vector)` — a plane

The vector is the **normal** to the plane.

```python
pi = P.plano(Q, R)     # plane through P, Q, R
pi = plano(P, n)       # plane through P with normal n
```

| Method | Result |
|---|---|
| `pi.tem_ponto(P)` | True if P lies on the plane |
| `pi == sigma` | True if same plane |

---

### Affine Transformations

`Transf` represents a 4×4 homogeneous transformation matrix.

```python
T = Transf()   # identity
```

#### Setting a transformation

```python
T.translacao(v)           # translation by vector v
T.escalonamento(sx,sy,sz) # scale
T.rodaX(angle)            # rotation around X (radians)
T.rodaY(angle)            # rotation around Y
T.rodaZ(angle)            # rotation around Z
```

#### Composing transformations

```python
M = T1 * T2    # apply T2 first, then T1
```

#### Applying to a point

```python
Q = T.avalia(P)    # returns the transformed point
```

#### Projection matrices

| Method | Description |
|---|---|
| `T.ProjOrto()` | Orthographic projection onto XOY |
| `T.ProjTrime()` | Trimetric projection |
| `T.ProjCavaleira()` | Cavalier oblique projection |
| `T.ProjCabinet()` | Cabinet oblique projection |
| `T.Persp1fuga(pz)` | 1-point perspective (vanishing point at z=pz) |
| `T.Persp2fuga(px, py)` | 2-point perspective |
| `T.Persp3fuga(px, py, pz)` | 3-point perspective |
| `T.vista(O, X, Y, Z)` | View transformation from world to camera axes |

---

### 3D Scenes and Projection

#### `Cena` — a 3D scene

`cena` is pre-created for you. Add objects, set the viewpoint, then call `actualizaIG` to project and draw everything.

```python
cena = Cena()

# Set the view: where the camera is, and where it looks
cena.deAolharPara(ponto(700, 0, -1500), ponto(0, 0, 0))
cena.vectorUP = vector(0, 1, 0)
cena.Zoom = 0.5

# Choose a projection (default is orthographic)
T = Transf()
T.ProjOrto()
cena.projeccao = T

# Add geometry to the scene
f = FaceTriang(ponto(0,0,0), ponto(100,0,0), ponto(50,100,0))
cena.addObj(f)

# Draw it — True = wire frame, False = filled
actualizaIG(cena, True)
```

#### `FaceTriang(p1, p2, p3)` — a triangle

#### `FaceQuad(p1, p2, p3, p4)` — a quadrilateral

#### `Mesh` — a mesh loaded from a gmsh file

```python
m = Mesh()
m.load("mymodel.msh")
cena.addObj(m)
```

#### Multiple frames (key-frame animation)

`Frame` is a global list of `Cena` objects. By default `Frame[0]` is the same as `cena`.

```python
Frame[0].deAolharPara(ponto(700,0,-1500), ponto(0,0,0))
Frame[0].addObj(homem)

Frame.append(Cena())
Frame[1].deAolharPara(ponto(0,0,-1500), ponto(0,0,0))
Frame[1].addObj(homem)

DivideFrames(10)    # interpolate 10 frames between each keyframe
```

---

## Examples

### Example 1 — A point at the origin

```python
SetPen("red", 10, SOLID)
DrawPointPoint((0, 0))
```

### Example 2 — A rotated square

```python
import math

SetPen("red", 1, SOLID)
SetBrush("blue", TRANSPARENT)

square = [(30,40), (80,40), (80,80), (30,80)]
DrawPolygon(square)

def rotate(angle, points):
    return [(x*math.cos(angle) - y*math.sin(angle),
             x*math.sin(angle) + y*math.cos(angle))
            for (x, y) in points]

DrawPolygon(rotate(math.pi/3, square))
```

### Example 3 — Circumscribed circles

```python
import math

square = [(30,40), (80,40), (80,80), (30,80)]
SetBrush("blue", TRANSPARENT)
DrawCirclePoint((0,0), math.sqrt(30**2 + 40**2))
DrawCirclePoint((0,0), math.sqrt(80**2 + 80**2))
```

### Example 4 — Star of lines

```python
import math

L = [(0, 0, 100*math.cos(i*math.pi/10), 100*math.sin(i*math.pi/10))
     for i in range(11)]
DrawLineList(L)
```

### Example 5 — Point cloud

```python
pts = [(i, j) for i in range(100) for j in range(100)]
DrawPointList(pts)
```

### Example 6 — 3D triangle projected orthographically

```python
from MCGLib import *

f = FaceTriang(ponto(0,0,0), ponto(30,30,30), ponto(100,0,10))
c = Cena()
c.addObj(f)
actualizaIG(c, True)
```

### Example 7 — Perspective projection

```python
from MCGLib import *

T = Transf()
T.Persp1fuga(500)
cena.projeccao = T
cena.deAolharPara(ponto(200, 100, -800), ponto(0, 0, 0))
cena.Zoom = 1.0
actualizaIG(cena, False)
```

---

## Running a Script File

You can paste a script directly into the shell, or use Python's `execfile`:

```python
execfile("samples/example1.py")
```

> **Note:** script files must use the same Python 2.7 syntax as the shell. The drawing commands and `MCGLib` names are already in scope — you do not need to import them unless you are running the file outside PythonMCG.
