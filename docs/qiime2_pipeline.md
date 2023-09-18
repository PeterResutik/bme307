
# QIIME2 primer

## QIIME2 

???+ quote "What is QIIME 2?"
    A powerful, extensible, and decentralized microbiome analysis package with a focus on data and analysis transparency. QIIME 2 enables researchers to start an analysis with raw DNA sequence data and finish with publication-quality figures and statistical results.  

## QIIME2 Artifacts

Before we get started, let's briefly discuss the two main file types used with QIIME2. These are `.qza` files and `.qzv` files.

`.qza` - The QIIME2 artifact file. These contain data.

`.qzv` - The QIIME2 visualization file. These contain visualizations that can be viewed using [QIIME 2 View](https://view.qiime2.org/).

These are zipped files that contain provenance and other information in addition to data. Each artifact has a unique identifier so that you can easily track provenance.

You can simply use `unzip` to access the data, or use `qiime tools export`. Check out the [QIIME2 export tutorial](https://docs.qiime2.org/2023.7/tutorials/exporting/) for more information on exporting data and visualizations.


