# Airbus Ship Detection

A semantic segmentation project for detecting ships in satellite imagery using **U-Net** with a **ResNet34 encoder**.

The model identifies ship regions at the pixel level and generates segmentation masks from satellite images in the Airbus Ship Detection dataset.

## Project Overview

The goal of this project is to segment ships at the pixel level rather than simply classify whether a ship is present in an image.

The pipeline includes:

* Converting RLE-encoded annotations into segmentation masks
* Preparing a dataset with a balanced number of positive and negative **images**
* Resizing images to `256 × 256`
* Training a basic U-Net as a baseline
* Training a U-Net with a ResNet34 encoder
* Evaluating segmentation performance
* Comparing predictions with ground-truth masks

## Dataset

The project uses the **Airbus Ship Detection** dataset.

Ship locations are provided as **Run-Length Encoding (RLE)** annotations. These annotations are decoded into binary segmentation masks before training.

During data preparation:

* Images containing ships are identified.
* Images without ships are selected.
* Positive and negative samples are balanced **at the image level**.
* The dataset is split into `80%` training and `20%` validation sets.

## Data Preparation

RLE annotations are decoded and converted into binary masks matching the corresponding image dimensions.

Before training:

* Images are resized to `256 × 256`.
* Pixel values are normalized to the `0–1` range.
* Multiple ship annotations within an image are combined into a single segmentation mask.

## Model

Two U-Net based approaches were explored.

### Basic U-Net

A standard U-Net architecture was first implemented as a baseline.

* Input: `256 × 256 × 3`
* Encoder-decoder architecture
* Skip connections
* Sigmoid output
* Dice Loss

The baseline model was used to validate the segmentation pipeline and provide a reference for the main model.

### ResNet34 U-Net

The main model uses a **ResNet34 encoder** with a U-Net decoder.

```text
Input Image
     │
     ▼
ResNet34 Encoder
     │
     ▼
Feature Extraction
     │
     ▼
U-Net Decoder
     │
     ▼
Segmentation Mask
```

The ResNet34 encoder uses **ImageNet-pretrained weights** for transfer learning.

### Training Configuration

| Parameter       |              Value |
| --------------- | -----------------: |
| Input Size      |          256 × 256 |
| Backbone        |           ResNet34 |
| Architecture    |              U-Net |
| Encoder Weights |           ImageNet |
| Optimizer       |               Adam |
| Learning Rate   |              0.001 |
| Loss            | BCE + Jaccard Loss |
| Metric          |                IoU |
| Epochs          |                 30 |
| Batch Size      |                 16 |
| Early Stopping  |                Yes |

## Results

The dataset contains a strong class imbalance between background and ship pixels. To address this, the model was evaluated using segmentation metrics rather than pixel accuracy alone.

An inference threshold of **0.525** was selected to improve the balance between false positives and false negatives.

Final validation results:

* **Precision:** 0.98
* **Recall:** 0.79
* **F1-Score:** 0.88

These results show that the model can effectively distinguish ship regions from background areas such as water, waves, and clouds.

## Predictions

The model was evaluated on unseen images and its predictions were compared with the corresponding ground-truth masks.

<img width="744" height="730" alt="Ekran Resmi 2026-08-11 22 33 57" src="https://github.com/user-attachments/assets/0df23c81-bd6f-4eeb-8a31-5b0b1cfea483" />


## Project Structure

```text
airbus-ship-detection/
│
├── autonomous-maritime-target-segmentation-from-satel.ipynb
└── README.md
```

## Technologies

* Python
* TensorFlow / Keras
* Segmentation Models
* NumPy
* Pandas
* OpenCV
* scikit-image
* scikit-learn
* Matplotlib

## Future Improvements

* Training with higher-resolution images
* Exploring additional data augmentation techniques
* Comparing different encoder architectures
* Improving segmentation of small ships

## License

This project is intended for educational and research purposes.
