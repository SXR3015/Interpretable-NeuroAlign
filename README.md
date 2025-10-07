# Interpretable-NeuroAlign  
**Hierarchical Alignments + Dual-Domain Cross-Attention + SAM for Interpretable Cognitive Decline Detection**

The framework integrates **static/dynamic functional connectivity (sFC/dfc)**, **ALFF**, and **structural diffusion features (FA)** through a novel dual-domain cross-attention mechanism, enhanced with hierarchical alignment modules. Interpretability is achieved via a multi-modal extension of ScoreCAM (**SAM**).

---

## 🌐 Environment Setup

Install the required dependencies with the exact versions below:

```diff
torch == 1.13.1
numpy == 1.22.3
nibabel == 1.10.2
torchcam == 0.3.2
torchvision == 0.14.1
einops == 0.6.0
python == 3.9.0
imageio == 2.31.1
```
💡 We recommend using a virtual environment (e.g., conda or venv) to avoid version conflicts.

## 🧠 Data Preparation
Before training, you must extract the following imaging features offline:
```
Feature	| Tool		|Notes
|：--------|：--------：|--------：|
sFC / dFC / ALFF	|	MATLAB + SPM12	|	Use SPM12 batch processing for fMRI preprocessing
FA (Fractional Anisotropy)		| PANDA or FSL	|	Both are supported; ensure consistent spatial normalization
```
⚠️ Important:
 * Input tensor shape affects the kernel_size of the average pooling layer in the network backbone.
 * If you encounter shape-related errors during training, manually adjust the pooling kernel size in the model definition to match your input dimensions.
## ▶️ Running the Model
### * 1. Generate k-Fold Cross-Validation Splits
Create stratified fold-wise CSV files for reproducible evaluation:

```
python generate_csv.py
```
Output: fold_0.csv, fold_1.csv, ..., stored in the data/ directory (or as configured).

### * 2. Train and Validate
Start training with cross-validation:

```
python train.py
```
The script automatically loads data according to the fold configuration and logs metrics/checkpoints.

### * 3. Test the Model
Evaluate the trained model on the test set:

```
python test.py
```
Outputs include classification metrics (accuracy, AUC, etc.) and saved predictions.

## 🌈 Generating Multi-Modal Activation Maps (MM-ScoreCAM)
To visualize interpretable activation maps across MRI modalities:

### * Step 1: Patch the torchcam Package
Replace the following files in your installed torchcam package (typically in site-packages/torchcam/) with the customized versions from the method/ folder in this repository:

```
activation.py
core.py
```
🔧 Tip: Locate your torchcam installation via:

```
import torchcam; print(torchcam.__file__)
```
### * Step 2: Run Inference with Visualization
Execute the test script with activation map generation enabled:

```
python test.py
```
✅ Ensure that opt.generate_cam = True (or equivalent flag) is set in your configuration.

### * Step 3: View Results
Generated activation maps are saved as overlay images in the images/ directory, organized by subject and modality.

📁 Project Highlights
```
🔗 Multi-modal fusion: Jointly models sFC, dFC, ALFF, and FA.
🧩 Hierarchical alignment: Aligns features across spatial and temporal granularities.
👁️ Interpretable: MM-ScoreCAM provides modality-specific contribution maps.
📊 Reproducible: Full k-fold pipeline with version-controlled dependencies.
```
