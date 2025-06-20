# TaxaPLN: a taxonomy-aware augmentation strategy for microbiome-trait classification including metadata

> Microbiome datasets are often high dimensional, limited in size and highly variable, making it hard for machine learning models to find reliable patterns.
> In this context, data augmentation has emerged as a promising solution to improve model performance by generating synthetic samples.
>
> Here, we develop and evaluate TaxaPLN, an augmentation strategy which leverages taxonomic information and a VAMP sampler to generate new biologically faithful microbiome compositions. 
> It can also generate microbiomes conditioned on exogenous information, allowing for covariate-aware augmentation.

# ⚡️ General usage

First, install the `plntree` package, which contains the implementation of TaxaPLN
```bash
pip install plntree
```

Then, import your microbiome data as a `pandas.DataFrame` with the counts of each taxon (rows: samples, columns: taxa), as well as your covariates if you want to include them.
We provide a fast access to microbiome studies from the `curatedMetagenomicData` package, which can be used to retrieve the data and covariates as follows:
```python
import curatedMetagenomicData as cMD

taxa_abundance = cMD.taxa_abundance(
    directory='./',              # Relative path to the data directory
    study='ZhuF_2020',           # Study name
    taxonomic_levels=('c', 's'), # Taxonomic levels to retrieve
    prevalence=0.,               # Minimum prevalence of taxa to include
    total_reads=100_000          # Total abundance of each sample (proportions to counts)
)
covariates = cMD.metadata(
    directory='./',              # Relative path to the data directory
    study='ZhuF_2020',           # Study name
)
```

Upon data retrieval, you can use the `PLNTree` class to train a model on your data, as follows:
```python
from plntree import PLNTree

model = PLNTree(
    taxa_abundance,   # DataFrame with counts (rows: samples, columns: taxa)
    covariates=covariates,  # DataFrame with covariates (optional, default None)
    device='cpu',     # Device to use for training (default CPU, or 'cuda' for GPU)
    seed=0,           # Random seed for reproducibility (default None)
)
```

Then, you can fit the model on your data:
```python
model.fit(max_epoch=5_000, batch_size=512, learning_rate=1e-3)
```

Finally, you can use the model to generate synthetic samples using the `sample` method:
```python
X_aug, Z_aug = model.vamp_sample(n_samples=1000, seed=0)
```
which you can concatenate with your original data to augment your training set as follows:
```python
import pandas as pd

X_L = X_aug[:, -1, :]                                   # Last layer of the hierarchy contains the same entries as the original data
X_L = pd.DataFrame(X_L, columns=model.counts.columns)   # Convert to DataFrame, keeping columns in order of model counts
X_L = X_L[taxa_abundance.columns]                       # Reorder taxa according to the original count data
X_augmented = pd.concat([taxa_abundance, X_L], axis=0)  # Concatenate original and augmented data
```

# ⚡️ Reproducing our results

We implemented TaxaPLN as part of the PLN-Tree package. 
In this folder, you will find the code and experiments that we ran in the context of [our paper available here](.).

To reproduce our results from this paper, you first need to install the `plntree` package:
```bash
pip install plntree
```

Then, you need to train the PLN-Tree models for each dataset from the `curatedMetagenomicData` package we used, using the following command:
```bash
python -m python_script.py
```
Since we ran comprehensive experiments in 25 x 5-Fold CV (125 models per dataset), this procedure takes several days to end.

Upon completion, the models should be cached in `cache`, allowing you to run the [plntree_data_augmentation-CV.ipynb](.) notebook and reproduce the results of our paper.

## 📜 Citation

Please cite our work using the following reference:

-