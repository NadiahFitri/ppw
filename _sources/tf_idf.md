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

# **TF-IDF (Term Frequency - Inverse Document Frequency)**

# Tugas 4 : TF-IDF PTA Prodi Manajemen (Abstrak Bahasa Indonesia)

## 1. Import Library

```{code-cell}
import pandas as pd
from sklearn.feature_extraction.text import TfidfVectorizer
from IPython.display import display
import re
```

## 2. Load Dataset

```{code-cell}
pta_manajemen_preprocessing = pd.read_csv("PPW_Tugas3_PTATrunojoyo(7Manajemen)_PreProcessing_1.csv")
display(pta_manajemen_preprocessing.head())
```

## 3. Pembersihan Kolom Hasil PreProcessing

```{code-cell}
# Bersihkan kolom hasil preprocessing
def pembersihan_teks(text):
    # pastikan tipe string
    text = str(text)
    # hapus tanda kurung siku [ ]
    text = re.sub(r"[\[\]]", " ", text)
    # hapus tanda kutip tunggal / ganda
    text = re.sub(r"[\'\"]", " ", text)
    # hapus koma
    text = re.sub(r",", " ", text)
    # hapus spasi berlebih
    text = re.sub(r"\s+", " ", text).strip()
    return text
```

```{code-cell}
pta_manajemen_preprocessing['abstrak_bindonesia_preprocessing_bersih'] = pta_manajemen_preprocessing['abstrak_bindonesia_preprocessing'].apply(pembersihan_teks)
```

```{code-cell}
display(pta_manajemen_preprocessing[["abstrak_bindonesia_preprocessing", "abstrak_bindonesia_preprocessing_bersih"]].head(10))
```

```{code-cell}
# kolom abstrak bahasa indonesia hasil preprocessing yang sudah dibersihkan ditambahkan di kolom paling kanan dataset
display(pta_manajemen_preprocessing.head())
```

## 4. Mengambil Corpus

```{code-cell}
# list abstrak bahasa indonesia hasil preprocessing yang sudah dibersihkan
corpus = pta_manajemen_preprocessing['abstrak_bindonesia_preprocessing_bersih'].tolist()
```

```{code-cell}
# menampilkan corpus
print(corpus)
```

## 5. TF-IDF Vectorizer

```{code-cell}
vectorizer = TfidfVectorizer()
```

```{code-cell}
# Fit & transform corpus menjadi matriks TF-IDF
X = vectorizer.fit_transform(corpus)
```

## 6. Mengambil Fitur (Kosakata Unik)

```{code-cell}
features = vectorizer.get_feature_names_out()
```

```{code-cell}
features
```

## 7. Mengubah Matriks ke Bentuk DataFrame

```{code-cell}
tfidf_pta_manajemen_preprocessing = pd.DataFrame(X.toarray(), columns=features)
```

```{code-cell}
display(tfidf_pta_manajemen_preprocessing.head())
```

## 8. Menggabungkan Metadata

```{code-cell}
pta_manajemen_tfidf = pd.concat([pta_manajemen_preprocessing[['no', 'id_prodi', 'nama_prodi', 'penulis', 'judul', 'pembimbing_pertama', 'pembimbing_kedua', 'abstrak_bindonesia', 'abstrak_binggris', 'abstrak_bindonesia_preprocessing', 'abstrak_bindonesia_preprocessing_bersih']], tfidf_pta_manajemen_preprocessing], axis=1)
```

```{code-cell}
display(pta_manajemen_tfidf.head())
```

## 9. Ukuran Matriks TF-IDF

```{code-cell}
print("Ukuran matriks TF-IDF:", X.shape)
```

## 10. Menyimpan Hasil TF-IDF

```{code-cell}
pta_manajemen_tfidf.to_csv("PPW_Tugas4_PTATrunojoyo(7Manajemen)_TFIDF.csv", index=False)
```

## 11. Code Tugas 4 - TF-IDF PTA Prodi Manajemen (Abstrak Bahasa Indonesia)
- [PPW_Tugas4_TF-IDF(PTAAbstrak)](https://colab.research.google.com/drive/1ZZiE4GNgoxPSNepAtwv0fd1p2AEVp_E3?usp=sharing)


# Tugas 4 : TF-IDF Berita Online

## 1. Import Library

```{code-cell}
import pandas as pd
from sklearn.feature_extraction.text import TfidfVectorizer
from IPython.display import display
import re
```

## 2. Load Dataset

```{code-cell}
berita_preprocessing = pd.read_csv("PPW_Tugas3_BeritaOnline_PreProcessing.csv")
display(berita_preprocessing.head())
```

## 3. Pembersihan Kolom Hasil PreProcessing

```{code-cell}
def pembersihan_teks(text):
    # pastikan tipe string
    text = str(text)
    # hapus tanda kurung siku [ ]
    text = re.sub(r"[\[\]]", " ", text)
    # hapus tanda kutip tunggal / ganda
    text = re.sub(r"[\'\"]", " ", text)
    # hapus koma
    text = re.sub(r",", " ", text)
    # hapus spasi berlebih
    text = re.sub(r"\s+", " ", text).strip()
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

## 4. Mengambil Corpus

```{code-cell}
# list isi berita hasil preprocessing yang sudah dibersihkan
corpus = berita_preprocessing['isi_berita_preprocessing_bersih'].tolist()
```

```{code-cell}
print(corpus)
```

## 5. TF-IDF Vectorizer

```{code-cell}
vectorizer = TfidfVectorizer()
```

```{code-cell}
# Fit & transform corpus menjadi matriks TF-IDF
X = vectorizer.fit_transform(corpus)
```

## 6. Mengambil Fitur (Kosakata Unik)

```{code-cell}
features = vectorizer.get_feature_names_out()
```

```{code-cell}
features
```

## 7. Mengubah Matriks ke DataFrame

```{code-cell}
tfidf_berita_preprocessing = pd.DataFrame(X.toarray(), columns=features)
```

```{code-cell}
display(tfidf_berita_preprocessing.head())
```

## 8. Menggabungkan Metadata

```{code-cell}
berita_tfidf = pd.concat(
    [
        berita_preprocessing[['id_berita', 'judul_berita', 'isi_berita', 'tanggal_berita', 'isi_berita_preprocessing', 'isi_berita_preprocessing_bersih']],
        tfidf_berita_preprocessing,
        berita_preprocessing[['kategori_berita']]
    ],
    axis=1
)
```

```{code-cell}
display(berita_tfidf.head())
```

## 9. Ukuran Matriks TF-IDF

```{code-cell}
print("Jumlah Dokumen   :", X.shape[0])
print("Jumlah Fitur (Kosakata) :", X.shape[1])
print("Total Kolom (Metadata + TF-IDF + Label):", berita_tfidf.shape[1])
```

## 10. Menyimpan Hasil TF-IDF

```{code-cell}
berita_tfidf.to_csv("PPW_Tugas4_BeritaOnline_TFIDF.csv", index=False)
```

## 11. Code Tugas 4 - TF-IDF Berita Online
- [PPW_Tugas4_TF-IDF(BeritaOnline)](https://colab.research.google.com/drive/15yfuKCPxa5KLKxWjVTiRl-kp685WrqU5?usp=sharing)