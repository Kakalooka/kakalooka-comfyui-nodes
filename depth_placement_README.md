# ComfyUI SIMTU Depth-Based Placement

Procedural furniture placement nodes for virtual staging pipelines in ComfyUI.  
Part of the [SIMTU](https://simtu.store) virtual staging toolkit — built for architects, interior designers, and real estate professionals.

## Nodes

### SIMTU Depth-Based Placement
Calculates the correct scale and floor position for a furniture piece in a room photo. Fully procedural — no manual tuning per image or per furniture item.

**Key features:**
- Horizon detection via vanishing points (Hough transform)
- Criminisi cross-ratio scaling when ceiling is in frame
- Non-metric VP-based scaling when ceiling is out of frame (v5.2)
- DA3 metric depth integration for procedural `floor_y` detection (v5.6)
- Fallback to DepthAnythingV2 when DA3 is not connected

**Inputs:**
| Input | Description |
|---|---|
| `room_image` | Room photo (IMAGE) |
| `sofa_width_cm` | Real-world furniture width in cm |
| `sofa_height_cm` | Real-world furniture height in cm |
| `ceiling_in_frame` | `auto` / `yes` / `no` |
| `nonmetric_fraction` | Fraction of image height occupied by furniture at back wall |
| `depth_floor_fraction` | Depth fraction for procedural floor_y (DA3 mode) |
| `cam_pitch_deg_in` | Camera pitch from node 35 (SIMTU Estimate Camera Pitch) |
| `horizon_y_px` | Horizon Y from node 35 |
| `depth_image_in` | DA3 metric depth image (optional, replaces internal DepthAnythingV2) |

**Outputs:** `scale_factor`, `floor_y`, `x_offset`, `sofa_w_scaled`, `sofa_h_scaled`, `depth_image`, `anchor_offset_px`

---

### SIMTU Pad Sofa to Room
Resizes and pads the rendered furniture splat to room dimensions, placing the furniture base precisely at `floor_y`.

**Anchor hierarchy (v5.4):**
1. `anchor_offset_3d` from node 25 (3D geometry — most accurate)
2. `depth_image_in` + mask blend (procedural v5.3)
3. Alpha mask fallback

**Inputs:** `sofa_image`, `sofa_mask`, `room_image`, `scale_factor`, `floor_y`, `x_offset`, optional overrides and 3D anchor

---

### SIMTU Mask Image to Mask
Utility: converts a 3-channel IMAGE mask to ComfyUI MASK type.

---

## Dependencies

- ComfyUI
- [comfyui-depthanythingv2](https://github.com/stavsap/comfyui-depthanythingv2) — for internal DepthAnythingV2 fallback
- [ComfyUI-DepthAnythingV3](https://github.com/PozzettiAndrea/ComfyUI-DepthAnythingV3) — optional, for metric depth
- `opencv-python`, `numpy`, `torch`, `safetensors`

**Required model (DepthAnythingV2 fallback):**  
`depth_anything_v2_vitb_fp32.safetensors` → `ComfyUI/models/depthanything/`  
Download from [Hugging Face](https://huggingface.co/depth-anything/Depth-Anything-V2-Base/resolve/main/depth_anything_v2_vitb_fp32.safetensors)

---

## Installation

### Via ComfyUI Manager
Search for `SIMTU Depth-Based Placement` and install.

### Manual
```bash
cd ComfyUI/custom_nodes
git clone https://github.com/simtu-store/comfyui-simtu-depth-placement
```

---

## Usage

Connect in this order:
```
Room Photo → SIMTU Estimate Camera Pitch → SIMTU Depth-Based Placement → SIMTU Pad Sofa to Room
                                                       ↑
                               DA3 metric depth (optional but recommended)
```

Best results with [ComfyUI-DepthAnythingV3](https://github.com/PozzettiAndrea/ComfyUI-DepthAnythingV3) connected to `depth_image_in` with `normalization_mode=Raw`.

---

## Part of the SIMTU pipeline

This node works together with:
- [comfyui-simtu-splat-render](https://github.com/simtu-store/comfyui-simtu-splat-render) — Gaussian Splat rendering with auto-camera
- [comfyui-simtu-light-shadow](https://github.com/simtu-store/comfyui-simtu-light-shadow) — deterministic shadow and relighting

---

## License

MIT License. See [LICENSE](LICENSE).
