+++
title = 'Unzip all zip files in a folder'
date = 2024-08-27T15:03:29+03:00
draft = false
tags = ['bash', 'zip']
+++

Each zip file is extracted inside a separate folder.

```bash
for i in *.zip; do unzip "{$i}" -d "${i%.*}"; done
```
