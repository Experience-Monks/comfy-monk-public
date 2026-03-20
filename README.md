<https://github.com/user-attachments/assets/c06b6fba-fcb8-4831-a402-04dd64125547>

# Comfy Monk

A Photoshop UXP plugin that connects to a ComfyUI backend via FastAPI, enabling AI-powered image generation and inpainting directly inside Photoshop.

Built by the Monks creative technology team.

---

## What it does

| Mode | Description |
|------|-------------|
| Text to Image | Generate an image from a prompt, placed as a new layer |
| Inpaint | Make a selection, describe the edit, get a result placed in place |
| Guided | Inpaint with one or two reference images for guided generation |
| Guided (Nano) | Guided inpainting with Gemini-assisted prompt and image generation |
| Upscale | Upscale the current Photoshop canvas with SeedVR2 |
| Upscale (Nano) | Upscale via Imagen4 at 2×/3×/4×, requires GCP project ID |
| QA Tool | Compare ASIN/SKU, original, and regenerated canvas against client feedback |

---

## ⚡ Getting started

**1. Download and install the plugin** — grab the latest `.ccx` from the [Comfy Monk Downloads](https://drive.google.com/drive/folders/1FfHtJLrqEHy9x7ILJtwKZPM0CN1pYD9K?usp=sharing) folder and open it. Photoshop installs it automatically.

**2. Get a studio** — ask a CT for your machine credentials, or grab a pre-configured studio from the Lightning AI dashboard.

**3. Open the plugin, click ⚙ Settings, and fill in:**

| Field | Value |
|-------|-------|
| API URL | Your studio's URL (`https://8000-XXXX.cloudspaces.litng.ai`) |
| Studio ID | Your studio name from the Lightning dashboard (e.g. `comfy-az-vclq`) |
| Teamspace | The shared org teamspace name |
| Machine | GPU tier to wake the studio with (L40S default) |
| Gemini Key | Required for Guided (Nano) and QA Tool modes only |

Hit **Save**.

**4. Hit Wake** in the plugin. First boot takes ~10 minutes for ComfyUI to fully load — subsequent wakes are faster.

**5. Generate.**

> **New studio?** First wake must be done from the Lightning dashboard to confirm the machine type. After that, Wake/Sleep works from the plugin.
> 
> **Outside the AI-Retail-UI teamspace?** Duplicate `aru-comfymonk-001` in the Lightning dashboard, rename it using your teamspace acronym as prefix (e.g. `xyz-comfymonk-001`), then follow the steps above.


---

## 🔧 Full install — set up from scratch

Use this if you're setting up a new GPU studio from scratch rather than duplicating the template.

### Prerequisites

- Adobe Photoshop 26.0+ with UXP support
- A Lightning AI account with GPU access
- A Lightning AI API key (Settings → Keys → Programmatic Access)
- A Gemini API key (for Guided Nano and QA modes)

### Models

Download and place these on the GPU studio:

| Model | Folder |
|-------|--------|
| `flux-2-klein-9b-fp8.safetensors` | `ComfyUI/models/diffusion_models/` |
| `qwen_3_8b.safetensors` | `ComfyUI/models/clip/` |
| `flux2-vae.safetensors` | `ComfyUI/models/vae/` |

For Upscale mode, also install the SeedVR2 models referenced by `workflows/comfymonk-upscale.json` and any required custom nodes via ComfyUI Manager.

### GPU studio setup

**1. Clone the repo on the studio:**
```bash
git clone git@github.com:Experience-Monks/comfy-monk.git ~/comfy-monk
```

**2. Install ComfyUI** if not already present:
```bash
git clone https://github.com/comfyanonymous/ComfyUI.git ~/ComfyUI
pip install -r ~/ComfyUI/requirements.txt
```

**3. Install comfy-monk dependencies:**
```bash
pip install -r ~/comfy-monk/requirements.txt
```

**4. Create a `.env` file** at `/teamspace/studios/this_studio/.env`:
```bash
LIGHTNING_USER_ID=your-user-id
LIGHTNING_API_KEY=your-api-key
LIGHTNING_TEAMSPACE=your-teamspace
LIGHTNING_ORG=your-org
GEMINI_API_KEY=your-gemini-key
```

**5. Set up the startup script:**
```bash
cat > ~/.lightning_studio/on_start.sh << 'EOF'
#!/usr/bin/env bash
bash /teamspace/studios/this_studio/comfy-monk/scripts/on_start.sh
EOF
chmod +x ~/.lightning_studio/on_start.sh
```

**6. Set up a deploy key** so `on_start.sh` can pull updates on wake:
```bash
ssh-keygen -t ed25519 -C "comfy-monk-studio" -f ~/.ssh/comfy_monk_deploy -N ""
cat ~/.ssh/comfy_monk_deploy.pub
```
Add the public key to the repo: **GitHub → Settings → Deploy keys → Add deploy key** (read-only).

Then configure SSH to use it:
```bash
cat >> ~/.ssh/config << 'EOF'
Host github.com
  IdentityFile ~/.ssh/comfy_monk_deploy
  StrictHostKeyChecking no
EOF
git -C ~/comfy-monk remote set-url origin git@github.com:Experience-Monks/comfy-monk.git
```

**7. Expose ports** in Lightning AI (My Studio → Ports → Add Port):

| Port | Service |
|------|---------|
| `8188` | ComfyUI |
| `8000` | FastAPI bridge |

**8. Wake the studio** from the Lightning dashboard. After ~60s, verify:
```
https://8000-XXXX.cloudspaces.litng.ai/health
```

### Plugin setup

Load unpacked via **Plugins → Development → Load Unpacked Plugin** → point to the `comfy-monk/` folder on your Mac.

Then follow steps 5–6 from the Fast Install section above.

---

## Usage

**Machine controls** (top of plugin):
- **Wake** — starts the GPU studio
- **Sleep** — stops it (saves cost)
- Status dot: 🟢 running · ⚫ stopped · 🟡 starting/stopping

**Generation controls:**
- **Steps** — diffusion steps (default: 4)
- **Batch** — sequential generations
- **Mask Res** — inpaint crop resolution (higher = more detail, slower)
- **Seed** — set manually or lock to repeat a result

---

## ⚠ Known limitations

**300 DPI canvases** — the plugin only supports 72 DPI documents. At 300 DPI the result will be placed at the wrong scale.
Workaround: Image → Image Size → uncheck Resample → set Resolution to 72.

---

## Contributing

Branch off `main`, test in Photoshop before opening a PR. Follow the branch → PR → merge → tag → release flow. Ping `#ps-comfy-plugin` on Slack with questions.

For infrastructure changes (Cloudflare Worker, CPU proxy, Lightning SDK setup), see [`INFRASTRUCTURE.md`](./INFRASTRUCTURE.md).
