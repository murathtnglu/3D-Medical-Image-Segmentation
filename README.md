# 3D U-Net for Kidney and Tumor Segmentation

## 1. Overview

This work is the **extended stage of my final-year project’s segmentation pipeline**. While we initially focused on **2D segmentation of chest X-rays**, the next step was to tackle **3D volumetric segmentation**. Here, we built a **3D U-Net**–based pipeline to segment kidneys and tumors from CT scans, leveraging volumetric context for more accurate delineation compared to slice-based approaches.

---

## 2. Dataset

The pipeline uses the **KiTS23 (Kidney Tumor Segmentation Challenge)** dataset:
- **489 abdominal CT volumes**
- **Classes:**  
  - Background (0)
  - Kidney (1)
  - Tumor (2)
  - Cyst (3)
- **Note:**  Removed cyst class (3) to simplify the segmentation task and improve model focus on primary clinical targets (kidney/tumor boundary)

---

## 3. Preprocessing

To prepare the dataset for training, several preprocessing steps were applied:

- **Parallel Download**: Implemented concurrent downloading of 489 cases (~45GB) from KiTS23 repository using 8 workers with automatic retry logic
- **Format Optimization**: Converted compressed NIfTI files (.nii.gz) to uncompressed format (.nii), reducing I/O overhead by ~2x during training
- **Resampling & resizing:** Volumes were resampled to a consistent voxel spacing and cropped/resized to a fixed 3D patch size (128x128x128) for efficient training.
- **Label Reduction**: Removed cyst labels (class 3), converting 4-class to 3-class problem (background, kidney, tumor)
- **Train/Validation Split**: 80/20 stratified division (391 training, 98 validation cases)

---

## 4. Model Architecture

We implemented a **3D U-Net** architecture with encoder–decoder skip connections for volumetric segmentation.  

- **Backbone:** `ResNet-18` was integrated as the encoder backbone, bringing stronger **feature extraction capabilities** and more efficient gradient flow compared to a plain CNN encoder.  

- This allows the model to capture richer contextual features while keeping computational requirements manageable.  

---

## 5. Loss Function - Focal Tversky Loss

A **Tversky loss** was adopted to handle the severe **class imbalance** (small tumor regions vs. large background).  

---

## 6. Optimization & Training
- Optimizer: Adam (initial lr=1e-4)  
- Batch size: 8 (optimized for 16GB GPU memory)
- Epochs: 50 epochs with early stopping (patience=40)
- Model checkpoints saved for best performance  

---

## 7. Evaluation Metrics

Performance was evaluated with metrics commonly used in medical segmentation challenges:  
- **Dice coefficient** (kidney, tumor, overall)  
- **Jaccard Index (IoU)**  
- **Volumetric similarity**  
- **Hausdorff distance** (boundary quality) 

---

## 8. Used Technologies
- **Deep Learning Frameworks:** TensorFlow, Keras  
- **Medical Imaging Libraries:** NiBabel, SimpleITK  
- **Data Processing & Utilities:** NumPy, SciPy, scikit-learn  
- **Visualization:** Matplotlib, Seaborn  

---

## 9. Conclusion
This 3D pipeline significantly improves segmentation quality over 2D methods by incorporating volumetric context. The use of **Tversky/Focal Tversky loss** effectively addresses tumor class imbalance, while the **ResNet18 backbone** enhances feature learning efficiency.  
This stage represents a crucial step forward in building a complete **surgical planning system** capable of handling real volumetric medical data.


