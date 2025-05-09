# MIC_DP: Maximum Information Coefficient Differential Privacy

`mic_dp` is a Python package that enables differentially private data transformation guided by the *Maximum Information Coefficient* (MIC), with application to both supervised and unsupervised learning tasks. Traditional differential privacy (DP) mechanisms often degrade utility uniformly across features. In contrast, `mic_dp` uses MIC to scale the noise injection, preserving more utility in informative features.

## Summary

This package includes functions for:
- Calculating MIC, Pearson, and Mahalanobis-based feature relevance
- Feature selection based on scaled importance
- Applying Gaussian or Laplace DP mechanisms using custom noise scaling
- Evaluating MAE, clustering scores, and plotting results

Our experiments show that MIC-guided DP mechanisms consistently outperform Pearson, Mahalanobis, and baseline DP in terms of feature and prediction accuracy under privacy constraints. In unsupervised settings, MIC-DP preserves cluster structures better, as shown by silhouette score, ARI, and V-measure.

## Installation

You can install the package directly from PyPI:

```bash
pip install micdp
```

Or install from source:

```bash
git clone https://github.com/merlery/mic_dp.git
cd mic_dp
pip install -e .
```

## Quick Start

Here's a simple example of how to use `mic_dp` for supervised learning:

```python
import pandas as pd
import numpy as np
from sklearn.preprocessing import MinMaxScaler
from mic_dp.core import (
    noise_scaling_MIC, 
    calculate_sensitivity, 
    correlated_dp_gaussian,
    mean_absolute_error
)

# Load and preprocess your data
df = pd.read_csv('your_dataset.csv')
df.dropna(inplace=True)
X = df.select_dtypes(include=['number'])
X_norm = pd.DataFrame(MinMaxScaler().fit_transform(X), columns=X.columns)
y = df['target_column']  # Your target variable

# Calculate MIC-based noise scaling factors
noise_factors = noise_scaling_MIC(y, X_norm, amplification_factor=5)

# Calculate sensitivity for each feature
sensitivity = calculate_sensitivity(X_norm)

# Apply differential privacy with MIC-guided noise scaling
private_X = correlated_dp_gaussian(
    X_norm.copy(), 
    noise_factors, 
    sensitivity, 
    epsilon=0.5,  # Privacy budget
    delta=1e-5    # Privacy relaxation parameter
)

# Evaluate the utility loss
mae = mean_absolute_error(X_norm, private_X)
print(f"Mean Absolute Error: {mae:.4f}")
```

## Detailed Example

For a more comprehensive example, see the [supervised_experiment.py](examples/supervised_experiment.py) script, which demonstrates:

1. Loading and preprocessing the Adult Census Income dataset
2. Calculating feature relevance using MIC, Pearson, and Mahalanobis methods
3. Applying differential privacy with different noise scaling strategies
4. Evaluating and comparing the utility of each approach
5. Visualizing the results

To run the example:

```bash
python examples/supervised_experiment.py
```

## Experimental Results

MIC-guided noise scaling consistently outperforms conventional approaches in preserving prediction accuracy and clustering structure under differential privacy constraints.

<p align="center">
  <img src="MAE.png" alt="Feature MAE comparison for MIC-DP vs. state-of-art approaches" width="400"/>&nbsp;
  <img src="MAE_pred.png" alt="Prediction MAE comparison for MIC-DP vs. state-of-art approaches" width="400"/>
</p>


## API Reference

### Core Functions

- `noise_scaling_MIC(target, features, factor)`: Calculate noise scaling factors based on Maximum Information Coefficient
- `noise_scaling_pearson(target, features, factor)`: Calculate noise scaling factors based on Pearson correlation
- `noise_scaling_mahalanobis_distances(target, features, factor)`: Calculate noise scaling factors based on Mahalanobis distances
- `calculate_sensitivity(features)`: Calculate sensitivity for each feature based on its range
- `correlated_dp_gaussian(X, noise_factors, sensitivity, epsilon, delta)`: Apply Gaussian differential privacy with custom noise scaling
- `correlated_dp_laplace(X, noise_factors, sensitivity, epsilon, delta)`: Apply Laplace differential privacy with custom noise scaling
- `feature_selection(percentage, X, noise_scaling_factor)`: Select features based on their noise scaling factors
- `mean_absolute_error(y_true, y_pred)`: Calculate mean absolute error between true and predicted values

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgements

This material is based upon work supported by the U.S. Department of Energy, Office of Science, Office of Advanced Scientific Computing Research under Contract No. DE-AC05-00OR22725. This manuscript has been co-authored by UT-Battelle, LLC under Contract No. DE-AC05-00OR22725 with the U.S. Department of Energy. The United States Government retains and the publisher, by accepting the article for publication, acknowledges that the United States Government retains a non-exclusive, paid-up, irrevocable, world-wide license to publish or reproduce the published form of this manuscript, or allow others to do so, for United States Government purposes. The Department of Energy will provide public access to these results of federally sponsored research in accordance with the DOE Public Access Plan (http://energy.gov/downloads/doe-public-access-plan).

## AERIS Lab

<img src="aires-lab.png" alt="AERIS Lab at UW Tacoma" width="160" align="left"/>

<div align="justify">
This work is conducted and supported by AERIS Lab at the University of Washington Tacoma. At the AERIS Lab (AI & Embedded Research in Intelligent Systems), we advance the frontier of intelligent, secure, and networked technologies for the Internet of Things (IoT), edge computing, and cyber-physical systems. Our mission is to design, develop, and deploy scalable AI-driven architectures that enable real-time decision-making, privacy-preserving analytics, and seamless integration of sensing, computing, and communication. We emphasize responsible AI, sustainability, and societal impact through interdisciplinary research in smart infrastructure, embedded intelligence, and trustworthy computing. For more information, please visit: http://faculty.uw.edu/ealmasri
</div>


## References
