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

# **UTS Email**

# Analisa Clustering Dengan Ekstraksi Fitur TF-IDF Dann Model K-Means Clustering

## Load Data

```{code-cell}
# Import library
import pandas as pd
from IPython.display import display
```

```{code-cell}
# Load dataset
email = pd.read_csv("spam.csv", encoding='latin-1')
# Tampilkan data
display(email)
```

```{code-cell}
# Informasi dataset
print("Info Data Spam:")
print(email.info())
```

## Drop Kolom Banyak NaN

```{code-cell}
# drop kolom yang banyak NaN
email = email.drop(columns=['Unnamed: 2', 'Unnamed: 3', 'Unnamed: 4'])
```

```{code-cell}
# Tampilkan hasil setelah drop
display(email)
```

```{code-cell}
# Cek kembali struktur data
print("Info Data Email setelah drop kolom:")
print(email.info())
```

## PreProcessing

### 1. Punctuation Removal

```{code-cell}
!pip install requests
```

```{code-cell}
!pip install beautifulsoup4
```

```{code-cell}
import pandas as pd
import re
import string
from bs4 import BeautifulSoup
```

```{code-cell}
def pembersihan_teks(text):
    if pd.isnull(text):  # cek kalau ada NaN
        return ""
    text = text.lower()  # ubah ke huruf kecil
    text = re.sub(r'\d+', '', text)  # hapus angka
    text = text.translate(str.maketrans('', '', string.punctuation))  # hapus tanda baca
    text = re.sub(r'\W+', ' ', text)  # hapus karakter non-alfabet (ganti dengan spasi)
    text = BeautifulSoup(text, "html.parser").get_text()  # hapus tag HTML
    text = text.strip()  # hapus spasi berlebih di awal/akhir
    return text
```

```{code-cell}
email["Text_bersih"] = email["Text"].apply(pembersihan_teks)
```

```{code-cell}
# Perbandingan kolom Text sebelum dan sesudah punctuation removal
display(email[["Text", "Text_bersih"]].head(10))
```

```{code-cell}
# kolom Text setelah punctuation removal di tambahkan ke kolom paling kanan dari dataset
display(email.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data Text hasil punctuation removal, bisa aktifkan :
# email.to_csv("PPW_UTS_Email_PunctuationRemoval.csv", index=False)
```

### 2. Hapus Emoji

```{code-cell}
!pip install emoji
```

```{code-cell}
import emoji
```

```{code-cell}
def hapus_emoji(text):
    if pd.isnull(text):
        return ""
    return emoji.demojize(text, language="en")
```

```{code-cell}
email["Text_noemoji"] = email["Text_bersih"].apply(hapus_emoji)
```

```{code-cell}
# Perbandingan kolom Text sebelum dan sesudah hapus emoji
display(email[["Text_bersih", "Text_noemoji"]].head(10))
```

```{code-cell}
# kolom Text setelah hapus emoji di tambahkan ke kolom paling kanan dari dataset
display(email.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data Text hasil hapus emoji, bisa aktifkan :
# email.to_csv("PPW_UTS_Email_HapusEmoji.csv", index=False)
```

### 3. Tokenisasi

```{code-cell}
from nltk.tokenize import word_tokenize
import nltk
```

```{code-cell}
nltk.download('punkt_tab')
```

```{code-cell}
def tokenisasi(text):
    if pd.isnull(text):
        return []
    return word_tokenize(text)
```

```{code-cell}
email["Text_tokenisasi"] = email["Text_noemoji"].apply(tokenisasi)
```

```{code-cell}
# Perbandingan kolom Text sebelum dan sesudah tokenisasi
display(email[["Text_noemoji", "Text_tokenisasi"]].head(10))
```

```{code-cell}
# kolom isi berita setelah tokenisasi di tambahkan ke kolom paling kanan dari dataset
display(email.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data Text hasil tokenisasi, bisa aktifkan :
# email.to_csv("PPW_UTS_Email_Tokenisasi.csv", index=False)
```

### 4. Hapus Stopword

```{code-cell}
from nltk.corpus import stopwords
```

```{code-cell}
nltk.download('stopwords')
```

```{code-cell}
# Mengambil stopword bahasa Inggris
stop_words = set(stopwords.words('english'))
```

```{code-cell}
def hapus_stopword(tokens):
    if not isinstance(tokens, list):
        return []
    return [word for word in tokens if word.lower() not in stop_words]
```

```{code-cell}
email["Text_nostopword"] = email["Text_tokenisasi"].apply(hapus_stopword)
```

```{code-cell}
# Perbandingan kolom text sebelum dan sesudah hapus stopword
display(email[["Text_tokenisasi", "Text_nostopword"]].head(10))
```

```{code-cell}
# kolom text setelah hapus stopword di tambahkan ke kolom paling kanan dari dataset
display(email.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data text hasil hapus stopword, bisa aktifkan :
# email.to_csv("PPW_UTS_Email_HapusStopword.csv", index=False)
```

```{code-cell}
# OPSIONAL
# cek tipe data : pastikan kolom Text_nostopword itu list
print(email["Text_nostopword"].apply(type).head())
```

### 5. Cek Ejaan (Spell Checker)

```{code-cell}
# Install library pyspellchecker
!pip install pyspellchecker
```

```{code-cell}
# Import library spellchecker dari pyspellchecker
from spellchecker import SpellChecker
```

```{code-cell}
# Inisialisasi spell checker (bahasa Inggris)
spell = SpellChecker()
```

```{code-cell}
# Fungsi untuk melakukan koreksi ejaan pada setiap token
def ejaan_benar(tokens):
    if not isinstance(tokens, list):
        return []
    return [spell.correction(word) for word in tokens]
```

```{code-cell}
email["Text_cekejaan"] = email["Text_nostopword"].apply(ejaan_benar)
```

```{code-cell}
# Perbandingan kolom Text sebelum dan sesudah di cek ejaan
display(email[["Text_nostopword", "Text_cekejaan"]].head(10))
```

```{code-cell}
# kolom Text setelah dicek ejaan di tambahkan ke kolom paling kanan dari dataset
display(email.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data Text hasil cek ejaan, bisa aktifkan :
# email.to_csv("PPW_UTS_Email_PemeriksaanEjaan.csv", index=False)
```

```{code-cell}
# OPSIONAL
# cek tipe data : pastikan kolom Text_cekejaan itu list
# print(email["Text_cekejaan"].apply(type).head())
```

### 6. Stemming

```{code-cell}
!pip install Sastrawi
```

```{code-cell}
import pandas as pd
import ast
from Sastrawi.Stemmer.StemmerFactory import StemmerFactory
# supaya tau progress stemming pakai bar
from tqdm import tqdm
```

```{code-cell}
# Menyiapkan stemmer
factory = StemmerFactory()
stemmer = factory.create_stemmer()
```

```{code-cell}
def stemming(tokens):
    if not isinstance(tokens, list):
        return []
    return [stemmer.stem(str(word)) for word in tokens if word is not None]
```

```{code-cell}
tqdm.pandas()

# karena stemming ini tahap akhir preprocessing, maka nama kolom jadi : Text_preprocessing
# kolom Text_preprocessing akan disimpan ke file dataset
# kolom tahap preprocessing lain (sebelum stemming) akan di drop
email["Text_preprocessing"] = email["Text_cekejaan"].progress_apply(stemming)
```

```{code-cell}
# Perbandingan kolom Text sebelum dan sesudah stemming
display(email[["Text_cekejaan", "Text_preprocessing"]].head(10))
```

```{code-cell}
# kolom isi berita setelah stemming di tambahkan ke kolom paling kanan dari dataset
display(email.head(10))
```

```{code-cell}
# Menyimpan data hasil setiap tahap preprocessing
email.to_csv("PPW_UTS_Email_TahapPreProcessing.csv", index=False)
```

```{code-cell}
# OPSIONAL
# cek tipe data : pastikan kolom Text_preprocessing (hasil stemming) itu list
# print(email["Text_preprocessing"].apply(type).head())
```

### Hapus Kolom Hasil Setiap Tahap Preprocessing

```{code-cell}
import pandas as pd
```

```{code-cell}
data_email = pd.read_csv("PPW_UTS_Email_TahapPreProcessing.csv")
```

```{code-cell}
display(data_email.head(10))
```

```{code-cell}
# hapus kolom tahapan preprocessing (sebelum stemming)
# hanya menyisakan kolom hasil tahap preprocessing akhir (stemming)
hapus_kolom = [
    'Text_bersih',
    'Text_noemoji',
    'Text_tokenisasi',
    'Text_nostopword',
    'Text_cekejaan'
]
```

```{code-cell}
preprocessing_data_email = data_email.drop(columns=hapus_kolom)
```

```{code-cell}
# menampilkan data kolom
print(preprocessing_data_email.dtypes)
```

```{code-cell}
display(preprocessing_data_email.head(10))
```

```{code-cell}
preprocessing_data_email.to_csv("PPW_UTS_Email_PreProcessing.csv", index=False)
```

## TF-IDF

### Import Library

```{code-cell}
import pandas as pd
from sklearn.feature_extraction.text import TfidfVectorizer
from IPython.display import display
import re
```
### Load Dataset

```{code-cell}
email_preprocessing = pd.read_csv("PPW_UTS_Email_PreProcessing.csv")
display(email_preprocessing.head())
```

### Pembersihan Kolom Hasil PreProcessing

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
email_preprocessing['Text_preprocessing_bersih'] = email_preprocessing['Text_preprocessing'].apply(pembersihan_teks)
```

```{code-cell}
display(email_preprocessing[["Text_preprocessing", "Text_preprocessing_bersih"]].head(10))
```

```{code-cell}
# kolom Text hasil preprocessing yang sudah dibersihkan ditambahkan ke kolom terakhir
display(email_preprocessing.head())
```

### Mengambil Corpus

```{code-cell}
# list Text hasil preprocessing yang sudah dibersihkan
corpus = email_preprocessing['Text_preprocessing_bersih'].tolist()
```

```{code-cell}
print(corpus)
```

### TF-IDF Vectorizer

```{code-cell}
vectorizer = TfidfVectorizer()
```

```{code-cell}
# Fit & transform corpus menjadi matriks TF-IDF
X = vectorizer.fit_transform(corpus)
```

### Mengambil Fitur (Kosakata Unik)

```{code-cell}
features = vectorizer.get_feature_names_out()
```

```{code-cell}
features
```

### Mengubah Matriks ke DataFrame

```{code-cell}
tfidf_email_preprocessing = pd.DataFrame(X.toarray(), columns=features)
```

```{code-cell}
display(tfidf_email_preprocessing.head())
```

### Menggabungkan Metadata

```{code-cell}
email_tfidf = pd.concat(
    [
        email_preprocessing[['id', 'Text', 'Text_preprocessing', 'Text_preprocessing_bersih']],
        tfidf_email_preprocessing
    ],
    axis=1
)
```

```{code-cell}
display(email_tfidf.head())
```

### Ukuran Matriks TF-IDF

```{code-cell}
print("Jumlah Dokumen   :", X.shape[0])
print("Jumlah Fitur (Kosakata) :", X.shape[1])
print("Total Kolom (Metadata + TF-IDF + Label):", email_tfidf.shape[1])
```

```{code-cell}
email_tfidf.to_csv("PPW_UTS_Email_TFIDF.csv", index=False)
```

## Clustering

### Import Library

```{code-cell}
import numpy as np
import pandas as pd
from IPython.display import display
import matplotlib.pyplot as plt

# scikit-learn tools
from sklearn.cluster import KMeans
from sklearn.metrics import silhouette_score
from sklearn.decomposition import TruncatedSVD
```

### Inisialisasi

```{code-cell}
# parameter
jumlah_klaster = range(2, 11)   # kita mulai dari 2 sampai 10 (silhouette tidak terdefinisi untuk k=1)
RandomState = 42        # supaya hasil reproducible
jumlah_dokumen = 5      # jumlah contoh dokumen per klaster yang ditampilkan
```

```{code-cell}
print("Tipe X:", type(X))
print("Shape X:", X.shape)
print("Columns in email_tfidf:", email_tfidf.columns[:6].tolist())
```

### Elbow (inertia) & Silhouette Untuk Memilih Jumlah Klaster Optimal

```{code-cell}
inertias = []
silhouettes = []

for k in jumlah_klaster:
    km = KMeans(n_clusters=k, random_state=RandomState, n_init=10)
    km.fit(X)
    inertias.append(km.inertia_)
    # Silhouette: memerlukan k >= 2
    labels = km.labels_
    nilai_silhouette_score = silhouette_score(X, labels)   # bekerja dengan sparse matrix
    silhouettes.append(nilai_silhouette_score)
    print(f"k={k}  inertia={km.inertia_:.2f}  silhouette={nilai_silhouette_score:.4f}")
```

```{code-cell}
# Tampilkan grafik Elbow (Inertia) dan Silhouette
fig, ax = plt.subplots(1, 2, figsize=(14, 4))

ax[0].plot(list(jumlah_klaster), inertias, marker='o')
ax[0].set_title("Elbow Method (Inertia) untuk memilih k")
ax[0].set_xlabel("k (jumlah cluster)")
ax[0].set_ylabel("Inertia")   # jumlah kuadrat jarak tiap titik data ke pusat klaster terdekat

ax[1].plot(list(jumlah_klaster), silhouettes, marker='o')
ax[1].set_title("Silhouette Score untuk setiap k")
ax[1].set_xlabel("k (jumlah cluster)")
ax[1].set_ylabel("Silhouette score")

plt.tight_layout()
plt.show()
```

```{code-cell}
# k terbaik berdasarkan Silhouette (nilai tertinggi)
best_k_idx = int(np.argmax(silhouettes))
best_k = list(jumlah_klaster)[best_k_idx]
print(f"k terbaik menurut silhouette_score: {best_k} (silhouette = {silhouettes[best_k_idx]:.4f})")
```

### K-Means Dengan Klaster Optimal

```{code-cell}
klaster_optimal = best_k
kmeans_model = KMeans(n_clusters=klaster_optimal, random_state=RandomState, n_init=10)
kmeans_model.fit(X)
klaster = kmeans_model.labels_

# Simpan label ke DataFrame (email_tfidf)
email_tfidf['cluster'] = klaster
```

```{code-cell}
display(email_tfidf)
```

### Jumlah Dokumen Setiap Klaster

```{code-cell}
hitung_klaster = email_tfidf['cluster'].value_counts().sort_index()
print("Jumlah dokumen per cluster:")
display(hitung_klaster)
```

### Menampilkan Contoh Dokumen Setiap Klaster

```{code-cell}
# gunakan kolom 'Text' (asli) dan 'Text_preprocessing_bersih'
dokumen = []
if 'Text' in email_tfidf.columns:
    dokumen.append('Text')
# preferensi untuk menampilkan hasil preprocessing (jika ada)
if 'Text_preprocessing' in email_tfidf.columns:
    dokumen.append('Text_preprocessing')
elif 'Text_preprocessing_bersih' in email_tfidf.columns:
    dokumen.append('Text_preprocessing_bersih')
```

```{code-cell}
print(f"\nMenampilkan {jumlah_dokumen} contoh per cluster (kolom: {dokumen})\n")
for c in sorted(email_tfidf['cluster'].unique()):
    print(f"=== Cluster {c} (count: {hitung_klaster.loc[c]}) ===")
    display(email_tfidf[email_tfidf['cluster'] == c][dokumen].head(jumlah_dokumen))
    print()
```

### Visualisasi 2D Menggunakan TruncatedSVD

```{code-cell}
# mirip PCA untuk sparse TF-IDF
svd = TruncatedSVD(n_components=2, random_state=RandomState)
X_2d = svd.fit_transform(X)   # bekerja langsung untuk sparse X
```

```{code-cell}
# Transform centroid ke ruang 2D (agar bisa digambar)
centroids_2d = svd.transform(kmeans_model.cluster_centers_)
```

```{code-cell}
# Plot scatter
plt.figure(figsize=(10, 7))
scatter = plt.scatter(X_2d[:, 0], X_2d[:, 1], c=klaster, cmap='tab10', s=10, alpha=0.7)
plt.scatter(centroids_2d[:, 0], centroids_2d[:, 1], marker='X', s=200, c='black', label='centroids')
plt.title(f'Visualisasi KMeans (k={klaster_optimal}) pada 2D (TruncatedSVD)')
plt.xlabel('Component 1')
plt.ylabel('Component 2')
plt.legend(*scatter.legend_elements(), title="Cluster", bbox_to_anchor=(1.05, 1), loc='upper left')
plt.grid(alpha=0.2)
plt.show()
```

```{code-cell}
display(email_tfidf)
```

```{code-cell}
# simpan hasil clustering ke csv
email_tfidf.to_csv("PPW_UTS_Email_Clustering(K-Means).csv", index=False)
```

## Code Tugas UTS - TF-IDF dan Clustering Email
- [PPW_UTS_Email](https://colab.research.google.com/drive/1VA-2KEzN7WEYAn1vkfPU-4PzCeZKzHlF?usp=sharing)