<h3 align="center">
<p>ExCorD
<a href="https://github.com/dmis-lab/excord/blob/master/LICENSE">
   <img alt="GitHub" src="https://img.shields.io/badge/License-MIT-yellow.svg">
</a>
</h3>
<div align="center">
    <p><b>Ex</b>plicit Guidance on How to Resolve <b>Co</b>nve<b>r</b>sational <b>D</b>ependency
</div>

<div align="center">
  <img alt="ExCorD Overview" src="https://github.com/dmis-lab/excord/blob/main/images/ExCorD_overview.png" width="400px">
</div>

We present ExCorD for conversational question answering (CQA). You can train RoBERTa by using our framework, ExCorD, described in our [paper](https://aclanthology.org/2021.acl-long.478/). Once you train the model with ExCorD, you can easily evaluate it in the same way with that of common CQA models.


## Requirements
```bash
$ conda create -n excord python=3.8
$ conda activate excord
$ conda install tqdm
$ conda install pytorch==1.5.0 torchvision==0.6.0 cudatoolkit=10.1 -c pytorch
$ pip install transformers==3.3.1
```
Note that Pytorch has to be installed depending on the version of CUDA.

### Datasets

[Download link](https://drive.google.com/drive/folders/1TJ-cWllUs8SFJlwJdUjUmJJW_kkr-l_u?usp=share_link)

We use the [QuAC (Choi et al., 2018)](https://quac.ai/) dataset for training and evaluating our models. Datasets you can download from the above link consist of `train.json`, `valid.json` and `dev.json`. Note that `dev.jon` is the official development set of QuAC. On the other hand, `train.json` includes the self-contained questions generated by human annotators in [CANARD (Elgohary et al., 2019)](https://sites.google.com/view/qanta/projects/canard) or our question rewriting (QR) model. You can search optimal hyperparameters by evaluating your model with `valid.json`.

## Train

The following example fine-tunes RoBERTa on the QuAC dataset by using ExCorD. 
A single 24GB GPU (RTX TITAN) is used for the example so we recommend you to use similar or better equipments.

```bash
INPUT_DIR=./datasets/
OUTPUT_DIR=./tmp/

python run_quac.py \
	--model_type roberta \
	--model_name_or_path roberta-base \
	--do_train \
	--data_dir ${INPUT_DIR} \
	--train_file train.json \
	--output_dir ${OUTPUT_DIR} \
	--per_gpu_train_batch_size 12 \
	--num_train_epochs 2 \
	--learning_rate 3e-5 \
	--weight_decay 0.01 \
	--threads 20 \
	--excord_cons_coeff 0.5 \
	--excord_softmax_temp 1 \
```

For efficiency, you can also add `--fp16` arguement after setting up `apex` from [here](https://github.com/NVIDIA/apex). Additionally, preprocessing step can be done faster by setting a larger number for `--threads`, which indicates the number of CPU cores assigned to the process.

## Evaluation

The following example evaluates our trained model with the development set of QuAC.

```bash
INPUT_DIR=./datasets/
MODEL_DIR=./model/
OUTPUT_DIR=./tmp/

python run_quac.py \
	--model_type roberta \
	--model_name_or_path ${MODEL_DIR} \
	--cache_prefix roberta-base \
	--data_dir ${INPUT_DIR} \
	--predict_file dev.json \
	--output_dir ${OUTPUT_DIR} \
	--do_eval \
	--per_gpu_eval_batch_size 100 \
	--threads 20 \
```

### Result
Evaluating models trained with predefined hyperparameters yields the following results:

```bash
Results: {'F1': 67.23447159600119}
```

### Best Model
[Download link](https://drive.google.com/drive/folders/1TJ-cWllUs8SFJlwJdUjUmJJW_kkr-l_u?usp=share_link)

You can also download our best model and its predictions on `dev.json` from the above link.

## Citation

```bibtex
@inproceedings{kim2021conversation,
    title={Learn to Resolve Conversational Dependency: A Consistency Training Framework for Conversational Question Answering},
    author={Kim, Gangwoo and Kim, Hyunjae and Park, Jungsoo and Kang, Jaewoo},
    booktitle={Association for Computational Linguistics (ACL)},
    year={2021},
}
```