<p align="center">
  <img src="https://img.shields.io/badge/FireGuard-AI%20Fire%20%26%20Smoke%20Safety-FF3D00?style=for-the-badge&logoColor=white" alt="FireGuard Header"/>
</p>

<h1 align="center">🔥 FireGuard: Edge-to-Cloud AI Fire & Smoke Safety System</h1>

<p align="center">
  <strong>Autonomous real-time vision safety system featuring custom-trained YOLOv26s edge inference on NVIDIA Jetson, asynchronous WebSocket telemetry, and a Qt6 desktop command center.</strong>
</p>

<p align="center">
  <a href="https://github.com/AffanZulfiqar/FireGuard/stargazers"><img src="https://img.shields.io/github/stars/AffanZulfiqar/FireGuard?style=flat-square&color=gold" alt="GitHub Stars"/></a>
  <a href="https://github.com/AffanZulfiqar/FireGuard/network/members"><img src="https://img.shields.io/github/forks/AffanZulfiqar/FireGuard?style=flat-square&color=blue" alt="GitHub Forks"/></a>
  <a href="https://github.com/AffanZulfiqar/FireGuard/issues"><img src="https://img.shields.io/github/issues/AffanZulfiqar/FireGuard?style=flat-square&color=red" alt="GitHub Issues"/></a>
  <img src="https://img.shields.io/badge/Python-3.10+-3776AB?style=flat-square&logo=python&logoColor=white" alt="Python 3.10+"/>
  <img src="https://img.shields.io/badge/YOLO-v26s%20Custom-FF4500?style=flat-square&logo=pytorch&logoColor=white" alt="YOLOv26s"/>
  <img src="https://img.shields.io/badge/PySide6-Qt%206.7+-41CD52?style=flat-square&logo=qt&logoColor=white" alt="PySide6"/>
  <img src="https://img.shields.io/badge/FastAPI-0.115+-009688?style=flat-square&logo=fastapi&logoColor=white" alt="FastAPI"/>
  <img src="https://img.shields.io/badge/Hardware-NVIDIA%20Jetson-76B900?style=flat-square&logo=nvidia&logoColor=white" alt="NVIDIA Jetson"/>
  <img src="https://img.shields.io/badge/mAP%4050-80.48%25-brightgreen?style=flat-square" alt="mAP@50 80.48%"/>
  <img src="https://img.shields.io/badge/License-MIT-purple?style=flat-square" alt="License MIT"/>
</p>

<p align="center">
  <a href="#-overview">Overview</a> •
  <a href="#-key-features">Key Features</a> •
  <a href="#-system-architecture">Architecture</a> •
  <a href="#-dashboard--ui-showcase">UI Showcase</a> •
  <a href="#-model-performance--benchmarks">Model Benchmarks</a> •
  <a href="#-tech-stack">Tech Stack</a> •
  <a href="#-getting-started">Getting Started</a> •
  <a href="#-configuration">Configuration</a> •
  <a href="#-production-deployment">Deployment</a> •
  <a href="#-project-structure">Project Structure</a> •
  <a href="#-license">License</a>
</p>

---

## ⚡ Overview

Traditional ionization and optical smoke detectors often respond too late, cannot detect open flames without smoke buildup, and lack spatial localization. **FireGuard** is an end-to-end computer vision and IoT safety platform designed to replace legacy alarm workflows with sub-second optical detection.

Built as an enterprise-ready solution for industrial complexes, warehouses, server rooms, and academic campuses, FireGuard unites three tightly integrated tiers:

1. **Edge Intelligence (NVIDIA Jetson / Linux):** Runs a custom fine-tuned YOLOv26s model on continuous RTSP camera streams with hardware CUDA acceleration and temporal noise gating.
2. **Central Broker & Storage Hub (FastAPI + SQLite WAL):** Ingests lightweight alert payloads, manages stateful camera registries, persists audit logs, and routes real-time events over WebSocket.
3. **Desktop Command Center (PySide6 / Qt6 on Windows):** A reactive desktop interface offering live video feeds, real-time threat charts via PyQtGraph, audio alarms, incident logging, and one-click remote SSH management for edge nodes.

```
       [ IP / USB Cameras ]
                 │ (RTSP / USB Streams)
                 ▼
       [ NVIDIA Jetson Edge ] ──(Inference & Temporal Filter)──┐
                 │                                            │ (Sub-second Alert)
                 ▼                                            ▼
       [ FastAPI Central Hub ] ◄────────────────── [ WebSocket Event Pipe ]
                 │
                 ├─► [ SQLite DB (WAL Mode) ] (Persistent Incident Logs & Snapshots)
                 │
                 ▼
    [ PySide6 Desktop Command Center ] ──► [ Audio Alarm + PyQtGraph Analytics + SSH Remote Control ]
```

---

## 🌟 Key Features

- 🔥 **Dual-Class Simultaneous Detection:** Independent classifiers trained specifically for both rapid flame eruption and subtle, volumetric smoke plumes.
- ⚡ **Sub-Second Latency:** Frame capture, batch inference, alert qualification, and UI broadcast complete in under 500 ms.
- 🛡️ **Temporal Noise Gate:** Eliminates false alarms caused by transient flashes or lighting shifts via configurable consecutive-frame persistence logic.
- 📡 **Bandwidth Optimization:** Transmits only lightweight structured JSON telemetry and JPEG snapshots upon confirmed incidents—eliminating the need to stream raw high-bitrate video to the server.
- 📊 **Real-Time Visual Analytics:** Dynamic threat distribution, camera incident ranking, and timeline graphing powered by PyQtGraph.
- 🔊 **Automated Hazard Response:** Audible alarm system with mute, snooze, and multi-level hazard severity coloring (Critical Fire vs. Warning Smoke).
- 🗄️ **Complete Audit Trail:** Incident timestamps, camera identifiers, confidence scores, and inline snapshot previews with CSV export capabilities.
- 🛠️ **Remote Over-The-Air (OTA) Management:** Built-in SSH bridge allows operators to update model weights (`best.pt`), tweak confidence thresholds, and restart remote Jetson daemons directly from the desktop UI.
- 🚀 **Turnkey Installer:** Packaged as a standalone Windows executable and Inno Setup installer, accompanied by an automated one-line setup script for Jetson devices.

---

## 🏗 System Architecture

```mermaid
graph TD
    classDef default fill:#1E1E1E,stroke:#333,stroke-width:2px,color:#fff;
    classDef edge fill:#1565C0,stroke:#0D47A1,stroke-width:2px,color:#fff;
    classDef server fill:#6A1B9A,stroke:#4A148C,stroke-width:2px,color:#fff;
    classDef ui fill:#2E7D32,stroke:#1B5E20,stroke-width:2px,color:#fff;
    classDef camera fill:#E65100,stroke:#BF360C,stroke-width:2px,color:#fff;
    classDef db fill:#00695C,stroke:#004D40,stroke-width:2px,color:#fff;

    subgraph Sources["📷 Video Sources"]
        C1["IP Cameras (RTSP)"]:::camera
        C2["USB Webcams"]:::camera
    end

    subgraph EdgeNode["⚙️ Edge Node (NVIDIA Jetson / Linux PC)"]
        direction TB
        Capture["OpenCV VideoCapture (Stride=2)"]:::edge
        YOLO["YOLOv26s Inference Engine (CUDA)"]:::edge
        Logic["Temporal Buffering & Alert Gate"]:::edge
        Storage1["Local Snapshot & Clip Writer"]:::db
        WS_Client["WebSocket Telemetry Client"]:::edge

        Capture -->|Raw Frames| YOLO
        YOLO -->|Bounding Boxes + Conf| Logic
        Logic -->|Persist Artifacts| Storage1
        Logic -->|JSON Payload + JPEG| WS_Client
    end

    subgraph CentralServer["🌐 Central Hub (FastAPI Server)"]
        direction TB
        WS_Server["WebSocket Server Endpoint"]:::server
        AlertProc["Alert Verification & Telemetry Processor"]:::server
        SQLite[("SQLite Database\n(WAL Mode)")]:::db
        WS_Broadcaster["WebSocket Broadcaster Hub"]:::server

        WS_Server -->|Telemetry Ingest| AlertProc
        AlertProc -->|ACID Persistence| SQLite
        AlertProc -->|Real-Time Broadcast| WS_Broadcaster
    end

    subgraph DesktopApp["💻 Desktop Command Center (PySide6 / Windows)"]
        direction TB
        UI["PySide6 Application Window"]:::ui
        PyQtGraph["PyQtGraph Live Metrics"]:::ui
        Audio["QSound Audio Alarm Module"]:::ui
        SSH["Paramiko Remote Jetson Manager"]:::ui
        
        UI --- PyQtGraph
        UI --- Audio
        UI --- SSH
    end

    C1 -->|RTSP H.264 Stream| Capture
    C2 -->|DirectShow / V4L2| Capture
    WS_Client -->|"WebSocket (500ms intervals)"| WS_Server
    WS_Broadcaster -->|Live Events| UI
    SSH -.->|"SSH Commands / OTA Updates"| EdgeNode
```

### Data Pipeline Overview

| Pipeline Stage | Module / Technology | Technical Details |
|---|---|---|
| **Frame Acquisition** | OpenCV `cv2.VideoCapture` | Hardware-accelerated capture with configurable frame skip (`stride=2`) to minimize buffer lag |
| **Edge Inference** | YOLOv26s (PyTorch + CUDA) | 640×640 input resolution, Conf ≥ 0.80, IoU ≥ 0.61, batched edge execution |
| **Noise Filtering** | Temporal Continuity Gate | Requires $N$ consecutive positive detections before triggering an alert |
| **Telemetry Transport**| Async WebSocket (`websockets`) | Compressed base64 JPEG thumbnail + structured JSON incident metadata sent every 500ms |
| **Data Persistence** | SQLite (WAL Enabled) | High-concurrency Write-Ahead Logging mode with automatic retention cleanup |
| **Operator UI** | PySide6 (Qt 6) + PyQtGraph | Custom dark theme, hardware-accelerated charting, asynchronous network handlers |

---

## 🖥 Dashboard & UI Showcase

### 1. Live Fire Detection Event
> **Instant Warning**: When fire is confirmed, the top status bar flashes critical red, an audible siren sounds, and a timestamped snapshot is archived with camera coordinates.

<p align="center">
  <img src="model/app_screen_shots/Screenshot%202026-06-16%20151902.png" alt="Fire Detected Screenshot" width="95%"/>
</p>

---

### 2. Live Smoke Detection Event
> **Early Plume Warning**: Detects smoke formation before combustion reaches critical heat thresholds, preventing widespread structural damage.

<p align="center">
  <img src="model/app_screen_shots/Screenshot%202026-06-16%20152013.png" alt="Smoke Detected Screenshot" width="95%"/>
</p>

---

### 3. Multi-Camera Grid & Feed Management
> **Fleet Overview**: Add, inspect, and monitor unlimited IP cameras (RTSP) and local USB feeds. Real-time indicators display connection health, last-seen timestamps, and ping latency.

<p align="center">
  <img src="model/app_screen_shots/Screenshot%202026-06-16%20152335.png" alt="Camera Config Screenshot" width="95%"/>
</p>

---

### 4. Incident Audit Log & Event Inspection
> **Forensic Audit**: Comprehensive event log with severity filtering, camera sorting, inline snapshot inspection, acknowledgment tracking, and one-click CSV export.

<p align="center">
  <img src="model/app_screen_shots/Screenshot%202026-06-16%20152412.png" alt="Alert Log Screenshot" width="95%"/>
</p>

---

### 5. Real-Time Telemetry & Analytics
> **Actionable Intelligence**: Live PyQtGraph time-series charts displaying detection frequency, severity distribution, and risk ranking by camera sector.

<p align="center">
  <img src="model/app_screen_shots/Screenshot%202026-06-16%20152346.png" alt="Analytics Screenshot" width="95%"/>
</p>

---

### 6. Sensitivity & Audio Alarm Configuration
> **Threshold Tuning**: Granular control over confidence cutoffs for fire and smoke, audio alarm durations, snooze toggles, and storage retention policies.

<p align="center">
  <img src="model/app_screen_shots/Screenshot%202026-06-16%20152422.png" alt="Settings Screenshot" width="95%"/>
</p>

---

### 7. Remote Edge Node Management (Jetson OTA Control)
> **Zero-Friction Maintenance**: Deploy updated model weights (`best.pt`), edit remote YAML configurations, and restart the Jetson systemd background daemon over secure SSH.

<p align="center">
  <img src="model/app_screen_shots/Screenshot%202026-06-16%20152432.png" alt="Edge Config Screenshot" width="95%"/>
</p>

---

## 📊 Model Performance & Benchmarks

The detection model is a custom **YOLOv26s** network trained on **21,000+ curated and annotated fire, smoke, and non-hazard background images** across 200 epochs.

### Training Overview

<p align="center">
  <img src="model/26s_runs%20(2)/fireguard_v2_showcase_different.png" alt="Training Report Showcase" width="95%"/>
</p>

### Metric Progression (200 Epochs)

<p align="center">
  <img src="model/26s_runs%20(2)/runs/detect/fireguard_outputs/fire_smoke_26s/results.png" alt="Training Curves" width="95%"/>
</p>

### Evaluation Metrics (Best Checkpoint — Epoch 167)

| Metric | Score | Description |
|---|:---:|---|
| **mAP@50 (Overall)** | **80.48%** | Mean Average Precision at IoU threshold 0.50 |
| **mAP@50-95** | **51.87%** | Mean Average Precision across strict IoU range 0.50 to 0.95 |
| **Fire mAP@50** | **89.00%** | Flame detection precision |
| **Smoke mAP@50** | **87.00%** | Volumetric smoke detection precision |
| **Average F1 Score** | **85.80%** | Balanced harmonic mean of precision and recall |
| **Fire Precision** | **91.70%** | Low false positive rate for open flame |
| **Fire Recall** | **85.40%** | Consistent detection across varied illumination |
| **Smoke Precision** | **93.10%** | Resilience against fog, steam, and ambient dust |
| **Smoke Recall** | **82.30%** | Effective tracking of dispersing smoke plumes |

### Training Hyperparameters

| Hyperparameter | Value | Notes |
|---|---|---|
| **Architecture** | Custom YOLOv26s | Optimized for embedded Jetson CUDA cores |
| **Dataset Size** | 21,000 images | Balanced across fire, smoke, industrial, and false-positive negatives |
| **Input Resolution** | 640 × 640 px | High spatial resolution for distant flame points |
| **Batch Size** | 16 | Distributed across GPU memory |
| **Optimizer** | SGD | Initial $lr = 0.01$, Cosine Annealing, Patience = 50 |
| **Augmentation** | Mosaic, MixUp (0.1), CopyPaste (0.2), RandAugment | Robustness against reflections, glare, and low-light noise |

### Validation Predictions vs. Ground Truth

| Ground Truth Labels | Model Detections |
|:---:|:---:|
| <img src="model/26s_runs%20(2)/runs/detect/val/val_batch0_labels.jpg" width="100%" alt="Batch 0 Labels"/> | <img src="model/26s_runs%20(2)/runs/detect/val/val_batch0_pred.jpg" width="100%" alt="Batch 0 Predictions"/> |
| <img src="model/26s_runs%20(2)/runs/detect/val/val_batch1_labels.jpg" width="100%" alt="Batch 1 Labels"/> | <img src="model/26s_runs%20(2)/runs/detect/val/val_batch1_pred.jpg" width="100%" alt="Batch 1 Predictions"/> |

---

## 🛠 Tech Stack

### Desktop Command Center (Windows)
- **UI Framework:** PySide6 (Qt 6.7+)
- **Interactive Charting:** PyQtGraph
- **Backend API & Ingestion:** FastAPI + Uvicorn
- **Networking:** Async WebSocket (`websockets`, `httpx`)
- **Computer Vision & Media:** OpenCV (`opencv-python`), Pillow
- **Database:** SQLite3 with Write-Ahead Logging (`WAL`)
- **Remote Diagnostics:** Paramiko (SSH / SFTP)
- **Packaging:** PyInstaller + Inno Setup Compiler

### Edge Inference Node (NVIDIA Jetson / Linux)
- **Model Engine:** Ultralytics YOLOv26s
- **Deep Learning Framework:** PyTorch with NVIDIA JetPack CUDA acceleration
- **Video Capture:** OpenCV VideoCapture with hardware pipeline support
- **Daemon Lifecycle:** Linux `systemd` service integration

---

## 🚀 Getting Started

### Prerequisites
- **Operating System:** Windows 10/11 (for Desktop Dashboard) & Linux / JetPack (for Edge Node)
- **Python:** Version `3.10` or higher
- **Git**

---

### Method 1: Desktop Command Center (Windows)

#### Option A: One-Click Installer (Recommended)
1. Download `FireGuard_Installer_v1.0.exe` from [GitHub Releases](https://github.com/AffanZulfiqar/FireGuard/releases).
2. Run the installer. It configures the directory structure, registers system shortcuts, and sets up execution permissions automatically.
3. Launch FireGuard from your Start Menu.

#### Option B: Run from Source
```powershell
# 1. Clone repository
git clone https://github.com/AffanZulfiqar/FireGuard.git
cd FireGuard

# 2. Create and activate virtual environment
python -m venv server\venv
server\venv\Scripts\activate

# 3. Install dependencies
pip install -r server\requirements.txt

# 4. Configure environment variables
copy server\.env.example server\.env

# 5. Launch application
python app.py
```
> The FastAPI service starts on `http://localhost:8000` and the Qt6 desktop dashboard opens automatically.

---

### Method 2: Edge Node (NVIDIA Jetson / Linux)

#### Option A: One-Line Automated Setup (Recommended)
On your NVIDIA Jetson Nano / Xavier / Orin terminal, run:

```bash
curl -sSL https://raw.githubusercontent.com/AffanZulfiqar/FireGuard/main/edge/install.sh | bash
```

The script automatically:
1. Installs system build essentials and OpenCV dependencies.
2. Configures NVIDIA-optimized PyTorch with CUDA acceleration.
3. Clones the repository to `~/fireguard`.
4. Prompts for your Central Hub PC IP address and token.
5. Registers, enables, and launches the `fireguard-edge.service` background systemd daemon.

#### Option B: Manual Setup
```bash
# 1. Clone repository
git clone https://github.com/AffanZulfiqar/FireGuard.git
cd FireGuard/edge

# 2. Setup virtual environment
python3 -m venv edge_venv
source edge_venv/bin/activate

# 3. Install requirements
pip install -r requirements.txt

# 4. Place model weights
# Place your trained best.pt in the edge/ directory

# 5. Edit configuration
nano config.yaml

# 6. Start inference pipeline
python main.py
```

#### Standalone Live Detection Viewer
If you want to view the detection bounding boxes directly on a monitor connected to the edge device without streaming to the central hub, launch the standalone viewer:

```bash
python edge/live_viewer.py --camera 0 --conf 0.80
```

---

## ⚙️ Configuration

### 1. Edge Node — `edge/config.yaml`
```yaml
cameras:
  - id: 1
    name: "Warehouse North Gate"
    url: "rtsp://admin:password@192.168.1.100:554/h264Preview_01_main"
  - id: 2
    name: "Server Rack Cam"
    url: 0   # Local USB webcam (device index 0)

model:
  path: best.pt
  conf: 0.80        # Confidence cutoff threshold
  iou: 0.61         # NMS IoU threshold
  imgsz: 640        # Inference square resolution
  device: '0'       # '0' for CUDA GPU, 'cpu' for CPU mode

server:
  url: "ws://YOUR_SERVER_IP:8000/ws/edge"
  token: "fire-secret-token"    # Must match EDGE_TOKEN in server/.env

alert:
  min_consecutive: 2    # Consecutive positive frames required to confirm alert
  cooldown_sec: 30      # Minimum seconds between repeat alerts for same camera
  clip_duration_sec: 10 # Length of incident video clip recorded
  save_clips: true
  save_snapshots: true
```

### 2. Central Server — `server/.env`
Copy `server/.env.example` to `server/.env`:

| Key | Default | Description |
|---|---|---|
| `HOST` | `0.0.0.0` | Ingestion server bind address |
| `PORT` | `8000` | Port for HTTP endpoints and WebSocket pipelines |
| `EDGE_TOKEN` | `fire-secret-token` | Pre-shared secret matching edge `config.yaml` |
| `DASHBOARD_TOKEN` | `dashboard-secret` | Authentication token for desktop client sessions |
| `STORAGE_ROOT` | `./storage` | Directory where incident snapshots and clips are persisted |
| `ALERT_RETENTION_DAYS` | `30` | Auto-purging retention window for incident records |
| `MAX_SNAPSHOT_SIZE_MB` | `5` | Maximum allowable payload size for incoming frame buffers |
| `LOG_LEVEL` | `info` | Logging verbosity: `debug`, `info`, `warning`, `error` |

---

## 📦 Production Deployment

### Building the Windows Installer (`.exe`)

The codebase includes a preconfigured [PyInstaller spec](FireGuard_fixed.spec) and an [Inno Setup script](FireGuard_Setup.iss).

```powershell
# 1. Activate server venv
server\venv\Scripts\activate

# 2. Build standalone binary distribution
pyinstaller FireGuard_fixed.spec --noconfirm
```
This produces `dist\FireGuard\FireGuard.exe` bundling all dependencies, Qt plugins, sound assets, and `fireguard.ico`.

#### Creating the Setup Wizard:
1. Open **Inno Setup Compiler 6**.
2. Open `FireGuard_Setup.iss`.
3. Press `Ctrl + F9` to compile.
4. Output executable will be generated at `Output\FireGuard_Installer_v1.0.exe`.

---

### Managing the Edge Systemd Service on Jetson

```bash
# Check service health
sudo systemctl status fireguard-edge

# Restart service after configuration changes
sudo systemctl restart fireguard-edge

# Stop service
sudo systemctl stop fireguard-edge

# Stream live edge logs
sudo journalctl -u fireguard-edge -f
```

---

## 📁 Project Structure

```
FireGuard/
├── app.py                        # Unified bootstrap: launches FastAPI + PySide6 UI
├── FireGuard_fixed.spec          # PyInstaller bundle specification
├── FireGuard_Setup.iss           # Inno Setup Windows installer script
├── JETSON_SETUP_GUIDE.txt        # Jetson Nano terminal setup reference
├── fireguard.ico                 # Application high-resolution icon
├── start_background_edge.bat     # Windows background runner for local testing
│
├── server/                       # Central server & desktop command center
│   ├── screens/                  # PySide6 views (Home, Cameras, Alerts, Analytics, Settings)
│   ├── services/                 # WebSocket broker, alert processor, telemetry engine
│   ├── database/                 # SQLite ORM models, WAL handlers, query utilities
│   ├── workers/                  # Background threading workers
│   ├── utils/                    # System loggers, path resolvers, helpers
│   ├── assets/                   # QSS dark styles, sound alerts, SVG icons
│   ├── config.py                 # Pydantic environment configuration
│   ├── requirements.txt          # Server and desktop dependencies
│   └── .env.example              # Environment variables template
│
├── edge/                         # Embedded inference engine
│   ├── main.py                   # Edge pipeline entry point
│   ├── live_viewer.py            # Local desktop visualization tool
│   ├── config.yaml               # Camera, threshold, and broker configuration
│   ├── install.sh                # Automated Jetson installer script
│   ├── requirements.txt          # Edge dependencies (PyTorch, Ultralytics, OpenCV)
│   ├── inference/                # YOLOv26s wrapper & pre/post-processing
│   ├── stream/                   # RTSP and webcam stream buffer manager
│   ├── transmission/             # Async WebSocket client & alert packager
│   └── monitoring/               # Jetson CPU / RAM / GPU metrics collector
│
├── model/                        # Model artifacts & benchmark visuals
│   ├── app_screen_shots/         # Application interface screenshots
│   └── 26s_runs (2)/             # YOLOv26s training run artifacts
│       ├── fireguard_v2_showcase_different.png
│       └── runs/detect/          # Loss graphs, PR curves, and validation batches
│
└── storage/                      # Runtime data (auto-created, git-ignored)
    ├── clips/                    # Incident video recordings
    ├── snapshots/                # Incident JPEG captures
    └── fire.db                   # SQLite transactional database
```

---

## 📄 License

This project is licensed under the **MIT License** — feel free to use, modify, and distribute for academic, personal, or commercial applications.

<p align="center">
  <sub>Developed with passion for life safety and smart vision engineering. ⭐ Star this repository if you find it valuable!</sub>
</p>
