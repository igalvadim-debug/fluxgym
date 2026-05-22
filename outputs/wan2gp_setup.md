# Wan2GP Setup — Lokale Modelle & Konfiguration

Dieses Dokument beschreibt den aktuellen Stand der Wan2GP-Konfiguration mit lokalen Modellen, erstellten JSON-Definitionen und offenen Punkten für die weitere Einrichtung.

---

## Systemübersicht

- **Wan2GP Pfad:** `D:\claudeAgent\Wan2GP`
- **Modelle (primär):** `C:\ai_diffusion\models\`
- **Modelle (ComfyUI):** `D:\ComfyUiCuda\models\`
- **GPU:** RTX 5060 (8GB VRAM)
- **Python:** 3.12

---

## wgp_config.json — Aktueller Stand

```json
{
    "lm_decoder_engine": "D:\\SillyTavernNeurogen\\kobold\\models\\Dolphin3.0-Llama3.1-8B-abliterated.i1-Q4_K_M.gguf",
    "checkpoints_paths": [
        "ckpts",
        "C:\\ai_diffusion\\models\\vae",
        "C:\\ai_diffusion\\models\\text_encoders",
        "C:\\ai_diffusion\\models\\text_encoders\\gguf",
        "C:\\ai_diffusion\\models\\unet\\LtxVideo",
        "C:\\ai_diffusion\\models\\unet\\Flux1",
        "C:\\ai_diffusion\\models\\unet\\Flux2",
        "D:\\ComfyUiCuda\\models\\diffusion_models",
        "D:\\ComfyUiCuda\\models\\unet",
        "."
    ],
    "loras_root": "C:\\ai_diffusion\\models\\loras\\wan_loras"
}
```

**Wichtig:** Doppelte Backslashes (`\\`) sind in der echten JSON erforderlich.

---

## Gelöschte Dateien (aus `ckpts\`)

Diese Dateien wurden manuell gelöscht um Speicher zu sparen:

| Datei | Größe | Grund |
|-------|-------|-------|
| `umt5-xxl/models_t5_umt5-xxl-enc-bf16.safetensors` | 10.58 GB | Klon vorhanden in `C:\ai_diffusion\models\text_encoders` |
| `ltxv_0.9.8_13B_distilled_quanto_bf16_int8.safetensors` | 13.2 GB | Ersetzt durch `LTX-2.3-dev-Q4_K_M.gguf` |
| `ltxv_0.9.7_VAE.safetensors` | 2.49 GB | Nur für das 13B safetensors-Modell benötigt |

**Ersatz Text Encoder:** `C:\ai_diffusion\models\text_encoders\gguf\umt5-xxl-encoder-Q6_K.gguf`

---

## Neu erstellte JSON-Definitionen (`defaults\`)

Alle Dateien müssen **UTF-8 ohne BOM** sein. PowerShell-Befehl zum sicheren Schreiben:
```powershell
[System.IO.File]::WriteAllText("pfad\datei.json", '<inhalt>', [System.Text.Encoding]::UTF8)
```

### flux2_klein_9b_gguf.json
```json
{
    "model": {
        "name": "Flux 2 Klein 9B GGUF Q5_0",
        "architecture": "flux2_klein_9b",
        "description": "FLUX.2 Klein 9B GGUF Q5_0 local.",
        "URLs": [
            "C:\\ai_diffusion\\models\\unet\\Flux2\\flux-2-klein-9b-Q5_0.gguf"
        ]
    },
    "prompt": "a glass greenhouse filled with lush tropical plants",
    "resolution": "1024x1024",
    "num_inference_steps": 4
}
```

### ltxv_distilled_gguf.json
```json
{
    "model": {
        "name": "LTX Video 2.3 Dev GGUF Q4_K_M",
        "architecture": "ltxv_13B",
        "description": "LTX Video 2.3 Dev GGUF Q4_K_M. Expects long prompts, use Prompt Enhancer.",
        "URLs": [
            "C:\\ai_diffusion\\models\\unet\\LtxVideo\\LTX-2.3-dev-Q4_K_M.gguf"
        ],
        "preload_URLs": "ltxv_13B",
        "LTXV_config": "models/ltx_video/configs/ltxv-13b-0.9.8-distilled.yaml"
    },
    "num_inference_steps": 6
}
```

### ltxv_2b_f16_gguf.json
```json
{
    "model": {
        "name": "LTX Video 2b v0.9 F16 GGUF",
        "architecture": "ltxv_13B",
        "description": "LTX Video 2b v0.9 F16 GGUF - fast lightweight version.",
        "URLs": [
            "C:\\ai_diffusion\\models\\unet\\LtxVideo\\ltx-video-2b-v0.9-F16.gguf"
        ],
        "LTXV_config": "models/ltx_video/configs/ltxv-13b-0.9.8-distilled.yaml"
    },
    "num_inference_steps": 6
}
```

---

## Lokale GGUF-Modelle (Übersicht)

### Text Encoder (`C:\ai_diffusion\models\text_encoders\gguf\`)
| Datei | Größe |
|-------|-------|
| `umt5-xxl-encoder-Q6_K.gguf` | 4.67 GB |
| `t5-v1_1-xxl-encoder-Q5_K_M.gguf` | 3.39 GB |
| `t5xxl_fp16-q4_0.gguf` | 2.90 GB |
| `Qwen3-4B-Q4_K_M.gguf` | 2.50 GB |
| `qwen2.5-vl-7b-instruct-q8_0.gguf` | 8.10 GB |
| `Mistral-Small-3.2-24B-Instruct-2506-UD-IQ1_M.gguf` | 6.02 GB |

### UNet / Diffusion Models
| Datei | Pfad |
|-------|------|
| `flux-2-klein-9b-Q5_0.gguf` | `unet\Flux2\` |
| `flux1-dev-Q5_0.gguf` | `unet\Flux1\` |
| `flux1-dev-Q6_K.gguf` | `unet\Flux1\` |
| `flux1-fill-dev-Q4_0.gguf` | `unet\Flux1\` |
| `fluxNSFWUNLOCKED_v3Q5KM.gguf` | `unet\Flux1\` |
| `fluxunchained-dev-q4-0.gguf` | `unet\Flux1\` |
| `chroma-unlocked-v46-detail-calibrated-Q4_0.gguf` | `unet\` |
| `LTX-2.3-dev-Q4_K_M.gguf` | `unet\LtxVideo\` |
| `ltx-video-2b-v0.9-F16.gguf` | `unet\LtxVideo\` |
| `ltx-video-2b-v0.9-f32.gguf` | `unet\LtxVideo\` |
| `ltx-video-2b-v0.9-q4_0.gguf` | `unet\LtxVideo\` |

### ComfyUI GGUFs (`D:\ComfyUiCuda\models\unet\`)
| Datei |
|-------|
| `wan2.1-i2v-14b-480p-Q4_K_S.gguf` |
| `Wan2.2-T2V-A14B-HighNoise-Q4_K_M.gguf` |
| `Wan2.2-T2V-A14B-LowNoise-Q4_K_M.gguf` |
| `Wan2.2-TI2V-5B-Q8_0.gguf` |
| `wan2.2_i2v_high_noise_14B_Q4_K_M.gguf` |
| `wan2.2_i2v_low_noise_14B_Q4_K_M.gguf` |
| `z-image-Q8_0.gguf` |
| `DasiwaWAN22I2V14BTastysinV8_q8High.gguf` |

---

## LoRA Struktur

**`loras_root`:** `C:\ai_diffusion\models\loras\wan_loras`

Wan2GP scannt rekursiv — Unterordner pro Modell-Familie sind bereits korrekt angelegt (`flux`, `flux2`, `ltxv`, `ltx2`, `z_image`, `wan`, etc.)

### Verschobene LoRAs
Diese LoRAs wurden aus `C:\ai_diffusion\models\loras\LTVX\` nach `wan_loras\ltx2\` verschoben:
- `ltx-2-19b-distilled-lora-384.safetensors`
- `ltx-2-19b-lora-camera-control-dolly-left.safetensors`

---

## Offene Punkte / Zu prüfen

- [ ] **flux2_klein_9b_gguf** testen — lädt das Modell ohne Download? Architecture `flux2_klein_9b` mit GGUF evtl. nicht unterstützt
- [ ] **ltxv_distilled_gguf** testen — erkennt Wan2GP `LTX-2.3-dev-Q4_K_M.gguf` korrekt über `ltxv_13B` Architecture?
- [ ] **umt5-xxl Text Encoder** — wird `umt5-xxl-encoder-Q6_K.gguf` automatisch gefunden ohne Download? (Pfad `text_encoders\gguf` ist in `checkpoints_paths`)
- [ ] **flux2_klein_base_4b** — safetensors wurde heruntergeladen, braucht aber `qwen3_bf16.safetensors` (8GB) als Text Encoder — noch nicht vorhanden
- [ ] **Prompt Enhancer** mit Dolphin 3.0 testen (`lm_decoder_engine` gesetzt)

---

## Bekannte Probleme

### UTF-8 BOM Fehler
`Set-Content` in PowerShell schreibt standardmäßig UTF-8 mit BOM — Wan2GP wirft `JSONDecodeError: Unexpected UTF-8 BOM`.
**Fix:** Immer `[System.IO.File]::WriteAllText(..., [System.Text.Encoding]::UTF8)` verwenden.

### Ungewollte Downloads
Wan2GP startet sofort einen Download wenn ein Modell aus der UI ausgewählt wird und die Datei lokal nicht gefunden wird — ohne Warnung. Vor Modellwechsel immer prüfen ob die Datei lokal vorhanden ist.

### GGUF vs. safetensors
Wan2GP-Architekturen unterstützen nicht immer beide Formate. Beispiel: `flux2_klein_base_4b` erwartet `safetensors` + separaten `qwen3_bf16.safetensors` Text Encoder (8GB) — GGUF-Variante existiert nicht offiziell.
