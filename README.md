
# DrugTarget AI
### Drug–Target Interaction and Binding Affinity Prediction Using Machine Learning and Geometric Graph Neural Networks

**DrugTarget AI** is an AI/ML-based project that predicts drug–target interactions and estimates binding affinity between small-molecule drugs and protein sequences. The project explores molecular feature extraction, protein sequence representation, neural network regression, and graph-based learning to support computational drug discovery.

The system uses the **KIBA and Davis benchmark datasets** and provides an interactive **Streamlit dashboard** for dataset exploration, model benchmarking, and affinity prediction.

---

## 1. Project Overview

Drug discovery involves identifying potential interactions between drug molecules and biological protein targets. Experimental screening of numerous drug–protein combinations can be expensive and time-consuming.

DrugTarget AI addresses this challenge by developing computational models that estimate binding affinity from molecular and protein sequence information. These predictions can help prioritize potential candidates for further experimental investigation.

**Project goal:** Develop and evaluate machine learning approaches for drug–target binding affinity prediction.

## 2. Key Objectives

- Analyze and preprocess the KIBA and Davis datasets.
- Extract molecular representations from drug SMILES strings.
- Represent protein sequences using amino acid composition features.
- Develop separate MLP regression models for KIBA and Davis.
- Evaluate a distance-aware GINE graph model as a baseline.
- Build an interactive Streamlit dashboard for data exploration and affinity prediction.

## 3. Datasets

The project uses two established drug–target affinity benchmark datasets.

| Dataset | Prepared Records | Unique Drugs | Unique Proteins | Target |
|---|---:|---:|---:|---|
| KIBA | 117,657 | 2,068 | 229 | KIBA score |
| Davis | 25,772 | 68 | 379 | pKd |

Repeated drug–protein pairs are grouped using their mean binding affinity.

*Note: KIBA and Davis use different target scales; their raw RMSE values should not be directly compared.*

## 4. Methodology

### 4.1 Data Preprocessing
- Load and inspect the datasets.
- Analyze missing values and duplicate records.
- Validate molecular structures using RDKit.
- Prepare drug and protein sequence representations.
- Group repeated drug–protein interactions using mean affinity.

### 4.2 Feature Representation

**Drug representation**
- Morgan molecular fingerprints generated using RDKit.
- Fingerprint radius: 2.
- Feature size: 1,024 binary features.

**Protein representation**
- Amino Acid Composition (AAC).
- 20 features representing the relative frequencies of the 20 standard amino acids.

**Combined representation:** 1,024 drug features + 20 protein features = **1,044 features per drug–protein pair**.

### 4.3 MLP Regression

Separate Multi-Layer Perceptron (MLP) regression models are trained for the KIBA and Davis datasets.

**Model architecture:**

`Input (1044) → Hidden Layer (256) → Hidden Layer (128) → Affinity Output`

Training configuration:
- Activation function: ReLU
- Optimizer: Adam
- Learning rate: 0.001
- Batch size: 256
- Regularization parameter (alpha): 0.001
- Maximum iterations: 100
- Early stopping: Enabled

### 4.4 Distance-Aware GINE Baseline

A distance-aware Graph Isomorphism Network with Edge features (GINE) is evaluated as a separate graph-based baseline on the KIBA dataset.

It uses 3D molecular information along with protein amino acid composition features.

**Model distinction:** The MLP regression model is the predictor used in the interactive dashboard. It is a feature-based neural network, not a GNN. The GINE model is evaluated separately as a baseline.

## 5. Model Evaluation

The MLP models are evaluated using an 80:20 random pair train–test split with `random_state = 42`.

### Regression Performance

| Metric | KIBA | Davis |
|---|---:|---:|
| MSE | 0.248469 | 0.380414 |
| RMSE | 0.498467 | 0.616777 |
| MAE | 0.338137 | 0.398893 |
| R² Score | **0.645966** | **0.414510** |

The KIBA model explains approximately 64.60% of the variance in its held-out test targets, while the Davis model explains approximately 41.45%.

### GINE vs. MLP: KIBA

| Metric | GINE Baseline | MLP |
|---|---:|---:|
| MSE | 0.6851 | 0.248469 |
| RMSE | 0.8277 | 0.498467 |
| MAE | 0.5927 | 0.338137 |
| R² Score | 0.0232 | **0.645966** |

On the reported KIBA test split, the MLP achieved lower prediction errors and a higher R² score than the GINE baseline.

### Secondary Classification Evaluation

Continuous affinity predictions are also thresholded into low- and high-affinity classes for additional evaluation.

| Metric | KIBA | Davis |
|---|---:|---:|
| Accuracy | 80.91% | 94.49% |
| Precision | 81.48% | 68.82% |
| Recall | 85.85% | 33.62% |
| F1-score | 83.60% | 45.17% |
| ROC-AUC | 0.8818 | 0.8971 |

This is a threshold-based evaluation of regression predictions, not a separately trained classification model. Davis has an imbalanced test set, which affects high-affinity recall.

## 6. Interactive Streamlit Dashboard

The project includes an interactive dashboard designed to make model outputs and dataset information accessible.

**Dashboard modules:**
- **Overview:** Project summary, dataset statistics, and workflow.
- **Dataset Explorer:** Explore prepared KIBA and Davis records.
- **Affinity Analysis:** Visualize binding affinity distributions.
- **Model Benchmark:** Review regression metrics and prediction comparisons.
- **Prediction:** Enter a drug SMILES string and protein sequence to estimate binding affinity.
- **Prediction History:** Review previous predictions, where implemented.

## 7. Technology Stack

| Category | Technologies |
|---|---|
| Programming Language | Python |
| Development Environment | Google Colab |
| Deep Learning | PyTorch |
| Machine Learning | Scikit-learn |
| Molecular Processing | RDKit |
| Data Processing | Pandas, NumPy |
| Visualization | Matplotlib, Seaborn |
| Web Dashboard | Streamlit |
| Data Storage | Google Drive |

## 8. Getting Started

The main implementation is provided as a Jupyter Notebook and is designed to run in **Google Colab**.

### Open the Notebook

[![Open in Google Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/BhanviV/ai_ml_files/blob/main/Drug_Target_Geometric_GNN.ipynb)

Alternatively, access the [GitHub Repository](https://github.com/BhanviV/ai_ml_files).

### Execution Steps

1. Open `Drug_Target_Geometric_GNN.ipynb` in Google Colab.
2. Connect to a Colab runtime. GPU acceleration can be enabled if available.
3. Execute the notebook cells in their intended order.
4. Upload the required KIBA dataset when prompted.
5. Allow the notebook to download and extract the Davis dataset.
6. Follow the notebook instructions for preprocessing, feature extraction, model training, and evaluation.
7. Mount Google Drive when prompted to access saved project files.
8. Run the Streamlit dashboard setup and launch cells.
9. Open the dashboard using the URL generated by Google Colab.

**Note:** The notebook uses Google Drive to store project files, trained models, datasets, and dashboard resources. Follow the paths and setup instructions defined in the notebook.

## 9. Limitations

- The evaluation uses a random pair split rather than cold-drug or cold-protein testing.
- The KIBA and Davis datasets have different affinity scales.
- Class imbalance in Davis affects high-affinity prediction recall.
- Computational affinity predictions require experimental validation.
- The MLP model does not directly incorporate full 3D protein structural information.

## 10. Future Scope

- Explore fully E(3)-equivariant graph neural networks.
- Incorporate protein structure embeddings.
- Evaluate using cold-drug and cold-protein splits.
- Expand training with larger datasets.
- Introduce uncertainty estimation for predictions.
- Develop a dedicated classification model.

## 11. Conclusion

DrugTarget AI demonstrates the application of machine learning and graph-based approaches to computational drug–target affinity prediction.

By combining molecular fingerprints, protein sequence features, neural network models, and an interactive dashboard, the project provides a computational workflow to help prioritize potential drug candidates for further investigation.

The system is intended to support early-stage drug discovery and does not replace laboratory experiments or biological validation.

## 12. References

1. Davis et al. (2011). *Comprehensive analysis of kinase inhibitor selectivity*. Nature Biotechnology.
2. Öztürk et al. (2018). *DeepDTA: Deep drug–target binding affinity prediction*. Bioinformatics.
3. Kingma, D. P., & Ba, J. (2015). *Adam: A method for stochastic optimization*. ICLR.
4. Rogers, D., & Hahn, M. (2010). *Extended-connectivity fingerprints*. Journal of Chemical Information and Modeling.
5. Pedregosa et al. (2011). *Scikit-learn: Machine learning in Python*. Journal of Machine Learning Research.

---

**Repository:** [github.com/BhanviV/ai_ml_files](https://github.com/BhanviV/ai_ml_files)

**Project:** DrugTarget AI  
**Domain:** Artificial Intelligence and Machine Learning  
**Application:** Computational Drug Discovery
