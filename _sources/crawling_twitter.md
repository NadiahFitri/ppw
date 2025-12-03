---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

<!-- # Notebooks with MyST Markdown

Jupyter Book also lets you write text-based notebooks using MyST Markdown.
See [the Notebooks with MyST Markdown documentation](https://jupyterbook.org/file-types/myst-notebooks.html) for more detailed instructions.
This page shows off a notebook written in MyST Markdown.

## An example cell

With MyST Markdown, you can define code cells with a directive like so:

```{code-cell}
print(2 + 2)
```

When your book is built, the contents of any `{code-cell}` blocks will be
executed with your default Jupyter kernel, and their outputs will be displayed
in-line with the rest of your content.

```{seealso}
Jupyter Book uses [Jupytext](https://jupytext.readthedocs.io/en/latest/) to convert text-based files to notebooks, and can support [many other text-based notebook files](https://jupyterbook.org/file-types/jupytext.html).
```

## Create a notebook with MyST Markdown

MyST Markdown notebooks are defined by two things:

1. YAML metadata that is needed to understand if / how it should convert text files to notebooks (including information about the kernel needed).
   See the YAML at the top of this page for example.
2. The presence of `{code-cell}` directives, which will be executed with your book.

That's all that is needed to get started!

## Quickly add YAML metadata for MyST Notebooks

If you have a markdown file and you'd like to quickly add YAML metadata to it, so that Jupyter Book will treat it as a MyST Markdown Notebook, run the following command:

```
jupyter-book myst init path/to/markdownfile.md
``` -->

# **Crawling Twitter**

# Tugas 8 - Crawling (Twitter)

## 1. Install Library

```{code-cell}
!pip install pandas tqdm

# Install Node.js (required for Tweet-Harvest)
!sudo apt-get update
!sudo apt-get install -y ca-certificates curl gnupg

!sudo mkdir -p /etc/apt/keyrings
!curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg

!NODE_MAJOR=20 && echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list

!sudo apt-get update
!sudo apt-get install -y nodejs -y

# Instal dependency agar Chromium dapat berjalan
!sudo apt-get install -y libnss3 libatk1.0-0 libatk-bridge2.0-0 libcups2 libxkbcommon0 libxdamage1 \
libxcomposite1 libxrandr2 libgbm1 libpango-1.0-0 libcairo2 libasound2
```

```{code-cell}
# cek node version
!node -v
```

## 2. Import Library

```{code-cell}
from tqdm.notebook import tqdm
import pandas as pd
import os
from IPython.display import display
```

## 3. Inisialisasi

```{code-cell}
token_tweeter = "85c8d4695e95b5679a7a4148f9c37a74616fd7a8"  # bisa ganti
```

```{code-cell}
limit = 200 # batas ambil data
nama_file = "PPW_Tugas8_Crawling(Tweeter).csv"
kata_kunci = "politik"
hashtag = "#politik"

query = f'({kata_kunci} OR {hashtag}) lang:id since:2024-01-01 until:2025-12-02'
print(f"Proses crawling akan dimulai menggunakan kata kunci '{kata_kunci}' dan hashtag '{hashtag}'")
```

## 4. Crawling

```{code-cell}
print("Crawling Twitter sedang berjalan...")
!npx -y tweet-harvest@2.6.1 -o "{nama_file}" -s "{query}" --tab "LATEST" -l {limit} --token {token_tweeter}
```

## 5. Load ke Dataset

```{code-cell}
file_path = f"tweets-data/{nama_file}"
if not os.path.exists(file_path):
    raise FileNotFoundError(f"File tidak ditemukan: {file_path}")

# data_tweeter = pd.read_csv(file_path)
data_tweeter = pd.read_csv(file_path, delimiter=",")
print("Menampilkan data tweeter:")
display(data_tweeter)
```

## 6. Code Tugas 8 - Crawling (Twitter)
- [PPW_Tugas8_Crawling(Twitter)](https://colab.research.google.com/drive/1arCsrlOsU7ur-Gsjrjb_mGX4ca4lKVmc?usp=sharing)