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

# **Topic Modelling Dan Klasifikasi**

# Tugas Pra UTS - Topic Modelling dan Klasifikasi Berita Online

Keterangan : Topic Modelling dan Klasifikasi dilakukan menggunakan dataset berita online hasil preprocessing melalui tahap hapus stopword dan tahap stemming

# Topic Modelling (LDA)

## 1. Install dan Import Library

```{code-cell}
!pip install pyLDAvis
```

```{code-cell}
!pip install gensim
```

```{code-cell}
import pandas as pd
import numpy as np
import re
import ast
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.decomposition import LatentDirichletAllocation
```

```{code-cell}
import matplotlib.pyplot as plt
```

```{code-cell}
import pyLDAvis
import warnings
warnings.filterwarnings("ignore")
```

```{code-cell}
from gensim import corpora
from gensim.models import CoherenceModel
```

```{code-cell}
from sklearn.model_selection import train_test_split
from sklearn.svm import SVC
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import confusion_matrix, classification_report, accuracy_score, precision_score, recall_score
from IPython.display import display
import seaborn as sns
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
display(berita_preprocessing)
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
display(berita_preprocessing)
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

## 7. Menghitung Metrik Kinerja Model

```{code-cell}
log_likelihood = lda_model.score(X)
perplexity = lda_model.perplexity(X)

print("Metrik Kinerja Model LDA:")
print(f"Log-Likelihood : {log_likelihood:.2f}")
print(f"Perplexity     : {perplexity:.2f}")
```

## 8. Distribusi Topik Terhadap Dokumen

### 1. DataFrame Distribusi Topik Terhadap Dokumen

```{code-cell}
kolom_topik = [f'T{i+1}' for i in range(jumlah_topik)]
df_topik_dokumen = pd.DataFrame(lda_output, columns=kolom_topik)
```

```{code-cell}
display(df_topik_dokumen)
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
display(df_hasil_topik)
```

### 3. Menyimpan Data Hasil Distribusi Topik Terhadap Dokumen

```{code-cell}
df_hasil_topik.to_csv("PPW_TugasPraUTS_BeritaOnline_TopicModeling.csv", index=False)
```

## 9. Distribusi Kata Terhadap Topik

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
df_kata_topik.to_csv("PPW_TugasPraUTS_BeritaOnline_KataTopik.csv", index=False)
```

## 10. Visualisasi : Distribusi Data Setiap Kelas

```{code-cell}
# menghitung jumlah data per kelas
hitung_kategori = berita_preprocessing['kategori_berita'].value_counts()

# menampilkan distribusi dalam bentuk tabel
print("Distribusi jumlah data per kelas:")
display(hitung_kategori)
```

```{code-cell}
# Visualisasi bar chart
plt.figure(figsize=(10,5))
hitung_kategori.plot(kind='bar')
plt.title('Distribusi Jumlah Data Setiap Kategori')
plt.xlabel('Kategori')
plt.ylabel('Jumlah Data')
plt.xticks(rotation=45, ha='right')
plt.show()
```

## 11. Visualisasi : Distribusi Topik Per Dokumen

```{code-cell}
# mengambil kolom topik dari DataFrame hasil topik modelling
df_topik_only = df_hasil_topik[[f'T{i+1}' for i in range(jumlah_topik)]]

# jumlah dokumen yang divisualisasi
jumlah_dokumen_visualisasi = 20
df_topik_visual = df_topik_only.head(jumlah_dokumen_visualisasi)

# membuat label sumbu x (nomor dokumen)
x_axis = [f'DOC{i+1}' for i in range(jumlah_dokumen_visualisasi)]
```

```{code-cell}
# membuat stacked bar plot
fig, ax = plt.subplots(figsize=(15, 8))

# Inisialisasi posisi bottom
bottom = np.zeros(len(x_axis))

# Untuk setiap topik, tambahkan batangnya secara bertumpuk
for col in df_topik_visual.columns:
    ax.bar(x_axis, df_topik_visual[col], bottom=bottom, label=col)
    bottom += np.array(df_topik_visual[col])

# Judul dan label sumbu
ax.set_title("Distribusi Topik terhadap Dokumen (20 Dokumen)", fontsize=14)
ax.set_xlabel("Dokumen", fontsize=12)
ax.set_ylabel("Proporsi Topik", fontsize=12)

# memindahkan legend ke luar grafik agar tidak menutupi batang
ax.legend(loc='center left', bbox_to_anchor=(1, 0.5), ncol=2, fontsize=9)

plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

## 12. Visualisasi : Hasil Topic Modelling Interaktif

```{code-cell}
# mengaktifkan tampilan interaktif di notebook
pyLDAvis.enable_notebook()

# menggunakan pyLDAvis
visualisasi_TopicModelling = pyLDAvis.prepare(
    topic_term_dists=lda_model.components_,   # distribusi kata terhadap topik
    doc_topic_dists=lda_output,               # distribusi topik terhadap dokumen
    doc_lengths=X.sum(axis=1).A1,             # panjang setiap dokumen
    vocab=vectorizer.get_feature_names_out(), # daftar kata
    term_frequency=X.sum(axis=0).A1           # frekuensi setiap kata
)

# menampilkan
visualisasi_TopicModelling
```

## 13. Menghitung Coherence Score

```{code-cell}
# fitur dari CountVectorizer sebelumnya
vocab = vectorizer.get_feature_names_out()

# menggunakan corpus hasil preprocessing : isi_berita_preprocessing bersih
tokenized_text = [doc.split() for doc in corpus]

# membuat dictionary dan corpus versi gensim
id2word = corpora.Dictionary(tokenized_text)
corpus_gensim = [id2word.doc2bow(text) for text in tokenized_text]

#mengambil distribusi kata terhadap topik dari model LDA sklearn
topic_word = lda_model.components_
topic_word = topic_word / topic_word.sum(axis=1)[:, np.newaxis]

# membuat representasi topik mirip gensim
# 10 kata teratas
topics = []
for topic in topic_word:
    top_tokens = [vocab[i] for i in topic.argsort()[:-11:-1]]
    topics.append(top_tokens)
```

### 1. Coherence Score

```{code-cell}
coherence_model_lda = CoherenceModel(
    topics=topics,
    texts=tokenized_text,
    dictionary=id2word,
    coherence='c_v'
)
coherence_lda = coherence_model_lda.get_coherence()

print(f"Coherence Score keseluruhan model LDA: {coherence_lda:.4f}")
```

### 2. Coherence Score Setiap Topik

```{code-cell}
coherence_per_topic = coherence_model_lda.get_coherence_per_topic()

df_coherence = pd.DataFrame({
    'Topik': [f'T{i+1}' for i in range(len(coherence_per_topic))],
    'Coherence_Score': coherence_per_topic
})

print("Coherence Score Setiap Topik:")
display(df_coherence)
```

### 3. Visualisasi : Coherence Score Setiap Topik

```{code-cell}
plt.figure(figsize=(12, 6))
plt.plot(df_coherence['Topik'], df_coherence['Coherence_Score'], marker='o', linestyle='-', linewidth=2)

# judul dan label
plt.title('Visualisasi Coherence Score Setiap Topik (LDA Topic Modelling)', fontsize=14)
plt.xlabel('Topik', fontsize=12)
plt.ylabel('Coherence Score', fontsize=12)

plt.grid(True, linestyle='--', alpha=0.6)

# menampilkan plot
plt.tight_layout()
plt.show()
```

# Klasifikasi

## 1. Mengambil Fitur Hasil Topic Modelling

```{code-cell}
X = df_hasil_topik[[f'T{i+1}' for i in range(100)]]
```

## 2. Label : Kategori Berita

```{code-cell}
y = df_hasil_topik['kategori_berita']
```

## 3. Cek Struktur

```{code-cell}
display(X)
```

```{code-cell}
display(y)
```

## 4. Split Data

```{code-cell}
# split data train dan test (80:20)
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42, stratify=y
)
```

```{code-cell}
print("Distribusi label di data training:")
print(y_train.value_counts())

print("\nDistribusi label di data testing:")
print(y_test.value_counts())
```

## 5. Klasifikasi Menggunakan SVM

```{code-cell}
# Inisialisasi model SVM
svm_model = SVC(kernel='linear', random_state=42)

# melatih model
svm_model.fit(X_train, y_train)

# Prediksi
y_pred_svm = svm_model.predict(X_test)
```

## 6. Klasifikasi Menggunakan Softmax

```{code-cell}
# Inisialisasi model Softmax Regression
softmax_model = LogisticRegression(
    multi_class='multinomial', solver='lbfgs', max_iter=1000, random_state=42
)

# melatih model
softmax_model.fit(X_train, y_train)

# Prediksi
y_pred_softmax = softmax_model.predict(X_test)
```

## 7. Evaluasi Model

```{code-cell}
def evaluasi_model(y_true, y_pred, model_name):
    print(f"\n=== Evaluasi Model: {model_name} ===")
    print("Accuracy :", accuracy_score(y_true, y_pred))
    print("Precision:", precision_score(y_true, y_pred, average='weighted'))
    print("Recall   :", recall_score(y_true, y_pred, average='weighted'))
    print("\nClassification Report:\n", classification_report(y_true, y_pred))

    # Confusion Matrix
    cm = confusion_matrix(y_true, y_pred)
    plt.figure(figsize=(8,6))
    sns.heatmap(cm, annot=True, fmt='d', cmap='Blues')
    plt.title(f'Confusion Matrix - {model_name}')
    plt.xlabel('Predicted')
    plt.ylabel('Actual')
    plt.show()
```

### 1. Evaluasi Model SVM

```{code-cell}
evaluasi_model(y_test, y_pred_svm, "SVM")
```

**Hasil Evaluasi :**

- **Performance :**

Akurasi 51,7% berarti model hanya mampu mengklasifikasikan sekitar separuh data uji dengan benar. Nilai precision (0.46) dan recall (0.51) juga menunjukkan bahwa performa model masih moderat, belum cukup baik untuk multi-class classification dengan jumlah kelas yang banyak (23 kelas).

- **Analisa Tiap Kelas :**

Kelas dengan jumlah data besar seperti NEWS, BOLA, REGIONAL, MONEY, dan OTOMOTIF memiliki nilai precision dan recall yang lebih tinggi. Sedangkan kelas dengan jumlah data sedikit (seperti ADVERTORIAL, CEK FAKTA, SKOLA, TRAVEL, LIFESTYLE) memiliki nilai 0.00, artinya model gagal mengenali kelas-kelas tersebut. Ini menandakan imbalance class problem (ketidakseimbangan data antar kategori).

- **Confussion Matrix :**

Sebagian besar prediksi model terfokus pada beberapa label populer seperti NEWS, BOLA, dan REGIONAL. Banyak kelas lain yang memiliki baris diagonal kosong, menandakan tidak ada prediksi benar untuk kelas tersebut.

**Penyebab Akurasi Rendah :**
*   Ketidakseimbangan Data (Class Imbalance) : Kelas dengan data sedikit tidak cukup belajar pola, menyebabkan bias ke kelas yang lebih besar.
*   Jumlah topik LDA (100) : jika terlalu banyak fitur jadi tersebar dan sulit dipelajari SVM. Jika terlalu sedikit topiknya kurang spesifik.
*   Parameter SVM Belum Dituning (C, kernel, gamma). Masih menggunakan parameter default kernel linear.

**Cara Meningkatkan Akurasi :**
*   Tunning Model LDA : bagian jumlah topik (100, ketentuan tugas)
*   Tunning Parameter SVM
*   Menangani Clas Imbalance : menggunakan SMOTE atau Class Weighting

### 2. Evaluasi Model Softmax

```{code-cell}
evaluasi_model(y_test, y_pred_softmax, "Softmax Regression")
```

**Hasil Evaluasi :**

- **Performance :**

Akurasi sebesar 50,99% menunjukkan bahwa model hanya mampu memprediksi sekitar setengah dari total data uji dengan benar.Nilai precision (0.455) dan recall (0.509) juga menunjukkan kinerja sedang, model belum optimal dalam mengklasifikasikan dokumen ke dalam 23 kategori berita yang berbeda.

- **Analisa Tiap Kelas :**

Kelas BOLA, NEWS, REGIONAL, MONEY, OTOMOTIF, dan PROPERTI memiliki precision dan recall yang cukup tinggi (sekitar 0.6–0.8). Artinya, model dapat mengenali kategori populer ini dengan cukup baik. Kelas seperti ADVERTORIAL, CEK FAKTA, SKOLA, LIFESTYLE, TRAVEL, TREN memiliki nilai 0.00 (model tidak mampu memprediksi kelas tersebut sama sekali). Hal ini menunjukkan adanya ketidakseimbangan data (class imbalance), di mana beberapa kelas memiliki jumlah data sangat sedikit dibanding kelas dominan seperti NEWS atau REGIONAL.

- **Confussion Matrix :**

sebagian besar prediksi terkonsentrasi pada kelas-kelas besar seperti NEWS, BOLA, REGIONAL, sama seperti model SVM. Banyak kelas minoritas memiliki baris diagonal kosong, tidak ada satupun prediksi benar untuk kelas tersebut. Artinya model cenderung bias terhadap kelas mayoritas.

**Penyebab Akurasi Rendah :**
*   Ketidakseimbangan Data (Class Imbalance) : Kelas dengan data banyak (misal: NEWS, REGIONAL) mendominasi proses pembelajaran, sedangkan kelas dengan data sedikit (misal: ADVERTORIAL, CEK FAKTA) tidak cukup belajar pola.
*   Jumlah topik LDA (100) : jika terlalu banyak fitur jadi tersebar dan sulit dipelajari Softmax. Jika terlalu sedikit topiknya kurang spesifik.
*   Parameter Softmax belum di tunning, jika pakai parameter default bisa menyebabkan overfitting.

**Cara meningkatkan akurasi :**
*   Tunning Model LDA : bagian jumlah topik (100, ketentuan tugas)
*   Tunning Parameter Softmax
*   Menangani Clas Imbalance : menggunakan SMOTE atau Class Weighting.

# Code Tugas Pra UTS - Topic Modelling dan Klasifikasi Berita Online
- [PPW_TugasPraUTS_TopicModelling_Klasifikasi_BeritaOnline](https://colab.research.google.com/drive/18IhHmp4aVh4TRnaVyvZE9GWYAuKZ3hFy?usp=sharing)