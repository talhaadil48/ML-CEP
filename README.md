# 🤟 ASL Alphabet Recognition System

Real-time American Sign Language (ASL) hand sign recognition for all 26 letters (A–Z), built using three different approaches — a custom CNN, EfficientNetB3 transfer learning, and a Random Forest on MediaPipe hand keypoints.

---

## 📌 What We Built

A system that recognizes ASL hand gestures in real time via webcam. Before any model runs, a MediaPipe hand detection stage isolates the hand region from the frame — this keeps the models focused on the gesture itself regardless of background or lighting.

We trained and compared **9 models total** across three model families to find what works best for this task.

---

## 📊 Dataset

We collected our own dataset rather than using a public one, to have full control over quality and real-world relevance. Images were captured by multiple people with different hand shapes and skin tones.

| Property | Value |
|----------|-------|
| Classes | 26 (A–Z) |
| Images per class (raw) | ~200+ |
| Images per class (after augmentation) | ~1,200 |
| Total images | ~31,200 |

Each image was augmented into 6 versions using horizontal flips, brightness adjustments, and slight rotations. Images were also manually cleaned — anything blurry, poorly lit, or ambiguous was removed.

---

## 🧠 Models

### 1. Custom CNN (from scratch)

A hybrid architecture inspired by ResNet and MobileNet. Uses residual connections to help gradient flow, depthwise separable convolutions to keep the parameter count down, and a graduated dropout strategy (stronger regularization at deeper layers). Trained entirely from scratch on our ASL dataset.

Three configurations were explored — baseline, a lighter filter setup with improved regularization, and a fine-tuned version with a lower learning rate and smaller batch size.

---

### 2. EfficientNetB3 (transfer learning)

An ImageNet-pretrained backbone adapted to ASL using a two-phase strategy: the backbone is frozen first while only the custom classification head trains, then upper backbone layers are selectively unfrozen at a much lower learning rate to adapt without losing pretrained knowledge.

Three configurations were explored — progressively more aggressive fine-tuning, with the final version unfreezing deeper backbone layers and adding gradient clipping for stability.

---

### 3. Random Forest on MediaPipe Keypoints

Instead of raw pixels, this approach uses MediaPipe to extract 21 hand joint positions (x, y, z per joint) as a 63-dimensional feature vector, which is then fed to a Random Forest classifier. Fast, lightweight, and easy to interpret — a good option when compute is limited.

Three configurations were explored using Grid Search and Randomized Search over different hyperparameter ranges.

---

## 📈 Results

| Model | Test Accuracy | Real-World Accuracy |
|-------|:---:|:---:|
| Custom CNN | ~97%+ | ~90% |
| EfficientNetB3 | ~95% | ~85% |
| Random Forest | ~90% | ~75% |

The custom CNN performed best overall — training from scratch on our task-specific data let it develop representations tuned to the subtle differences between similar signs like A, E, and S. EfficientNetB3 is a strong runner-up. The Random Forest is the lightest and most interpretable option, though it loses ground in real-world conditions where MediaPipe keypoint extraction becomes noisy.

---

## 👥 Built by

[Sitwat Samara](https://github.com/) · [Manahil Adeel](https://github.com/) · [Talha Adil](https://github.com/) · [Marium Shad](https://github.com/)

---

## 📄 License

MIT
