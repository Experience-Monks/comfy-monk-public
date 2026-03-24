<video src="https://github.com/user-attachments/assets/c06b6fba-fcb8-4831-a402-04dd64125547" controls></video>

# Comfy Monk

A Photoshop plugin that connects to a ComfyUI backend, enabling AI-powered image generation and inpainting directly inside Photoshop.

Built by the Monks creative technology team.

---

## ⚡ Install

**1. Download the latest `.ccx`** from the [Releases](../../releases) page and open it. Photoshop installs it automatically.

**2. Open the plugin, click ⚙ Settings, and fill in:**

| Field | Value |
|-------|-------|
| API URL | The shared endpoint — ask a CT for this |
| Gemini Key | Required for Guided (Nano) and QA Tool modes only |

Hit **Save**.

**3. Click Generate.** That's it.

The backend scales automatically — no setup, no Wake button. If it's been idle, the first generation takes a few minutes while the backend loads. After that it stays warm and responds instantly.

---

## What it does

| Mode | Description |
|------|-------------|
| Text to Image | Generate an image from a prompt, placed as a new layer |
| Inpaint | Make a selection, describe the edit, get a result placed in place |
| Guided | Inpaint with one or two reference images |
| Guided (Nano) | Guided inpainting powered by Gemini |
| Upscale | Upscale the current canvas with SeedVR2 |
| Upscale (Nano) | Upscale via Imagen4 at 2×/3×/4× |
| QA Tool | Compare ASIN/SKU, original, and regenerated canvas against client feedback |

---

## Usage

**Generation controls:**
- **Steps** — diffusion steps (default: 4)
- **Batch** — sequential generations
- **Mask Res** — inpaint crop resolution (higher = more detail, slower)
- **Seed** — set manually or lock to repeat a result

The status bar shows whether the backend is warm (🟢 Ready) or cold (⚫ Cold). After each generation a 5-minute countdown runs — if it hits zero before your next generation, expect a short wait on the next one.

---

## ⚠ Known limitations

**300 DPI canvases** — the plugin only supports 72 DPI documents. At 300 DPI the result will be placed at the wrong scale.  
Workaround: Image → Image Size → uncheck Resample → set Resolution to 72.

---

## Questions

Ping `#comfy-monk-plugin` on Slack.
