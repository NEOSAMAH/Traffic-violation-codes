# Traffic Violation Detection System

An advanced, automated traffic violation detection system using modern computer vision and machine learning technologies. This system can detect red light and speed violations in real-time, identify vehicles and their license plates, collect visual evidence, and store data systematically.

## Overview

This project uses YOLOv11 deep learning model for vehicle and license plate detection, integrated with SORT algorithm for object tracking. The system analyzes traffic light states using HSV color space and monitors vehicle timing through predefined regions of interest (ROI).

## Features

- **Real-time Traffic Violation Detection**
  - Red light violation detection
  - Speed violation detection
  - Automatic license plate recognition (ALPR)
  
- **Advanced Computer Vision**
  - YOLOv11 object detection
  - SORT algorithm for vehicle tracking
  - Enhanced image processing for license plate recognition
  - Traffic light state analysis using HSV color space

- **Graphical User Interface**
  - Easy setup and configuration
  - Real-time results display
  - Violation review and processing
  - Interactive ROI selection

- **Data Management**
  - SQLite database for violation storage
  - Automatic evidence collection
  - Violation image enhancement
  - Comprehensive reporting

## Requirements

### System Requirements
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

## Installation

### 1. Clone the Repository
```bash
git clone <repository-url>
cd traffic-violation-detector
```

### 2. Install Dependencies
```bash
# Install PyTorch (visit pytorch.org for specific CUDA version)
pip install torch torchvision torchaudio

# Install other requirements
pip install ultralytics opencv-python easyocr numpy pillow
```

### 3. Download YOLO Models
The system requires YOLO model weights. The default model is `yolo11s.pt`:
```bash
# The model will be automatically downloaded on first run
# Or manually download and place in the project root
```

### 4. Create Output Directories
```bash
mkdir -p output
mkdir -p violations
mkdir -p plates
```

## Usage

### GUI Application (Recommended)

#### Launch the GUI
```bash
python traffic_detector_gui.py
```

#### Using the GUI
1. **Setup Tab**: Configure detection parameters
   - Select input video file
   - Choose violation type (Red Light/Speed)
   - Set ROI (Region of Interest)
   - Configure detection parameters

2. **Results Tab**: View real-time detection results
   - Live violation count
   - Processing status
   - Detection logs

3. **Violations Tab**: Review and process violations
   - Browse detected violations
   - Enhance license plate images
   - Export reports

### Command Line Interface

#### Red Light Violation Detection
```bash
python main.py --video input_video.mp4 --type redlight --roi "x1,y1,x2,y2,x3,y3,x4,y4"
```

#### Speed Violation Detection
```bash
python main.py --video input_video.mp4 --type speed \
  --entry "x1,y1,x2,y2" --exit "x3,y3,x4,y4" \
  --speed-limit 50 --distance 10
```

#### Available Arguments
- `--video`: Input video file path (required)
- `--type`: Violation type (`redlight` or `speed`)
- `--output`: Output video file path
- `--model`: YOLO model path
- `--roi`: ROI points for red light detection
- `--entry`: Entry line for speed detection
- `--exit`: Exit line for speed detection
- `--speed-limit`: Speed limit in km/h
- `--distance`: Distance between lines in meters
- `--plate-interval`: License plate reading interval

## Configuration

### Main Configuration (`config.py`)
```python
# Model paths
YOLO_MODEL_PATH = "yolo11s.pt"

# Output directories
OUTPUT_DIR = "output"
VIOLATIONS_DIR = "violations"
PLATES_DIR = "plates"

# Detection parameters
PLATE_READING_INTERVAL = 30  # frames
CONFIDENCE_THRESHOLD = 0.5

# Database
DB_PATH = "traffic_violations.db"
```

### ROI Selection
The system includes an interactive ROI selector:

#### For Red Light Detection:
- Select 4 corner points defining the intersection area
- Vehicles entering this area during red light are flagged

#### For Speed Detection:
- Select 2 points for entry line
- Select 2 points for exit line
- System calculates speed based on time between crossings

## Project Structure

```
traffic-violation-detector/
├── main.py                 # Main entry point
├── traffic_detector_gui.py # GUI launcher
├── config.py              # Configuration settings
├── utils.py               # Utility functions
├── 
├── models/                # Detection models
│   ├── detector.py        # YOLO detector
│   ├── tracker.py         # Object tracker
│   ├── traffic_light.py   # Traffic light analyzer
│   ├── license_plate.py   # License plate reader
│   ├── plate_enhancer.py  # Image enhancement
│   └── enhanced_license_plate.py
│
├── detector/              # Enhanced detectors
│   ├── red_light_detector.py
│   └── speed_detector.py
│
├── gui/                   # GUI components
│   ├── gui_app.py         # Main GUI application
│   └── roi_selector.py    # ROI selection tool
│
├── output/                # Output videos
├── violations/            # Violation images
├── plates/               # License plate crops
└── debug_imports.py      # Debug utilities
```

## Database Schema

The system uses SQLite with the following main table:

```sql
CREATE TABLE violations (
    id INTEGER PRIMARY KEY,
    timestamp TEXT,
    violation_type TEXT,
    vehicle_id INTEGER,
    license_plate TEXT,
    confidence REAL,
    image_path TEXT,
    video_path TEXT
);
```

## Troubleshooting

### Common Issues

#### Import Errors
```bash
# If you encounter import errors, run:
python debug_imports.py

# Or fix imports manually:
python fix_imports.py
```

#### CUDA/GPU Issues
```bash
# Check GPU availability
python -c "import torch; print(torch.cuda.is_available())"

# If CUDA is not available, the system will fall back to CPU
```

#### Model Download Issues
```bash
# Manually download YOLO model
wget https://github.com/ultralytics/assets/releases/download/v0.0.0/yolo11s.pt
```

### Performance Optimization

#### For Better Performance:
- Use GPU-enabled PyTorch installation
- Reduce video resolution for faster processing
- Adjust confidence thresholds
- Optimize ROI areas

#### For Better Accuracy:
- Use higher resolution videos
- Ensure good lighting conditions
- Fine-tune detection thresholds
- Clean license plate regions

## Testing

### Test Individual Components
```bash
# Test enhanced system
python test_enhanced_system.py

# Test OCR accuracy
python test_ocr_accuracy.py

# Debug imports
python debug_imports.py
```

### Process Existing Violations
```bash
# Process all pending violations
python process_violations.py --pending

# Process specific violation
python process_violations.py --violation-id 123

# Process image directory
python process_violations.py --directory /path/to/images
```

## Development

### Adding New Features
1. Follow the existing module structure
2. Update configuration in `config.py`
3. Add GUI components if needed
4. Update database schema if required

### Contributing
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## License

This project is developed for educational and research purposes. Please ensure compliance with local traffic monitoring regulations before deployment.

## Support

For issues and questions:
1. Check the troubleshooting section
2. Run debug scripts
3. Check log files in the output directory
4. Review configuration settings

## Acknowledgments

- YOLO (Ultralytics) for object detection
- EasyOCR for license plate recognition
- OpenCV for computer vision operations
- SORT algorithm for object tracking