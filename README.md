# Traffic Violation Detection System

An automated real-time traffic violation detection system using computer vision and deep learning. Detects red-light and speed violations, recognizes license plates, collects visual evidence, and stores records systematically.

Developed as a B.Sc. graduation project at Istanbul Gelişim University (2024–2025)
Supervisor: Dr. Serkan Gönen

---

## Results

| Metric | Accuracy |
|---|---|
| Red-light violation detection | 92.4% |
| Speed violation detection | 89.7% |
| License plate recognition | 87.8% |

Tested on real-world traffic video footage under varying lighting and weather conditions.

---

## Overview

The system uses YOLOv11 for initial vehicle and license plate detection, later upgraded to the Transformer-based RF-DETR model for improved robustness under low-light and adverse-angle conditions. A custom StableTracker algorithm combining Kalman filter motion prediction with the Hungarian algorithm handles multi-frame vehicle tracking with persistent unique IDs.

Red-light violations are detected by analyzing traffic light state in HSV color space with temporal consistency checks and ROI-based vehicle monitoring. Speed violations are detected via a dual virtual-line method with pixel-to-metre calibration.

License plate recognition uses a multi-OCR fusion pipeline: Real-ESRGAN 4x super-resolution, CLAHE adaptive histogram equalisation, and bilateral filtering before weighted EasyOCR voting for the final plate read.

---

## Features

- Real-time red-light and speed violation detection
- Custom StableTracker (Kalman filter + Hungarian algorithm) for persistent vehicle tracking
- Automatic license plate recognition (ALPR) with image enhancement pipeline
- Traffic light state analysis using HSV color space
- SQLite database for violation records and evidence storage
- Tkinter GUI for live monitoring, configuration, and report management
- Command-line interface for batch processing

---

## Tech Stack

| Category | Tools |
|---|---|
| Detection | YOLOv11, RF-DETR |
| Computer Vision | OpenCV (cv2) |
| OCR & Enhancement | EasyOCR, Real-ESRGAN, CLAHE |
| Tracking | Custom StableTracker (Kalman + Hungarian) |
| Database | SQLite |
| GUI | Tkinter |
| Training & Labelling | Roboflow (~10,000 labelled images) |
| Language | Python 3.8+ |

---

## Requirements

### System
- Python 3.8 or higher
- CUDA-compatible GPU (recommended)
- Minimum 8GB RAM
- 10GB free disk space

### Python Dependencies

```
torch
torchvision
ultralytics
opencv-python
easyocr
numpy
pillow
tkinter
sqlite3
```

---

## Installation

### 1. Clone the Repository
```bash
git clone https://github.com/atohasan/traffic-violation-detection
cd traffic-violation-detection
```

### 2. Install Dependencies
```bash
# Install PyTorch (visit pytorch.org for your CUDA version)
pip install torch torchvision torchaudio

# Install remaining requirements
pip install ultralytics opencv-python easyocr numpy pillow
```

### 3. Download YOLO Model
The default model `yolo11s.pt` will be downloaded automatically on first run, or place it manually in the project root.

### 4. Create Output Directories
```bash
mkdir -p output violations plates
```

---

## Usage

### GUI Application (Recommended)
```bash
python traffic_detector_gui.py
```

**Setup Tab** — configure detection parameters:
- Select input video file
- Choose violation type (Red Light / Speed)
- Set ROI (Region of Interest)
- Configure detection thresholds

**Results Tab** — view real-time detection output and logs

**Violations Tab** — browse detected violations, enhance plate images, export reports

### Command Line

**Red-light detection:**
```bash
python main.py --video input_video.mp4 --type redlight --roi "x1,y1,x2,y2,x3,y3,x4,y4"
```

**Speed detection:**
```bash
python main.py --video input_video.mp4 --type speed \
  --entry "x1,y1,x2,y2" --exit "x3,y3,x4,y4" \
  --speed-limit 50 --distance 10
```

**Arguments:**

| Argument | Description |
|---|---|
| `--video` | Input video file path (required) |
| `--type` | Violation type: `redlight` or `speed` |
| `--output` | Output video file path |
| `--model` | YOLO model path |
| `--roi` | ROI points for red-light detection |
| `--entry` | Entry line for speed detection |
| `--exit` | Exit line for speed detection |
| `--speed-limit` | Speed limit in km/h |
| `--distance` | Distance between lines in metres |

---

## Project Structure

```
traffic-violation-detector/
├── main.py                       # Main entry point
├── traffic_detector_gui.py       # GUI launcher
├── config.py                     # Configuration settings
├── utils.py                      # Utility functions
│
├── models/                       # Detection models
│   ├── detector.py               # YOLO detector
│   ├── tracker.py                # StableTracker (Kalman + Hungarian)
│   ├── traffic_light.py          # Traffic light analyser
│   ├── license_plate.py          # License plate reader
│   ├── plate_enhancer.py         # Real-ESRGAN + CLAHE pipeline
│   └── enhanced_license_plate.py
│
├── detector/                     # Violation detectors
│   ├── red_light_detector.py
│   └── speed_detector.py
│
├── gui/                          # GUI components
│   ├── gui_app.py                # Main GUI application
│   └── roi_selector.py           # Interactive ROI selection tool
│
├── output/                       # Processed output videos
├── violations/                   # Violation evidence images
└── plates/                       # Cropped license plate images
```

---

## Database Schema

```sql
CREATE TABLE violations (
    id              INTEGER PRIMARY KEY,
    timestamp       TEXT,
    violation_type  TEXT,
    vehicle_id      INTEGER,
    license_plate   TEXT,
    confidence      REAL,
    image_path      TEXT,
    video_path      TEXT
);
```

---

## Configuration

```python
# config.py
YOLO_MODEL_PATH       = "yolo11s.pt"
OUTPUT_DIR            = "output"
VIOLATIONS_DIR        = "violations"
PLATES_DIR            = "plates"
PLATE_READING_INTERVAL = 30       # frames
CONFIDENCE_THRESHOLD  = 0.5
DB_PATH               = "traffic_violations.db"
```

---

## Troubleshooting

**Import errors:**
```bash
python debug_imports.py
```

**Check GPU availability:**
```bash
python -c "import torch; print(torch.cuda.is_available())"
```
If CUDA is unavailable the system falls back to CPU automatically.

**Manual model download:**
```bash
wget https://github.com/ultralytics/assets/releases/download/v0.0.0/yolo11s.pt
```

---

## Acknowledgements

- [Ultralytics YOLOv11](https://github.com/ultralytics/ultralytics) — object detection
- [RF-DETR](https://github.com/roboflow/rf-detr) — Transformer-based detection
- [EasyOCR](https://github.com/JaidedAI/EasyOCR) — license plate recognition
- [Real-ESRGAN](https://github.com/xinntao/Real-ESRGAN) — image super-resolution
- [OpenCV](https://opencv.org/) — computer vision operations
- [Roboflow](https://roboflow.com/) — dataset labelling and management

---

*This project was developed for educational and research purposes. Ensure compliance with local traffic monitoring regulations before any real-world deployment.*
