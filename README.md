# EL-module

This is the repository for Named Entity Linking. Currently, the current Entity Linking model used for this module is the BLINK model by facebook research (https://github.com/facebookresearch/BLINK), which is modified for faster and more accurate entity linking by using Elasticsearch BM25 retrieval instead for the first step.

## BLINK Model

BLINK is an Entity Linking python library that uses Wikipedia as the target knowledge base.

In a nutshell, BLINK uses a two stages approach for entity linking, based on fine-tuned BERT architectures. In the first stage, BLINK performs retrieval in a dense space defined by a bi-encoder that independently embeds the mention context and the entity descriptions. Each candidate is then examined more carefully with a cross-encoder, that concatenates the mention and entity text. BLINK achieves state-of-the-art results on multiple datasets.

## BLINK + Elasticsearch Model

For this repository, we are using a modified verision of BLINK, which is largely the same as the original model in terms of model architecture, but instead of using the bi-encoder to retrieve the candidate entities in the first step, we instead retrieve entities by making use of BM25 algorithm used by Elasticsearch. For this to work, the entities database must be pre-populated into Elasticsearch. This method is based on the findings by the paper https://arxiv.org/pdf/2205.04438.pdf.

# Setup
Setting up ElastDocker
```
git submodule init
git submodule update
cd elastdocker
make setup
sysctl -w vm.max_map_count=262144
make elk
```

Download & save BLINK models

First create a folder for the models by running:
```
mkdir models
```

Next, download the BLINK model weights by running:
```
cd src/blink_scripts/download_blink_models.sh
chmod +x download_blink_models.sh
./download_blink_models.sh
```
After which, move the downloaded models into the models folder that you created earlier

Also, you will also need to download the pre-trained language model 'bert-large-uncased' from Huggingface and put it in the 'models' folder

