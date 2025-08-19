

# Quantum-Classical Neural Network for Respiratory Disease Classification

This project implements a **hybrid classical-quantum neural network** to detect respiratory diseases using **lung sound signals**. We used the ICBHI 2017 dataset and applied advanced audio preprocessing and data augmentation, followed by classification using quantum circuits integrated with deep learning.

## Problem Statement

Early diagnosis of respiratory illnesses is crucial due to their high global mortality rate. Traditional methods often rely on manual auscultation. This project automates the classification of lung diseases based on sound signals using quantum machine learning.

## Dataset

- **Source**: ICBHI 2017 Challenge Dataset or Respiratory Sound Database from Kaggle  
- **Patients**: 126  
- **Audio Files**: 920 
- **Diagnosis Classes**: 6 

Each audio file ranges from 10–90 seconds and has varying sampling rates.
Main problem with the dataset: class imbalance 

## Data Preprocessing

- **Resampling** to 4 KHz
- **Snippet Generation**: Break long files into smaller time (6-sec) segments or snippets that contain crackles or wheezes.
- **Baseline or DC Wandering Removal** using Discrete Fourier Transform (0–1 Hz noise eliminated)


## Data Augmentation

To balance classes, we initially applied:
- **Time Stretching**: 0.7x and 0.9x speed
- **Pitch Shifting**: −2 and +1 semitones
- **Noise Addition**: White noise to minority class signals

Then , we applied Variational Auto-Encoder (VAE)
  

## Spectrogram Generation

- Converted processed audio to log-scaled **mel-spectrogram** images.


## Model Architecture

A **3-stage hybrid model**:

1. **Feature Extraction**  
   - Convolutional + MaxPooling layers for extracting spatial features

2. **Quantum Processing**  
   - 4-qubit quantum circuit (16 outputs as probabilities)  
   - Features encoded as rotation angles (θ₀ to θ₇) for quantum gates

3. **Classification**  
   - Fully connected layer maps quantum output to disease probabilities using softmax

## Quantum Circuit Design

<img src="./Images/Screenshot (7).png" alt="Alt text" width="500"/>
Images/Screenshot (7).png
- Each of the 4 qubits is rotated using 2 parameters
- Final measurement yields 16-dimensional probability vector
- Example output: `P(|0110⟩)` = Probability that 2nd and 3rd qubit are in state |1⟩

## Results
   Overall 
- **Accuracy**: 95.66 %  
- **Sensitivity**:  96.27 %
- **Specificity**: 98.55 %

  
## Tools & Technologies

- **Languages**: Python  
- **Libraries**: NumPy, SciPy, TorchAudio, Matplotlib, pandas
- **Deep Learning framework**: PyTorch  
- **Quantum ML**: Qiskit





