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

# **UTS Berita**

# Analisa Klasifikasi Berita Dengan Ekstraksi Fitur Topic Modelling Dengan Classifier Naive Bayes dan SVM

data : https://drive.google.com/file/d/1a776C4mmVbC-84gx_No0is9QDfpWFIme/view?usp=drive_link

## Load Data

```{code-cell}
import pandas as pd
from IPython.display import display
```

```{code-cell}
berita = pd.read_csv("Berita.csv")
display(berita.head())
```

```{code-cell}
print("Info Data Berita:")
print(berita.info())
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
berita["berita_bersih"] = berita["berita"].apply(pembersihan_teks)
```

```{code-cell}
# Perbandingan kolom isi berita sebelum dan sesudah punctuation removal
display(berita[["berita", "berita_bersih"]].head(10))
```

```{code-cell}
# kolom isi berita setelah punctuation removal di tambahkan ke kolom paling kanan dari dataset
display(berita.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data berita hasil punctuation removal, bisa aktifkan :
# berita.to_csv("PPW_UTS_Berita_PunctuationRemoval.csv", index=False)
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
berita["berita_noemoji"] = berita["berita_bersih"].apply(hapus_emoji)
```

```{code-cell}
# Perbandingan kolom isi berita sebelum dan sesudah hapus emoji
display(berita[["berita_bersih", "berita_noemoji"]].head(10))
```

```{code-cell}
# kolom isi berita setelah hapus emoji di tambahkan ke kolom paling kanan dari dataset
display(berita.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data isi berita hasil hapus emoji, bisa aktifkan :
# berita.to_csv("PPW_UTS_Berita_HapusEmoji.csv", index=False)
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
berita["berita_tokenisasi"] = berita["berita_noemoji"].apply(tokenisasi)
```

```{code-cell}
# Perbandingan kolom isi berita sebelum dan sesudah tokenisasi
display(berita[["berita_noemoji", "berita_tokenisasi"]].head(10))
```

```{code-cell}
# kolom isi berita setelah tokenisasi di tambahkan ke kolom paling kanan dari dataset
display(berita.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data isi berita hasil tokenisasi, bisa aktifkan :
# berita.to_csv("PPW_UTS_Berita_Tokenisasi.csv", index=False)
```

### 4. Hapus Stopword

```{code-cell}
from nltk.corpus import stopwords
```

```{code-cell}
nltk.download('stopwords')
```

```{code-cell}
stop_words = set(stopwords.words('indonesian'))
```

```{code-cell}
def hapus_stopword(tokens):
    if not isinstance(tokens, list):
        return []
    return [word for word in tokens if word.lower() not in stop_words]
```

```{code-cell}
berita["berita_nostopword"] = berita["berita_tokenisasi"].apply(hapus_stopword)
```

```{code-cell}
# Perbandingan kolom abstrak bahasa indonesia sebelum dan sesudah hapus stopword
display(berita[["berita_tokenisasi", "berita_nostopword"]].head(10))
```

```{code-cell}
# kolom isi berita setelah hapus stopword di tambahkan ke kolom paling kanan dari dataset
display(berita.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data isi berita hasil hapus stopword, bisa aktifkan :
# berita.to_csv("PPW_UTS_Berita_HapusStopword.csv", index=False)
```

```{code-cell}
# OPSIONAL
# cek tipe data : pastikan kolom isi_berita_nostopword itu list
print(berita["berita_nostopword"].apply(type).head())
```

### 5. Cek Ejaan (Peter Norvig Spell Checker)

```{code-cell}
import pandas as pd
import re
import ast
from collections import Counter
```

```{code-cell}
all_tokens = []
for doc in berita["berita_nostopword"].dropna():
    if isinstance(doc, list):  # kalau sudah list
        all_tokens.extend(doc)
    elif isinstance(doc, str):  # kalau string list
        try:
            tokens = ast.literal_eval(doc)
            if isinstance(tokens, list):
                all_tokens.extend(tokens)
        except:
            pass

# Membuat WORDS (kamus frekuensi)
WORDS = Counter(all_tokens)
print("Jumlah kata unik dalam WORDS:", len(WORDS))
```

```{code-cell}
def P(word, N=sum(WORDS.values())):
    "Probabilitas kata berdasarkan frekuensi dalam korpus."
    if N == 0:  # kalau WORDS kosong
        return 0
    return WORDS[word] / N

def correction(word):
    "Kata koreksi yang paling mungkin untuk input word."
    return max(candidates(word), key=P)

def candidates(word):
    "Menghasilkan kandidat koreksi untuk sebuah kata."
    return (known([word]) or known(edits1(word)) or known(edits2(word)) or [word])

def known(words):
    "Subset dari kata yang ada di kamus WORDS."
    return set(w for w in words if w in WORDS)

def edits1(word):
    "Semua kemungkinan edit (1 langkah) dari sebuah kata."
    letters    = 'abcdefghijklmnopqrstuvwxyz'
    splits     = [(word[:i], word[i:]) for i in range(len(word) + 1)]
    deletes    = [L + R[1:]               for L, R in splits if R]
    transposes = [L + R[1] + R[0] + R[2:] for L, R in splits if len(R) > 1]
    replaces   = [L + c + R[1:]           for L, R in splits if R for c in letters]
    inserts    = [L + c + R               for L, R in splits for c in letters]
    return set(deletes + transposes + replaces + inserts)

def edits2(word):
    "Semua kemungkinan edit (2 langkah) dari sebuah kata."
    return (e2 for e1 in edits1(word) for e2 in edits1(e1))
```

```{code-cell}
def ejaan_benar(tokens):
    if not isinstance(tokens, list):
        return []
    return [correction(word) for word in tokens]
```

```{code-cell}
berita["berita_cekejaan"] = (
    berita["berita_nostopword"]
    .apply(lambda x: ast.literal_eval(x) if isinstance(x, str) else x)
    .apply(ejaan_benar)
)
```

```{code-cell}
# Perbandingan kolom isi berita sebelum dan sesudah di cek ejaan
display(berita[["berita_nostopword", "berita_cekejaan"]].head(10))
```

```{code-cell}
# kolom isi berita setelah dicek ejaan di tambahkan ke kolom paling kanan dari dataset
display(berita.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data isi berita hasil cek ejaan, bisa aktifkan :
# berita.to_csv("PPW_UTS_Berita_PemeriksaanEjaan.csv", index=False)
```

```{code-cell}
# OPSIONAL
# cek tipe data : pastikan kolom isi_berita_cekejaan itu list
print(berita["berita_cekejaan"].apply(type).head())
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
    return [stemmer.stem(word) for word in tokens]
```

```{code-cell}
tqdm.pandas()

# karena stemming ini tahap akhir preprocessing, maka nama kolom jadi : berita_preprocessing
# kolom berita_preprocessing akan disimpan ke file dataset
# kolom tahap preprocessing lain (sebelum stemming) akan di drop
berita["berita_preprocessing"] = berita["berita_cekejaan"].progress_apply(stemming)
```

```{code-cell}
# Perbandingan kolom isi berita sebelum dan sesudah stemming
display(berita[["berita_cekejaan", "berita_preprocessing"]].head(10))
```

```{code-cell}
# kolom isi berita setelah stemming di tambahkan ke kolom paling kanan dari dataset
display(berita.head(10))
```

```{code-cell}
# Menyimpan data hasil setiap tahap preprocessing
berita.to_csv("PPW_UTS_Berita_TahapPreProcessing.csv", index=False)
```

```{code-cell}
# OPSIONAL
# cek tipe data : pastikan kolom isi_berita_preprocessing (hasil stemming) itu list
print(berita["berita_preprocessing"].apply(type).head())
```

### Hapus Kolom Hasil Setiap Tahap PreProcessing

```{code-cell}
import pandas as pd
```

```{code-cell}
data_berita = pd.read_csv("PPW_UTS_Berita_TahapPreProcessing.csv")
```

```{code-cell}
display(data_berita.head(10))
```

```{code-cell}
# hapus kolom tahapan preprocessing (sebelum stemming)
# hanya menyisakan kolom hasil tahap preprocessing akhir (stemming)
hapus_kolom = [
    'berita_bersih',
    'berita_noemoji',
    'berita_tokenisasi',
    'berita_nostopword',
    'berita_cekejaan'
]
```

```{code-cell}
preprocessing_data_berita = data_berita.drop(columns=hapus_kolom)
```

```{code-cell}
# menampilkan data kolom
print(preprocessing_data_berita.dtypes)
```

```{code-cell}
display(preprocessing_data_berita.head(10))
```

```{code-cell}
preprocessing_data_berita.to_csv("PPW_UTS_Berita_PreProcessing.csv", index=False)
```

## Topic Modelling

### Install dan Import Library

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

```{code-cell}
from sklearn.naive_bayes import MultinomialNB
```

### Load Dataset

```{code-cell}
berita_preprocessing = pd.read_csv("PPW_UTS_Berita_PreProcessing.csv")
```

```{code-cell}
# cek tipe data kolom isi berita hasil preprocessing
type(berita_preprocessing['berita_preprocessing'].iloc[0])
```

```{code-cell}
# mengubah tipe data kolom isi berita hasil preprocessing
# awalnya string diubah jadi list
berita_preprocessing['berita_preprocessing'] = berita_preprocessing['berita_preprocessing'].apply(
    lambda x: ast.literal_eval(x) if isinstance(x, str) else x
)
```

```{code-cell}
# cek tipe data kolom isi berita hasil preprocessing
type(berita_preprocessing['berita_preprocessing'].iloc[0])
```

```{code-cell}
# menampilkan data
display(berita_preprocessing)
```

### Pembersihan Kolom Berita Hasil PreProcessing

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
berita_preprocessing['berita_preprocessing_bersih'] = berita_preprocessing['berita_preprocessing'].apply(pembersihan_teks)
```

```{code-cell}
display(berita_preprocessing[["berita_preprocessing", "berita_preprocessing_bersih"]].head(10))
```

```{code-cell}
# kolom isi berita hasil preprocessing yang sudah dibersihkan ditambahkan ke kolom terakhir dataset
display(berita_preprocessing)
```

```{code-cell}
# cek tipe data kolom isi berita hasil preprocessing yang sudah dibersihkan
type(berita_preprocessing['berita_preprocessing_bersih'].iloc[0])
```

### Mengambil Corpus

```{code-cell}
corpus = berita_preprocessing['berita_preprocessing_bersih'].tolist()
```

```{code-cell}
print(corpus)
```

### Text Vectorizer Representasi BoW (Bag Of Word)

```{code-cell}
vectorizer = CountVectorizer()
X = vectorizer.fit_transform(corpus)
```

```{code-cell}
# Menampilkan jumlah dokumen dan fitur
print("Jumlah Dokumen :", X.shape[0])
print("Jumlah Fitur (Kosakata) :", X.shape[1])
```

### Cari Jumlah Topik Optimal

```{code-cell}
# Ambil kosakata dari vectorizer
vocab = vectorizer.get_feature_names_out()

# Tokenisasi agar cocok untuk Gensim
tokenized_text = [doc.split() for doc in corpus]

# Buat dictionary dan corpus versi Gensim
id2word = corpora.Dictionary(tokenized_text)
corpus_gensim = [id2word.doc2bow(text) for text in tokenized_text]
```

```{code-cell}
coherence_scores = []
topic_range = range(10, 105, 5)  # dari 10 sampai 100, kelipatan 5

for num_topics in topic_range:
    print(f"Sedang menghitung untuk jumlah topik = {num_topics} ...")

    # Buat model LDA dengan jumlah topik tertentu
    lda_model = LatentDirichletAllocation(
        n_components=num_topics,
        max_iter=20,
        learning_method='batch',
        random_state=42
    )
    lda_model.fit(X)

    # Ambil distribusi kata terhadap topik
    topic_word = lda_model.components_
    topic_word = topic_word / topic_word.sum(axis=1)[:, np.newaxis]

    # Ambil 10 kata teratas per topik
    topics = []
    for topic in topic_word:
        top_tokens = [vocab[i] for i in topic.argsort()[:-11:-1]]
        topics.append(top_tokens)

    # Hitung coherence score model
    coherence_model = CoherenceModel(
        topics=topics,
        texts=tokenized_text,
        dictionary=id2word,
        coherence='c_v'
    )
    coherence = coherence_model.get_coherence()
    coherence_scores.append(coherence)

    print(f"Jumlah Topik {num_topics} -> Coherence Score = {coherence:.4f}")
```

```{code-cell}
# visualisasi hasil
plt.figure(figsize=(10, 6))
plt.plot(topic_range, coherence_scores, marker='o', linestyle='-', linewidth=2)
plt.title('Grafik Coherence Score terhadap Jumlah Topik (LDA)', fontsize=14)
plt.xlabel('Jumlah Topik', fontsize=12)
plt.ylabel('Coherence Score', fontsize=12)
plt.grid(True, linestyle='--', alpha=0.6)
plt.show()
```

```{code-cell}
# Tampilkan jumlah topik dengan coherence tertinggi
jumlah_topik_optimal = topic_range[np.argmax(coherence_scores)]
score_coherence_terbaik = max(coherence_scores)

print(f"Jumlah Topik Optimal : {jumlah_topik_optimal}")
print(f"Coherence Score Tertinggi : {score_coherence_terbaik:.4f}")
```

### Menghitung LDA (Latent Dirichlet Allocation)

```{code-cell}
jumlah_topik = 20
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

### Menghitung Metrik Kinerja Model

```{code-cell}
log_likelihood = lda_model.score(X)
perplexity = lda_model.perplexity(X)

print("Metrik Kinerja Model LDA:")
print(f"Log-Likelihood : {log_likelihood:.2f}")
print(f"Perplexity     : {perplexity:.2f}")
```

### Distribusi Topik Terhadap Dokumen

```{code-cell}
kolom_topik = [f'T{i+1}' for i in range(jumlah_topik)]
df_topik_dokumen = pd.DataFrame(lda_output, columns=kolom_topik)
```

```{code-cell}
display(df_topik_dokumen)
```

```{code-cell}
# kategori_berita dipindah ke kolom terakhir
df_hasil_topik = pd.concat([
    berita_preprocessing[['No', 'judul', 'berita', 'tanggal', 'link', 'berita_preprocessing', 'berita_preprocessing_bersih']],
    df_topik_dokumen,
    berita_preprocessing[['kategori']]
], axis=1)
```

```{code-cell}
display(df_hasil_topik)
```

```{code-cell}
df_hasil_topik.to_csv("PPW_UTS_Berita_TopicModeling.csv", index=False)
```

### Distribusi Kata Terhadap Topik

```{code-cell}
def tampilkan_topik(model, feature_names, n_kata_teratas=10):
    topik_list = []
    for idx, topik in enumerate(model.components_):
        kata_teratas = [feature_names[i] for i in topik.argsort()[:-n_kata_teratas - 1:-1]]
        bobot_kata = topik[topik.argsort()[:-n_kata_teratas - 1:-1]]
        topik_list.append(pd.DataFrame({'Kata': kata_teratas, 'Bobot': bobot_kata}))
        print(f"\nTopik {idx+1}:")
        print(", ".join(kata_teratas))
    return topik_list
```

```{code-cell}
fitur_kata = vectorizer.get_feature_names_out()
topik_dominan = tampilkan_topik(lda_model, fitur_kata, n_kata_teratas=10)
```

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
df_kata_topik.to_csv("PPW_UTS_Berita_KataTopik.csv", index=False)
```

### Visualisasi : Distribusi Data Setiap Kelas

```{code-cell}
# menghitung jumlah data per kelas
hitung_kategori = berita_preprocessing['kategori'].value_counts()

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

### Visualisasi : Distribusi Topik Per Dokumen

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

### Visualisasi : Hasil Topic Modelling Interaktif

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

### Menghitung Coherence Score

```{code-cell}
# fitur dari CountVectorizer sebelumnya
vocab = vectorizer.get_feature_names_out()

# menggunakan corpus hasil preprocessing : berita_preprocessing bersih
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

```{code-cell}
# Coherence Score All
coherence_model_lda = CoherenceModel(
    topics=topics,
    texts=tokenized_text,
    dictionary=id2word,
    coherence='c_v'
)
coherence_lda = coherence_model_lda.get_coherence()

print(f"Coherence Score keseluruhan model LDA: {coherence_lda:.4f}")
```

```{code-cell}
# Coherence Score Tiap Topik
coherence_per_topic = coherence_model_lda.get_coherence_per_topic()

df_coherence = pd.DataFrame({
    'Topik': [f'T{i+1}' for i in range(len(coherence_per_topic))],
    'Coherence_Score': coherence_per_topic
})

print("Coherence Score Setiap Topik:")
display(df_coherence)
```

### Visualisasi : Coherence Score Setiap Topik

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

## Klasifikasi

```{code-cell}
# Mengambil Fitur Hasil Topic Modelling
X = df_hasil_topik[[f'T{i+1}' for i in range(20)]]
```

```{code-cell}
# Label : kategori
y = df_hasil_topik['kategori']
```

```{code-cell}
display(X)
```

```{code-cell}
display(y)
```

### Split Data

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

### Klasifikasi Menggunakan Naive Bayes

```{code-cell}
# Inisialisasi model
nb_model = MultinomialNB()

# Latih model
nb_model.fit(X_train, y_train)
```

```{code-cell}
# Prediksi label pada data uji
y_pred_nb = nb_model.predict(X_test)
```

### Evaluasi Model Naive Bayes

```{code-cell}
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score, classification_report

# Evaluasi model
acc_nb = accuracy_score(y_test, y_pred_nb)
prec_nb = precision_score(y_test, y_pred_nb, average='weighted', zero_division=0)
rec_nb = recall_score(y_test, y_pred_nb, average='weighted', zero_division=0)
f1_nb = f1_score(y_test, y_pred_nb, average='weighted', zero_division=0)

print("=== Evaluasi Model: Multinomial Naive Bayes ===")
print(f"Accuracy : {acc_nb}")
print(f"Precision: {prec_nb}")
print(f"Recall   : {rec_nb}\n")
print("Classification Report:\n", classification_report(y_test, y_pred_nb, zero_division=0))
```

```{code-cell}
# Buat confusion matrix
cm_nb = confusion_matrix(y_test, y_pred_nb, labels=nb_model.classes_)

plt.figure(figsize=(8,6))
sns.heatmap(
    cm_nb,
    annot=True,          # tampilkan angka di dalam kotak
    fmt='d',             # format angka jadi integer
    cmap='Blues',        # warna biru
    cbar=True,           # tampilkan color bar
    linewidths=0.5,      # garis antar kotak
    linecolor='gray',    # warna garis pembatas
    annot_kws={"size": 10}  # ukuran font angka
)
plt.title('Confusion Matrix - Multinomial Naive Bayes')
plt.xlabel('Predicted')
plt.ylabel('Actual')
plt.show()
```

### Klasifikasi Menggunakan SVM

```{code-cell}
# Inisialisasi model SVM
svm_model = SVC(kernel='linear', random_state=42)

# melatih model
svm_model.fit(X_train, y_train)

# Prediksi
y_pred_svm = svm_model.predict(X_test)
```

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

```{code-cell}
evaluasi_model(y_test, y_pred_svm, "SVM")
```

## Code Tugas UTS - Topic Modelling (LDA) dan Klasifikasi Berita
- [PPW_UTS_Berita](https://colab.research.google.com/drive/1b_yXhtiZZxZ20N20kIbXJYGZEkM07Lo6?usp=sharing)