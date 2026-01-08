# Cross-Domain Intrusion Detection with Explainability

> **Enhancing Explainability in Cross-Domain Intrusion Detection Models for Trustworthy Cybersecurity**

## Project Overview

This research demonstrates that enterprise-trained intrusion detection systems **catastrophically fail** when deployed to IoT environments (4.91% recall), but **few-shot learning** with minimal labeled data recovers performance to 86.03% recall—a **17.5× improvement** with just ~12.8 hours of labeling effort.

### Key Contributions

**Rigorous cross-domain evaluation** with n=10 runs and 95% confidence intervals  
**Novel CDACS metric** measuring explanation consistency across domains (0.7124)  
**Honest performance reporting** showing zero-shot and domain adaptation failures  
**Few-shot learning** as practical solution requiring only 10% labeled target data  
**SHAP/LIME explainability** revealing feature importance shifts across domains

### Quick Results

| Approach | Recall | Accuracy | Labeling Cost |
|----------|--------|----------|---------------|
| Zero-Shot | 4.91% ± 0.06% | 37.08% ± 1.39% | 0 hours |
| CORAL | 1.96% | 65.30% | 0 hours |
| **Few-Shot 10%** | **86.03% ± 0.49%** | **89.61% ± 0.15%** | **~12.8 hours** |
| Few-Shot 20% | 91.13% ± 0.45% | 91.25% ± 0.13% | ~25.5 hours |

---

## Repository Structure

```
.
├── Cross_Domain_IDS_Implementation_Notebook.ipynb    # Main implementation notebook
├── Data_Preprocessing_EDA_Notebook_.ipynb             # 10-run statistical validation
├── data/
│   └── processed/
│       └── processed_data.zip                        # Preprocessed datasets
├── requirements.txt                                   # Python dependencies
├── README.md                                          # This file
```

### Notebooks

#### 1. `Cross_Domain_IDS_Implementation_Notebook.ipynb`
Complete pipeline including:
- Data preprocessing and feature engineering
- Zero-shot, CORAL, MMD domain adaptation
- Few-shot learning (5%, 10%, 20%)
- SHAP/LIME explainability with CDACS metric
- Concept drift simulation
- Comprehensive visualizations

**Runtime:** ~30-45 minutes

#### 2. `Data_Preprocessing_EDA_Notebook_.ipynb`
Data preprocessing and exploratory analysis:
- CIC-IDS2017 and CICIoT2023 loading
- Domain-invariant feature engineering (6 features)
- SMOTE balancing and undersampling
- Exploratory Data Analysis (EDA)
- Class distribution analysis
- Feature correlation and statistics
- Data export for main notebook

**Runtime:** ~2-3 hours

### Data

`data/processed/processed_data.zip` contains:
- `cic_ids_2017_processed.csv` - Enterprise traffic (CIC-IDS2017)
- `cic_iot_2023_processed.csv` - IoT traffic (CICIoT2023)
- Domain-invariant features (6 features: TCP flags + packet rate)
- SMOTE-balanced training data
- Concept drift epoch files

---

## Original Datasets

### CIC-IDS2017 (Enterprise Network Traffic)

**Source:** Canadian Institute for Cybersecurity, University of New Brunswick  
**Download:** [https://www.unb.ca/cic/datasets/ids-2017.html](https://www.unb.ca/cic/datasets/ids-2017.html)

- **Size:** 2,830,743 flows
- **Features:** 79 flow-based features
- **Attacks:** DoS, DDoS, PortScan, Brute Force, Web Attack, Infiltration, Botnet
- **Class Distribution:** 80.3% benign, 19.7% attack

**Citation:**
```
Sharafaldin, I., Lashkari, A. H., & Ghorbani, A. A. (2018). 
Toward Generating a New Intrusion Detection Dataset and Intrusion Traffic Characterization. 
In ICISSP (pp. 108-116).
```

### CICIoT2023 (IoT Device Traffic)

**Source:** Canadian Institute for Cybersecurity, University of New Brunswick  
**Download:** [https://www.unb.ca/cic/datasets/iotdataset-2023.html](https://www.unb.ca/cic/datasets/iotdataset-2023.html)

- **Size:** 46,286,230 flows from 33 IoT devices
- **Attacks:** DDoS, DoS, Recon, Web-based, Brute Force, Spoofing, Mirai
- **Class Distribution:** 97.7% attack, 2.3% benign (rebalanced to 70/30 for evaluation)

**Citation:**
```
Neto, E. C. P., Dadkhah, S., Ferreira, R., Zohourian, A., Lu, R., & Ghorbani, A. A. (2023). 
CICIoT2023: A Real-Time Dataset and Benchmark for Large-Scale Attacks in IoT Environment.
Sensors, 23(13), 5941.
```

---

## Getting Started

### Prerequisites

- Python 3.8 or higher
- 8GB+ RAM recommended
- Jupyter Notebook or JupyterLab
- No GPU required (tree-based models run on CPU)

### Installation

1. **Clone the repository**
```bash
git clone https://github.com/hassnainalihere/Cross-Domain-IDS.git
cd Cross-Domain-IDS
```

```bash
pip install numpy pandas scikit-learn xgboost shap lime matplotlib seaborn scipy imbalanced-learn jupyter
```

2. **Extract preprocessed data**
```bash
cd data/processed
unzip processed_data.zip
cd ../..
```

### Running the Notebooks

**Recommended Order:**
1. Run `Data_Preprocessing_EDA_Notebook_.ipynb` first (prepares data)
2. Then run `Cross_Domain_IDS_Implementation_Notebook.ipynb` (uses preprocessed data)

#### Option 1: Local Jupyter
```bash
jupyter notebook
# Open notebooks in order
```

#### Option 2: JupyterLab
```bash
jupyter lab
```

#### Option 3: Google Colab
1. Upload both notebooks to Google Drive
2. Upload `processed_data.zip` to Drive (or run preprocessing notebook to generate)
3. Mount Drive in notebook:
```python
   from google.colab import drive
   drive.mount('/content/drive')
```

#### Option 4: VS Code
1. Install Jupyter extension
2. Open `.ipynb` file
3. Select Python kernel
4. Run cells

---

## Dependencies

```
numpy>=1.21.0
pandas>=1.3.0
scikit-learn>=1.0.0
xgboost>=1.5.0
shap>=0.41.0
lime>=0.2.0
matplotlib>=3.4.0
seaborn>=0.11.0
scipy>=1.7.0
imbalanced-learn>=0.8.0
jupyter>=1.0.0
```

Install all at once:
```bash
pip install -r requirements.txt
```

---

## Methodology

### 1. Domain-Invariant Feature Engineering

**6 Features:**
- `Has_SYN`, `Has_ACK`, `Has_RST`, `Has_FIN`, `Has_PSH` - TCP flag indicators (binary)
- `Packets_Per_Second` - Normalized flow rate

**Why these features?**
- Protocol-level transferability (universal across domains)
- Interpretable for security analysts
- Low computational overhead (<10ms per sample)

### 2. Cross-Domain Evaluation Protocol

**Training:** CIC-IDS2017 (enterprise) only  
**Testing:** CICIoT2023 (IoT) - completely unseen during training  
**Validation:** n=10 independent runs with 95% confidence intervals

### 3. Approaches Evaluated

1. **Zero-Shot:** Direct transfer without adaptation
2. **CORAL:** Correlation alignment
3. **MMD:** Maximum Mean Discrepancy
4. **Few-Shot:** Fine-tune with 5%, 10%, 20% labeled IoT data

### 4. Explainability Analysis

- **SHAP:** Global feature importance per domain
- **LIME:** Local per-instance explanations
- **CDACS:** Cross-Domain Attribution Consistency Score (novel metric)

---

## Key Results

### Zero-Shot Catastrophic Failure
- Recall: **4.91% ± 0.06%** (misses 95% of attacks)
- Accuracy: 37.08% ± 1.39%
- False Negative Rate: 95.09%

### Domain Adaptation Failure
- **CORAL:** 1.96% recall (worse than zero-shot)
- **MMD:** 0% recall (predicts all benign)

### Few-Shot Learning Success
**With only 10% labeled IoT data:**
- Recall: **86.03% ± 0.49%**
- Accuracy: **89.61% ± 0.15%**
- **17.5× improvement** over zero-shot
- Labeling cost: ~12.8 hours (7,651 samples)
- Statistical significance: t=307.009, p<0.001, Cohen's d=102.336

### Feature Importance Shifts
- **Has_RST:** +5090% (0.18% → 9.27%)
- **Has_ACK:** -76% (30.45% → 7.18%)
- **Packets_Per_Second:** +108% (31.60% → 65.63%)
- **CDACS:** 0.7124 (71% consistency)

---
