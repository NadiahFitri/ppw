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

# **Word Graph (Paper)**

# Tugas 9 - Word Graph (Paper)

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

from nltk.tokenize import sent_tokenize, word_tokenize
from nltk.corpus import stopwords
from collections import defaultdict, Counter
from IPython.display import display
```

## 3. Download Resources NLTK

```{code-cell}
nltk.download('punkt')
nltk.download('punkt_tab')
nltk.download('stopwords')
```

## 4. Load & Ekstrak PDF Menjadi Teks

```{code-cell}
paper = 'Handling Imbalance Dataset on Hoax Indonesian Political News Classification using IndoBERT and Random Sampling.pdf'

doc = pymupdf.open(paper)

full_text = ""
for page in doc:
    full_text += page.get_text()

print("Jumlah karakter hasil ekstraksi:", len(full_text))
print("\nPreview teks hasil ekstraksi:\n")
print(full_text)
```

## 5. Preprocessing Teks (Bersih & Formal)

```{code-cell}
def clean_text(text):
    text = text.replace('\n', ' ')    # Gabungkan baris terpotong
    text = text.lower()   # Lowercase
    text = re.sub(r'\S+@\S+', ' ', text)    # Hapus email
    text = re.sub(r'http\S+|www\S+|doi\S+', ' ', text)    # Hapus URL & DOI
    text = re.sub(r'\d+', ' ', text)    # Hapus angka
    text = re.sub(r'[^a-zA-Z\.\?\!\s]', ' ', text)    # Hapus simbol
    text = re.sub(r'\s+', ' ', text)    # Rapikan spasi
    return text.strip()

cleaned_text = clean_text(full_text)

print("Preview teks setelah preprocessing:\n")
print(cleaned_text)
```

## 6. Ekstraksi Kalimat (NLTK)

```{code-cell}
# Sentence Tokenization
sentences = sent_tokenize(cleaned_text)

print("Jumlah kalimat awal:", len(sentences))
```

```{code-cell}
# Filter kalimat minimal 5 kata
filtered_sentences = []

for sent in sentences:
    words = sent.split()
    if len(words) >= 5:
        filtered_sentences.append(sent)

print("Jumlah kalimat setelah filtering:", len(filtered_sentences))
```

```{code-cell}
# Tampilkan kalimat
df_sentences = pd.DataFrame(filtered_sentences, columns=['kalimat'])
display(df_sentences)
```

```{code-cell}
# simpan hasil kalimat ke csv
# periksa hasil ekstrak kalimat apakah sudah bagus? apakah sudah benar benar ke ekstrak?
df_sentences.to_csv("PPW_Tugas9_WordGraph(Paper)_2_Kalimat.csv", index=False, encoding="utf-8")
```

## 7. Tokenisasi Kata & Stopword Removal

```{code-cell}
stop_words = set(stopwords.words('english'))

all_words = []

for sent in filtered_sentences:
    tokens = word_tokenize(sent)
    tokens = [w for w in tokens if w not in stop_words and len(w) > 2]
    all_words.extend(tokens)

print("Jumlah kata total:", len(all_words))
print("Contoh kata:", all_words)
```

## 8. Co-Occurrence Matrix

```{code-cell}
# Co-Occurrence Matrix
window_size = 2
co_occurrences = defaultdict(Counter)

for i, word in enumerate(all_words):
    for j in range(max(0, i - window_size), min(len(all_words), i + window_size + 1)):
        if i != j:
            co_occurrences[word][all_words[j]] += 1
```

```{code-cell}
# Matrix ke DataFrame
unique_words = list(co_occurrences.keys())
word_index = {word: idx for idx, word in enumerate(unique_words)}

co_matrix = np.zeros((len(unique_words), len(unique_words)), dtype=int)

for word, neighbors in co_occurrences.items():
    for neighbor, count in neighbors.items():
        co_matrix[word_index[word]][word_index[neighbor]] = count

co_matrix_df = pd.DataFrame(co_matrix, index=unique_words, columns=unique_words)

display(co_matrix_df)
```

## 9. Word Graph (NetworkX)

```{code-cell}
# Membuat graph
G = nx.Graph()

for word, neighbors in co_occurrences.items():
    for neighbor, weight in neighbors.items():
        if weight > 0:
            G.add_edge(word, neighbor, weight=weight)

print("Jumlah node:", G.number_of_nodes())
print("Jumlah edge:", G.number_of_edges())
```

```{code-cell}
# Visualisasi Word Graph
plt.figure(figsize=(12, 10))
pos = nx.spring_layout(G, k=0.5)

nx.draw(
    G, pos,
    with_labels=True,
    node_size=700,
    node_color='lightblue',
    font_size=9,
    edge_color='gray'
)

plt.title("Word Graph Paper Berdasarkan Matrix Co-Occurrence", fontsize=14)
plt.show()
```

## 10. Centrality (PageRank)

```{code-cell}
pagerank_scores = nx.pagerank(G)

pagerank_df = pd.DataFrame(
    pagerank_scores.items(),
    columns=['kata', 'pagerank']
).sort_values(by='pagerank', ascending=False)

print("Score PageRank setiap kata :")
display(pagerank_df)
```

## 11. Kata Kunci Hasil Word Graph

```{code-cell}
top_keywords = pagerank_df.head(20)

print("Kata kunci penting berdasarkan hasil word graph dan PageRank:")
display(top_keywords)
```

## 12. Code Tugas 9 - Word Graph (Paper)
- [PPW_Tugas9_WordGraph(Paper)](https://colab.research.google.com/drive/1BGEkvpiamhJvFoPFwSg2CRQkP1_fJ-4U?usp=sharing)