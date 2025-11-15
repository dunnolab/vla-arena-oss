# LIBERO Russian Tasks Evaluation

This guide explains how to evaluate LIBERO models on Russian language task suites. The Russian tasks are translations and adaptations of the original LIBERO benchmarks.

## Available Russian Task Suites

- `ru_libero_spatial` - 10 tasks of `libero_saptial` (Russian)
- `ru_libero_object` - 10 tasks of `libero_object` (Russian)
- `ru_libero_goal` - 10 tasks `libero_goal` (Russian)
- `ru_libero_10` - 10 tasks of `libero_10` (Russian)
<!-- - `ru_libero_spatial` - 10 tasks focused on spatial relationships (Russian)
- `ru_libero_object` - 10 tasks focused on object manipulation (Russian)
- `ru_libero_goal` - 10 tasks focused on goal-oriented behaviors (Russian)
- `ru_libero_10` - 10 diverse tasks for general evaluation (Russian) -->

## Setup Instructions

If you're using LIBERO as a third-party dependency (e.g., in the OpenPI project), you'll need to copy the Russian task files into your `third_party/libero` directory. Before that please refer to the installation instruction of the original [openpi repository](https://github.com/Physical-Intelligence/openpi/tree/main/examples/libero)

<!-- ### Step 1: Copy Russian BDDL Files -->

The bddl files define the task specifications and init files contain the initial environment states in Russian. python files configure benchmark suite for compatibility of original libero with Russian tasks. Copy them from your appropriate directory to your third-party LIBERO installation:

```bash
cp -r libero-ru/bddl_files/ru_libero_* third_party/libero/libero/libero/bddl_files/

cp -r libero-ru/init_files/ru_libero_* third_party/libero/libero/libero/init_files/

cp libero-ru/benchmark/libero_suite_task_map.py third_party/libero/libero/libero/benchmark/libero_suite_task_map.py

cp libero-ru/benchmark/__init__.py third_party/libero/libero/libero/benchmark/__init__.py

cp libero-ru/examples/main.py examples/libero/main.py
```
then
```bash
'steps from the original openpi installation'
...
uv pip install -e third_party/libero
```
<!-- ```bash
# From your OpenPI (or similar) project root directory:
cp -r /path/to/libero-ru//libero/bddl_files/ru_libero_* third_party/libero/libero/libero/bddl_files/
cp -r /path/to/libero-ru/libero/libero/init_files/ru_libero_* third_party/libero/libero/libero/init_files/
``` -->

<!-- This will copy all four Russian task suite directories:
- `ru_libero_spatial/`
- `ru_libero_object/`
- `ru_libero_goal/`
- `ru_libero_10/` -->

<!-- ### Step 2: Copy Russian Init Files

The init files contain the initial environment states for each task. Copy them to your third-party LIBERO installation:

```bash
# From your OpenPI (or similar) project root directory:
cp -r /path/to/libero-ru/libero/libero/init_files/ru_libero_* third_party/libero/libero/libero/init_files/
```

This will copy the corresponding init files for all four Russian task suites.

### Step 3: Verify Installation

After copying, verify that the Russian task files are in place:

```bash
ls third_party/libero/libero/libero/bddl_files/ | grep ru_libero
ls third_party/libero/libero/libero/init_files/ | grep ru_libero
```

You should see all four Russian task suite directories listed in both locations. -->

## Running Evaluations on Russian Tasks
```bash
# ru_libero_spatial ru_libero_object ru_libero_goal ru_libero_10
python examples/libero/main.py \
    --args.num_trials_per_task 50 \
    --args.task-suite-name ru_libero_spatial \
    --args.port 8000
```

<!-- ### With Docker (recommended)

```bash
# Grant access to the X11 server:
sudo xhost +local:docker

# To run on Russian spatial tasks:
CLIENT_ARGS="--args.task-suite-name ru_libero_spatial" SERVER_ARGS="--env LIBERO" docker compose -f examples/libero/compose.yml up --build

# To run on Russian object tasks:
CLIENT_ARGS="--args.task-suite-name ru_libero_object" SERVER_ARGS="--env LIBERO" docker compose -f examples/libero/compose.yml up --build

# To run on Russian goal tasks:
CLIENT_ARGS="--args.task-suite-name ru_libero_goal" SERVER_ARGS="--env LIBERO" docker compose -f examples/libero/compose.yml up --build

# To run on Russian general tasks:
CLIENT_ARGS="--args.task-suite-name ru_libero_10" SERVER_ARGS="--env LIBERO" docker compose -f examples/libero/compose.yml up --build
```

If you encounter EGL errors, use the GLX renderer:

```bash
MUJOCO_GL=glx CLIENT_ARGS="--args.task-suite-name ru_libero_spatial" SERVER_ARGS="--env LIBERO" docker compose -f examples/libero/compose.yml up --build
```

### Without Docker

Terminal window 1:

```bash
# Activate virtual environment (if not already activated)
source examples/libero/.venv/bin/activate

# Run the simulation with Russian tasks:
python examples/libero/main.py --args.task-suite-name ru_libero_spatial

# Or with GLX renderer:
MUJOCO_GL=glx python examples/libero/main.py --args.task-suite-name ru_libero_spatial
```

Terminal window 2:

```bash
# Run the server
uv run scripts/serve_policy.py --env LIBERO
```

## Using Custom Checkpoints with Russian Tasks

You can combine custom checkpoints with Russian tasks:

```bash
# To load a custom checkpoint and evaluate on Russian tasks:
export SERVER_ARGS="--env LIBERO policy:checkpoint --policy.config pi05_libero --policy.dir ./my_custom_checkpoint"
export CLIENT_ARGS="--args.task-suite-name ru_libero_spatial"

docker compose -f examples/libero/compose.yml up --build
```

## Notes

- The Russian tasks maintain the same difficulty and structure as their English counterparts
- Task descriptions and language instructions are provided in Russian
- The physical environment setup and success conditions remain identical to the original tasks
- Models trained on English tasks may need additional training or fine-tuning to perform well on Russian language instructions -->

<!-- ## Task Suite Comparison

| Task Suite | Language | Number of Tasks | Focus |
|------------|----------|----------------|-------|
| libero_spatial | English | 10 | Spatial relationships |
| ru_libero_spatial | Russian | 10 | Spatial relationships |
| libero_object | English | 10 | Object manipulation |
| ru_libero_object | Russian | 10 | Object manipulation |s
| libero_goal | English | 10 | Goal-oriented behaviors |
| ru_libero_goal | Russian | 10 | Goal-oriented behaviors |
| libero_10 | English | 10 | General diverse tasks |
| ru_libero_10 | Russian | 10 | General diverse tasks |

## Troubleshooting

**Issue**: Task suite not found error
- **Solution**: Verify that you've copied both the `bddl_files` and `init_files` directories correctly

**Issue**: Missing task files
- **Solution**: Ensure you used `cp -r` (recursive copy) to include all subdirectories and files

**Issue**: Permission errors when copying files
- **Solution**: You may need to use `sudo` or check that you have write permissions to the target directory
 -->
