# Reusing ADM_DP

Vision, tactile and graph fusion for multi-arm diffusion policies.

**Current scope:** Research implementation; training requires per-arm Zarr datasets and the simulator stack.

This repository is a fork of [Enyi-Bean/ADM_DP](https://github.com/Enyi-Bean/ADM_DP). Original authorship, citations and upstream notices are retained. Changes here apply to this fork.

## Environment and first checks

Use Python 3.10 or newer for the pinned PyTorch 2.6 / torchvision 0.21 dependencies; the old Python 3.9 quick start was incompatible with these pins. Create a dedicated environment and install from the repository root:

```bash
python -m venv .venv
# Linux/macOS: source .venv/bin/activate
# Windows PowerShell: .venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
python scripts/check_repository.py
```

The requirements describe a research environment, not a complete reproducible lockfile. Simulator, CUDA, asset and optional policy dependencies still need to match your machine. Do not assume that installing the Python dependencies installs robot assets or recorded demonstrations.

## Code and data map

| Path | Purpose |
|---|---|
| `policy/Diffusion-Policy/diffusion_policy/model/vision/` | Tactile, point-cloud, graph and attention fusion encoders |
| `policy/Diffusion-Policy/diffusion_policy/config/robot_dp.yaml` | Policy, optimiser and training configuration |
| `policy/Diffusion-Policy/diffusion_policy/config/task/default_task.yaml` | Observation and action shapes |
| `policy/Diffusion-Policy/diffusion_policy/dataset/robot_image_dataset.py` | Zarr loading and observation conversion |
| `script/` | Demonstration generation and data conversion |
| `tests/` | Architecture and synthetic-data diagnostics |

Prepare one `data/zarr_data/<task>_Agent<id>_<episodes>.zarr` per arm. The supplied Zarr converter and active `postprocess()` path expect `head_camera` as `(N, 3, H, W)` uint8. Some legacy helpers and synthetic tests use channel-last arrays; check the active code path rather than copying those shapes. Match TCP keys, graph agent count, action dimensions and tactile shape across the dataset and YAML files. `episode_ends` contains cumulative episode boundaries.

Run `train.sh` from the repository root with all five arguments: task, episode count, agent ID, seed and GPU ID. It reads the Zarr path assembled from these arguments and writes configured training outputs. Start with one arm and a small local dataset before scheduling a full experiment. Evaluation needs a checkpoint for every participating arm.

## Verification and limits

`python tests/test_normalizer.py` exercises synthetic Zarr data when the training dependencies are installed. Several other files in `tests/` are executable experiments; they are not all isolated pytest unit tests. Review their paths and resource needs before running them. The lightweight structural check does not establish convergence, policy performance or simulator compatibility.

Keep train/validation episode boundaries, random seeds, data conversion settings, GPU details and checkpoint identities with each experiment. Record any change in observation layout before comparing results.

## Attribution and reuse permissions

No repository-wide licence file is included in this checkout. Confirm reuse and redistribution permissions with the relevant rights holders; this maintenance update does not add a licence or relicense upstream code, data or assets.

## Contributing

See [CONTRIBUTING.md](../CONTRIBUTING.md) for the local workflow and what to include with a proposed change.
