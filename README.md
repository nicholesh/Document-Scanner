# Document Scanner

A real-time document scanning application that uses computer vision to detect documents through a webcam and apply perspective correction — producing output comparable to a flatbed scanner.

![Python](https://img.shields.io/badge/Python-3.x-blue?logo=python&logoColor=white)
![OpenCV](https://img.shields.io/badge/OpenCV-4.x-green?logo=opencv&logoColor=white)
![NumPy](https://img.shields.io/badge/NumPy-1.x-013243?logo=numpy&logoColor=white)

---

## Overview

This project demonstrates applied computer vision by building a document scanner from scratch. The application captures live webcam footage, detects the edges of a document in frame, and applies a perspective warp transform to produce a clean, top-down scan — all in real time.

This is a common problem in mobile scanning apps (like Microsoft Lens or Adobe Scan), implemented here using pure OpenCV.

---

## Features

- **Real-time detection** — processes every frame of live video from a webcam
- **Edge & contour detection** — uses Canny edge detection and morphological operations to find document boundaries
- **Automatic corner detection** — identifies the four corners of the document even when tilted or skewed
- **Perspective correction** — applies a homography transform to produce a flat, rectangular output
- **Live preview** — displays a side-by-side view of the raw camera feed and the corrected scan

---

## How It Works

The pipeline processes each video frame through five stages:

```
Webcam Frame
     │
     ▼
1. Pre-processing
   Grayscale → Gaussian Blur → Canny Edge Detection → Dilation → Erosion
     │
     ▼
2. Contour Detection
   Find all external contours → Filter by area (>5000px) → Keep largest quadrilateral
     │
     ▼
3. Corner Reordering
   Sort the 4 detected corners into a consistent order:
   [top-left, top-right, bottom-left, bottom-right]
     │
     ▼
4. Perspective Transform
   Compute homography matrix → Warp the document to fill a 540×640 canvas
     │
     ▼
5. Output
   Crop 20px margins → Resize → Display warped scan
```

### Key Functions

| Function | Description |
|---|---|
| `preProcessing(img)` | Converts a frame to grayscale, applies Gaussian blur, Canny edge detection, dilation, and erosion to isolate edges |
| `getContours(img)` | Finds all external contours and returns the largest 4-sided polygon with area > 5000px (the document) |
| `reorder(myPoints)` | Normalises the 4 corner points into a consistent ordering using coordinate sum/difference |
| `getWarp(img, biggest)` | Computes the perspective transform matrix and warps the document region into a rectangular output |
| `stackImages(scale, imgArray)` | Utility to compose multiple OpenCV images into a single display grid |

---

## Tech Stack

| Technology | Role |
|---|---|
| **Python 3** | Core language |
| **OpenCV** | Image capture, processing, contour detection, perspective transformation |
| **NumPy** | Numerical operations on image arrays and coordinate geometry |

---

## Installation

**Prerequisites:** Python 3.x and a webcam.

```bash
# Clone the repository
git clone https://github.com/nicholesh/document-scanner.git
cd document-scanner

# Install dependencies
pip install opencv-python numpy
```

---

## Usage

```bash
python scanner.py
```

1. Point your webcam at a document on a contrasting background (e.g. white paper on a dark desk)
2. Hold the document steady — the blue outline indicates a successful detection
3. The right panel shows the corrected scan in real time
4. Press **`q`** to quit

> **Tip:** Good lighting and a high-contrast background produce the best results. The minimum detectable document area is 5,000 pixels.

---

## Project Structure

```
document-scanner/
├── scanner.py      # Main application — all CV logic and capture loop
└── README.md
```

---

## Computer Vision Concepts Demonstrated

- **Canny edge detection** — gradient-based edge finding with hysteresis thresholding
- **Morphological operations** — dilation and erosion to close edge gaps and reduce noise
- **Contour approximation** — Douglas-Peucker algorithm (`approxPolyDP`) to simplify detected contours into polygons
- **Perspective transform** — homography matrix computed from four point correspondences (`getPerspectiveTransform` + `warpPerspective`)
- **Real-time video processing** — per-frame processing loop with OpenCV's `VideoCapture`

---

## License

This project is open source and available under the [MIT License](LICENSE).
