+++
title = 'pipx: Install Python CLI tools in isolated environments'
date = 2025-06-27
tags = ['Python', 'CLI', 'pipx']
type = 'posts'
+++

`pipx` is a tool to help you install and run Python applications in isolated environments. 
It allows you to run the latest version of a CLI tool without worrying about dependency conflicts.

Instead of running `pip install <package>`, it is better to use `pipx install <package>`.
This way, the package is installed in an isolated environment and you will be able to
use it without activating any virtual environment.

For installation details, see the [pipx documentation](https://pipx.pypa.io/stable/installation/).