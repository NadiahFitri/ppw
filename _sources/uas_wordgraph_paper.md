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

# **UAS WORD GRAPH**

# Keyword Extraction Menggunakan Word Graph

## 1. Install Library

```{code-cell}
!pip install --upgrade pymupdf
!pip install nltk
!pip install networkx
!pip install matplotlib
!pip install pandas
```

## 2. Import Library

```{code-cell}
import pymupdf
import nltk
import re
import pandas as pd
import numpy as np
import networkx as nx
import matplotlib.pyplot as plt
import fitz  # PyMuPDF

from nltk.tokenize import sent_tokenize, word_tokenize
from nltk.corpus import stopwords
from collections import defaultdict, Counter
from IPython.display import display
from nltk.util import ngrams
from sklearn.preprocessing import MinMaxScaler
```

## 3. Download Resources NLTK

```{code-cell}
nltk.download('punkt')
nltk.download('punkt_tab')
nltk.download('stopwords')
```

## 4. Load & Ekstrak PDF Menjadi Teks

```{code-cell}
# Nama file PDF
paper = "Handling Imbalance Dataset on Hoax Indonesian Political News Classification using IndoBERT and Random Sampling.pdf"

# Nama file output TXT
paper_txt = "PPW_UAS_WordGraph(Paper)_HasilEkstraksi.txt"

# Buka PDF
doc = fitz.open(paper)

# Tulis ke file TXT
with open(paper_txt, "wb") as out:
    for page in doc:
        text = page.get_text().encode("utf8")
        out.write(text)
        out.write(bytes((12,)))  # form feed sebagai pemisah halaman
```

```{code-cell}
with open(paper_txt, "r", encoding="utf-8") as f:
    full_text = f.read()

print("Jumlah karakter hasil ekstraksi:", len(full_text))
```

```{code-cell}
print("Preview teks hasil ekstraksi:\n")
print(full_text)
```

## 5. Preprocessing Teks (Bersih & Formal)

```{code-cell}
def preprocessing(text):
    # 1. CLEANING DASAR
    text = text.replace('\n', ' ')
    text = text.lower()
    text = re.sub(r'\S+@\S+', ' ', text)                 # hapus email
    text = re.sub(r'http\S+|www\S+|doi\S+', ' ', text)   # hapus url & doi
    text = re.sub(r'\d+', ' ', text)                     # hapus angka
    text = re.sub(r'[^a-zA-Z\.\?\!\s]', ' ', text)       # hapus simbol (kecuali titik)
    text = re.sub(r'\s+', ' ', text)

    # 2. HAPUS METADATA JURNAL
    patterns_metadata = [
        r'jurnal\s+[a-z\s]+',
        r'volume\s+\d+',
        r'page\s+\d+[\-\–]\d+',
        r'issn\s+\d+[\-\d\s\(\)a-z]+',
        r'available\s+online\s+at\s+\S+',
        r'doi\s*[:]*\s*\S+',
        r'copyright\s+©*\s*\d+',
        r'submitted\s*[:]*.*?published\s*[:]*\s*\d+',
        r'email\s*addresses*[:]*\s*\S+'
    ]

    for p in patterns_metadata:
        text = re.sub(p, ' ', text)

    # 3. HAPUS REFERENCES
    text = re.split(
        r'references|daftar pustaka|bibliography',
        text,
        flags=re.IGNORECASE
    )[0]

    # 4. HAPUS SITASI & KATA TIDAK BERMAKNA
    text = re.sub(r'\[[0-9]+\]', ' ', text)
    text = re.sub(r'\([0-9]{4}\)', ' ', text)
    text = re.sub(r'et al', ' ', text)

    meaningless_words = [r'\betc\b', r'\bdll\b', r'\bdst\b', r'\bdsb\b']
    for w in meaningless_words:
        text = re.sub(w, ' ', text)

    # 5. HAPUS FOOTER JURNAL
    footer_patterns = [
        r'\bmib\b',
        r'\bmib\s*v\s*i\b',
        r'\bmuhammad\s+ammar\s+fathin\b',
        r'\bpage\b',
        r'\bsubmitted\b',
        r'\baccepted\b',
        r'\bpublished\b',
        r'\bjournal\b',
        r'\bvolume\b',
        r'\bnomor\b',
        r'\bmedia\s+informatika\b',
        r'\bbudidarma\b',
        r'\bcopyright\b'
    ]

    for p in footer_patterns:
        text = re.sub(p, ' ', text)

    # 6. HAPUS TOKEN SISA (v i dll)
    text = re.sub(r'\b[v|i]\b', ' ', text)
    text = re.sub(r'\bv\s+i\b', ' ', text)
    text = re.sub(r'\bi\s+v\b', ' ', text)
    text = re.sub(r'\b[a-z]\b', ' ', text)

    # 7. RAPISKAN TEKS
    text = re.sub(r'\s+', ' ', text)

    return text.strip()
```

```{code-cell}
cleaned_text = preprocessing(full_text)

print("Preview teks akhir setelah preprocessing total:\n")
print(cleaned_text)
```

## 6. Ekstraksi Kalimat (NLTK)

```{code-cell}
# Sentence Tokenization
sentences = sent_tokenize(cleaned_text)

print("Jumlah kalimat awal:", len(sentences))
```

```{code-cell}
# Tampilkan kalimat
df_sentences = pd.DataFrame(sentences, columns=['kalimat'])
display(df_sentences)
```

## 7. Bersihkan Tanda Baca

```{code-cell}
def clean_sentence_punctuation(sentence):
    sentence = re.sub(r'[^\w\s]', ' ', sentence)    # Hapus semua tanda baca
    sentence = re.sub(r'_', ' ', sentence)    # Hapus underscore bawaan \w
    sentence = re.sub(r'\s+', ' ', sentence)    # Rapikan spasi
    return sentence.strip()
```

```{code-cell}
# filter minimal 5 kata dalam 1 kalimat
clean_sentences = []

for sent in sentences:
    cleaned = clean_sentence_punctuation(sent)
    if len(cleaned.split()) >= 5:
        clean_sentences.append(cleaned)

print("Jumlah kalimat setelah cleaning & filtering:", len(clean_sentences))
```

```{code-cell}
df_clean_sentences = pd.DataFrame(clean_sentences, columns=['kalimat_bersih'])
display(df_clean_sentences)
```

```{code-cell}
# simpan hasil kalimat ke csv
# periksa hasil ekstrak kalimat apakah sudah bagus? apakah sudah benar benar ke ekstrak?
df_clean_sentences.to_csv("PPW_UAS_WordGraph(Paper)_Kalimat.tsv", index=False, sep="\t", encoding="utf-8-sig")
```

## 8. Stopword Removal

```{code-cell}
stop_words = set(stopwords.words('english'))
print("Jumlah stopwords:", len(stop_words))
```

## 9. Tokenisasi

### 1. Unigram

```{code-cell}
unigram_tokens = []

for sent in clean_sentences:
    tokens = word_tokenize(sent)

    # stopword removal & panjang kata
    tokens = [w for w in tokens if w not in stop_words and len(w) > 2]

    unigram_tokens.extend(tokens)

print("Jumlah unigram:", len(unigram_tokens))
```

```{code-cell}
print("Contoh unigram:", unigram_tokens)
```

```{code-cell}
df_unigram = pd.DataFrame(unigram_tokens, columns=['unigram'])
display(df_unigram)
```

### 2. Bigram

```{code-cell}
bigram_tokens = []

for sent in clean_sentences:
    tokens = word_tokenize(sent)
    tokens = [w for w in tokens if w not in stop_words and len(w) > 2]

    # bentuk bigram
    bigrams = list(ngrams(tokens, 2))
    bigram_tokens.extend(bigrams)

print("Jumlah bigram:", len(bigram_tokens))
```

```{code-cell}
print("Contoh bigram:", bigram_tokens)
```

```{code-cell}
df_bigram = pd.DataFrame(bigram_tokens, columns=['kata_1', 'kata_2'])
display(df_bigram)
```

```{code-cell}
# Ubah bigram tuple menjadi string
bigram_tokens_str = [' '.join(bg) for bg in bigram_tokens]
```

```{code-cell}
print("Contoh bigram:", bigram_tokens_str)
```

### 3. Gabungan Unigram + Bigram

```{code-cell}
all_tokens = unigram_tokens + bigram_tokens_str

print("Total token gabungan:", len(all_tokens))
```

```{code-cell}
print("Contoh token gabungan:", all_tokens)
```

## 10. Co-Occurrence Matrix

```{code-cell}
# Co-Occurrence Matrix
window_size = 2
co_occurrences = defaultdict(Counter)

for i, token in enumerate(all_tokens):
    for j in range(
        max(0, i - window_size),
        min(len(all_tokens), i + window_size + 1)
    ):
        if i != j:
            co_occurrences[token][all_tokens[j]] += 1
```

```{code-cell}
print("Jumlah node (token unik):", len(co_occurrences))
```

```{code-cell}
unique_tokens = list(co_occurrences.keys())
token_index = {token: idx for idx, token in enumerate(unique_tokens)}

co_matrix = np.zeros((len(unique_tokens), len(unique_tokens)), dtype=int)

for token, neighbors in co_occurrences.items():
    for neighbor, count in neighbors.items():
        co_matrix[token_index[token]][token_index[neighbor]] = count

co_matrix_df = pd.DataFrame(
    co_matrix,
    index=unique_tokens,
    columns=unique_tokens
)

display(co_matrix_df)
```

## 11. Word Graph (NetworkX)

```{code-cell}
# Membuat graph dari matrix co-occurrence
G = nx.Graph()

# Ambil daftar token (unigram + bigram)
tokens = co_matrix_df.index.tolist()

# Tambahkan node
G.add_nodes_from(tokens)

# Tambahkan edge berdasarkan bobot matrix
for i, token_i in enumerate(tokens):
    for j, token_j in enumerate(tokens):
        weight = co_matrix_df.iloc[i, j]
        if weight > 0 and i < j:  # i < j untuk hindari edge ganda
            G.add_edge(token_i, token_j, weight=weight)

print("Jumlah node (unigram + bigram):", G.number_of_nodes())
print("Jumlah edge:", G.number_of_edges())
```

```{code-cell}
# Visualisasi Word Graph
plt.figure(figsize=(14, 12))

pos = nx.spring_layout(G, k=0.7, seed=42)

nx.draw(
    G,
    pos,
    with_labels=True,
    node_size=900,
    node_color='lightblue',
    font_size=9,
    edge_color='gray'
)

plt.title(
    "Word Graph Paper (Unigram + Bigram) Berdasarkan Co-Occurrence Matrix",
    fontsize=14
)
plt.show()
```

## 12. Centrality

### 1. PageRank Centrality

```{code-cell}
pagerank_scores = nx.pagerank(G)

pagerank_df = pd.DataFrame(
    pagerank_scores.items(),
    columns=['token', 'pagerank']
).sort_values(by='pagerank', ascending=False)

print("Top 20 PageRank Centrality")
display(pagerank_df.head(20))
```

### 2. Degree Centrality

```{code-cell}
degree_scores = nx.degree_centrality(G)

degree_df = pd.DataFrame(
    degree_scores.items(),
    columns=['token', 'degree_centrality']
).sort_values(by='degree_centrality', ascending=False)

print("Top 20 Degree Centrality")
display(degree_df.head(20))
```

### 3. Betweeness Centrality

```{code-cell}
betweenness_scores = nx.betweenness_centrality(G)

betweenness_df = pd.DataFrame(
    betweenness_scores.items(),
    columns=['token', 'betweenness_centrality']
).sort_values(by='betweenness_centrality', ascending=False)

print("Top 20 Betweenness Centrality")
display(betweenness_df.head(20))
```

### 4. Closeness Centrality

```{code-cell}
closeness_scores = nx.closeness_centrality(G)

closeness_df = pd.DataFrame(
    closeness_scores.items(),
    columns=['token', 'closeness_centrality']
).sort_values(by='closeness_centrality', ascending=False)

print("Top 20 Closeness Centrality")
display(closeness_df.head(20))
```

### 5. Merge Score Centrality

```{code-cell}
centrality_df = pagerank_df \
    .merge(degree_df, on='token') \
    .merge(betweenness_df, on='token') \
    .merge(closeness_df, on='token')

display(centrality_df.head(20))
```

### 6. Normalisasi Score Centrality

```{code-cell}
scaler = MinMaxScaler()

centrality_df[['pagerank_norm',
               'degree_norm',
               'betweenness_norm',
               'closeness_norm']] = scaler.fit_transform(
    centrality_df[['pagerank',
                    'degree_centrality',
                    'betweenness_centrality',
                    'closeness_centrality']]
)

display(centrality_df.head(20))
```

### 7. Hitung Score Semua Centrality (Agregat)

```{code-cell}
centrality_df['aggregate_score'] = (
    centrality_df['pagerank_norm'] +
    centrality_df['degree_norm'] +
    centrality_df['betweenness_norm'] +
    centrality_df['closeness_norm']
)

centrality_df = centrality_df.sort_values(
    by='aggregate_score',
    ascending=False
)
```

## 11. Kata Kunci Hasil Word Graph

```{code-cell}
# hasil keyword ekstraksion berdasarkan hasil agregat semua centrality
top_keywords_aggregate = centrality_df[
    ['token', 'aggregate_score']
].head(20)

print("Top 20 Kata Kunci Berdasarkan Agregat Semua Centrality")
display(top_keywords_aggregate)
```

```{code-cell}
# visualisasi keyword ekstraksion
plt.figure(figsize=(10, 6))
plt.barh(
    top_keywords_aggregate['token'],
    top_keywords_aggregate['aggregate_score']
)
plt.gca().invert_yaxis()
plt.title("Top 20 Keyword Berdasarkan Agregat Centrality")
plt.xlabel("Aggregate Centrality Score")
plt.show()
```

## 12. Code Tugas UAS - Word Graph Paper
- [PPW_UAS_WordGraph(Paper)](https://colab.research.google.com/drive/1LVuHE_zKhcFixRJTauHb3gbnS3Zc3Vhx?usp=sharing)

## 13. Deploy Tugas UAS - Word Graph Paper
- [PPW_UAS_WordGraph_Paper](https://huggingface.co/spaces/NadiahFitri/PPW_UAS_WordGraph_Paper)