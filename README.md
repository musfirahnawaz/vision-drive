# Vision Drive: Road Object Detection with YOLOv8

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/musfirahnawaz/vision-drive/blob/main/vision_drive_yolov8_bdd100k.ipynb)

This is the object detection part of our semester project, **Vision Drive: Intelligent Road Perception and Driver Assistant System**.

In this notebook, I train a YOLOv8 model on dashcam images and then test it on a short driving video. The model finds these things on the road:

car, bus, truck, train, person, rider, bike, motor, traffic light and traffic sign.

## What I used

- **Dataset:** [BDD100K](https://www.bdd100k.com/) (100K driving images with labels)
- **Data size:** 5,000 images for training and 1,000 for validation, picked randomly
- **Model:** YOLOv8n, starting from COCO pretrained weights
- **Training:** 30 epochs, image size 640, batch size 16
- **Hardware:** Tesla T4 GPU on Google Colab

## Results

| Metric | Score |
|---|---|
| mAP50 | 0.376 |
| mAP50-95 | 0.198 |

A few things I noticed:

- Before training, I checked my labels with a pretrained COCO model. It matched **91.5%** of my car labels, so the labels fit the images well.
- The model works best on common classes like cars. Rare classes like train, motor and rider have very few examples, so their scores are lower. This is normal for BDD100K.
- I used only a small part of the dataset so training could finish on a free Colab GPU. More data and more epochs should improve the results.

## What the notebook does

1. Sets up Colab (GPU, Google Drive, `ultralytics`)
2. Downloads the BDD100K images from Kaggle and loads the labels from Drive
3. Converts the labels to YOLO format
4. Checks the data (sample images, label check, class counts)
5. Trains the model
6. Shows the results (scores, training curves, confusion matrix)
7. Runs the model on a demo video

## How to run it

1. Click the **Open in Colab** button above.
2. Go to **Runtime > Change runtime type** and choose **T4 GPU**.
3. Put `bdd100k_labels.zip` in a folder called `vision_drive` in your Google Drive. A `demo_clip.mp4` is optional.
4. Paste your Kaggle API token when the notebook asks for it.
5. Run the cells from top to bottom.

## Files

- `vision_drive_yolov8_bdd100k.ipynb`: the full notebook with all outputs
