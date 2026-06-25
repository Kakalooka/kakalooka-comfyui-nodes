# ComfyUI SIMTU Light & Shadow

Deterministic, AI-free shadow generation and relighting for virtual furniture staging in ComfyUI.  
Part of the [SIMTU](https://simtu.store) virtual staging toolkit.

## Node

### SIMTU Light & Shadow

Generates a contact shadow beneath furniture and applies Lambertian relighting and color transfer — all without any AI model. Fully deterministic and procedural.

**Key features:**
- **Auto light direction detection:** samples luminance of the room photo to determine light source direction (left/right, top/bottom)
- **Contact shadow:** Gaussian-profile shadow band at `floor_y`, with directional offset based on light direction
- **Auto-calibrated opacity and blur:** derived from floor luminance — darker floors get stronger, softer shadows
- **Lambertian relighting:** surface normals estimated from alpha mask, shading applied with detected light direction
- **Reinhard LAB color transfer:** matches furniture colors to room palette automatically
- No AI, no models, no GPU required — runs in ~50ms

**Inputs:**
| Input | Description |
|---|---|
| `room_image` | Room photo (IMAGE) |
| `sofa_image` | Padded furniture render from node 18 (SIMTU Pad Sofa to Room) |
| `sofa_mask` | Furniture mask (MASK) |
| `floor_y` | Floor Y coordinate from node 17 (SIMTU Depth-Based Placement) |
| `x_offset` | Furniture X offset from node 17 |
| `shadow_opacity` | Base shadow opacity (auto-calibrated from floor luminance) |
| `shadow_blur_px` | Shadow blur radius in pixels |
| `contact_band_px` | Height of contact shadow band |
| `ambient_light` | Ambient light level for Lambertian shading |
| `relight_strength` | Relighting blend (capped at auto-detected strength) |
| `shadow_dx/dy_override` | Manual light direction override (0.0 = auto) |

**Outputs:** `room_with_shadow` (IMAGE), `sofa_relighted` (IMAGE), `shadow_dx` (FLOAT), `shadow_dy` (FLOAT)

---

## Dependencies

- ComfyUI
- `numpy`, `torch` (no additional models needed)

---

## Installation

### Via ComfyUI Manager
Search for `SIMTU Light & Shadow` and install.

### Manual
```bash
cd ComfyUI/custom_nodes
git clone https://github.com/simtu-store/comfyui-simtu-light-shadow
```

---

## Part of the SIMTU pipeline

This node works together with:
- [comfyui-simtu-splat-render](https://github.com/simtu-store/comfyui-simtu-splat-render) — Gaussian Splat rendering with auto-camera
- [comfyui-simtu-depth-placement](https://github.com/simtu-store/comfyui-simtu-depth-placement) — procedural scale and floor placement

---

## License

MIT License. See [LICENSE](LICENSE).
