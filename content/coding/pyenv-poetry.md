+++
title = 'pyenv and Poetry: Setup Python environment without sudo'
date = 2026-05-06
tags = ['Python', 'pyenv', 'Poetry']
type = 'posts'
+++

`pyenv` and `Poetry` are two tools that allow you to manage your Python environment without sudo.
`pyenv` allows you to install multiple Python versions and switch between them.
`Poetry` allows you to manage your dependencies and create virtual environments.


## Install `pyenv`

Install `pyenv` via the official installer:

```bash
curl https://pyenv.run | bash
```

Add the following lines to your `~/.bashrc` (or `~/.zshrc` if you use zsh):

```bash
export PYENV_ROOT="$HOME/.pyenv"
[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
```

Then reload your shell:

```bash
source ~/.bashrc
```

### 1.3 Verify pyenv is working

```bash
pyenv --version
```

---

## 2. Install Python 3.11

### 2.1 Install Python 3.11

```bash
pyenv install 3.11
```

> **Note:** This may take a few minutes as it compiles Python from source.

### 2.2 Set Python 3.11 as your default

```bash
pyenv global 3.11
```

### 2.3 Verify Python version

```bash
python --version
```

---

## 3. Install Poetry

### 3.1 Install Poetry

```bash
curl -sSL https://install.python-poetry.org | python -
```

### 3.2 Add Poetry to your PATH

Add the following to your `~/.bashrc` (or `~/.zshrc`):

```bash
export PATH="$HOME/.local/bin:$PATH"
```

Then reload your shell:

```bash
source ~/.bashrc
```

### 3.3 Configure Poetry to create virtual environments inside the project

```bash
poetry config virtualenvs.in-project true
```

> This will make Poetry create a `.venv` folder inside your project directory instead of a global cache. Easier to manage and project-specific.

### 3.4 Verify Poetry is working

```bash
poetry --version
```

---

## Quick Sanity Check

Run all three to confirm everything is set up correctly:

```bash
pyenv --version
python --version
poetry --version
```