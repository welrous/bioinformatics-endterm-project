# Methodology

## Overview

This project predicts protein secondary structure (Q3 classification) from amino acid sequences using deep learning.
The workflow includes data preprocessing, sequence encoding, model training with multiple neural network architectures, and evaluation.

All methods are implemented in Python using TensorFlow/Keras and scikit-learn.

---

## 1. Data Acquisition

Dataset: PDB Secondary Structure Dataset
Source: [https://github.com/zyxue/pdb-secondary-structure](https://github.com/zyxue/pdb-secondary-structure)

The dataset contains:

* `pdb_id` – protein identifier
* `seq` – amino acid sequence
* `sst3` – secondary structure labels (Q3: H, E, C)
* `has_nonstd_aa` – indicator for nonstandard amino acids

The dataset was loaded using:

```python
df = pd.read_csv("dataset.csv")
```

---

## 2. Data Preprocessing

### 2.1 Filtering

Sequences containing nonstandard amino acids were removed:

```python
df = df[df['has_nonstd_aa'] == False]
```

This ensures biological validity and consistent encoding.

---

### 2.2 Sequence Encoding

Each amino acid was converted to an integer representation.

Dictionary used:

```
ACDEFGHIKLMNPQRSTVWY
```

Encoding scheme:

* Each amino acid → integer
* 0 reserved for padding

```python
aa_to_int = {aa: i+1 for i, aa in enumerate(amino_acids)}
```

Sequences were truncated to a maximum length of:

```
MAX_LEN = 700
```

Then encoded:

```python
df['seq_encoded'] = df['seq'].apply(encode_sequence)
```

---

### 2.3 Secondary Structure Encoding (Q3)

Secondary structure labels were mapped to integers:

* Helix (H)
* Sheet (E)
* Coil (C)

Encoded using:

```python
df['sst3_encoded'] = df['sst3'].apply(encode_ss)
```

---

### 2.4 Padding

All sequences were padded to equal length (700 residues):

```python
X = pad_sequences(df['seq_encoded'], maxlen=MAX_LEN, padding='post', value=0)
y = pad_sequences(df['sst3_encoded'], maxlen=MAX_LEN, padding='post', value=2)
```

Padding ensures compatibility with neural networks.

---

### 2.5 Train/Test Split

Data was split into training and testing sets:

```python
train_test_split(X, y, test_size=0.2, random_state=42)
```

This allows unbiased evaluation of model performance.

---

## 3. Computational Methods

Three neural network architectures were implemented and compared.

---

### 3.1 Feedforward Neural Network

Architecture:

* Embedding layer
* Dense hidden layer
* Output layer with softmax

Purpose:
Provides a baseline deep learning model for sequence classification.

---

### 3.2 Bidirectional LSTM Model

Architecture:

* Embedding layer
* Bidirectional LSTM
* Dense softmax output

Purpose:
Captures sequential dependencies in amino acid sequences and improves structure prediction accuracy.

Loss function:

```
sparse_categorical_crossentropy
```

Optimizer:

```
adam
```

---

### 3.3 Convolutional Neural Network (CNN)

Architecture:

* Embedding layer
* Conv1D layers
* Dense output layer

Purpose:
Detects local patterns in protein sequences that correlate with structural motifs.

---

## 4. Model Training

Each model was trained using:

* Training dataset
* Validation split
* Multiple epochs

Training performance was tracked using:

* Accuracy
* Validation accuracy

Visualization:

```python
plot_history(history)
```

---

## 5. Evaluation

Models were evaluated on the test dataset using:

### Metrics

* Accuracy
* Classification report
* Confusion matrix

Predictions were generated:

```python
y_pred = model.predict(X_test)
```

Evaluation function:

```python
evaluate_model(model, X_test, y_test)
```

Confusion matrices were visualized using seaborn heatmaps.

---

## 6. Comparison of Methods

Three approaches were compared:

1. Feedforward neural network
2. LSTM model
3. CNN model

This allows analysis of:

* Sequential modeling effectiveness
* Pattern detection capability
* Overall prediction performance

---

## 7. Reproducibility

The project is fully reproducible:

1. Install dependencies:

```
pip install -r requirements.txt
```

2. Run notebook:

```
jupyter notebook notebook.ipynb
```

All steps from data loading to evaluation are included in the notebook.

---

## 8. Limitations

* Sequence length capped at 700 residues
* No hyperparameter tuning performed
* Dataset imbalance may affect accuracy
* Structural context beyond sequence not used

---

## 9. Future Improvements

* Transformer models
* Larger datasets
* Hyperparameter optimization
* Cross-validation
* Structural feature integration
