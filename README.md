# Semantically Constrained Memory Allocation (SCMA) for Embedding inEfficient Recommendation Systems


## Description:

This project applys similarity-based shared memory(SCMA) embeddings on DLRM. It is modified from Facebook's DLRM repository(https://github.com/facebookresearch/dlrm). 


## Implementation

**DLRM + SCMA: PyTorch**. Currently we only support SCMA for PyTorch.

       dlrm_s_pytorch.py - Main script for DLRM model
       lsh_pp_pretraining.py - Pretraining script for SCMA, run this file to generate the minhash table.
       min_hash_generator.py - Min hash table generator. Most of the SCMA + DLRM experiments in the paper are finished using min hash table generated by this script, including the hyperparameter tuning experiments.
       min_hash_generator_universal_hash.py - Min hash table generator using updated universal hash functions. The 540M parameters' experiment for Criteo dataset is finished using min hash table generated by this updated script.

## How to run our code?
### Prerequests:
pytorch-nightly (*6/10/19*)

scikit-learn

numpy

onnx (*optional*)

pydot (*optional*)

torchviz (*optional*)

cuda-dev(*optional*)

tqdm

### Data preparation
Download the dataset from https://www.kaggle.com/c/criteo-display-ad-challenge and Put the train.txt file and test.txt file in /dlrm_ssm/input/


### Run SCMA embeddings with DLRM:
#### Note: This code provides a proof of concept of LMA-DLRM . It precomputes lsh based mapping and uses it in code. We are in process of releasing the efficient code where mapping is computed on the fly. 
#### 1. Pre-training
Use tricks/lsh_pp_pretraining.py to generate the min_hash_table for SCMA embeddings. 3 hyperparameter is needed：

|command line arguments|usage|
|:--------------------|:----|
|EMBEDDING|embedding dimension|
|NUM_HASH|number of hash functions used to generate min_hash_table|
|NUM_PT|number of datapoints used to represent the dataset|

For example:  

```
python tricks/lsh_pp_pretraining 128 2 125000
```
will generate a min hash table with 128 embedding dimension using 2 hash functions and 125k datapoints.

#### 2. Training
Use dlrm_s_pytorch.py to train the DLRM model with SCMA embeddings.

command line arguments
|command line arguments|type|usage|
|:--------------------|:---|:----|
|lsh-emb-flag|-|enable SSM embedding for DLRM|
|lsh-emb-compression-rate|float|1/expansion rate|
|rand-emb-flag|-|enable HashNet embedding for DLRM|
|rand-emb-compression-rate|float|1/expansion rate|
|arch-sparse-feature-size|string|we use "13-512-256-128" which is the default value for DLRM|
|arch-mlp-bot|string|we use "1024-1024-512-256-1" which is the default value for DLRM|
|data-generation|string|use "dataset" to run our code, default is "ramdom"|
|data-set|string|use "kaggle" to run our code|
|raw-data-file|string|please use "./input/train.txt"|
|processed-data-file|string|please use "./input/kaggleAdDisplayChallenge_processed.npz"|
|loss-function|string|please use "bce"|
|round-targets|boolean|pleause use "True"|
|learning-rate|double|default learning rate is 0.1|
|mini-batch-size|int|the batch size is set to 2048 in our experiments|
|nepochs|int|the number of epochs is set to 15 in our experiments|
|print-freq|int|the frequency to print log|
|print-time|-||
|test-mini-batch-size|int|the batch size for test, please use 16384 to run our code| 
|test-num-workers|int|we use 16 for our experiments|


#### A sample run of DLRM with SSM ebmedding code
A sample that runs our code is in:
```
bench/dlrm_s_criteo_kaggle.sh
```

### Run HashNet embeddings with DLRM:
#### 1. Install HashNet
cuda-dev is a prerequest for our implementation of HashNet embeddings.
```
python tricks/hashedEmbeddingBag/setup.py install
```
will install the HashNet

#### 2. Run DLRM with HashNet embedding
Use --rand-emb-flag in bench/dlrm_s_criteo_kaggle.sh to enbale HashNet embedding, use --rand-emb-compression-rate to set your expansion rate. Other settings can stay the same as SSM embedding.

## License

This source code is licensed under the MIT license found in the
LICENSE file in the root directory of this source tree.
