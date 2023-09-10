
# QIIME2 pipeline

???+ note "What is QIIME 2?"
    A powerful, extensible, and decentralized microbiome analysis package with a focus on data and analysis transparency. QIIME 2 enables researchers to start an analysis with raw DNA sequence data and finish with publication-quality figures and statistical results.  

### 1. Initial set-up and import libraries

 
<!--
  1. Material for MkDocs uses [semantic versioning][^2], which is why it's a
    good idea to limit upgrades to the current major version.

    This will make sure that you don't accidentally [upgrade to the next
    major version], which may include breaking changes that silently corrupt
    your site. Additionally, you can use `pip freeze` to create a lockfile,
    so builds are reproducible at all times:

    ```
    pip freeze > requirements.txt
    ```

    Now, the lockfile can be used for installation:

    ```
    pip install -r requirements.txt
    ```

> **_NOTE:_** The note content.

??? question "How to add plugins to the Docker image?"

  Material for MkDocs only bundles selected plugins in order to keep the size
  of the official image small. If the plugin you want to use is not included,
  you can add them easily:

  === "Material for MkDocs" 

    Create a `Dockerfile` and extend the official image:

    ``` Dockerfile title="Dockerfile"
    FROM squidfunk/mkdocs-material
    RUN pip install mkdocs-macros-plugin
    RUN pip install mkdocs-glightbox
    ```

  === "Insiders"

    Clone or fork the Insiders repository, and create a file called
    `user-requirements.txt` in the root of the repository. Then, add the
    plugins that should be installed to the file, e.g.:

    ``` txt title="user-requirements.txt"
    mkdocs-macros-plugin
    mkdocs-glightbox
    ```

  Next, build the image with the following command:

  ```
  docker build -t squidfunk/mkdocs-material .
  ```

  The new image will have additional packages installed and can be used
  exactly like the official image.

-->


#### 1.1 Some useful terminal commands

- `pwd` (print working directory)
- `ls` (list)
- `nano` (basic editor for creating small text files)
- `rm` (remove files)
- `mkdir` (make a directory)
- `cd` (change directory)
- `mv` (rename or move files)
- `less` (view files)
- `man` (manual)
- `cp` (copy)

!!! tip "Tip" 
    Add the `time` command in the beginning of any chunk of code to check run time.

#### 1.2 QIIME2 Artifacts

Before we get started, let's briefly discuss the two main file types used with QIIME2. These are `.qza` files and `.qzv` files.

`.qza` - The QIIME2 artifact file. These contain data.

`.qzv` - The QIIME2 visualization file. These contain visualizations that can be viewed using [QIIME 2 View](https://view.qiime2.org/).

These are zipped files that contain provenance and other information in addition to data. Each artifact has a unique identifier so that you can easily track provenance.

You can simply use `unzip` to access the data, or use `qiime tools export`. Check out the [QIIME2 export tutorial](https://docs.qiime2.org/2023.7/tutorials/exporting/) for more information on exporting data and visualizations.

#### 1.3 Activate QIIME 2 environment

```bash
conda activate qiime2-2023.5
```

### 2. Import raw data


=== "w/o time"

    ``` bash
    qiime tools import \
        --type 'SampleData[PairedEndSequencesWithQuality]' \
        --input-path Raw_data/Raw_data_zipped \
        --input-format CasavaOneEightSingleLanePerSampleDirFmt \
        --output-path Output/demux-paired-end.qza
    ```

=== "w/ time"

    ``` bash
    time \
    qiime tools import \
        --type 'SampleData[PairedEndSequencesWithQuality]' \
        --input-path Raw_data/Raw_data_zipped \
        --input-format CasavaOneEightSingleLanePerSampleDirFmt \
        --output-path Output/demux-paired-end.qza
    ```

#### 2.1 Check summary of imported data

``` bash
qiime demux summarize \
    --i-data Output/demux-paired-end.qza \
    --o-visualization Output/demux-paired-end-summary.qzv  
```

### 3.Remove primers

```bash
qiime cutadapt trim-paired \
    --i-demultiplexed-sequences Output/demux-paired-end.qza \
    --p-front-f GTGYCAGCMGCCGCGGTAA \
    --p-front-r CCGYCAATTYMTTTRAGTTT \
    --p-match-adapter-wildcards \
    --p-match-read-wildcards \
    --p-discard-untrimmed \
    --verbose \
    --o-trimmed-sequences Output/paired-end-demux-trimmed.qza | tee Output/cutadaptresults.log
```
 

### 4.Denoise reads with DADA2

<!-- > **_NOTE:_** Tamara: need to change directory to jupyter_pipeline to access paired-end-demux-trimmed.qza --- takes a while -->
 
```bash
qiime dada2 denoise-paired \
    --i-demultiplexed-seqs Output/paired-end-demux-trimmed.qza \
    --p-trunc-len-f 225 \
    --p-trunc-len-r 225 \
    --o-table Output/table.qza \
    --o-representative-sequences Output/rep-seqs.qza \
    --o-denoising-stats Output/denoising-stats.qza 
```

#### 4.1. Summarize reads and frequencies
```bash
qiime feature-table summarize \
    --i-table Output/table.qza \
    --o-visualization Output/table.qzv \
    --m-sample-metadata-file Output/metadata.tsv
```

```bash
qiime feature-table tabulate-seqs \
    --i-data Output/rep-seqs.qza \
    --o-visualization Output/rep-seqs.qzv
```
 
##### unzip the file to view results
```bash
unzip table.qzv
```
 
##### Open the index.html file

> **_NOTE:_** Think about this one! Update the path to the "new" extracted directory: open 637b925a-4039-4e0c-8606-bab23ff0f284/data/index.html

### 5. Assign taxonomy


```bash
time \
qiime feature-classifier classify-sklearn \
    --i-classifier silva-138-ssu-nr99-97-V4V5-classifier.qza \
    --i-reads rep-seqs.qza \
    --o-classification taxonomy.qza
```

##### tabulate taxonomy
```bash
time \
qiime metadata tabulate \
    --m-input-file taxonomy.qza \
    --o-visualization taxonomy.qzv
```
 

#### 5.1 Filter out seqeunces which are not bacterial

!!! tip "16S rRNA"
    By targeting 16S rRNA, we want to target bacteria and archaea. Therefore, we can exclude sequences that are unexpected such as those from chloroplasts or mitochondria. By setting --p-include p__, we are retaining only sequences annotated at a minimum to the phylum level. Note: this will look different depending on the database used. Greengenes specifically uses the following format for annotations: k__;p__;c__;o__;f__;g__;s__. Also, --p-mode contains ensures that search terms are case insensitve (e.g., mitochondria versus Mitochondria).

 
```bash
time \
qiime taxa filter-table \
--i-table table.qza \
--i-taxonomy taxonomy.qza \
--p-mode contains \
--p-include p__ \
--p-exclude 'p__;,Chloroplast,Mitochondria' \
--o-filtered-table filtered-table.qza
```
 
```bash
time \
qiime feature-table filter-seqs \
--i-data rep-seqs.qza \
--i-table filtered-table.qza \
--o-filtered-data filtered-sequences.qza
```
 

#### 5.2 Generate taxonomic barplots

```bash
time \
qiime taxa barplot \
--i-table filtered-table.qza \
--i-taxonomy taxonomy.qza \
--m-metadata-file metadata.tsv \
--o-visualization taxa-bar-plots-1.qzv
```
 
```bash
unzip taxa-bar-plots-1.qzv
```
 
> **_NOTE:_** Update the path to the "new" extracted directory: open e5f2facf-2367-4fb0-8329-7dd687e7b155/data/index.html

### 6. Create the phylogenetic tree

```bash
qiime phylogeny align-to-tree-mafft-fasttree \
--i-sequences filtered-sequences.qza \
--output-dir phylogeny-align-to-tree-mafft-fasttree
```
 
### 7. Create the rarefaction curve

!!! tip "Rarefuction curve" 
    A rarefaction curve plots the number of counts sampled (rarefaction depth) vs. the expected value of species diversity. --- Weiss et al. 2017
    
    - Let's take a look at an alpha rarefaction curve.
    
    --p- max-depth is taken from the max frequency from the table.qzv object.
    
    - help on aplha diversity: $ !qiime diversity alpha-rarefaction --help


```bash
qiime diversity alpha-rarefaction \
--i-table filtered-table.qza \
--i-phylogeny phylogeny-align-to-tree-mafft-fasttree/rooted_tree.qza \
--m-metadata-file metadata.tsv \
--p-max-depth 80000 \
--o-visualization alpha-rarefaction-plot_80000.qzv
```
 
 
```bash
#!unzip alpha-rarefaction-plot_50000.qzv

#!unzip alpha-rarefaction-plot_60000.qzv

unzip alpha-rarefaction-plot_80000.qzv
```
 
!!! warning "Update the path to the "new" extracted directory"
     Update the path to the "new" extracted directory: !open new_directory/data/index.html

open 2dcca9b2-070d-43a1-af5d-99fc3b55799b/data/index.html

!!! tip "Rarefaction curve" 
    Rarefaction curve: As the sequencing depth increases, you recover more and more of the diversity observed in the data. At a certain point (read depth), diversity will stabilize, meaning the diversity in the data has been fully captured. This point of stabilization will result in the diversity measure of interest plateauing.

### 8. Core metrics phylogenetic: alpha and beta diversities

!!! info "info" 
    We will produce a number of core diversity metrics (alpha and beta) using a QIIME 2 pipeline, qiime diversity core-metrics-phylogenetic.

???+ note "info"
    The parameters we need to know include the path to our rooted tree (--i-phylogeny), the path to our feature table (--i-table), the sampling depth at which we would like to rarefy (--p-sampling-depth), the path to the sample information (--m-metadata-file), and the name of the directory we would like to save our results to (--output-dir). If you do not have a tree, or you are not interested in phylogenetic diversity metrics, you can also use qiime diversity core-metrics. We can speed up this command by including the --p-n-jobs-or-threads parameter.

???+ info "info?"
    - The rarefaction curve shows the sampling depth and the number of samples.
    - Meghna chose 40000 sampling depth here so that we can still look at all the 18 samples.
    - Meghna tried 50000 sampling depth and the total number of samples was reduced to 9 in future plots.
    - Tamara and Meghna tried 20000 sampling depth due to alpha-rarefaction-plot_80000.qzv --> first sample drops out at around 22000 sequencing depth

```bash
qiime diversity core-metrics-phylogenetic \
--i-phylogeny phylogeny-align-to-tree-mafft-fasttree/rooted_tree.qza \
--i-table filtered-table.qza \
--p-sampling-depth 20000 \
--p-n-jobs-or-threads 4 \
--m-metadata-file metadata.tsv \
--output-dir diversity-core-metrics-phylogenetic
```
 
```bash
ls -l diversity-core-metrics-phylogenetic
```
 

#### 8.1 Alpha diversity
```bash
qiime diversity alpha-group-significance \
--i-alpha-diversity diversity-core-metrics-phylogenetic/observed_features_vector.qza \
--m-metadata-file metadata.tsv \
--o-visualization alpha-group-sig-obs-feats.qzv
```
 
```bash
unzip alpha-group-sig-obs-feats.qzv
```
 
```bash
open 51553b22-2b97-4265-90cb-f211b22016c1/data/index.html
```
 

#### 8.2 Beta rarefaction

???+ note "Note"
    Again, rarefaction is used to eliminate issues due to differences in library size prior to beta diversity. This method is built-in to QIIME 2 core metrics pipelines. We can examine the stability of a beta diversity metric using qiime diversity beta-rarefaction.

 
```bash
qiime diversity beta-rarefaction \
--i-table filtered-table.qza \
--p-metric braycurtis \
--p-clustering-method nj \
--p-sampling-depth 20000 \
--m-metadata-file metadata.tsv \
--o-visualization braycurtis-rarefaction-plot.qzv
```
 
```bash
unzip braycurtis-rarefaction-plot.qzv
```

```bash
open c9a5f4f9-22af-4a41-9dba-abb20ad140e1/data/index.html
```
 

#### 8.3 PCoA plots for beta diversity

???+ info "info?"
    PCoA was included by default in our core-metrics-phylogenetic pipeline. Because these are longitudinal data, we will customize the axis to include the varaible, week-relative-to-hct.

 
```bash
unzip uu-pcoa-emperor-w-time.qzv
```

```bash
open e864fa09-a2a2-45ef-9c7c-cdb4ffc077b2/data/index.html
```
 
```bash
qiime emperor plot \
--i-pcoa diversity-core-metrics-phylogenetic/weighted_unifrac_pcoa_results.qza \
--m-metadata-file metadata.tsv diversity-core-metrics-phylogenetic/faith_pd_vector.qza diversity-core-metrics-phylogenetic/evenness_vector.qza diversity-core-metrics-phylogenetic/shannon_vector.qza \
--o-visualization wu-pcoa-emperor-w-time.qzv
```
 
```bash
unzip wu-pcoa-emperor-w-time.qzv
```

```bash
open 8312bd2c-1554-45a3-a3ba-1d3878e9b564/data/index.html
```
