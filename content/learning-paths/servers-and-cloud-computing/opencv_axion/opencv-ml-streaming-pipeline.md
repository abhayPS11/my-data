---
title: OpenCV ML Streaming Pipeline on GCP Axion (Arm)
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## OpenCV ML Streaming Pipeline on GCP Axion (Arm)

In this section, you will extend your pipeline by integrating a **Machine Learning model (YOLO)** and enabling **real-time browser visualization**.


## Learning Objectives

- Integrate ML model with OpenCV  
- Perform real-time inference on video  
- Stream output in browser  
- Optimize performance for real-time use  


## Navigate to project and activate environment
Move to your project directory and activate the virtual environment.

```bash
cd ~/opencv-project
source cv-env/bin/activate
```

## Install ML dependencies
Install PyTorch (CPU version) and Ultralytics YOLO.

```bash
pip install torch torchvision --index-url https://download.pytorch.org/whl/cpu
pip install ultralytics
```

## Verify OpenCV and ML setup 
Ensure OpenCV and YOLO are working correctly.

```bash
python -c "import cv2; print(cv2.__version__)"
python -c "from ultralytics import YOLO; print('YOLO OK')"
```

## Create ML pipeline script

This script integrates YOLO with OpenCV and processes video frames.

```bash
vi ml_pipeline.py
```

```python
import cv2
import time
from ultralytics import YOLO

# Load lightweight YOLO model
model = YOLO("yolov8n.pt")

# Open video source
cap = cv2.VideoCapture("video.mp4")

if not cap.isOpened():
    print("Video not opening")
    exit()

# Optional: limit CPU threads
cv2.setNumThreads(4)

while True:
    ret, frame = cap.read()

    if not ret:
        # Restart video when finished
        cap.set(cv2.CAP_PROP_POS_FRAMES, 0)
        continue

    # Resize for faster processing
    frame = cv2.resize(frame, (480,360))

    # Run ML inference
    results = model(frame)

    # Draw detection results
    frame = results[0].plot()

    # Add pipeline label
    cv2.putText(frame, "ML PIPELINE", (20,40),
                cv2.FONT_HERSHEY_SIMPLEX, 1, (0,0,255), 2)

    # Save frame for browser
    cv2.imwrite("latest.jpg", frame)

    print("ML frame updated")

    # Control frame rate
    time.sleep(0.05)
```

### What this script does

- Loads YOLOv8 model
- Reads video frame-by-frame
- Runs object detection on each frame
- Draws bounding boxes
- Saves output as `latest.jpg`
- Enables real-time browser visualization

## Run ML pipeline
Start the ML-based video processing.

```bash
python ml_pipeline.py
```

## Verify in browser

Open the updated frames in browser:

```text
http://<VM-IP>:8000/latest.jpg
```

If no objects are detected, you will see:
```bash
(no detections)
```
This is normal because the sample video contains only text frames.


## Create Live Web UI

Instead of refreshing manually, create an auto-refreshing web page.

```bash
vi index.html
```

```html
<html>
<head>
<title>Live ML Pipeline</title>
</head>
<body>

<h2>Live ML Pipeline Feed</h2>

<img id="img" src="latest.jpg" width="640">

<script>
setInterval(function(){
    document.getElementById("img").src =
        "latest.jpg?t=" + new Date().getTime();
}, 500);
</script>

</body>
</html>
```

### What this file does

- Displays latest.jpg in browser
- Auto-refreshes every 500ms
- Simulates live video streaming

## Open live UI in browser

```text
http://<VM-IP>:8000/index.html
```

## (Optional) Test real detection

To verify ML detection, use a real image:

```bash
wget https://ultralytics.com/images/bus.jpg
```

## Modify script temporarily:

```bash
frame = cv2.imread("bus.jpg")
```

**You will see:**

- Object detection boxes
- Person / vehicle detection

## Performance Optimization
To improve real-time performance:

### Resize frames

```bash
frame = cv2.resize(frame, (480,360))
```

### Control FPS

```bash
time.sleep(0.05)
```

### Skip frames (optional)

```bash
if frame_count % 2 != 0:
    continue
```

## Use lightweight model

```bash
YOLO("yolov8n.pt")
```

## What you've learned

You have successfully:

- Integrated ML with OpenCV
- Built real-time inference pipeline
- Streamed output in browser
- Optimized performance for ARM
