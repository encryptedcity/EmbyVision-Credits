# EmbyVision Credits

**AI-powered end credits detection for Emby**

EmbyVision Credits automatically finds where the end credits start in TV episodes and creates chapter markers so Emby can skip them. It combines OCR (PaddleOCR / Tesseract), black-frame analysis, audio fingerprinting (Chromaprint), and episode learning for high accuracy across live-action and anime.

**Version:** 1.0.0.1 (OCR Edition)  
**Target:** Emby Server · .NET 6

---

## Features

### Detection engines
- **PaddleOCR** (CPU & GPU) — deep-learning OCR with strong multi-language support
- **Tesseract OCR** — traditional OCR engine
- **Black Frame Detection** — excellent for anime and many live-action shows
- **Chromaprint / Audio Fingerprinting** — matches credit sequences by audio
- **Automatic Episode Learning** — uses previous episodes in a season to narrow the search window

### Detection techniques
- Keyword & text detection on video frames
- Character density detection
- Black-screen / black-frame analysis
- Audio silence & audio pattern detection
- Video pattern & scene-change detection
- Scrolling credits pattern detection
- Fuzzy keyword matching
- Region of Interest (ROI), sharpening, and image preprocessing
- Adaptive frame rate & predictive sampling
- Smart frame skipping and early-stop on consecutive matches
- Parallel processing with configurable batch size and delays

### Emby integration
- Creates Emby-compatible **credits chapter markers**
- Works with Emby’s **Auto Skip** feature
- Per-series Auto Skip exclusions
- Optional use of embedded chapter markers
- Thumbnail generation at detected credit points
- Scheduled library-wide detection task
- Scheduled marker backup task

### Management & tooling
- Manual detection (series / season / episode)
- Queue all shows, dry run, and dry-run debug
- Live progress monitoring and cancel support
- Per-series / tag / studio / library **detection rules**
- Backup & restore of all credits markers (JSON)
- Bulk export / import
- Tracer mode for failed and detected episodes
- Notifications on scheduled or auto detection
- Full configuration UI with Setup Guide and API docs

---

## Requirements

| Component | Requirement |
|-----------|-------------|
| Emby Server | Recent version with plugin support |
| Plugin runtime | .NET 6 |
| OCR service (recommended) | EmbyVision OCR / PaddleOCR (CPU or GPU) **or** Tesseract |
| Optional | NVIDIA GPU + drivers for GPU OCR edition |

The plugin runs **inside Emby**. The OCR engine runs as a **separate service** (usually Docker) that the plugin calls over HTTP.

---

## Installation

### 1. Install the plugin

1. Download the latest release DLL (or build from source — see below).
2. Copy `EmbyVisionCredits.dll` into Emby’s plugins folder:
   - **Windows:** `%AppData%\Emby-Server\programdata\plugins`
   - **Linux / Docker:** `/config/plugins` (or your mapped plugins path)
   - **Unraid:** typically `/mnt/user/appdata/emby/plugins`
3. Restart Emby Server.
4. Confirm **EmbyVision Credits** appears under **Dashboard → Plugins**.

### 2. Run an OCR service

The plugin needs an OCR endpoint. Two common options:

#### EmbyVision OCR — CPU Edition (port 8000)

```bash
docker run -d --name embyvision-ocr-cpu \
  -p 8000:8000 \
  your-ocr-cpu-image
```

#### EmbyVision OCR — GPU Edition (port 8001)

Requires NVIDIA Container Toolkit and a compatible GPU.

```bash
docker run -d --name embyvision-ocr-gpu \
  --gpus all \
  -p 8001:8001 \
  your-ocr-gpu-image
```

> Exact image names and compose examples are in the plugin’s **Setup Guide** tab after installation.

### 3. Configure the plugin

1. Open **EmbyVision Credits** in the Emby dashboard.
2. Go to **Settings**.
3. Enable **External OCR** if using a remote service.
4. Set **OCR Engine** to `PaddleOCR` (or `Tesseract`).
5. Set **PaddleOCR API Endpoint**, for example:
   - `http://192.168.1.x:8000` (CPU)
   - `http://192.168.1.x:8001` (GPU)
   - `http://localhost:8000` (same machine)
6. Click **Test Connection**. You should see a success message.
7. Tune detection options (search start, frame rate, keywords, etc.) or keep the defaults.
8. Optionally create **Rules** for specific series, tags, studios, or libraries.

---

## Quick start

1. Install plugin + OCR service and confirm **Test Connection** succeeds.
2. Open **Manual Detection**.
3. Select a series (or queue all shows).
4. Optionally enable **Skip existing credit markers**.
5. Click **Process Selection** or **Dry Run** first.
6. When finished, check episode chapter markers in Emby — credits should be marked and skippable.

For library-wide automation, enable the scheduled task **Detect Credits in TV Shows** under Emby’s Scheduled Tasks.

---

## Configuration overview

| Area | What it controls |
|------|------------------|
| **OCR Service Connection** | Endpoint, engine (PaddleOCR / Tesseract), timeout, retries |
| **OCR Detection** | Search start, frame rate, max frames, confidence, keywords |
| **Episode Comparison** | Learn from previous episodes to speed up and improve accuracy |
| **Character Density** | Optional density-based detection and false-positive filters |
| **Black Frame** | Thresholds, duration, density, parallel sessions, anime mode |
| **Chromaprint** | Audio fingerprint matching, fallbacks, similarity thresholds |
| **Hardware / FFmpeg** | CUDA/NVDEC, VAAPI, threads, software fallback |
| **Rules** | Per-series / tag / studio / library overrides |
| **Auto Skip** | Series excluded from automatic credit skip |
| **Notifications** | Alerts on scheduled or auto detection |
| **Backup** | Export/import markers, scheduled backups, auto-backup |
| **Tracer** | Track detected and failed episodes |

Full option descriptions are available in the plugin UI and Setup Guide.

---

## Building from source

```bash
git clone https://github.com/YOUR_USER/EmbyVision-Credits.git
cd EmbyVision-Credits
dotnet build -c Release
```

Output DLL:

```text
bin/Release/net6.0/EmbyVisionCredits.dll
```

Copy that file into Emby’s plugins folder and restart Emby.

**Requirements to build:** .NET 6 SDK.

---

## API

The plugin exposes REST-style endpoints under `/CreditsDetector/`, including:

- Start / cancel detection and queue management
- Dry run and progress
- Marker update, apply-to-season, season validation
- Backup export / import and bulk export
- Tracer and failure-marker management
- OCR connection test
- Thumbnails and debug helpers

See the **API** tab in the plugin configuration UI for the full list and examples.

---

## Troubleshooting

| Problem | Things to check |
|---------|-----------------|
| **Test Connection fails** | OCR container running? Correct IP/port? Firewall? Endpoint must be reachable from the Emby host. |
| **“Status was not successful”** | Ensure you’re on a build that accepts common PaddleOCR response shapes (results/data arrays, etc.). |
| **No markers created** | Run a Dry Run, check Tracer for failures, verify keywords and search-start settings. |
| **Slow detection** | Enable Episode Comparison, lower frame rate, enable hardware acceleration, or use GPU OCR. |
| **Anime credits missed** | Try Black Frame detection / anime mode and adjust black-frame thresholds. |
| **Plugin not loading** | Confirm .NET 6-compatible Emby build and that the DLL is in the correct plugins folder. |

Logs: Emby server logs + OCR container logs (`docker logs <container>`).

---

## Project structure (source)

```text
EmbyVision-Credits/
├── EmbyVisionCredits/                 # Plugin entry, config, enums
├── EmbyVisionCredits.Api/             # Request/response models & routes
├── EmbyVisionCredits.Services/        # Detection, markers, backup, tracer
│   └── DetectionMethods/              # OCR, BlackFrame, Chromaprint, …
├── EmbyVisionCredits.ScheduledTasks/  # Detection & backup tasks
├── EmbyVisionCredits.Configuration.*  # HTML/JS configuration UI
└── EmbyVisionCredits.csproj
```

---

## Version

| Field | Value |
|-------|--------|
| Plugin version | **1.0.0.1** |
| Target framework | **net6.0** |
| Edition | **OCR Edition** |

---

## License

Specify your license here (e.g. MIT, GPL-3.0, or proprietary).  
Until a license file is added, all rights are reserved by the author.

---

## Credits

- **EmbyVision Credits** — end credits detection plugin for Emby
- OCR powered by **PaddleOCR** / **Tesseract** (via external service)
- Audio fingerprinting via **Chromaprint**
- Built for the Emby media server ecosystem

---

## Support

- Use the **Tracer** and **Dry Run Debug** tools in the UI for diagnostics.
- Check Emby logs and OCR service logs when reporting issues.
- Open a GitHub Issue with: Emby version, plugin version, OCR setup (CPU/GPU), and relevant log snippets.

---

**EmbyVision Credits** — intelligent end credits detection for Emby.
