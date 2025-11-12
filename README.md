# Tools for RU-VLA

## Avalilable Datasets 
### Combined Le-Robot Datasets


### Libero-RU Dataset

## Setup Training On Le-Robot Datasets
### Clone this repository

```shell
git clone https://github.com/dunnolab/vla-arena-oss.git
cd vla-arena-oss
export VLA_ARENA_OSS_PWD=$(pwd)
```

### Clone Open-Pi from the official repository
```shell
git clone --recurse-submodules https://github.comPhysical-Intelligence/openpi.git
cd openpi
git reset --hard 95aadc6b16d170e4b13ab2e0ac64fbf2d1bb8e31
GIT_LFS_SKIP_SMUDGE=1 git submodule update --init --recursive
```

### Patch policy and config files
```shell
cp $VLA_ARENA_OSS_PWD/openpi-patches/policies/arc_policy.py src/openpi/policies/
cp $VLA_ARENA_OSS_PWD/openpi-patches/training/config.py src/openpi/training
```

### Install patched repository
```shell
uv venv .venv && GIT_LFS_SKIP_SMUDGE=1 uv pip install -e . --python .venv/bin/python

cp -r ./src/openpi/models_pytorch/transformers_replace/* .venv/lib/python3.11/site-packages/transformers/
```

## Using our models

Choose `<model-name>` depending on your purposes:
- `pi-0.5-lerobot-arc-eng` - pi-0.5 (pre-trained) checkpoint finetuned on English version of combined Le-Robot dataset
- `pi-0.5-lerobot-arc-ru` - pi-0.5 (pre-trained) checkpoint finetuned on Russian version of combined Le-Robot dataset
- `pi-0.5-scratch-arc-libero-eng` - pi-0.5 (from-scratch) checkpoint prtrained on English version of combined Le-Robot dataset and Finetuned on Libero Dataset from Physical-Intelligence (#TODO insert link)
- `pi-0.5-scratch-arc-libero-ru` - pi-0.5 (from-scratch) checkpoint prtrained on Russian version of combined Le-Robot dataset and Finetuned on Libero-RU Dataset from (#TODO insert link)
- `pi-0.5-scratch-libero-ru` - pi-0.5 (from-scratch) checkpoint on Libero-RU Dataset

HF repository contains both model parameters and global normalization statistics which were used to train our models.

### Load model from HF repo
```shell
# Make sure the hf CLI is installed
curl -LsSf https://hf.co/cli/install.sh | bash
```

```shell
# Download model from 
hf download dunnolab/<model-name> 
```

### Run finetuning

### Run evaluatio on Libero RU/ENG
