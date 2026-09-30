<div align="center">

# 🌿 PhytoScan AI

### AI-Powered Medicinal Plant Recognition System

[![Python](https://img.shields.io/badge/Python-3.10-blue?logo=python)](https://python.org)
[![TensorFlow](https://img.shields.io/badge/TensorFlow-2.x-orange?logo=tensorflow)](https://tensorflow.org)
[![EfficientNet](https://img.shields.io/badge/Model-EfficientNetB0-green)](https://arxiv.org/abs/1905.11946)
[![License](https://img.shields.io/badge/License-MIT-yellow)](LICENSE)
[![University](https://img.shields.io/badge/NUML-Islamabad-blue)](https://numl.edu.pk)

*A plant image classification and information retrieval system
developed as a Semester 4 undergraduate AI project.*

</div>

---

## 👥 Team

| Name | Roll Number |
|------|------------|
| Sara Jahangir | 9248208 |
| Dur-e-Adan | 9248207 |

**Supervisor:** Sir Danyial Khan
**Program:** BS Artificial Intelligence — Semester 4
**University:** NUML Islamabad
**Year:** 2026

---

## 📌 Project Overview

PhytoScan AI is a plant image classification system that accepts
a leaf photograph as input and returns the predicted plant species
along with a set of pre-compiled pharmacological information from
a structured knowledge base.

The system addresses the practical difficulty of identifying
medicinal plants visually and accessing basic information about
their traditional uses, chemical constituents, toxicity level,
dosage, and known herb combinations.

**The project does not provide medically validated advice and
is intended for educational purposes only.**

**What the system does:**
- Classifies leaf images into one of 30 plant categories
- Retrieves knowledge base entries for the predicted species
- Displays toxicity level, traditional uses, dosage notes,
  and pre-defined herb combination outcomes
- Runs as a Streamlit web application launched from Google Colab

---

## 🤖 AI Model

### Architecture
The classifier uses EfficientNetB0 pretrained on ImageNet as
a feature extraction backbone with additional classification
layers added on top.

### Training Configuration

| Parameter | Value |
|-----------|-------|
| Base Model | EfficientNetB0 (ImageNet weights) |
| Unfrozen Layers | Last 30 layers |
| Input Size | 224 × 224 × 3 |
| Optimizer | Adam |
| Learning Rate | 0.0001 |
| Loss Function | Categorical Cross-Entropy |
| Batch Size | 32 |
| Max Epochs | 15 |
| Early Stopping | patience=5, monitor=val_accuracy |
| Dropout Rate | 0.3 |

### Preprocessing

EfficientNetB0 requires its own specific preprocessing function
(`tf.keras.applications.efficientnet.preprocess_input`) rather
than standard 1/255 pixel normalization.

Using standard normalization during an earlier training attempt
caused accuracy to remain near 5% across 10 epochs. Switching
to the correct preprocessing produced 81.4% validation accuracy
at Epoch 1. This is documented in the notebook as a key finding.

### Data Augmentation (Training Only)

- Rotation: ±20°
- Horizontal flip
- Zoom: ±20%
- Shear: ±20%
- Width shift: ±20%
- Height shift: ±20%

### Reported Performance

| Metric | Value | Note |
|--------|-------|------|
| Best Validation Accuracy | 90.40% | Monitored during training |
| Final Training Accuracy | ~92.57% | Last epoch |
| Macro Precision | 0.90 | Test set evaluation |
| Macro Recall | 0.90 | Test set evaluation |
| Macro F1-Score | 0.90 | Test set evaluation |

> **Note:** The 90.4% figure is the best **validation accuracy**
> recorded during training. A separate held-out test set
> (6,000 images) was also evaluated with macro F1 = 0.90.

---

## 📊 Dataset

**Source:** [Plants Classification Dataset — Kaggle](https://www.kaggle.com/datasets/marquis03/plants-classification)

| Split | Images per Class | Total |
|-------|-----------------|-------|
| Training | 700 | 21,000 |
| Validation | 100 | 3,000 |
| Test | 200 | 6,000 |
| **Total** | **1,000** | **30,000** |

- 30 plant classes
- Perfectly balanced — 700 images per class in training

---

## 🌿 30 Plant Classes

The following class names are taken directly from the dataset:
aloevera      banana        bilimbi       cantaloupe    cassava
coconut       corn          cucumber      curcuma       eggplant
galangal      ginger        guava         kale          longbeans
mango         melon         orange        paddy         papaya
peperchili    pineapple     pomelo        shallot       soybeans
spinach       sweetpotatoes tobacco       waterapple    watermelon

---

## 📉 Per-Class Performance (Selected)

Results from the classification report on the test set:

| Plant | Precision | Recall | F1 | Accuracy |
|-------|-----------|--------|----|----------|
| shallot | 0.94 | 1.00 | 0.97 | 100.0% |
| sweetpotatoes | 0.97 | 0.99 | 0.98 | 99.0% |
| waterapple | 0.89 | 0.98 | 0.94 | 98.5% |
| bilimbi | 0.97 | 0.98 | 0.97 | 98.0% |
| aloevera | 0.98 | 0.94 | 0.96 | 94.0% |
| ginger | 0.86 | 0.83 | 0.85 | 83.0% |
| melon | 0.47 | 0.82 | 0.60 | 82.5% |
| orange | 0.85 | 0.79 | 0.82 | 79.0% |
| cantaloupe | 0.33 | 0.07 | 0.12 | 7.0% |

The low accuracy on cantaloupe (7.0%) is a notable result.
The confusion matrix shows that cantaloupe images were frequently
predicted as melon — both belong to *Cucumis melo* and share
similar leaf morphology. This is an example of the inter-class
similarity challenge common in fine-grained image classification.

---

## 📈 Statistical Analysis

Statistical methods were applied to evaluate and characterize
model performance:

| Test | What Was Tested | Result | Decision |
|------|----------------|--------|----------|
| One-sample t-test | Mean accuracy vs 85% target | t=1.764 > t_crit=1.699 (α=0.05) | Reject H₀ |
| Chi-square GoF | Uniform vs observed tier distribution | χ²=22.4 > 5.991 (α=0.05) | Reject H₀ |
| Pearson correlation | Epoch vs validation accuracy | r = +0.94, p < 0.001 | Strong positive |
| Linear regression | Epoch predicting accuracy | R² = 0.883, F=35.04, p<0.001 | Significant |

**Descriptive statistics — per-class accuracy (n=30):**

| Measure | Value |
|---------|-------|
| Mean | 90.27% |
| Median | 94.25% |
| Std Dev | 16.36% |
| Min | 7.0% (cantaloupe) |
| Max | 100.0% (shallot) |
| IQR | 8.0% |

The median (94.25%) exceeds the mean (90.27%), indicating a
left-skewed distribution where a small number of poorly
performing classes pull the mean downward.

---

## 🧪 Knowledge Base

A structured JSON knowledge base was manually compiled for all
30 plant species. For each plant the knowledge base stores:

- Common name and scientific name
- Plant family
- Geographic origin
- Active chemical compounds
- Traditional medical uses
- Toxicity level (0–3 scale)
- Dosage notes by administration route
- Preparation method
- Contraindications
- Pre-defined herb combination outcomes

**Toxicity classification:**

| Level | Label | Count |
|-------|-------|-------|
| 0 | Safe | 24 species |
| 1 | Mild Caution | 4 species |
| 2 | Moderate Toxic | 1 species (cassava) |
| 3 | Highly Toxic | 1 species (tobacco) |

> **Important:** The knowledge base entries were compiled
> manually for this project. They are not clinically validated
> and should not be used as a substitute for professional
> medical advice.

---

## 🔗 Herb Combination Component

The system includes a rule-based herb combination component.
For a predicted plant, the knowledge base entry contains a
predefined list of interaction outcomes with selected other
plants in the dataset.

Each interaction is labelled as one of:

| Label | Meaning |
|-------|---------|
| Synergistic ✅ | Described as enhancing combined effect |
| Additive ➕ | Described as combining effects |
| Neutral ⚪ | Described as no notable interaction |
| Antagonistic ⚠️ | Described as reducing effectiveness |
| Dangerous ❌ | Described as contraindicated |

These outcomes are **pre-defined entries in the knowledge base**,
not predictions from a trained model. They reflect curated
information for a subset of plant pairs and are not exhaustive.

---

## 🌐 Web Application

PhytoScan AI includes a Streamlit web application
(`phytoscan_app.py`) that provides:

- Image upload interface
- EfficientNetB0 inference
- Confidence score display
- Toxicity alert (color-coded)
- Five information tabs: Basic Info, Medical Uses,
  Dosage, Compounds, Combinations

The application is launched from Google Colab and made
accessible via a temporary Cloudflare Tunnel URL.

---

## 📁 Project Structure

> The trained model (`phytoscan_model.keras`),
> knowledge base (`knowledge_base.json`),
> class names (`class_names.json`), and
> graphs are stored on Google Drive and
> are not included in this repository.
PhytoScan-AI/
│
├── PhytoScan_AI_Complete.ipynb   ← Full project notebook
├── README.md                     ← This file
└── LICENSE
---

## 🚀 How to Run

### Requirements

### Google Colab (Recommended)

1. Open `PhytoScan_AI_Complete.ipynb` in Google Colab
2. Connect to a GPU runtime (T4 recommended)
3. Mount Google Drive
4. Run cells sequentially
5. The deployment cells install Streamlit and
   create a Cloudflare Tunnel URL for browser access

### Local

```bash
pip install streamlit tensorflow pillow numpy
streamlit run phytoscan_app.py
```

The local setup requires the trained model and JSON files
to be available at the paths expected by `phytoscan_app.py`.

---

## 📥 Model Download

The trained model file is stored externally due to size.

> **[Model download link — to be added]**

---

## ⚠️ Limitations

- **Low accuracy on visually similar classes:** Cantaloupe
  achieved only 7.0% accuracy, primarily predicted as melon.
  Both share similar leaf morphology (*Cucumis melo*).
- **Closed set classifier:** The model predicts one of 30
  fixed classes. Images outside these classes will receive
  an incorrect prediction.
- **Image quality dependency:** Blurry, heavily occluded,
  or non-leaf images may produce unreliable predictions.
- **Knowledge base not clinically validated:** Combination
  and dosage information is manually compiled and not
  medically reviewed.
- **Temporary deployment URL:** The Cloudflare Tunnel URL
  changes every Colab session.
- **No offline support:** The current setup requires an
  active internet connection and a running Colab session.

---

## 🔮 Future Work

- Improve classification of visually similar species
  (Cucurbitaceae family) through targeted augmentation
  or architectural changes
- Expand the dataset to cover additional medicinal
  plant species
- Replace rule-based combination component with a
  data-driven approach if suitable interaction datasets
  become available
- Implement persistent deployment instead of
  session-dependent Cloudflare Tunnel
- Develop a mobile-compatible interface
- Have knowledge base entries reviewed by a subject
  matter expert

---

## 📚 References

**Dataset:**
- Marquis. (2022). Plants Classification Dataset. Kaggle.
  https://www.kaggle.com/datasets/marquis03/plants-classification

**Model:**
- Tan, M. and Le, Q. V. (2019). EfficientNet: Rethinking
  Model Scaling for Convolutional Neural Networks. ICML 2019.
  https://arxiv.org/abs/1905.11946

**Framework:**
- Abadi, M. et al. (2015). TensorFlow.
  https://www.tensorflow.org
- Chollet, F. (2015). Keras.
  https://keras.io
- Pedregosa, F. et al. (2011). Scikit-learn.
  https://scikit-learn.org
- Streamlit Inc. (2023). Streamlit.
  https://streamlit.io

**Pharmacological sources:**
- WHO. (2019). Monographs on Selected Medicinal Plants.
  https://www.who.int/medicines/publications/pharmacopoeia
- USDA Plants Database.
  https://plants.usda.gov
- Kim, S. et al. (2023). PubChem 2023 Update.
  https://pubchem.ncbi.nlm.nih.gov

---

## ⚕️ Medical Disclaimer

> PhytoScan AI is an undergraduate educational project.
> All plant information provided by this system is for
> informational purposes only.
> It does not constitute medical advice, diagnosis,
> or treatment recommendations.
> Always consult a qualified healthcare professional
> before using any plant or herb for therapeutic purposes.

---

## 🔒 Security Note

> If your Kaggle API credentials were included in the
> notebook before uploading to GitHub, please revoke
> and regenerate them immediately at
> https://www.kaggle.com/settings

---

## 📄 License

MIT License — see [LICENSE](LICENSE) file.

---

<div align="center">

**NUML Islamabad — BS Artificial Intelligence — Semester 4 — 2026**

*Sara Jahangir & Dur-e-Adan*

</div>



The classifier uses EfficientNetB0 pretrained on ImageNet as
a feature extraction backbone with additional classification
layers added on top.
