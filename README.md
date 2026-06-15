# 🤟 ASL Alphabet Recognition System

Real-time American Sign Language (ASL) hand sign recognition for all 26 letters (A–Z), built with a custom CNN, EfficientNetB3 transfer learning, and a Random Forest classifier on MediaPipe keypoints.

---

## ✨ What it does

- Recognizes all 26 ASL alphabet gestures in real time via webcam
- Runs a MediaPipe hand detection stage before every model to isolate the hand region — works across different backgrounds and lighting conditions
- Lets you swap between three model backends depending on your accuracy vs. speed tradeoff

---

## 🎥 Demo

📹 [Watch the live demo](https://drive.google.com/file/d/1BWibWGO7RvF-XbX7dYOGqEzAKuB350xr/view?usp=drive_link)

---

## 🗂️ Repo Structure

```
ASL-Recognition/
│
├── data/
│   ├── raw/                  # Original collected images (26 class folders)
│   └── cropped/              # Cleaned, hand-region-only images
│
├── models/
│   ├── cnn_scratch/          # Custom CNN weights & outputs
│   ├── efficientnet/         # EfficientNetB3 weights & outputs
│   └── random_forest/        # Saved RF classifiers
│
├── notebooks/
│   ├── 1_data_preprocessing.ipynb
│   ├── 2_cnn_scratch.ipynb
│   ├── 3_efficientnet.ipynb
│   └── 4_random_forest_mediapipe.ipynb
│
├── inference/
│   ├── realtime_demo.py      # Live webcam inference
│   └── test_image.py         # Static image inference
│
├── results/
│   ├── confusion_matrices/
│   ├── training_curves/
│   └── validation_curves/
│
└── README.md
```

---

## ⚡ Quickstart

```bash
pip install tensorflow keras mediapipe scikit-learn opencv-python numpy pandas matplotlib
```

**Live webcam:**
```bash
python inference/realtime_demo.py --model cnn       # or: efficientnet / rf
```

**Single image:**
```bash
python inference/test_image.py --image path/to/image.jpg --model cnn
```

---

## 📊 Dataset

We collected a custom dataset rather than using a public one, to keep control over quality and ensure real-world relevance. Images were captured by multiple people across different hand shapes and skin tones.

| Property | Value |
|----------|-------|
| Classes | 26 (A–Z) |
| Images per class (after augmentation) | ~1,200 |
| Total images | ~31,200 |
| Input size (CNN) | 128 × 128 px, RGB |
| Input size (EfficientNetB3) | 300 × 300 px, RGB |

**Augmentations applied:** horizontal flip, brightness up/down, slight rotation up/down — each original image expanded to 6 versions total.

---

## 🧠 Models

### 1. Custom CNN (from scratch)

A hybrid architecture inspired by ResNet and MobileNet — residual connections for gradient flow, depthwise separable convolutions to cut parameters, and graduated dropout (stronger regularization at deeper layers where overfitting risk is highest).

| | Model 1 | Model 2 | Model 3 |
|---|---|---|---|
| Filters | 32→64→128→256→512 | 16→32→64→128→256 | 16→32→64→128→256 |
| Batch size | 32 | 32 | 16 |
| Learning rate | 1e-3 | 1e-3 | 1e-4 |
| Optimizer | Adam | AdamW | AdamW |
| Label smoothing | ✗ | ✓ | ✓ |
| Dropout | Uniform | Graduated | Graduated |

---

### 2. EfficientNetB3 (transfer learning)

ImageNet-pretrained backbone with a two-phase training strategy: freeze the backbone and train only the custom head first, then selectively unfreeze upper layers at a much lower learning rate to adapt without catastrophic forgetting.

| | Model 1 | Model 2 | Model 3 |
|---|---|---|---|
| Phase 1 epochs | 30 | 20 | 20 |
| Phase 2 epochs | 20 | 20 | 20 |
| Phase 1 LR | 1e-3 | 5e-4 | 5e-4 |
| Phase 2 LR | 1e-4 | 1e-5 | 2e-5 |
| Fine-tune from layer | 300 | 300 | 280 (Block 7) |
| Gradient clipping | ✗ | ✗ | ✓ |

---

### 3. Random Forest on MediaPipe Keypoints

MediaPipe extracts 21 hand landmarks → (x, y, z) per joint → 63-dimensional feature vector → Random Forest classifier. Fast, lightweight, and interpretable — useful when you can't run a full CNN.

| | Model 1 | Model 2 | Model 3 |
|---|---|---|---|
| Search | GridSearchCV | RandomizedSearchCV | RandomizedSearchCV |
| n_estimators | [100, 200, 300] | [100–250] | randint(150, 450) |
| n_iter / CV folds | exhaustive / 3 | 15 / 3 | 20 / 2 |

---

## 📈 Results

| Model | Test Accuracy | Real-World Accuracy |
|-------|:---:|:---:|
| Custom CNN | ~97%+ | ~90% |
| EfficientNetB3 | ~95% | ~85% |
| Random Forest | ~90% | ~75% |

The custom CNN came out on top — training from scratch on task-specific data let it develop representations tuned to the subtle differences between similar signs (A, E, S). EfficientNetB3 is a strong runner-up. Random Forest is the go-to for resource-constrained deployments where speed and interpretability matter more than peak accuracy.

---

## 👥 Built by

[Sitwat Samara](https://github.com/) · [Manahil Adeel](https://github.com/) · [Talha Adil](https://github.com/) · [Marium Shad](https://github.com/)

---

## 📄 License

MIT
