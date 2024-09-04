+++
title = 'Unzip all zip files in a folder'
date = 2024-08-27
tags = ['bash', 'zip']
type = 'posts'
+++

Each zip file is extracted inside a separate folder.

```bash
for i in *.zip; do unzip "{$i}" -d "${i%.*}"; done
```
