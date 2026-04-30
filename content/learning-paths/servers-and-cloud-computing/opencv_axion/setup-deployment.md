---
title: Build OpenCV Pipelines on GCP Axion (Arm) - Part 1
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Build OpenCV Pipelines on GCP Axion (Arm) - Part 1

This section guides you through setting up OpenCV on an Arm-based VM and building **image and video processing pipelines with browser visualization**.

## Learning Objectives

- Install OpenCV on Arm  
- Build image pipeline  
- Build video pipeline  
- Visualize output in browser  

---

## Update your system

```bash
sudo zypper refresh
```

## Install dependencies

```bash
sudo zypper install -y \
python311 python311-pip python311-devel \
gcc gcc-c++ make cmake \
```

## Create project

```bash
mkdir -p ~/opencv-project
cd ~/opencv-project
```

## Setup Python environment

```bash
python3.11 -m venv cv-env
source cv-env/bin/activate
```

## Install Python packages

```bash
pip install --upgrade pip
pip install numpy opencv-python-headless flask
```

## Start browser server (used in all steps)

```bash
python -m http.server 8000
```

Open:

```text
http://<VM-IP>:8000/
```

## Image Pipeline
Create script

```bash
vi image_pipeline.py
```

```python
import cv2

img = cv2.imread("input.jpg")

if img is None:
    print("Image not found")
    exit()

img = cv2.resize(img, (800,600))

cv2.putText(img, "IMAGE PIPELINE", (20,40),
            cv2.FONT_HERSHEY_SIMPLEX, 1, (0,255,0), 2)

cv2.imwrite("latest.jpg", img)
```

## Download image

```bash
wget https://ultralytics.com/images/bus.jpg -O input.jpg
```

## Run

```bash
python image_pipeline.py
```

## Verify

```text
http://<VM-IP>:8000/latest.jpg
```

## Video Pipeline
Create video

```bash
vi create_video.py
```

```python
import cv2
import numpy as np

out = cv2.VideoWriter("video.mp4",
                      cv2.VideoWriter_fourcc(*'mp4v'),
                      20,
                      (640,480))

for i in range(200):
    frame = np.zeros((480,640,3), dtype=np.uint8)
    cv2.putText(frame, f"Frame {i}", (100,240),
                cv2.FONT_HERSHEY_SIMPLEX, 1, (255,255,255), 2)
    out.write(frame)

out.release()
```

```bash
python create_video.py
```

## Create pipeline

```bash
vi video_pipeline.py
```

```python
import cv2
import time

cap = cv2.VideoCapture("video.mp4")

while True:
    ret, frame = cap.read()

    if not ret:
        cap.set(cv2.CAP_PROP_POS_FRAMES, 0)
        continue

    cv2.putText(frame, "VIDEO PIPELINE", (20,40),
                cv2.FONT_HERSHEY_SIMPLEX, 1, (255,0,0), 2)

    cv2.imwrite("latest.jpg", frame)

    time.sleep(0.05)
```

## Run

```bash
python video_pipeline.py
```

## Verify

```text
http://<VM-IP>:8000/latest.jpg
```

## What you've learned

- Installed OpenCV on Arm
- Built image processing pipeline
- Built video pipeline
- Viewed results in browser

## Next
You will:

- Integrate ML (YOLO)
- Enable real-time detection
- Build live streaming UI
