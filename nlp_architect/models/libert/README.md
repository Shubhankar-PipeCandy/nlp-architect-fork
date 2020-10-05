# LiBERT

## Installation

Intructions for machines with CUDA 10.2 installed:

```bash
python3.6 -m pip install -U pip setuptools virtualenv
python3.6 -m venv libert_env
source libert_env/bin/activate
git clone --branch libert https://github.com/NervanaSystems/nlp-architect.git
pip install -r nlp-architect/nlp_architect/models/libert/requirements.txt
```

## Prepare Data

### SemEval14-15 (Tokenized) Dataset and KDD-2004 Dataset

SemEval14-15 data contains reviews in restaurants and laptops domains. It can can be obtained from [this](https://github.com/HKUST-KnowComp/RINANTE) GitHub Repository ([MIT License](https://github.com/HKUST-KnowComp/RINANTE/blob/master/LICENSE)).
KDD-2004 data contains reviews the devices domains. It can can be obtained from [this](https://github.com/happywwy/Recursive-Neural-Structural-Correspondence-Network) GitHub Repository (unlicenesed).
Here are the commands for downloading and pre-processing train, dev and test datasets for laptops, restaurants and devices domains.

Let's define some variables for these steps:

```bash
export SE_URL=https://raw.githubusercontent.com/HKUST-KnowComp/RINANTE/master/rinante-data
export LAPTOPS_FILES=laptops_test_sents.json,laptops_test_texts_tok_pos.txt,laptops_train_sents.json,laptops_train_texts_tok_pos.txt
export RESTAURANTS_FILES=restaurants_test_sents.json,restaurants_test_texts_tok_pos.txt,restaurants_train_sents.json,restaurants_train_texts_tok_pos.txt
export KDD_URL=https://raw.githubusercontent.com/happywwy/Recursive-Neural-Structural-Correspondence-Network/master/util/data_semEval
export KDD_FILES=addsenti_device,aspect_op_device
```

Download the datasets into the required folder structure:

```bash
mkdir -p nlp-architect/nlp_architect/models/libert/data/Dai2019/semeval14/laptops && cd $_
curl -# -L "$SE_URL/semeval14/laptops/{$LAPTOPS_FILES}" -O --remote-name-all
mkdir ../restaurants && cd $_
curl -# -L "$SE_URL/semeval14/restaurants/{$RESTAURANTS_FILES}" -O --remote-name-all
mkdir -p ../../semeval15/restaurants && cd $_
curl -# -L "$SE_URL/semeval15/restaurants/{$RESTAURANTS_FILES}" -O --remote-name-all
mkdir -p ../../../Wang2018 && cd $_
curl -# -L "$KDD_URL/{$KDD_FILES}" -O --remote-name-all
cd ../..
```

Convert the datasets into CoNLL format, and then into CSV with added lingusitic info:

```bash
python preprocess.py
python add_linguistic_info.py
```

## Running experiments

Parameters for an experiment are defined in a YAML configuration file for that expermient.

- Create `CONFIG_NAME.yaml` file with hyperparams in config folder (see `example.yaml` file).

- Run:

    ```bash
    python run.py CONFIG_NAME
    ```

## Accessing TensorBoard remotely

- On remote machine:

    ```bash
    tensorboard --host REMOTE_HOST --port PORT --logdir path/to/nlp-architect/models/libert/logs
    ```

- On local machine:

    ```bash
    ssh user@REMOTE_HOST -L PORT:REMOTE_HOST:PORT
    ```

- Point browser to:

    `http://localhost:PORT/`