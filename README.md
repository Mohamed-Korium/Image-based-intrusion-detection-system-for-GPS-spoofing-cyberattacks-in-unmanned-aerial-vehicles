# Image-based-intrusion-detection-system-for-GPS-spoofing-cyberattacks-in-unmanned-aerial-vehicles

Developed IDS system that uses drone flight logs to detect GPS spoofing, an attack in which fake GPS signals can mislead a drone about its location. The authors converted groups of 32 consecutive sensor records into 32 × 32 RGB images (image encoding), then trained image classifiers to distinguish normal flight data from spoofing. 

## Data Preprocessing and Model Training

| Step | Method | Purpose | Numbers or outcome |
|---|---|---|---|
| Feature scaling | `QuantileTransformer` | Puts numerical sensor features on a common scale. | Original feature scales → **0–1** |
| Dimensionality reduction | Principal Component Analysis (PCA) | Finds how many components capture most of the variance in the sensor data. | **32 components** reached the **99%** cumulative explained variance threshold. |
| Train/test split | `train_test_split` | Separates the data used for training from the data reserved for testing. | **474,865 records** → **379,892 training (80%)** and **94,973 test (20%)** |
| Class balancing | ADASYN oversampling | Creates synthetic minority-class records in the training set. | Class `1` went from **9,303 → 360,004** (+350,701). Class `0` stayed at **370,589**. |
| Image encoding | RGB image conversion | Converts numerical sensor data into images that a CNN can process. | In the code shown, **96 rows × 32 values** → **one 32 × 32 × 3 RGB image** saved as a PNG. |
| Data augmentation | Training image augmentation | Creates variations of the encoded images to increase training diversity and reduce overfitting. | Original training images → additional image variants |
| Data loading | TensorFlow `prefetch(tf.data.AUTOTUNE)` | Loads upcoming batches while the model processes the current batch. | Used on the training, validation, and test datasets. |
| Transfer learning | ImageNet-pretrained CNNs | Reuses learned image features for GPS spoofing detection. | Compared **8 CNN architectures**, freezing lower layers and fine-tuning higher layers. |
| Hyperparameter tuning | Bayesian optimization with Hyperopt TPE | Searches for better Inception-v3 settings. | Unseen-dataset accuracy improved from **46.44% → 99.06%**. |
| Model evaluation | Held-out test split and separate 3DR IRIS dataset | Measures how well the models detect GPS spoofing on data from a different drone. | **VGG-19** reached **100%** on the original test split and **99.25%** on the unseen dataset. |

## Model Performance

The models were evaluated on a held-out test split from the original UAV attack dataset and on a separate, unseen 3DR IRIS dataset.

| Model | Original dataset test accuracy | Unseen 3DR IRIS accuracy |
|---|---:|---:|
| **VGG-19** | **100%** | **99.25%** |
| ResNet-152 | 100% | 99.00% |
| ResNet-50 | 99.65% | 75.25% |
| Inception-v3 | 99.65% | 46.44% |
| Inception-v3 after Bayesian tuning | — | 99.06% |

**VGG-19** achieved the highest accuracy on the unseen dataset. Bayesian hyperparameter tuning improved Inception-v3's accuracy on that dataset from **46.44% to 99.06%**.

[Source: Korium et al. (2024), *Ad Hoc Networks*](https://doi.org/10.1016/j.adhoc.2024.103597)
