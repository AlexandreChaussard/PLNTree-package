![PyPI](https://img.shields.io/pypi/v/plntree)
![GitHub](https://img.shields.io/github/license/AlexandreChaussard/PLNTree-package)
![Python Versions](https://img.shields.io/badge/python-3.8+-blue)
![GPU Support](https://img.shields.io/badge/GPU-Supported-brightgreen)
# PLN-Tree: Hierarchical Poisson Log-Normal models
> The Poisson Log-Normal (PLN) models are used for the
> analysis of multivariate count data. PLN-Tree extends this framework to 
> hierarchically organized count data by incorporating tree-like structures
> into the analysis. This package provides efficient algorithms to perform the PLN-Tree inference
> by leveraging PyTorch with GPU acceleration.
> 
> PLN-Tree has shown interesting applications to metagenomics, exploiting the taxonomy 
> as a guide for microbiome modeling. 

> Typical applications involve:
> - **Hierarchical Modeling**: investigate the relationships between taxa at different levels of the taxonomy, between levels relationships and covariates impact.
> - **Data Augmentation**: generate synthetic samples to inflate training sets and enhance performances. See TaxaPLN augmentation.
> - **Counts preprocessing**: transform counts using the LP-CLR transform to tackle the challenges of compositionality and integer constraints of count data.

## 📖 Documentation and tutorials

Want to learn how to use the package? 
Start with the quickstart guide below, 
then explore the [documentation]().

## 🛠 Installation

**PLN-Tree** is available on [PyPI](https://pypi.org/project/plntree/) for faster installation.

```sh
pip install plntree
```

## ⚡️ Quickstart

This package comes with human microbiome data from the [curatedMetagenomicData](https://waldronlab.io/curatedMetagenomicData/index.html) library.
```python
from plntree.data import cMD

taxa_abundance = cMD.get_study(
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

The `taxa_abundance` is a `pandas.DataFrame` containing the microbial composition 
of each patient, while the `covariates` is a `pandas.DataFrame` with the metadata 
associated to each patient.

### Training a PLN-Tree model

The `PLNTree` class allows to specify the parameters of the model, and perform the inference on the training data.
```python
from plntree import PLNTree
model = PLNTree(...)
```

TODO: define the parameters of the model.

The package comes with visualization functions to help interpret the data, notably
calling the `tree.plot` method, which will display the tree structure.
```python
from plntree import PLNTree
model.tree.plot()
```

Training a PLN-Tree model is done by calling the `fit` method on the model.
```python
model.fit(...)
```
TODO: define the parameters of the fit method.

### Applications

#### Data Augmentation
PLN-Tree can be used to generate synthetic samples to augment training sets and 
improve downstream tasks performances.

For microbiome data, an effective way to perform data augmentation relies on the [TaxaPLN](.) strategy,
which is thoroughly described in [this paper](). In a nutshell, TaxaPLN uses the PLN-Tree model to generate synthetic samples
through a post-hoc VAMP sampler that is instanciated from the trained model.
```python
X_aug, Z_aug = model.vamp_sample(n_samples=1000)
```
TODO: define the parameters and outputs of the vamp_sample method.

#### Count Preprocessing with LP-CLR
PLN-Tree can also be used to preprocess count data using the LP-CLR transform, 
which is a log-ratio transformation that addresses the challenges of compositionality 
and integer constraints of count data by leveraging the latent space.

Upon training a PLN-Tree model, applying the preprocessing can be done through the `latent_proportion` method
which defines counts in the latent space, before applying the CLR transform.
```python
X_transformed = model.latent_proportion(taxa_abundance)
X_LPCLR = ...
```
TODO: define the parameters and outputs of the latent_proportion method.

## 👐 Contributing

Want to contribute? Check the guidelines in [CONTRIBUTING.md](https://github.com/AlexandreChaussard/PLNTree-package/blob/main/CONTRIBUTING.md).

## 📜 Citations

Please cite our work using the following references:

- Chaussard, A., Bonnet, A., Gassiat, E., & Le Corff, S. (2024). *Tree-based variational inference for Poisson log-normal models.* [arXiv preprint arXiv:2406.17361](https://arxiv.org/abs/2406.17361).

