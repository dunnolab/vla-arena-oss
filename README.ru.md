# Инструменты для RU-VLA

[![en](https://img.shields.io/badge/lang-en-red.svg)](https://github.com/dunnolab/vla-arena-oss/blob/main/README.md)
[![ru](https://img.shields.io/badge/lang-ru-green.svg)](https://github.com/dunnolab/vla-arena-oss/blob/main/README.ru.md)

## Доступные Датасеты 

### Объединённые датасеты Le-Robot

Мы предоставляем консолидированный датасет для робота Le-Robot, объединяющий **598 открытых датасетов** сообщества в единый корпус. Датасет включает в себя **22,709 эпизодов** и около **9.4 млн кадров**, охватывающих **563 различных задачи**. Доступны две версии: с аннотациями задач на [английском](https://huggingface.co/datasets/dunnolab/so-combined-eng) и с аннотациями, переведёнными на [русский язык](https://huggingface.co/datasets/dunnolab/so-combined-ru).

### Датасет Libero-RU

Также, доступна переведенная на руский язык [версия](https://huggingface.co/datasets/dunnolab/libero-ru) датасета [Libero](https://huggingface.co/datasets/physical-intelligence/libero) от Physical Intelligence.


## Настройка обучения на датасетах Le-Robot
### Склонировать данный репозиторий

```shell
git clone https://github.com/dunnolab/vla-arena-oss.git
cd vla-arena-oss
export VLA_ARENA_OSS_PWD=$(pwd)
cd ../
```

### Склонировать официальный репозиторий Open-Pi
```shell
git clone --recurse-submodules https://github.com/Physical-Intelligence/openpi.git
cd openpi
git reset --hard 95aadc6b16d170e4b13ab2e0ac64fbf2d1bb8e31
GIT_LFS_SKIP_SMUDGE=1 git submodule update --init --recursive
```

### Применить патчи на файлы политик и конфигов
```shell
cp $VLA_ARENA_OSS_PWD/openpi-patches/policies/vla_arena_policy.py src/openpi/policies/
cp $VLA_ARENA_OSS_PWD/openpi-patches/training/config.py src/openpi/training
```

### Установить скорректированный репозиторий
```shell
uv venv .venv && GIT_LFS_SKIP_SMUDGE=1 uv pip install -e . --python .venv/bin/python

uv sync

cp -r ./src/openpi/models_pytorch/transformers_replace/* .venv/lib/python3.11/site-packages/transformers/
```

## Использование моделей

Выберите `<model-name>` в зависимости задачи:
- `pi05-libero-ru-en` - чекпоинт pi05 от Physical-Intelligence до-обученный  на датасетах [Libero-EN](https://huggingface.co/datasets/physical-intelligence/libero) и [Libero-RU](https://huggingface.co/datasets/dunnolab/libero-ru).
- `pi05-pnp-ru` - чекпоинт pi05 от Physical-Intelligence дообученный на русскоязычном датасете pick-and-place для робота SO101.

HF репозиторий содержит как веса моделей, так и статистики для глобальной нормализации, использованные при обучении.

### Загрузка модели из HF репозитория
```shell
# Убедиться, что hf CLI установлен
curl -LsSf https://hf.co/cli/install.sh | bash
```

```shell
# Загрузка модели из
huggingface-cli download dunnolab/<model-name> --local-dir <your-local-dir> --local-dir-use-symlinks False
# Директория будет содержать веса (папка `params/` либо файл `model.safetensors`)
# и папку `assets/` с нормализационными статистиками, использованными 
# при обучении и инференсе
```

### Запуск дообучения
Перед запуском обучения важно скопировать нормализационные статистики 
(предполагая, что команды запускаются из рута репозитория openpi)

```sh
cp <your-local-dir>/assets/norm_stats.json assets/<config_name>/<repo_id>/
# correct path example: assets/pi05_libero_ru/dunnolab/libero_ru/norm_stats.json
```
Мы предоставляем следущие конфиги для обучения:
- `pi05_so_combined_eng` - обучение pi05 на датасете `dunnolab/so-combined-eng`
- `pi05_so_combined_ru` - обучение pi05 на датасете `dunnolab/so-combined-ru`
- `pi05_libero_ru` - обучение pi05 на датасете `dunnolab/libero_ru`
- `pi0_libero_ru` - обучение pi0 на датасете `dunnolab/libero_ru`
- `pi0_so_combined_ru` - обучение pi0 на датасете `dunnolab/so-combined-ru`
- `pi0_so_combined_eng` - обучение pi0 на датасете `dunnolab/so-combined-eng`

```sh
uv run torchrun \
  --standalone \
  --nnodes=1 \
  --nproc_per_node=8 \
  scripts/train_pytorch.py pi05_libero_ru \
  --project-name openpi-ru \
  --exp-name pi05-libero-ru-experiment \
  --num-train-steps 1000001 \
  --save-interval 5000 \
  --pytorch-weight-path /path/to/your/folder/with/pytorch/model

# Если вы хотите запустить обучение на JAX для выбранного чекпоинта
# Необходимо поменять путь в аргументе `weight_loader` в `TrainConfig`
export XLA_PYTHON_CLIENT_MEM_FRACTION=0.9
uv run scripts/train.py pi0_so_combined_eng \
  --exp-name=my_experiment \
  --overwrite
```

### Запуск валидации на LIBERO RU/ENG
Доступны следующие бенчмарки для LIBERO-RU:
- `ru_libero_spatial` - 10 задач из `libero_saptial` (Русский язык)
- `ru_libero_object` - 10 задач из `libero_object` (Русский язык)
- `ru_libero_goal` - 10 задач из  `libero_goal` (Русский язык)
- `ru_libero_10` - 10 задач из  `libero_10` (Русский язык)

Если вы используете LIBERO как стороннюю зависимость (например, в проекте OpenPI), вам необходимо скопировать файлы русскоязычных задач в директорию `third_party/libero`. Перед этим, пожалуйста, ознакомьтесь с инструкцией по установке в оригинальном [репозитории openpi](https://github.com/Physical-Intelligence/openpi/tree/main/examples/libero).

Файлы `bddl` содержат спецификации задач, а файлы `init` включают начальные состояния среды на русском языке. Python-файлы настраивают набор бенчмарков для обеспечения совместимости оригинального репозитория LIBERO с русскоязычными задачами. Скопируйте их из соответствующей директории при установке LIBERO как сторонней зависимости.

```shell
cp -r $VLA_ARENA_OSS_PWD/libero-ru/bddl_files/ru_libero_* third_party/libero/libero/libero/bddl_files/

cp -r $VLA_ARENA_OSS_PWD/libero-ru/init_files/ru_libero_* third_party/libero/libero/libero/init_files/

cp $VLA_ARENA_OSS_PWD/libero-ru/benchmark/libero_suite_task_map.py third_party/libero/libero/libero/benchmark/libero_suite_task_map.py

cp $VLA_ARENA_OSS_PWD/libero-ru/benchmark/__init__.py third_party/libero/libero/libero/benchmark/__init__.py

cp $VLA_ARENA_OSS_PWD/libero-ru/examples/main.py examples/libero/main.py
```

Далее необходимо следовать процессу [оригинальной установки](https://github.com/Physical-Intelligence/openpi/tree/main/examples/libero#without-docker-not-recommended) LIBERO в репозитории OpenPI:
```shell
...
# Шаши для установки LIBERO в репозитории OpenPI
...

uv pip install -e packages/openpi-client
uv pip install -e third_party/libero
export PYTHONPATH=$PYTHONPATH:$PWD/third_party/libero
```

Далее, в качестве примера, можно использовать следующую команду для запуска валидации на LIBERO-RU/EN

```shells
# ru_libero_spatial ru_libero_object ru_libero_goal ru_libero_10
python examples/libero/main.py \
    --args.num_trials_per_task 50 \
    --args.task-suite-name ru_libero_spatial \
    --args.port 8000
```
