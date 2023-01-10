# EL-module

This is the repository for Named Entity Linking. Currently, the current Entity Linking model used for this module is the BLINK model by facebook research (https://github.com/facebookresearch/BLINK), which is modified for faster and more accurate entity linking by using Elasticsearch BM25 retrieval instead for the first step.

## BLINK Model

BLINK is an Entity Linking python library that uses Wikipedia as the target knowledge base.

In a nutshell, BLINK uses a two stages approach for entity linking, based on fine-tuned BERT architectures. In the first stage, BLINK performs retrieval in a dense space defined by a bi-encoder that independently embeds the mention context and the entity descriptions. Each candidate is then examined more carefully with a cross-encoder, that concatenates the mention and entity text. BLINK achieves state-of-the-art results on multiple datasets.

## BLINK + Elasticsearch Model

For this repository, we are using a modified verision of BLINK, which is largely the same as the original model in terms of model architecture, but instead of using the bi-encoder to retrieve the candidate entities in the first step, we instead retrieve entities by making use of BM25 algorithm used by Elasticsearch. For this to work, the entities database must be pre-populated into Elasticsearch. This method is based on the findings by the paper https://arxiv.org/pdf/2205.04438.pdf.

# Setup
## Setting up ElastDocker (if running as a standalone module)
```
git submodule init
git submodule update
cd elastdocker
make setup
sysctl -w vm.max_map_count=262144
make elk
```

## Download & save BLINK models

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

## Populating the Elasticseach Entity Knowledge Base
In order to use the EL module, we will require an Elasticsearch instance to host the Entity Knowledge Base on. If running this module as a standalone module, you should set up an Elasticsearch instance as per the steps above in "Setting up ElastDocker". Ensure that the Elasticsearch settings in the configs/blink.yaml are configured to your Elasticsearch instance settings. Similarly, if you are using the EL-module as part of a bigger stack, just ensure that you change the Elasticsearch configurations in configs/blink.yaml to that of your ES instance.

Next,  run the src/upload_wikipedia_to_elastic.py script to populate the Entity Knowledge Base. Ensure that the paths to 'all_entities_large.t7' and 'entity.jsonl' are correct before running the script, as well as the Elasticsearch connection settings.

## Docker Compose
If you are using the EL module as part of a bigger system, add the blink service in the build/docker-compose.yml file to your system's docker compose and change the build path of the service to where the 
Dockerfile of the EL-module is located in the repository

Once the Docker Compose service is build and started, the EL-module API should be started. To understand and view all the available EL-module API endpoints, refer to the script src/api_service.py