# Tools for RU-VLA
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

## Run training on combined Le-Robot dataset

```shell

```