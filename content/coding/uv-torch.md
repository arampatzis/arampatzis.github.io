+++
title = 'uv - How to install PyTorch'
date = 2025-06-28
tags = ['Python']
type = 'posts'
+++

## Installing PyTorch with CUDA 12.8

PyTorch's CUDA builds aren't published to PyPI, so we pull them from PyTorch's own 
package index.
Add `torch` with:

```bash
uv add --index pytorch=https://download.pytorch.org/whl/cu128 torch
```

The `--index pytorch=...` flag registers a named extra index (`pytorch`) pointing at 
the CUDA 12.8 wheels. `uv` records this in `pyproject.toml` so subsequent installs 
keep using the same source.

### Why `index-strategy = "unsafe-best-match"` is required

After adding the PyTorch index, the following block must be present in `pyproject.toml`:

```toml
[tool.uv]
index-strategy = "unsafe-best-match"
```

Without it, resolution fails with an error like:

```
× No solution found when resolving dependencies:
╰─▶ Because only numpy<=2.4.3 is available and your project depends on numpy>=2.4.4,
    we can conclude that your project's requirements are unsatisfiable.
    hint: `numpy` was found on https://download.pytorch.org/whl/cu128, but not at the
    requested version (numpy>=2.4.4). A compatible version may be available on a
    subsequent index (e.g., https://pypi.org/simple).
```

**What's happening:** By default, `uv` uses a "first-match" index strategy — once it 
finds a package on any configured index, it will only consider versions from *that* 
index, ignoring later ones. 
This is a security measure against 
[dependency confusion attacks](https://medium.com/@alex.birsan/dependency-confusion-4a5d60fec610), 
where a malicious package on a public index can shadow a private one.

In our case, the PyTorch index happens to mirror a few common packages (like `numpy`), 
but only at older versions pinned to what PyTorch was built against. 
When our project requires a newer `numpy`, `uv` refuses to look at PyPI for it.

Setting `index-strategy = "unsafe-best-match"` tells `uv` to consider *all* configured 
indexes and pick the best-matching version across them. 
It's labeled "unsafe" because you're explicitly opting out of the 
dependency-confusion protection — acceptable here since both the PyTorch 
index and PyPI are trusted public sources.

### Alternatives

If you'd rather not loosen the index strategy globally, you can instead pin packages 
to specific indexes with `[tool.uv.sources]`, forcing `torch` to come from the PyTorch 
index while everything else resolves against PyPI normally. 
See the 
[uv docs on PyTorch integration](https://docs.astral.sh/uv/guides/integration/pytorch/) 
for that setup.



## Managing PyTorch with uv

PyTorch wheels live on dedicated indexes (not PyPI) and differ per accelerator 
(CPU, CUDA 11.8/12.6/12.8/13.0, ROCm, Intel GPU). 
Below are the configuration patterns for the common cases. 
Full reference: <https://docs.astral.sh/uv/guides/integration/pytorch/>.

> Requires uv ≥ 0.5.3.

### Available indexes

| Backend   | URL                                            |
| --------- | ---------------------------------------------- |
| CPU       | `https://download.pytorch.org/whl/cpu`         |
| CUDA 11.8 | `https://download.pytorch.org/whl/cu118`       |
| CUDA 12.6 | `https://download.pytorch.org/whl/cu126`       |
| CUDA 12.8 | `https://download.pytorch.org/whl/cu128`       |
| CUDA 13.0 | `https://download.pytorch.org/whl/cu130`       |
| ROCm 6.4  | `https://download.pytorch.org/whl/rocm6.4`     |
| Intel GPU | `https://download.pytorch.org/whl/xpu`         |

Always declare indexes with `explicit = true` so they're only used for packages that 
opt in — everything else resolves from PyPI normally. 
This avoids needing `index-strategy = "unsafe-best-match"`.

### Option A — Single backend everywhere

Use when every target machine uses the same accelerator (e.g., all Linux + CUDA 12.8).

```toml
[project]
dependencies = ["torch>=2.9.1", "torchvision>=0.24.1"]

[tool.uv.sources]
torch       = [{ index = "pytorch-cu128" }]
torchvision = [{ index = "pytorch-cu128" }]

[[tool.uv.index]]
name = "pytorch-cu128"
url = "https://download.pytorch.org/whl/cu128"
explicit = true
```

Install: `uv sync`.

### Option B — Per-platform (OS markers)

Use when the OS determines the backend (e.g., CUDA on Linux, CPU on macOS/Windows). 
Note that PyTorch does not publish CUDA builds for macOS.

```toml
[project]
dependencies = ["torch>=2.9.1", "torchvision>=0.24.1"]

[tool.uv.sources]
torch = [
  { index = "pytorch-cpu",   marker = "sys_platform != 'linux'" },
  { index = "pytorch-cu128", marker = "sys_platform == 'linux'" },
]
torchvision = [
  { index = "pytorch-cpu",   marker = "sys_platform != 'linux'" },
  { index = "pytorch-cu128", marker = "sys_platform == 'linux'" },
]

[[tool.uv.index]]
name = "pytorch-cpu"
url = "https://download.pytorch.org/whl/cpu"
explicit = true

[[tool.uv.index]]
name = "pytorch-cu128"
url = "https://download.pytorch.org/whl/cu128"
explicit = true
```

Install: `uv sync`. uv picks the right index automatically per platform.

### Option C — User-selected extra (multi-CUDA support)

Use when different Linux machines have different CUDA versions. 
Each machine chooses at sync time.

```toml
[project]
dependencies = []

[project.optional-dependencies]
cpu   = ["torch>=2.9.1", "torchvision>=0.24.1"]
cu126 = ["torch>=2.9.1", "torchvision>=0.24.1"]
cu128 = ["torch>=2.9.1", "torchvision>=0.24.1"]

[tool.uv]
conflicts = [[
  { extra = "cpu" },
  { extra = "cu126" },
  { extra = "cu128" },
]]

[tool.uv.sources]
torch = [
  { index = "pytorch-cpu",   extra = "cpu" },
  { index = "pytorch-cu126", extra = "cu126" },
  { index = "pytorch-cu128", extra = "cu128" },
]
torchvision = [
  { index = "pytorch-cpu",   extra = "cpu" },
  { index = "pytorch-cu126", extra = "cu126" },
  { index = "pytorch-cu128", extra = "cu128" },
]

[[tool.uv.index]]
name = "pytorch-cpu"
url = "https://download.pytorch.org/whl/cpu"
explicit = true

[[tool.uv.index]]
name = "pytorch-cu126"
url = "https://download.pytorch.org/whl/cu126"
explicit = true

[[tool.uv.index]]
name = "pytorch-cu128"
url = "https://download.pytorch.org/whl/cu128"
explicit = true
```

Install per machine:

```bash
uv sync --extra cu128   # or --extra cu126, --extra cpu
```

The `[tool.uv] conflicts` block marks the extras as mutually exclusive — required 
for the lockfile to hold multiple resolutions.


### Publishing a library that depends on PyTorch

If you're shipping a package to PyPI rather than deploying an application, 
do **not** bake a PyTorch index into `pyproject.toml`. 
Declare `torch` as a plain dependency and let downstream users install the variant 
matching their hardware. 
This is the convention followed by most of the ML ecosystem 
(transformers, lightning, etc.).