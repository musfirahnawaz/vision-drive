# VisionDrive: Road Object Detection with YOLOv8

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/musfirahnawaz/vision-drive/blob/main/vision_drive_yolov8_bdd100k.ipynb)
![Python](https://img.shields.io/badge/Python-3.x-blue)
![YOLOv8](https://img.shields.io/badge/Model-YOLOv8n-orange)
![Dataset](https://img.shields.io/badge/Dataset-BDD100K-green)

Object-detection layer of **VisionDrive: Intelligent Road Perception and Driver Assistance System**

A YOLOv8n model is fine-tuned on dashcam images from BDD100K to detect 10 road-object classes, then run on a driving video to produce an annotated demo.

**Team:** Musfirah Nawaz, Laiba Afzal, Minahil Fatima

![Demo preview](assets/demo_preview.gif)

## Highlights

| Metric | Value |
| --- | --- |
| mAP@0.5 | **0.376** |
| mAP@0.5:0.95 | **0.198** |
| Precision / Recall | 0.604 / 0.356 |
| Inference (Tesla T4) | 2.6 ms model, about 4.9 ms end to end per image |
| Model size | 6.5 MB (3.1M parameters, 8.6 GFLOPs) |
| Training time | about 38 minutes (30 epochs) |

## Detected classes

`car` · `bus` · `truck` · `train` · `person` · `rider` · `bike` · `motor` · `traffic light` · `traffic sign`

## Setup

| Item | Details |
| --- | --- |
| Dataset | [BDD100K](https://www.bdd100k.com/), reproducible random subset of 5,000 train and 1,000 val images (seed 0) |
| Boxes | 91,219 train and 19,038 val bounding boxes |
| Model | YOLOv8n, COCO-pretrained, head replaced for 10 classes |
| Training | 30 epochs, image size 640, batch size 16, Ultralytics defaults with mosaic, flip, HSV jitter, scale, translate and random erasing |
| Hardware | Tesla T4 GPU on Google Colab |

## Results

### Training curves
Losses fall steadily on both train and validation sets and stay close together, so there is no sign of overfitting. Validation mAP@0.5 rose from 0.230 to 0.375 and was still climbing at epoch 30.

### Per-class performance (validation, 1,000 images)

| Class | Train boxes | Precision | Recall | mAP@0.5 |
| --- | ---: | ---: | ---: | ---: |
| car | 50,338 | 0.711 | 0.628 | 0.682 |
| person | 6,566 | 0.598 | 0.414 | 0.462 |
| traffic sign | 16,912 | 0.622 | 0.419 | 0.459 |
| truck | 2,152 | 0.520 | 0.461 | 0.451 |
| traffic light | 13,155 | 0.546 | 0.436 | 0.424 |
| bus | 883 | 0.506 | 0.390 | 0.406 |
| bike | 595 | 0.461 | 0.287 | 0.306 |
| rider | 366 | 0.568 | 0.279 | 0.292 |
| motor | 242 | 0.509 | 0.241 | 0.280 |
| train | 10 | 1.000 | 0.000 | 0.000 |

`train` has only 3 validation boxes, so its score is not meaningful. Excluding it, mean mAP@0.5 over the other nine classes is about 0.418.

## Key findings

- **Class imbalance drives results.** Car makes up 55.2% of training boxes, while train has 10. Rare classes (rider, bike, motor) score lowest.
- **Recall is the bottleneck.** Precision (0.604) is well above recall (0.356), so the model misses small, distant or occluded objects more often than it raises false alarms.
- **Typical confusions:** bus and truck are often predicted as car, rider as person, bike and motor are mixed up, and traffic lights and signs are sometimes swapped.
- **Labels were verified before training.** A pretrained COCO model matched 91.5% of the car labels (IoU > 0.5), which confirms the converted labels line up with the images.

## What the notebook does

1. Sets up Colab (GPU, Google Drive, `ultralytics`)
2. Downloads BDD100K images from Kaggle and loads the labels from Drive
3. Converts labels to YOLO format (clipping boxes, dropping degenerate ones, normalising to 0-1)
4. Checks the data (sample images, label alignment check, class counts)
5. Trains YOLOv8n
6. Evaluates the model (metrics, curves, confusion matrix)
7. Runs the model on a demo video

## How to run

1. Click the **Open in Colab** badge above.
2. Choose **Runtime > Change runtime type > T4 GPU**.
3. Put `bdd100k_labels.zip` (from the [official BDD100K page](https://doc.bdd100k.com)) in a Drive folder called `vision_drive`. An optional `demo_clip.mp4` goes in the same folder.
4. Paste your Kaggle API token when the notebook asks for it. Never commit your token to the repo.
5. Run the cells from top to bottom.

## Repository contents

```
vision-drive/
├── README.md
├── vision_drive_yolov8_bdd100k.ipynb   # full notebook with outputs
└── assets/                             # figures used in this README
```

Trained weights (`best.pt`, 6.5 MB) and the annotated demo video: _add link here_.

## Roadmap

- Train longer and on more data (validation mAP was still rising)
- Address class imbalance (class weighting or oversampling of rare classes)
- Try a larger model such as YOLOv8s
- Add lane detection and risk classification on top of the detector, the next VisionDrive stages

## Dataset licence and citation

BDD100K is released by The Regents of the University of California for educational, research and not-for-profit use. This project uses it for academic purposes only.

> Yu, F., Chen, H., Wang, X., Xian, W., Chen, Y., Liu, F., Madhavan, V., and Darrell, T. (2020). BDD100K: A Diverse Driving Dataset for Heterogeneous Multitask Learning. CVPR 2020.

Model: [Ultralytics YOLOv8](https://github.com/ultralytics/ultralytics) (Jocher, Chaurasia and Qiu, 2023).
