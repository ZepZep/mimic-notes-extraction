# MIMIC-III notes extraction
Implementation of methods used in the "Unsupervised extraction, classification and visualization of clinical note segments using the MIMIC-III dataset" paper.


## How to run
1. The `make_segments.py` script segments the clinical notes
    * inputs: modify the `MIMIC_DATA` variable to the folder that contains `NOTEEVENTS.csv.gz` file. You can edit `good_categories` and `note_relevance` to get the subset of notes you need.
    * outputs: it outputs two files into the `dataset` folder:
        * `dataset/parts.feather` which contains the individual note segments
        * `dataset/titles.feather` which contains normalized titles and their frequencies
2. You can now train the vector based methods (lsa and doc2vec) using the `train_vectors.py` script. It should automatically read the files from the dataset folder. It outputs  metrics into the `metrics` folder.
3. In order to be able to train Bi-LSTM and RobeCzech models, we need to create a Huggingface dataset using the `make_hf_dataset.py` script. It should automatically read the files from the dataset folder. It creates two folders in the dataset folder: `train.hf` and `test.hf`
4. You can now train Bi-LSTM and transformer models (`train_bilstm.py` and `train_transformer.py`). They should automatically load the HF dataset. They output metrics into the `metrics` folder.


## Adapting for different datasets / languages
* the segmentation function probably needs to be tweaked to fit your dataset formating (`cut_record` function inside `make_segments.py`)
* title normalization function might need to be tweaked for some languages (`normalize_title` function inside `make_segments.py`)
* you may want to use different tokenization function for the vector methods (`tokenize_doc` function in `train_vectors.py`)
* you may want to use different Huggingface transformer model and tokenizer. Make sure that the model is compatible with the tokenizer.
    * change the `modelname` in `make_hf_dataset.py`
    * change the `modelname` in `train_transformer.py`

## Interactive visualisations

### Latent space
We use the Tensorboard embedding projector to visualise the vector space of the 2021 extracted titles. It is available [here](https://zepzep.github.io/mimic-notes-extraction/pages/projector/).

The bookmarks (bottom right) broke recently and dont work properly. However if you manually upload the [`bookmarks_mimic.txt`](https://zepzep.github.io/mimic-notes-extraction/pages/projector/oss_data/bookmarks_mimic.txt) file, it will show the same T-SNE visualisation that is in the paper.


### Clustering treemaps
We include 2 interactive treemaps:
* [Distance](https://zepzep.github.io/mimic-notes-extraction/pages/treemap/distance.html) - color representsthe cophenetic distance between children
* [Cluster F1](https://zepzep.github.io/mimic-notes-extraction/pages/treemap/cluster_f1.html) - color represents how well (F1 score) the Med-BERT classifier is able to distinguish between segment types iside and outside of the cluster.

There is a lot more information in the treemaps that is visible while hovering over clusters
 * `MF1` and `wF1` - macro F1 and weighted F1 scores
 * `distance` and `clusterF1` - exact cophenetic distance and Cluster F1 score
 * `miss_to` - what segment types the classifier wrongly predicts from outside the cluster given a segment from inside the cluster
 * `miss_from` - what segment types outside the cluster the classifier wrongly classifies as if they should be inside the cluster


