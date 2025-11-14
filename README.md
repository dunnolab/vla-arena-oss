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
# 
```

### Run finetuning

### Run evaluation on LIBERO RU/ENG
We provide the following LIBERO-RU tasks:
- `ru_libero_spatial` - 10 tasks of `libero_saptial` (Russian)
- `ru_libero_object` - 10 tasks of `libero_object` (Russian)
- `ru_libero_goal` - 10 tasks `libero_goal` (Russian)
- `ru_libero_10` - 10 tasks of `libero_10` (Russian)

If you're using LIBERO as a third-party dependency (e.g., in the OpenPI project), you'll need to copy the Russian task files into your `third_party/libero` directory. Before that please refer to the installation instruction of the original [openpi repository](https://github.com/Physical-Intelligence/openpi/tree/main/examples/libero)

The bddl files define the task specifications and init files contain the initial environment states in Russian. python files configure benchmark suite for compatibility of original libero repo with Russian tasks. Copy them from your appropriate directory to your third-party LIBERO installation:

```bash
cp -r $VLA_ARENA_OSS_PWD/libero-ru/bddl_files/ru_libero_* third_party/libero/libero/libero/bddl_files/

cp -r $VLA_ARENA_OSS_PWD/libero-ru/init_files/ru_libero_* third_party/libero/libero/libero/init_files/

cp $VLA_ARENA_OSS_PWD/libero-ru/benchmark/libero_suite_task_map.py third_party/libero/libero/libero/benchmark/libero_suite_task_map.py

cp $VLA_ARENA_OSS_PWD/libero-ru/benchmark/__init__.py third_party/libero/libero/libero/benchmark/__init__.py

cp $VLA_ARENA_OSS_PWD/libero-ru/examples/main.py examples/libero/main.py
```

Then you can follow the original setup of openpi LIBERO installation:
```bash
...
'steps from the original openpi libero installation'
...

uv pip install -e third_party/libero
```

Then, as an example, you can use such a script for LIBERO-RU/EN evaluation
```bash
# ru_libero_spatial ru_libero_object ru_libero_goal ru_libero_10
python examples/libero/main.py \
    --args.num_trials_per_task 50 \
    --args.task-suite-name ru_libero_spatial \
    --args.port 8000
```
