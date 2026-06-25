# ComfyUI SIMTU Gaussian Splat Render

Renders 3D Gaussian Splat `.ply` files to RGBA images inside ComfyUI, with automatic camera orientation matched to the room photo perspective.  
Part of the [SIMTU](https://simtu.store) virtual staging toolkit.

## Node

### Gaussian Shot Render + Alpha (SIMTU)

Renders a Gaussian Splat furniture model (`.ply`) to an RGBA image. Fully procedural — camera parameters are calculated automatically from the PLY geometry and room camera pitch, requiring zero manual tuning per furniture item or room photo.

**Key features:**
- **Auto-yaw (v2.2):** PCA on Gaussian positions detects the front face of the furniture automatically
- **Auto-camera (v2.3):** `pivot_y` and `cam_distance` computed from Gaussian centroid and extents
- **Pitch correction (v2.4):** Camera pitch from node 35 (SIMTU Estimate Camera Pitch) is applied with correct sign convention
- **3D anchor output (v2.5):** Projects the PLY floor point (Y_min) to screen space and returns `anchor_offset_px` for precise floor placement — eliminates floating caused by weak leg Gaussians
- Reinhard tone mapping (linear → sRGB) for correct color rendering
- Alpha mask with configurable threshold and edge erosion

**Inputs:**
| Input | Description |
|---|---|
| `ply_path` | Path to `.ply` Gaussian Splat file |
| `auto_yaw` | True = PCA auto-detect front face |
| `cam_pitch_deg_in` | Camera pitch from node 35 (SIMTU Estimate Camera Pitch) |
| `distance_multiplier` | `cam_distance = multiplier × max(X_range, Z_range)/2` |
| `alpha_threshold` | Minimum alpha for Gaussian visibility |
| `edge_erode_px` | Alpha mask edge erosion in pixels |
| `exposure_override` | 0.0 = auto tone mapping |

**Outputs:**
| Output | Description |
|---|---|
| `image` | Rendered RGBA image (IMAGE) |
| `alpha_mask` | Alpha mask (MASK) |
| `out_width` / `out_height` | Rendered dimensions |
| `cam_yaw_deg_out` | Detected yaw angle — connect to node 17 (SIMTU Depth-Based Placement) |
| `anchor_offset_px` | 3D-geometry-based anchor — connect to node 18 (SIMTU Pad Sofa to Room) |

---

## Dependencies

- ComfyUI
- `gaussian_shot_node.py` — the underlying rasterizer (must be present in the same folder; included in this repo)
- `plyfile`, `numpy`, `torch`, `scipy`

---

## Installation

### Via ComfyUI Manager
Search for `SIMTU Gaussian Splat Render` and install.

### Manual
```bash
cd ComfyUI/custom_nodes
git clone https://github.com/simtu-store/comfyui-simtu-splat-render
```

---

## PLY file requirements

The `.ply` file must be a standard 3DGS format with vertex properties:
`x, y, z, f_dc_0, f_dc_1, f_dc_2, opacity, scale_0, scale_1, scale_2, rot_0, rot_1, rot_2, rot_3`

Tested with exports from [Nerfstudio](https://github.com/nerfstudio-project/nerfstudio) (`splatfacto`, `sh_degree=1`) and [PostShot](https://www.jawset.com/).

**Orientation note:** The node expects Y-down convention. If your splat was trained with a Z=180 rotation baked in (Blender → NeRF axis fix), no flip nodes are needed.

---

## Part of the SIMTU pipeline

This node works together with:
- [comfyui-simtu-depth-placement](https://github.com/simtu-store/comfyui-simtu-depth-placement) — procedural scale and floor placement
- [comfyui-simtu-light-shadow](https://github.com/simtu-store/comfyui-simtu-light-shadow) — deterministic shadow and relighting

---

## License

MIT License. See [LICENSE](LICENSE).
