# Laboratory 2 – Solar Flare Prediction using FISTA and LASSO

**Authors:** Di Via & Hiller-Szala

## Overview

This laboratory focuses on predicting solar flare occurrences within the next 24 hours using machine learning techniques and solar observations collected by NASA's Solar Dynamics Observatory (SDO), through its Helioseismic and Magnetic Imager (HMI).

The objective is to formulate a binary classification problem and solve it using **LASSO regularization**, optimized through the **Fast Iterative Shrinkage-Thresholding Algorithm (FISTA)**.

The model analyzes magnetic and morphological features extracted from HMI Active Region Patches (HARPs) to predict whether a solar active region is likely to produce a flare.

## Dataset

The dataset is based on HMI observations collected between September 15, 2012, and April 11, 2013.

It consists of:

- **100 training observations**, corresponding to solar active regions.
- **23 features** extracted from HMI images for each observation.
- **Binary labels** indicating whether a solar flare occurred:
  - `0`: No solar flare (85 observations).
  - `1`: Solar flare (15 observations).
- **1 test observation**, recorded on April 13, 2013.

The dataset is imbalanced, with significantly fewer positive samples than negative ones.

### Input files

The MATLAB implementation uses the following data files:

- `X_training_standardizzato.mat` – Standardized training feature matrix.
- `Y_training.mat` – Training labels.
- `X_test_standardizzato.mat` – Standardized test observation.

## Methodology

The laboratory is divided into three main steps.

### 1. FISTA implementation for LASSO

We formulate the learning problem as a LASSO-regularized least squares minimization problem:

\[
\min_{\beta}\frac{1}{2}\|X\beta-y\|_2^2+\lambda\|\beta\|_1
\]

The optimization problem is solved using FISTA, which combines proximal gradient descent with an acceleration mechanism.

The implementation includes:

- Gradient computation for the least squares term.
- Soft-thresholding proximal operator.
- Momentum updates for acceleration.
- Convergence monitoring and visualization.

**Parameters used:**

| Parameter | Value |
|---|---:|
| Initial coefficients | Zero vector |
| Regularization parameter (\(\lambda\)) | 0.05 |
| Step size (\(\gamma\)) | \(1/\|X^TX\|_2\) |
| Tolerance | \(10^{-6}\) |
| Maximum iterations | 5000 |

The algorithm converges in **2553 iterations**, producing a coefficient vector with 2 zero components.

### 2. Optimal threshold computation

Since the model produces continuous predictions, a threshold is required to obtain binary classifications.

We evaluate 100 thresholds over the range of training predictions and select the one maximizing the **True Skill Statistic (TSS)**.

The TSS is particularly suitable for imbalanced datasets and is defined as:

\[
TSS = \frac{TP}{TP+FN}+\frac{TN}{TN+FP}-1
\]

We also compute Accuracy to assess the overall proportion of correctly classified observations.

**Results:**

| Metric | Value |
|---|---:|
| Optimal threshold | 0.1415 |
| Maximum TSS | 0.7294 |
| Accuracy | 91.00% |

The confusion matrix at the optimal threshold reports:
- 6 false positives.
- 3 false negatives.

In this application, false positives are preferable to false negatives, since a predicted flare can be manually verified, whereas a missed flare may have more serious consequences.

### 3. Generalization on test data

Finally, we apply the trained model to the unseen test observation recorded on April 13, 2013.

The prediction is obtained by comparing the model output against the optimal threshold:

\[
\hat{y}_{test} =
\begin{cases}
1 & \text{if } X_{test}\beta \geq t^*\\
0 & \text{otherwise}
\end{cases}
\]

**Prediction:**

`A solar flare is likely to occur!`

The prediction is correct: on April 13, 2013, the Sun produced an **M6.5-class solar flare**, accompanied by a coronal mass ejection (CME), originating from active region AR 11719.

## Implementation

The laboratory is implemented in **MATLAB**.

The main components are:

- **Data loading:** Importing the standardized training and test datasets.
- **FISTA optimization:** Solving the LASSO minimization problem.
- **Threshold selection:** Evaluating TSS and Accuracy across 100 thresholds.
- **Performance evaluation:** Generating performance plots and a confusion matrix.
- **Prediction:** Applying the trained model to the unseen test observation.

The custom `fista` function implements the optimization algorithm, including input validation, initialization, momentum updates, stopping criterion and convergence visualization.

## Visualizations

The implementation produces the following plots:

- FISTA convergence plot, showing the difference between consecutive objective function values.
- TSS and Accuracy as functions of the classification threshold.
- Confusion matrix evaluated at the optimal threshold.

## Requirements

- MATLAB
- Statistics and Machine Learning Toolbox (for `confusionchart`)

The three `.mat` dataset files must be available in the MATLAB working directory.

## How to run

1. Clone or download this repository.
2. Place the dataset files in the MATLAB working directory.
3. Ensure that the `fista.m` function is available in the same directory (if stored separately).
4. Open the main MATLAB script.
5. Execute the script.

The script will load the data, train the LASSO model using FISTA, determine the optimal classification threshold, display evaluation plots and generate the final solar flare prediction.

## Conclusions

This laboratory demonstrates the application of proximal optimization methods to a real-world binary classification problem.

The combination of LASSO regularization and FISTA provides an efficient approach to feature selection and model optimization. Despite the limited and imbalanced training dataset, the model achieves a TSS of **0.7294** and an Accuracy of **91%**.

Most importantly, the model correctly predicts the occurrence of the significant M6.5 solar flare recorded on April 13, 2013, demonstrating its ability to generalize to an unseen observation.

---

**Laboratory 2 – Di Via & Hiller-Szala**