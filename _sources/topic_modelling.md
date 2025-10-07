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

# Topic Modelling

# Tugas 5 : Topic Modelling Berita Online
Keterangan : Topic Modelling dilakukan menggunakan data hasil preprocessing berita (isi berita) tanpa tahap hapus stopword dan tanpa stemming.

## 1. Import Library

```{code-cell}
import pandas as pd
import numpy as np
import re
import ast
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.decomposition import LatentDirichletAllocation
from IPython.display import display
```


## 2. Load Dataset

```{code-cell}
berita_preprocessing = pd.read_csv("PPW_Tugas3_BeritaOnline_PreProcessing_2.csv")
```

```{code-cell}
# cek tipe data kolom isi berita hasil preprocessing
type(berita_preprocessing['isi_berita_preprocessing'].iloc[0])
```

```{code-cell}
# mengubah tipe data kolom isi berita hasil preprocessing
# awalnya string diubah jadi list
berita_preprocessing['isi_berita_preprocessing'] = berita_preprocessing['isi_berita_preprocessing'].apply(
    lambda x: ast.literal_eval(x) if isinstance(x, str) else x
)
```

```{code-cell}
# cek tipe data kolom isi berita hasil preprocessing
type(berita_preprocessing['isi_berita_preprocessing'].iloc[0])
```

```{code-cell}
# menampilkan data
display(berita_preprocessing.head())
```


## 3. Pembersihan Kolom Isi Berita Hasil PreProcessing

```{code-cell}
def pembersihan_teks(text):
    text = str(text)
    text = re.sub(r"[\[\]]", " ", text)    # hapus [ ]
    text = re.sub(r"[\'\"]", " ", text)    # hapus ' dan "
    text = re.sub(r",", " ", text)         # hapus koma
    text = re.sub(r"\s+", " ", text).strip()  # rapikan spasi
    return text
```

```{code-cell}
berita_preprocessing['isi_berita_preprocessing_bersih'] = berita_preprocessing['isi_berita_preprocessing'].apply(pembersihan_teks)
```

```{code-cell}
display(berita_preprocessing[["isi_berita_preprocessing", "isi_berita_preprocessing_bersih"]].head(10))
```

```{code-cell}
# kolom isi berita hasil preprocessing yang sudah dibersihkan ditambahkan ke kolom terakhir dataset
display(berita_preprocessing.head())
```

```{code-cell}
# cek tipe data kolom isi berita hasil preprocessing yang sudah dibersihkan
type(berita_preprocessing['isi_berita_preprocessing_bersih'].iloc[0])
```


## 4. Mengambil Corpus

```{code-cell}
corpus = berita_preprocessing['isi_berita_preprocessing_bersih'].tolist()
```

```{code-cell}
print(corpus)
```


## 5. Text Vectorizer Representasi BoW (Bag Of Word)

```{code-cell}
vectorizer = CountVectorizer()
X = vectorizer.fit_transform(corpus)
```

```{code-cell}
# Menampilkan jumlah dokumen dan fitur
print("Jumlah Dokumen :", X.shape[0])
print("Jumlah Fitur (Kosakata) :", X.shape[1])
```


## 6. Menghitung LDA (Latent Dirichlet Allocation)

```{code-cell}
jumlah_topik = 100
```

```{code-cell}
lda_model = LatentDirichletAllocation(
    n_components=jumlah_topik,
    max_iter=20,           # jumlah iterasi agar konvergen
    learning_method='batch',
    random_state=42
)
```

```{code-cell}
lda_model
```

```{code-cell}
lda_output = lda_model.fit_transform(X)
```

```{code-cell}
lda_output
```


## 7. Distribusi Topik Terhadap Dokumen

### 1. DataFrame Distribusi Topik Terhadap Dokumen

```{code-cell}
kolom_topik = [f'T{i+1}' for i in range(jumlah_topik)]
df_topik_dokumen = pd.DataFrame(lda_output, columns=kolom_topik)
```

```{code-cell}
display(df_topik_dokumen.head(10))
```

### 2. Menggabungkan Metadata

```{code-cell}
# kategori_berita dipindah ke kolom terakhir
df_hasil_topik = pd.concat([
    berita_preprocessing[['id_berita', 'judul_berita', 'isi_berita', 'tanggal_berita', 'isi_berita_preprocessing', 'isi_berita_preprocessing_bersih']],
    df_topik_dokumen,
    berita_preprocessing[['kategori_berita']]
], axis=1)
```

```{code-cell}
display(df_hasil_topik.head(10))
```

### 3. Menyimpan Data Hasil Distribusi Topik Terhadap Dokumen

```{code-cell}
df_hasil_topik.to_csv("PPW_Tugas5_BeritaOnline_TopicModeling.csv", index=False)
```


## 8. Distribusi Kata Terhadap Topik

### 1. Fungsi Menampilkan Topik

```{code-cell}
def tampilkan_topik(model, feature_names, n_kata_teratas=10):
    topik_list = []
    for idx, topik in enumerate(model.components_):
        kata_teratas = [feature_names[i] for i in topik.argsort()[:-n_kata_teratas - 1:-1]]
        bobot_kata = topik[topik.argsort()[:-n_kata_teratas - 1:-1]]
        topik_list.append(pd.DataFrame({'Kata': kata_teratas, 'Bobot': bobot_kata}))
        print(f"\n🔹 Topik {idx+1}:")
        print(", ".join(kata_teratas))
    return topik_list
```

### 2. Menampilkan 10 Kata Dominan Setiap Topik

```{code-cell}
fitur_kata = vectorizer.get_feature_names_out()
topik_dominan = tampilkan_topik(lda_model, fitur_kata, n_kata_teratas=10)
```

### 3. Menyimpan Daftar Kata Dominan Setiap Topik

```{code-cell}
daftar_topik = []
for i, df in enumerate(topik_dominan, start=1):
    df_temp = df.copy()
    df_temp['Topik'] = f'T{i}'
    daftar_topik.append(df_temp)
```

```{code-cell}
df_kata_topik = pd.concat(daftar_topik)
```

```{code-cell}
display(df_kata_topik)
```

```{code-cell}
df_kata_topik.to_csv("PPW_Tugas5_BeritaOnline_TopicModelling_KataTopik.csv", index=False)
```


## 9. Code Tugas 5 - Topic Modelling Berita Online
- [PPW_Tugas5_BeritaOnline_TopicModelling](https://colab.research.google.com/drive/1wiEqnHtZQDGBcRbxt_7JEqvC3tHr-J4B?usp=sharing)