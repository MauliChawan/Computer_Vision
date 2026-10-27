# CCA 3 – Computer Vision Practical Assignment (Units 3, 4 and 5)

Image restoration, segmentation, feature extraction, object recognition and motion estimation,
implemented in **Jupyter notebooks** with **OpenCV, NumPy, Matplotlib, scikit-image and Ultralytics YOLO**.
All experiments use my own photographs and my own recorded video.

| Notebook | Unit | Techniques |
|---|---|---|
| `01_Task1_Restoration_Segmentation.ipynb` | 3 | Gaussian & salt-and-pepper noise, median/Gaussian filtering, PSNR/SSIM, Otsu thresholding, erosion/dilation/opening/closing, Canny, watershed |
| `02_Task2_Feature_Extraction_Matching.ipynb` | 4 | HOG, SIFT keypoints & descriptors, ratio-test matching, RANSAC, rotation/scale experiments |
| `03_Task3_Object_Recognition.ipynb` | 5 | Viola–Jones Haar cascades (face, eye, full body, cat face) vs YOLOv8n, error analysis |
| `04_Task4_Motion_Estimation.ipynb` | 5 | Frame differencing, camera-motion-compensated differencing, GMM (MOG2) background subtraction, Lucas–Kanade optical flow |

Each notebook explains the theory, runs the code, displays every result inline and ends with a comparison and conclusion.

## Project structure

```
CV_CCA3/
├── 01_Task1_Restoration_Segmentation.ipynb
├── 02_Task2_Feature_Extraction_Matching.ipynb
├── 03_Task3_Object_Recognition.ipynb
├── 04_Task4_Motion_Estimation.ipynb
├── data/
│   ├── photo1.jpg         # original photograph
│   ├── photo2.jpg         # same scene, different viewpoint (for matching)
│   └── video.mp4          # NOT in Git – download from Google Drive
├── models/                # pretrained Haar cascade XML files (OpenCV)
├── outputs/               # generated images, tables (.csv) and videos, one folder per task
├── requirements.txt
└── README.md
```

## Dataset

| File | Description | Used in |
|---|---|---|
| `photo1.jpg` | Original phone photograph (960×1280): a person in a white shirt holding a decorative sword in front of parked cars and trees | Tasks 1, 2, 3 |
| `photo2.jpg` | Second photograph of the same scene, taken a moment later from a slightly different position | Tasks 2, 3 |
| `video.mp4` | Original phone video (464×832, 30 fps, about 6 s): a man breaking a coconut and moving around a new car decorated with a flower garland | Tasks 3, 4 |

**Getting the video:** download it from the Google Drive link in the technical report and save it as `data/video.mp4`.
To run the notebooks on other inputs, replace the three files and keep the same names.
The photos are resized to 750 px width inside the code; the original files are never modified.

## How to run

```bash
# 1. create and activate a virtual environment (recommended)
python -m venv venv
venv\Scripts\activate          # Windows
# source venv/bin/activate     # macOS / Linux

# 2. install dependencies
pip install -r requirements.txt

# 3. start Jupyter (opens in the browser)
jupyter notebook
```

Open the notebooks in order and choose **Run → Run All Cells** (Kernel → Restart & Run All).
The YOLOv8n weights (`yolov8n.pt`, 6 MB) download automatically on the first run, so an internet connection is needed once.
The notebooks can also be opened in VS Code with the Jupyter extension.

## Key results

**Task 1: restoration (PSNR / SSIM against the clean photo)**

| Noise | Filter | PSNR (dB) | SSIM |
|---|---|---|---|
| Gaussian σ=25 | none | 20.55 | 0.506 |
| Gaussian σ=25 | Median 5×5 | 24.50 | 0.711 |
| Gaussian σ=25 | **Gaussian 5×5** | **25.59** | **0.785** |
| Salt & pepper 5% | none | 17.83 | 0.506 |
| Salt & pepper 5% | **Median 5×5** | **25.72** | **0.825** |
| Salt & pepper 5% | Gaussian 5×5 | 24.69 | 0.738 |

Marker-based watershed separated 26 touching dark objects (hair, jeans, tree trunks, car shadows).

**Task 2: SIFT matching (photo 1 vs photo 2)**

| Experiment | Good matches | RANSAC inliers | Inlier ratio |
|---|---|---|---|
| Original | 3,494 | 3,432 | 98.2% |
| Photo 2 rotated 30° | 4,102 | 4,045 | 98.6% |
| Photo 2 resized 0.5× | 1,125 | 1,077 | 95.7% |
| Resized 0.6× + rotated −45° | 1,852 | 1,818 | 98.2% |

**Task 3:**
- YOLOv8n detected the person in 176 of 176 video frames (mean confidence 0.90).
- YOLO errors: the decorated car labelled "boat" or "truck", the AC unit labelled "tv", the slipper labelled "skateboard". The sword was missed because it isn't a COCO class.
- Haar cascades found the face in the photos, with false positives in the tree leaves. They detected nothing in the video frames, where the person was crouching, bending or looking away.

**Task 4:** the camera was hand-held.

| Method | Precision (FG inside person box) | Coverage of person box | Time / frame |
|---|---|---|---|
| Frame differencing | 33.5% | 7.3% | 0.7 ms |
| Frame diff + camera compensation | 77.6% | 3.6% | 12 ms |
| GMM (MOG2) | 35.2% | 19.9% | 21 ms |

Lucas–Kanade: once the camera motion is subtracted, the person moves about 1.5× more than the background.

## Author

Name: _\<Mauli Chavhan>_ · PRN no.: _\<1272250992\>_ · Course: _\<SYMCA>_
