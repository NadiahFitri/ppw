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

# **CBOW (Continuous Bag Of Words)**

# Tugas 4 : CBOW PTA Prodi Manajemen (Abstrak Bahasa Indonesia)

## 1. Install dan Import Library

```{code-cell}
!pip install --upgrade gensim
```

```{code-cell}
import pandas as pd
import re
from gensim.models import Word2Vec
import numpy as np
from IPython.display import display
```

## 2. Load Dataset

```{code-cell}
pta_manajemen_preprocessing = pd.read_csv("PPW_Tugas3_PTATrunojoyo(7Manajemen)_PreProcessing_1.csv")
display(pta_manajemen_preprocessing.head())
```

## 3. Pembersihan Kolom Hasil PreProcessing

```{code-cell}
def pembersihan_teks(text):
    text = str(text)
    text = re.sub(r"[\[\]]", " ", text)    # hapus [ ]
    text = re.sub(r"[\'\"]", " ", text)    # hapus ' "
    text = re.sub(r",", " ", text)         # hapus koma
    text = re.sub(r"\s+", " ", text).strip()  # rapikan spasi
    return text
```

```{code-cell}
pta_manajemen_preprocessing['abstrak_bindonesia_preprocessing_bersih'] = pta_manajemen_preprocessing['abstrak_bindonesia_preprocessing'].apply(pembersihan_teks)
```

```{code-cell}
display(pta_manajemen_preprocessing[["abstrak_bindonesia_preprocessing", "abstrak_bindonesia_preprocessing_bersih"]].head(10))
```

```{code-cell}
# kolom abstrak bahasa indonesia hasil preprocessing yang sudah dibersihkan ditambahkan dikolom terakhir
display(pta_manajemen_preprocessing.head())
```

## 4. Mengambil Corpus

```{code-cell}
# list abstrak bahasa indonesia hasil preprocessing yang sudah dibersihkan
corpus = [row.split() for row in pta_manajemen_preprocessing['abstrak_bindonesia_preprocessing_bersih']]
```

```{code-cell}
print(corpus)
```

## 5. Membangun Model Word2Vec CBOW

```{code-cell}
# CBOW = sg=0, Skip-Gram = sg=1
model = Word2Vec(sentences=corpus, vector_size=100, window=5, min_count=1, sg=0, workers=4)
```

```{code-cell}
model
```

```{code-cell}
print(model)
```

## 6. Word Embedding

```{code-cell}
word_embedding = model.wv
```

```{code-cell}
word_embedding
```

```{code-cell}
# Debugging: embedding kata tertentu
contoh_kata = "abstrak"
if contoh_kata in word_embedding:
    print(f"Embedding untuk kata '{contoh_kata}':")
    print(word_embedding[contoh_kata])
```

## 7. Dokumen Embedding

```{code-cell}
# representasi dokumen dengan rata-rata embedding kata di dalamnya
dokument_embedding = []
for tokens in corpus:
    valid_vectors = [word_embedding[w] for w in tokens if w in word_embedding]
    if valid_vectors:
        dokument_embedding.append(np.mean(valid_vectors, axis=0))
    else:
        dokument_embedding.append(np.zeros(model.vector_size))
```

```{code-cell}
# DataFrame dokumen embeddings
embedding_pta_manajemen_preprocessing = pd.DataFrame(dokument_embedding, columns=[f"f{i+1}" for i in range(model.vector_size)])
```

```{code-cell}
display(embedding_pta_manajemen_preprocessing.head(10))
```

## 8. Menggabungkan Metadata

```{code-cell}
pta_manajemen_cbow = pd.concat([pta_manajemen_preprocessing[['no', 'id_prodi', 'nama_prodi', 'penulis', 'judul', 'pembimbing_pertama', 'pembimbing_kedua', 'abstrak_bindonesia', 'abstrak_binggris', 'abstrak_bindonesia_preprocessing', 'abstrak_bindonesia_preprocessing_bersih']], embedding_pta_manajemen_preprocessing], axis=1)
```

```{code-cell}
display(pta_manajemen_cbow.head())
```

## 9. Menyimpan Hasil CBOW

```{code-cell}
pta_manajemen_cbow.to_csv("PPW_Tugas4_PTATrunojoyo(7Manajemen)_CBOW.csv", index=False)
```

## 10. Code Tugas 4 - CBOW PTA Prodi Manajemen (Abstrak Bahasa Indonesia)
- [PPW_Tugas4_CBOW(PTAAbstrak)](https://colab.research.google.com/drive/1gh6f8e463yKkPzHwLcGcwnnZzmgCnhSg?usp=sharing)


# Tugas 4 : CBOW Berita Online

## 1. Install dan Import Library

```{code-cell}
!pip install --upgrade gensim
```

```{code-cell}
import pandas as pd
import re
import numpy as np
import ast
from gensim.models import Word2Vec
from IPython.display import display
```

## 2. Load Dataset

```{code-cell}
berita_preprocessing = pd.read_csv("PPW_Tugas3_BeritaOnline_PreProcessing.csv")
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
    text = re.sub(r"[\'\"]", " ", text)    # hapus ' "
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
# kolom isi berita hasil preprocessing yang sudah dibersihkan ditambahkan ke kolom terakhir
display(berita_preprocessing.head())
```

```{code-cell}
# cek tipe data kolom isi berita hasil preprocessing yang sudah dibersihkan
type(berita_preprocessing['isi_berita_preprocessing_bersih'].iloc[0])
```

## 4. Mengambil Corpus

```{code-cell}
# list isi berita hasil preprocessing
corpus = berita_preprocessing['isi_berita_preprocessing'].tolist()
```

```{code-cell}
print(corpus)
```

## 5. Membangun Model Word2Vec CBOW

```{code-cell}
# word embedding
model = Word2Vec(
    sentences=corpus,
    vector_size=100,   # dimensi embedding
    window=5,          # ukuran konteks
    min_count=1,       # minimum frekuensi kata
    sg=0,              # 0 = CBOW
    workers=4
)
```

```{code-cell}
model
```

```{code-cell}
print(model)
```

## 6. Dokumen Embedding

```{code-cell}
# fungsi mendapatkan embedding dokumen (rata-rata vektor kata)
def dokument_vector(doc, model):
    valid_words = [word for word in doc if word in model.wv]
    if valid_words:
        return np.mean(model.wv[valid_words], axis=0)
    else:
        return np.zeros(model.vector_size)
```

```{code-cell}
# Transformasi semua dokumen menjadi embedding
berita_preprocessing['embedding_array'] = berita_preprocessing['isi_berita_preprocessing'].apply(lambda x: dokument_vector(x, model))
```

## 7. Membuat DataFrame Baru Dengan Metadata + Embedding

```{code-cell}
embedding_berita_preprocessing = berita_preprocessing[['id_berita','judul_berita','isi_berita','tanggal_berita',
                   'isi_berita_preprocessing','isi_berita_preprocessing_bersih',
                   'embedding_array','kategori_berita']]
```

```{code-cell}
display(embedding_berita_preprocessing.head())
```

## 8. Mengubah Embedding (Array) Menjadi Kolom Numerik (VSM Versi CBOW)

```{code-cell}
num_features = len(embedding_berita_preprocessing['embedding_array'].iloc[0])  # dimensi embedding
columns = [f'f{i+1}' for i in range(num_features)]
```

```{code-cell}
# Ekstrak array ke DataFrame
data_dict = {col: [] for col in columns}
for emb in embedding_berita_preprocessing['embedding_array']:
    for i, value in enumerate(emb):
        data_dict[f'f{i+1}'].append(value)
```

```{code-cell}
embedding_features = pd.DataFrame(data_dict)
```

```{code-cell}
display(embedding_features.head(10))
```

## 9. Menggabungkan Dengan Metadata

```{code-cell}
# kategori_berita dipindah ke akhir
berita_cbow = pd.concat([
    embedding_berita_preprocessing[['id_berita','judul_berita','isi_berita','tanggal_berita',
                  'isi_berita_preprocessing','isi_berita_preprocessing_bersih']].reset_index(drop=True),
    embedding_features.reset_index(drop=True),
    embedding_berita_preprocessing[['kategori_berita']].reset_index(drop=True)
], axis=1)
```

```{code-cell}
display(berita_cbow.head(10))
```

## 10. Menyimpan Hasil CBOW

```{code-cell}
berita_cbow.to_csv("PPW_Tugas4_BeritaOnline_CBOW.csv", index=False)
```

## 11. Code Tugas 4 - CBOW Berita Online
- [PPW_Tugas4_CBOW(BeritaOnline)](https://colab.research.google.com/drive/1_H2BafYEkR2y_V6m-qM8MGhMANshX28S?usp=sharing)