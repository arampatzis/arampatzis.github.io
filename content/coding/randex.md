+++
title = 'RandEx: Randomized multiple choice exams'
date = 2025-06-28
tags = ['Python', 'LaTeX', 'CLI']
type = 'posts'
+++

`randex` is a library and a CLI tool that creates exams by randomizing multiple-choice 
questions selected from a user-defined pool of questions. The final exam is generated 
as a LaTeX document and compiled into a PDF. Multiple exams can be created at once.

To install it with `pipx` (see also [this article](https://arampatzis.github.io/coding/pipx/)),
run the following command:

```bash
pipx install randex
```

Now you can use the `randex` CLI tool to create exams.

First, download some example to demonstrate the usage of the CLI tool:

```bash
randex download-examples
```

### Create an exam with all the available questions

Then run:

```bash
randex validate -t examples/en/template-exam.yaml -o tmp --overwrite "examples/en/folder_*"
```

in order to create one exam with all the available questions in the `examples/en/folder_*` folders.

Use the flag `--show-answers` to show the answers in the exam.

Open the file `tmp/exam.pdf` to see the exam.

### Create a batch of exams

To create a batch of exams, you can use the `randex create-batch` command.

```bash
randex batch -b 5 -n 2 -n 2 -n 2 -t examples/en/template-exam.yaml -o tmp --overwrite "examples/en/folder_*"
```

This will create 5 exams with 2 questions each from the `examples/en/folder_*` folders.
The exams will be saved in the `tmp` folder.
Open the file `tmp/exams.pdf` to see all the exams.






