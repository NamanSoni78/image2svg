# image2svg

A free, zero-dependency, 100% client-side raster-to-vector converter powered by the **MechTrace v3.1** engine. Convert JPG, PNG, WebP, GIF, and BMP images into crisp, scalable SVG files entirely inside your browser—with no server uploads, sign-ups, or conversion limits.

**Live Demo:** [image2svg.namansoni.in](https://image2svg.namansoni.in/)

---

## Features

- **Auto Best Mode:** Automatically fingerprints the input image (measuring sensor noise, silhouette geometry, gradient coverage, and line-art density) and races multiple tracing strategies against each other.
- **Closed-Loop Perceptual Verification:** Evaluates candidate SVGs against the original raster using **SSIM** (Structural Similarity), **OKLab $\Delta E$** perceptual color difference, and **Edge IoU** accuracy to pick the highest-scoring output.
- **100% Client-Side & Private:** Runs locally inside an inline Blob Web Worker (with automatic main-thread fallback). Images never leave your device.
- **Sub-Pixel Anti-Aliasing Recovery:** Recovers sub-pixel edge midpoints across alpha boundaries, region seams, and global gradients.
- **Gradient & Primitive Detection:**
  - Decomposes global linear/radial backgrounds, specular highlights, and circular rims.
  - Merges smooth color transitions into per-region linear or radial gradients via a Region Adjacency Graph (RAG).
  - Snaps geometric shapes (`<circle>`, `<ellipse>`, `<rect>`, rounded rectangles) and extracts constant-width centerline strokes using Euclidean Distance Transform (EDT) and Zhang-Suen thinning.
- **Interactive Inspection Views:**
  - **Compare:** Draggable before/after split slider.
  - **Side by Side:** Dual-pane original vs. vector view.
  - **SVG Only:** Clean rendered vector output.
  - **Wireframe:** Geometry and path stroke inspection.
  - **$\Delta E$ Heatmap:** Visualizes per-pixel perceptual color error against the source image.
- **Flexible Input & Export:** Drag-and-drop, file picker, or direct clipboard paste (`Ctrl + V`), with one-click `.svg` download or raw SVG code copy.

---

## Conversion Modes & Controls

| Mode | Resolution | Max Colors | Best For |
| :--- | :--- | :--- | :--- |
| **Compact** | `256px` | `10` | Lightweight icons and web UI assets (~3–4 KB typical) |
| **Auto Best ★** | `384px` | `24` | General use; auto-selects archetype strategy (default) |
| **Max Detail** | `512px` | `34` | Intricate illustrations and complex shaded artwork |

### Advanced Manual Overrides
Expanding **Advanced options** switches the engine to manual override with granular controls for:
- **Feature Toggles:** Sub-Pixel AA, Global Gradients, Region Gradients, and Centerline Strokes.
- **Max Palette Colors:** `2` – `36`
- **Curve Tolerance ($\varepsilon$):** `0.25` – `4.50`
- **Trace Resolution:** `128px` – `512px`
- **Coordinate Precision:** `0` – `2` decimal places

---

## Architecture & How It Works

The entire application is self-contained in a single `index.html` file with zero external runtime dependencies:

1. **Image Fingerprinting:** Estimates Immerkaer noise ($\sigma$), extracts exterior-flood-filled silhouettes, and classifies images into archetypes (*Line Art / Mono*, *Gradient Badge*, *Flat Geometric*, *Shaded Illustration*, or *Complex Artwork*).
2. **Edge-Preserving Denoising:** Applies multi-pass bilateral filtering when sensor noise is detected.
3. **OKLab Quantization & Cleanup:** Performs edge-weighted K-Means++ clustering in perceptual OKLab space, followed by boundary tooth filtering and small-speckle/anti-aliasing halo dissolution.
4. **Planar Shared-Boundary Graph:** Constructs topologically shared edge chains between regions so adjacent shapes meet seamlessly without gaps or overlaps.
5. **Potrace DP + $C^1$ Bézier Fitting:** Uses disk-tangent cone-sweep dynamic programming for optimal polygon vertices, total least squares (TLS) sub-pixel vertex adjustment, and $C^1$-continuous cubic/quadratic Bézier curve fitting.

---

## Running Locally

Because `image2svg` has no build step or external dependencies, you can run it directly:

1. Clone the repository:
   ```bash
   git clone [https://github.com/namansoni78/image2svg.git](https://github.com/namansoni78/image2svg.git)
   cd image2svg
