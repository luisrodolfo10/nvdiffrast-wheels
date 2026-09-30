# nvdiffrast CUDA wheels

Prebuilt Linux wheels for [NVIDIA nvdiffrast](https://github.com/NVlabs/nvdiffrast),
a library of GPU primitives for differentiable rendering with PyTorch. It provides
rasterization, interpolation, texture sampling, and antialiasing with gradients.
This repository distributes wheels built from upstream source; it is not an
official NVIDIA release.

## Why this wheel exists

Installing nvdiffrast from Git normally compiles its CUDA extension locally,
requiring a CUDA toolkit, C++ compiler, and a compatible PyTorch installation.
This wheel lets matching workstations and cluster jobs install the compiled
extension directly, avoiding repeated source builds and fixing the source revision
and GPU targets for reproducible deployments. Building and validation used isolated
uv environments, without Conda.

## Current release

[nvdiffrast-253ac4f-torch2.10.0-cu129-v1](https://github.com/luisrodolfo10/nvdiffrast-wheels/releases/tag/nvdiffrast-253ac4f-torch2.10.0-cu129-v1)

| Property | Value |
| --- | --- |
| Upstream version | nvdiffrast 0.4.0 |
| Unmodified source revision | `253ac4fcea7de5f396371124af597e6cc957bfae` |
| Platform | Linux x86_64 |
| Python | CPython 3.13 (built with 3.13.12) |
| PyTorch | `2.10.0+cu129` |
| CUDA build toolkit | 12.9 (`nvcc` 12.9.86) |
| C++ compiler | GCC 13.3.0 |
| Build host glibc | 2.39 |
| Extension ELF requirements | GLIBC 2.32, GLIBCXX 3.4.21 |
| Native GPU targets | `7.5 8.0 8.6 8.9 9.0` |
| Wheel filename | `nvdiffrast-0.4.0-cp313-cp313-linux_x86_64.whl` |

Targets include RTX 8000 (7.5) and RTX 3090 (8.6). All five targets were compiled
into the extension. Local runtime validation passed on an RTX 3060 Laptop GPU
(8.6), covering rasterization, interpolation and bilinear texture references,
finite nonzero gradients, and central-difference gradient comparisons.
Other targets still require runtime validation on their actual GPUs.

The GLIBC/GLIBCXX values above describe this extension only. They do not establish
the minimum requirements of the complete PyTorch stack. Cluster compatibility has
not been confirmed. Check the target system's OS, C++ runtime, and NVIDIA driver
against the matching PyTorch CUDA installation. This wheel does not support
Windows, macOS, ARM, or other Python versions.

## Install with uv

Install the matching PyTorch build, then the wheel:

```bash
uv venv --python 3.13
uv pip install --python .venv/bin/python --only-binary :all: \
  'torch==2.10.0+cu129' --index https://download.pytorch.org/whl/cu129
uv pip install --python .venv/bin/python --only-binary :all: \
  'https://github.com/luisrodolfo10/nvdiffrast-wheels/releases/download/nvdiffrast-253ac4f-torch2.10.0-cu129-v1/nvdiffrast-0.4.0-cp313-cp313-linux_x86_64.whl'
```

The wheel uses external PyTorch and CUDA shared libraries; those libraries are
not bundled in the wheel. Its upstream metadata declares NumPy but does not
constrain PyTorch, so select the exact PyTorch build explicitly. A local CUDA
compiler is not needed to install this wheel.

### Use in pyproject.toml

Keep `nvdiffrast` in your project's dependencies and replace its existing Git
entry under `[tool.uv.sources]` with:

```toml
[tool.uv.sources]
nvdiffrast = { url = "https://github.com/luisrodolfo10/nvdiffrast-wheels/releases/download/nvdiffrast-253ac4f-torch2.10.0-cu129-v1/nvdiffrast-0.4.0-cp313-cp313-linux_x86_64.whl" }
```

Use your project's PyTorch index configuration to select `torch==2.10.0+cu129`,
then regenerate the lockfile and verify installation in a fresh environment:

```bash
uv lock
UV_PROJECT_ENVIRONMENT=.venv-wheel-check uv sync --locked --no-build
```

## Build provenance and integrity

The wheel was built from the pinned source using:

```bash
export TORCH_CUDA_ARCH_LIST="7.5 8.0 8.6 8.9 9.0"
uv build --wheel --no-build-isolation
```

Each release provides the original wheel filename, its `.sha256` checksum,
`manifest.json` with build and validation details, `LICENSE.txt`, and
`NOTICES.txt`. After downloading the wheel and checksum to the same directory:

```bash
sha256sum --check nvdiffrast-0.4.0-cp313-cp313-linux_x86_64.whl.sha256
```

Release tags and assets are intended to remain immutable. Rebuilds should use a
new versioned release tag rather than replacing an existing asset. Updating this
README does not change the released wheel or its pinned upstream source.

## License

The wheel is distributed under the upstream NVIDIA Source Code License
(1-Way Commercial). Use is limited to noncommercial research or evaluation.
The complete license and retained notices accompany the release; read them before
redistributing or using the wheel. Samples and their separately licensed data
assets are not included.
