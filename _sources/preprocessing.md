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

# **PreProcessing**

# Tugas 3 - PreProcessing PTA Prodi Manajemen (Abstrak Bahasa Indonesia)

# 1. Mengambil Data Prodi Manajemen

## 1. Import Library

```{code-cell}
import pandas as pd
from IPython.display import display
```

## 2. Load Dataset

```{code-cell}
data_pta = pd.read_csv("PPW_HasilCrawling_Tugas2(PTATrunojoyo)_2.csv")
display(data_pta.head())
```

## 3. Lihat Struktur Data

```{code-cell}
print("Info Data PTA Trunojoyo:")
print(data_pta.info())

print("\nData ID prodi :")
print(data_pta['id_prodi'].unique())

print("\nData nama prodi :")
print(data_pta['nama_prodi'].unique())
```

## 4. Ambil Data Prodi Manajemen

```{code-cell}
pta_prodi_manajemen = data_pta[(data_pta['id_prodi'] == 7) & (data_pta['nama_prodi'] == 'Manajemen')]

# cek hasil filter
print("Jumlah baris dan kolom data pta prodi manajemen:")
print(pta_prodi_manajemen.shape)
display(pta_prodi_manajemen.head())
```

## 5. Menyimpan Data PTA Prodi Manajemen

```{code-cell}
pta_prodi_manajemen.to_csv("PPW_Tugas3_PTATrunojoyo(7Manajemen).csv", index=False)
```



# 2. PreProcessing

## 1. Load Dataset

```{code-cell}
# data pta prodi manajemen
pta_manajemen = pd.read_csv("PPW_Tugas3_PTATrunojoyo(7Manajemen).csv")
```


## 2. Punctuation Removal (Membersihkan Teks)

### 1. Install dan Import Library

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

### 2. Fungsi Pembersihan Teks

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

### 3. Penerapan Kolom Abstrak Bahasa Indonesia

```{code-cell}
pta_manajemen["abstrak_bindonesia_bersih"] = pta_manajemen["abstrak_bindonesia"].apply(pembersihan_teks)
```

```{code-cell}
# Perbandingan kolom abstrak bahasa indonesia sebelum dan sesudah punctuation removal
display(pta_manajemen[["abstrak_bindonesia", "abstrak_bindonesia_bersih"]].head(10))
```

```{code-cell}
# kolom abstrak bahasa indonesia setelah punctuation removal di tambahkan ke kolom paling kanan dari dataset
display(pta_manajemen.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data abstrak bahasa indonesia hasil punctuation removal, bisa aktifkan :
# pta_manajemen.to_csv("PPW_Tugas3_PTATrunojoyo(7Manajemen)_PunctuationRemoval.csv", index=False)
```


## 3. Hapus Emoji

### 1. Install dan Import Library

```{code-cell}
!pip install emoji
```

```{code-cell}
import emoji
```

### 2. Fungsi Hapus Emoji

```{code-cell}
def hapus_emoji(text):
    if pd.isnull(text):
        return ""
    return emoji.demojize(text, language="en")
```

### 3. Penerapan Kolom Abstrak Bahasa Indonesia

```{code-cell}
pta_manajemen["abstrak_bindonesia_noemoji"] = pta_manajemen["abstrak_bindonesia_bersih"].apply(hapus_emoji)
```

```{code-cell}
# Perbandingan kolom abstrak bahasa indonesia sebelum dan sesudah hapus emoji
display(pta_manajemen[["abstrak_bindonesia_bersih", "abstrak_bindonesia_noemoji"]].head(10))
```

```{code-cell}
# kolom abstrak bahasa indonesia setelah hapus emoji di tambahkan ke kolom paling kanan dari dataset
display(pta_manajemen.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data abstrak bahasa indonesia hasil hapus emoji, bisa aktifkan :
# pta_manajemen.to_csv("PPW_Tugas3_PTATrunojoyo(7Manajemen)_HapusEmoji.csv", index=False)
```


## 4. Tokenisasi

### 1. Install dan Import Library

```{code-cell}
from nltk.tokenize import word_tokenize
import nltk
```

```{code-cell}
nltk.download('punkt_tab')
```

### 2. Fungsi Tokenisasi

```{code-cell}
def tokenisasi(text):
    if pd.isnull(text):
        return []
    return word_tokenize(text)
```

### 3. Penerapan Kolom Abstrak Bahasa Indonesia

```{code-cell}
pta_manajemen["abstrak_bindonesia_tokenisasi"] = pta_manajemen["abstrak_bindonesia_noemoji"].apply(tokenisasi)
```

```{code-cell}
# Perbandingan kolom abstrak bahasa indonesia sebelum dan sesudah tokenisasi
display(pta_manajemen[["abstrak_bindonesia_noemoji", "abstrak_bindonesia_tokenisasi"]].head(10))
```

```{code-cell}
# kolom abstrak bahasa indonesia setelah tokenisasi di tambahkan ke kolom paling kanan dari dataset
display(pta_manajemen.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data abstrak bahasa indonesia hasil tokenisasi, bisa aktifkan :
# pta_manajemen.to_csv("PPW_Tugas3_PTATrunojoyo(7Manajemen)_Tokenisasi.csv", index=False)
```


## 5. Hapus Stopword

### 1. Install dan Import Library

```{code-cell}
from nltk.corpus import stopwords
```

```{code-cell}
nltk.download('stopwords')
```

### 2. Daftar Stopword Bahasa Indonesia

```{code-cell}
stop_words = set(stopwords.words('indonesian'))
```

### 3. Fungsi Hapus Stopword

```{code-cell}
def hapus_stopword(tokens):
    if not isinstance(tokens, list):
        return []
    return [word for word in tokens if word.lower() not in stop_words]
```

### 4. Penerapan Kolom Abstrak Bahasa Indonesia

```{code-cell}
pta_manajemen["abstrak_bindonesia_nostopword"] = pta_manajemen["abstrak_bindonesia_tokenisasi"].apply(hapus_stopword)
```

```{code-cell}
# Perbandingan kolom abstrak bahasa indonesia sebelum dan sesudah hapus stopword
display(pta_manajemen[["abstrak_bindonesia_tokenisasi", "abstrak_bindonesia_nostopword"]].head(10))
```

```{code-cell}
# kolom abstrak bahasa indonesia setelah hapus stopword di tambahkan ke kolom paling kanan dari dataset
display(pta_manajemen.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data abstrak bahasa indonesia hasil hapus stopword, bisa aktifkan :
# pta_manajemen.to_csv("PPW_Tugas3_PTATrunojoyo(7Manajemen)_HapusStopword.csv", index=False)
```

```{code-cell}
# OPSIONAL
# cek tipe data : pastikan kolom abstrak_bindonesia_nostopword itu list
# print(pta_manajemen["abstrak_bindonesia_nostopword"].apply(type).head())
```


## 6. Cek Ejaan (Peter Norvig Spell Checker)

### 1. Install dan Import Library

```{code-cell}
import pandas as pd
import re
import ast
from collections import Counter
```

### 2. Buat Korpus

- Korpus dibuat dari dataset : kolom abstrak_bindonesia_nostopword.
- digunakan sebagai kamus untuk cek ejaan

```{code-cell}
all_tokens = []
for doc in pta_manajemen["abstrak_bindonesia_nostopword"].dropna():
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

### 3. Fungsi Probabilitas Dan Koreksi Kata

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

### 4. Fungsi Penerapan Koreksi Kata

```{code-cell}
def ejaan_benar(tokens):
    if not isinstance(tokens, list):
        return []
    return [correction(word) for word in tokens]
```

### 5. Penerapan Kolom Abstrak Bahasa Indonesia

```{code-cell}
pta_manajemen["abstrak_bindonesia_cekejaan"] = (
    pta_manajemen["abstrak_bindonesia_nostopword"]
    .apply(lambda x: ast.literal_eval(x) if isinstance(x, str) else x)
    .apply(ejaan_benar)
)
```

```{code-cell}
# Perbandingan kolom abstrak bahasa indonesia sebelum dan sesudah di cek ejaan
display(pta_manajemen[["abstrak_bindonesia_nostopword", "abstrak_bindonesia_cekejaan"]].head(10))
```

```{code-cell}
# kolom abstrak bahasa indonesia setelah dicek ejaan di tambahkan ke kolom paling kanan dari dataset
display(pta_manajemen.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data abstrak bahasa indonesia hasil cek ejaan, bisa aktifkan :
# pta_manajemen.to_csv("PPW_Tugas3_PTATrunojoyo(7Manajemen)_PemeriksaanEjaan.csv", index=False)
```

```{code-cell}
# OPSIONAL
# cek tipe data : pastikan kolom abstrak_bindonesia_cekejaan itu list
# print(pta_manajemen["abstrak_bindonesia_cekejaan"].apply(type).head())
```


## 7. Stemming

### 1. Install dan Import Library

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

### 2. Fungsi Stemming

```{code-cell}
def stemming(tokens):
    if not isinstance(tokens, list):
        return []
    return [stemmer.stem(word) for word in tokens]
```

### 3. Penerapan Kolom Abstrak Bahasa Indonesia

```{code-cell}
tqdm.pandas()

# karena stemming ini tahap akhir preprocessing, maka nama kolom jadi : abstrak_bindonesia_preprocessing
# kolom abstrak_bindonesia_preprocessing akan disimpan ke file dataset
# kolom tahap preprocessing lain (sebelum stemming) akan di drop
pta_manajemen["abstrak_bindonesia_preprocessing"] = pta_manajemen["abstrak_bindonesia_cekejaan"].progress_apply(stemming)
```

```{code-cell}
# Perbandingan kolom abstrak bahasa indonesia sebelum dan sesudah stemming
display(pta_manajemen[["abstrak_bindonesia_cekejaan", "abstrak_bindonesia_preprocessing"]].head(10))
```

```{code-cell}
# kolom abstrak bahasa indonesia setelah stemming di tambahkan ke kolom paling kanan dari dataset
display(pta_manajemen.head(10))
```

```{code-cell}
# Menyimpan data hasil setiap tahap preprocessing
pta_manajemen.to_csv("PPW_Tugas3_PTATrunojoyo(7Manajemen)_TahapPreProcessing.csv", index=False)
```

```{code-cell}
# OPSIONAL
# cek tipe data : pastikan kolom abstrak_bindonesia_preprocessing (hasil stemming) itu list
# print(pta_manajemen["abstrak_bindonesia_preprocessing"].apply(type).head())
```



# 3. Frekuensi Kata (Term)

## 1. Install dan Import Library

```{code-cell}
import pandas as pd
import ast
from collections import Counter
```

## 2. Menggabungkan Token

```{code-cell}
all_tokens = []
for doc in pta_manajemen["abstrak_bindonesia_preprocessing"].dropna():
    if isinstance(doc, list):
        all_tokens.extend(doc)
```

## 3. Hitung Frekuensi Kata

```{code-cell}
frekuensi_kata = Counter(all_tokens)
```

```{code-cell}
# diubah jadi tabel
data_frekuensi_kata = pd.DataFrame(frekuensi_kata.items(), columns=["kata", "frekuensi"])
```

```{code-cell}
# diurutkan mulai dari kata paling sering muncul sampai kata paling sedikit muncul
data_frekuensi_kata = data_frekuensi_kata.sort_values(by="frekuensi", ascending=False).reset_index(drop=True)
```

## 4. Meng-index Term Berdasarkan Frekuensi Kemunculan Kata

```{code-cell}
data_frekuensi_kata.index = data_frekuensi_kata.index + 1
data_frekuensi_kata.index.name = "no"
```

## 5. Menampilkan Kata Paling Sering Muncul

```{code-cell}
print("10 kata paling sering muncul:")
display(data_frekuensi_kata.head(10))
```

```{code-cell}
# menampilkan jumlah data (baris dan kolom) tabel frekuensi kata
print("Jumlah data tabel frekuensi kata:")
print(data_frekuensi_kata.shape)
```

## 6. Menyimpan Term Dan Frekuensi Yang Sudah Diindex

```{code-cell}
data_frekuensi_kata.to_csv("PPW_Tugas3_PTATrunojoyo(7Manajemen)_FrekuensiKata.csv", index=True)
```



# 4. Hapus Kolom Hasil Setiap Tahap Preprocessing

## 1. Import Library

```{code-cell}
import pandas as pd
```

## 2. Load Dataset

```{code-cell}
data_pta_manajemen = pd.read_csv("PPW_Tugas3_PTATrunojoyo(7Manajemen)_TahapPreProcessing.csv")
```

```{code-cell}
display(data_pta_manajemen.head(10))
```

## 3. Hapus Kolom

```{code-cell}
# hapus kolom tahapan preprocessing (sebelum stemming)
# hanya menyisakan kolom hasil tahap preprocessing akhir (stemming)
hapus_kolom = [
    'abstrak_bindonesia_bersih',
    'abstrak_bindonesia_noemoji',
    'abstrak_bindonesia_tokenisasi',
    'abstrak_bindonesia_nostopword',
    'abstrak_bindonesia_cekejaan'
]
```

```{code-cell}
preprocessing_pta_manajemen = data_pta_manajemen.drop(columns=hapus_kolom)
```

## 4. Menampilkan Data Setelah PreProcessing

```{code-cell}
# menampilkan data kolom
print(preprocessing_pta_manajemen.dtypes)
```

```{code-cell}
display(preprocessing_pta_manajemen.head(10))
```

## 5. Menyimpan Data Akhir Hasil PreProcessing

```{code-cell}
preprocessing_pta_manajemen.to_csv("PPW_Tugas3_PTATrunojoyo(7Manajemen)_PreProcessing.csv", index=False)
```



# 5. Hapus Data Kosong

## 1. Import Library

```{code-cell}
import pandas as pd
```

## 2. Load Dataset

```{code-cell}
pta_manajemen_preprocessing = pd.read_csv("PPW_Tugas3_PTATrunojoyo(7Manajemen)_PreProcessing.csv")
```

## 3. Cek Data Kosong Kolom Abstrak Bahasa Indonesia

```{code-cell}
missing_count = pta_manajemen_preprocessing["abstrak_bindonesia"].isna().sum()
print("Jumlah baris kosong:", missing_count)
```

## 4. Hapus Baris Kosong

```{code-cell}
pta_manajemen_bersih = pta_manajemen_preprocessing.dropna(subset=["abstrak_bindonesia"])
```

## 5. Cek Hasil Data dan Ukuran Data

```{code-cell}
# menampilkan ukuran data
print("jumlah data sebelum pembersihan:", pta_manajemen_preprocessing.shape)
print("jumlah data sesudah pembersihan:", pta_manajemen_bersih.shape)

# menampilkan data
display(pta_manajemen_bersih.head(10))
```

## 6. Simpan Data Hasil PreProcessing dan Pembersihan

```{code-cell}
pta_manajemen_bersih.to_csv("PPW_Tugas3_PTATrunojoyo(7Manajemen)_PreProcessing_1.csv", index=False)
```



# 6. Code Tugas 3 - PreProcessing PTA Prodi Manajemen (Abstrak Bahasa Indonesia)
- [PPW_Tugas3_PreProcessing(PTAAbstrak)](https://colab.research.google.com/drive/1EQ43kQ8nTahrwYE0aEwQ5KdBViP2wHDe?usp=sharing)




# Tugas 3 - PreProcessing Berita Online

# 1. Import Library

```{code-cell}
import pandas as pd
from IPython.display import display
```


# 2. Load Dataset

```{code-cell}
berita = pd.read_csv("PPW_HasilCrawling_Tugas2(BeritaOnline).csv")
display(berita.head())
```


# 3. Struktur Data

```{code-cell}
print("Info Data Berita:")
print(berita.info())
```


# 4. PreProcessing

## 1. Punctuation Removal (Membersihkan Teks)

### 1. Install dan Import Library

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

### 2. Fungsi Pembersihan Teks

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

### 3. Penerapan Kolom Isi Berita

```{code-cell}
berita["isi_berita_bersih"] = berita["isi_berita"].apply(pembersihan_teks)
```

```{code-cell}
# Perbandingan kolom isi berita sebelum dan sesudah punctuation removal
display(berita[["isi_berita", "isi_berita_bersih"]].head(10))
```

```{code-cell}
# kolom isi berita setelah punctuation removal di tambahkan ke kolom paling kanan dari dataset
display(berita.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data isi berita hasil punctuation removal, bisa aktifkan :
# berita.to_csv("PPW_Tugas3_BeritaOnline_PunctuationRemoval.csv", index=False)
```


## 2. Hapus Emoji

### 1. Install dan Import Library

```{code-cell}
!pip install emoji
```

```{code-cell}
import emoji
```

### 2. Fungsi Hapus Emoji

```{code-cell}
def hapus_emoji(text):
    if pd.isnull(text):
        return ""
    return emoji.demojize(text, language="en")
```

### 3. Penerapan Kolom Isi Berita

```{code-cell}
berita["isi_berita_noemoji"] = berita["isi_berita_bersih"].apply(hapus_emoji)
```

```{code-cell}
# Perbandingan kolom isi berita sebelum dan sesudah hapus emoji
display(berita[["isi_berita_bersih", "isi_berita_noemoji"]].head(10))
```

```{code-cell}
# kolom isi berita setelah hapus emoji di tambahkan ke kolom paling kanan dari dataset
display(berita.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data isi berita hasil hapus emoji, bisa aktifkan :
# berita.to_csv("PPW_Tugas3_BeritaOnline_HapusEmoji.csv", index=False)
```


## 3. Tokenisasi

### 1. Install dan Import Library

```{code-cell}
from nltk.tokenize import word_tokenize
import nltk
```

```{code-cell}
nltk.download('punkt_tab')
```

### 2. Fungsi Tokenisasi

```{code-cell}
def tokenisasi(text):
    if pd.isnull(text):
        return []
    return word_tokenize(text)
```

### 3. Penerapan Kolom Isi Berita

```{code-cell}
berita["isi_berita_tokenisasi"] = berita["isi_berita_noemoji"].apply(tokenisasi)
```

```{code-cell}
# Perbandingan kolom isi berita sebelum dan sesudah tokenisasi
display(berita[["isi_berita_noemoji", "isi_berita_tokenisasi"]].head(10))
```

```{code-cell}
# kolom isi berita setelah tokenisasi di tambahkan ke kolom paling kanan dari dataset
display(berita.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data isi berita hasil tokenisasi, bisa aktifkan :
# berita.to_csv("PPW_Tugas3_BeritaOnline_Tokenisasi.csv", index=False)
```


## 4. Hapus Stopword

### 1. Install dan Import Library

```{code-cell}
from nltk.corpus import stopwords
```

```{code-cell}
nltk.download('stopwords')
```

### 2. Daftar Stopword Bahasa Indonesia

```{code-cell}
stop_words = set(stopwords.words('indonesian'))
```

### 3. Fungsi Hapus Stopword

```{code-cell}
def hapus_stopword(tokens):
    if not isinstance(tokens, list):
        return []
    return [word for word in tokens if word.lower() not in stop_words]
```

### 4. Penerapan Kolom Isi Berita

```{code-cell}
berita["isi_berita_nostopword"] = berita["isi_berita_tokenisasi"].apply(hapus_stopword)
```

```{code-cell}
# Perbandingan kolom abstrak bahasa indonesia sebelum dan sesudah hapus stopword
display(berita[["isi_berita_tokenisasi", "isi_berita_nostopword"]].head(10))
```

```{code-cell}
# kolom isi berita setelah hapus stopword di tambahkan ke kolom paling kanan dari dataset
display(berita.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data isi berita hasil hapus stopword, bisa aktifkan :
# berita.to_csv("PPW_Tugas3_BeritaOnline_HapusStopword.csv", index=False)
```

```{code-cell}
# OPSIONAL
# cek tipe data : pastikan kolom isi_berita_nostopword itu list
# print(berita["isi_berita_nostopword"].apply(type).head())
```


## 5. Cek Ejaan (Peter Norvig Spell Checker)

### 1. Install dan Import Library

```{code-cell}
import pandas as pd
import re
import ast
from collections import Counter
```

### 2. Buat Korpus

- Korpus dibuat dari dataset : kolom isi_berita_nostopword.
- digunakan sebagai kamus untuk cek ejaan

```{code-cell}
all_tokens = []
for doc in berita["isi_berita_nostopword"].dropna():
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

### 3. Fungsi Probabilitas Dan Koreksi Kata

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

### 4. Fungsi Penerapan Koreksi Kata

```{code-cell}
def ejaan_benar(tokens):
    if not isinstance(tokens, list):
        return []
    return [correction(word) for word in tokens]
```

### 5. Penerapan Kolom Isi Berita

```{code-cell}
berita["isi_berita_cekejaan"] = (
    berita["isi_berita_nostopword"]
    .apply(lambda x: ast.literal_eval(x) if isinstance(x, str) else x)
    .apply(ejaan_benar)
)
```

```{code-cell}
# Perbandingan kolom isi berita sebelum dan sesudah di cek ejaan
display(berita[["isi_berita_nostopword", "isi_berita_cekejaan"]].head(10))
```

```{code-cell}
# kolom isi berita setelah dicek ejaan di tambahkan ke kolom paling kanan dari dataset
display(berita.head(10))
```

```{code-cell}
# OPSIONAL
# kalau mau menyimpan data isi berita hasil cek ejaan, bisa aktifkan :
# berita.to_csv("PPW_Tugas3_BeritaOnline_PemeriksaanEjaan.csv", index=False)
```

```{code-cell}
# OPSIONAL
# cek tipe data : pastikan kolom isi_berita_cekejaan itu list
# print(berita["isi_berita_cekejaan"].apply(type).head())
```


## 6. Stemming

### 1. Install dan Import Library

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

### 2. Fungsi Stemming

```{code-cell}
def stemming(tokens):
    if not isinstance(tokens, list):
        return []
    return [stemmer.stem(word) for word in tokens]
```

### 3. Penerapan Kolom Isi Berita

```{code-cell}
tqdm.pandas()

# karena stemming ini tahap akhir preprocessing, maka nama kolom jadi : isi_berita_preprocessing
# kolom isi_berita_preprocessing akan disimpan ke file dataset
# kolom tahap preprocessing lain (sebelum stemming) akan di drop
berita["isi_berita_preprocessing"] = berita["isi_berita_cekejaan"].progress_apply(stemming)
```

```{code-cell}
# Perbandingan kolom isi berita sebelum dan sesudah stemming
display(berita[["isi_berita_cekejaan", "isi_berita_preprocessing"]].head(10))
```

```{code-cell}
# kolom isi berita setelah stemming di tambahkan ke kolom paling kanan dari dataset
display(berita.head(10))
```

```{code-cell}
# Menyimpan data hasil setiap tahap preprocessing
berita.to_csv("PPW_Tugas3_BeritaOnline_TahapPreProcessing.csv", index=False)
```

```{code-cell}
# OPSIONAL
# cek tipe data : pastikan kolom isi_berita_preprocessing (hasil stemming) itu list
# print(berita["isi_berita_preprocessing"].apply(type).head())
```


# 5. Frekuensi Kata (Term)

## 1. Install dan Import Library

```{code-cell}
import pandas as pd
import ast
from collections import Counter
```

## 2. Menggabungkan Token

```{code-cell}
all_tokens = []
for doc in berita["isi_berita_preprocessing"].dropna():
    if isinstance(doc, list):
        all_tokens.extend(doc)
```

## 3. Hitung Frekuensi Kata

```{code-cell}
frekuensi_kata = Counter(all_tokens)
```

```{code-cell}
# diubah jadi tabel
data_frekuensi_kata = pd.DataFrame(frekuensi_kata.items(), columns=["kata", "frekuensi"])
```

```{code-cell}
# diurutkan mulai dari kata paling sering muncul sampai kata paling sedikit muncul
data_frekuensi_kata = data_frekuensi_kata.sort_values(by="frekuensi", ascending=False).reset_index(drop=True)
```

## 4. Meng-index Term Berdasarkan Frekuensi Kemunculan Kata

```{code-cell}
data_frekuensi_kata.index = data_frekuensi_kata.index + 1
data_frekuensi_kata.index.name = "no"
```

## 5. Menampilkan Kata Paling Sering Muncul

```{code-cell}
print("10 kata paling sering muncul:")
display(data_frekuensi_kata.head(10))
```

```{code-cell}
# menampilkan jumlah data (baris dan kolom) tabel frekuensi kata
print("Jumlah data tabel frekuensi kata:")
print(data_frekuensi_kata.shape)
```

## 6. Menyimpan Term Dan Frekuensi Yang Sudah Diindex

```{code-cell}
data_frekuensi_kata.to_csv("PPW_Tugas3_BeritaOnline_FrekuensiKata.csv", index=True)
```


# 6. Hapus Kolom Hasil Setiap Tahap Preprocessing

## 1. Import Library

```{code-cell}
import pandas as pd
```

## 2. Load Dataset

```{code-cell}
data_berita = pd.read_csv("PPW_Tugas3_BeritaOnline_TahapPreProcessing.csv")
```

```{code-cell}
display(data_berita.head(10))
```

## 3. Hapus Kolom

```{code-cell}
# hapus kolom tahapan preprocessing (sebelum stemming)
# hanya menyisakan kolom hasil tahap preprocessing akhir (stemming)
hapus_kolom = [
    'isi_berita_bersih',
    'isi_berita_noemoji',
    'isi_berita_tokenisasi',
    'isi_berita_nostopword',
    'isi_berita_cekejaan'
]
```

```{code-cell}
preprocessing_data_berita = data_berita.drop(columns=hapus_kolom)
```

## 4. Menampilkan Data Setelah PreProcessing

```{code-cell}
# menampilkan data kolom
print(preprocessing_data_berita.dtypes)
```

```{code-cell}
display(preprocessing_data_berita.head(10))
```

## 5. Menyimpan Data Akhir Hasil PreProcessing

```{code-cell}
preprocessing_data_berita.to_csv("PPW_Tugas3_BeritaOnline_PreProcessing.csv", index=False)
```


# 7. Cek Data Kosong

## 1. Import Library

```{code-cell}
import pandas as pd
```

## 2. Load Dataset

```{code-cell}
data_berita_preprocessing = pd.read_csv("PPW_Tugas3_BeritaOnline_PreProcessing.csv")
```

## 3. Cek Data Kosong Kolom Isi Berita

```{code-cell}
missing_count = data_berita_preprocessing["isi_berita_preprocessing"].isna().sum()
print("Jumlah baris kosong:", missing_count)
```


# 8. Code Tugas 3 - PreProcessing Berita Online
- [PPW_Tugas3_PreProcessing(BeritaOnline)](https://colab.research.google.com/drive/1YFLjN9FFoF3hROWWO2_3WS7M70YawDwg?usp=sharing)