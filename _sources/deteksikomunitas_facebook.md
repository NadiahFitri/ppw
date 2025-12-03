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

# **Deteksi Komunitas (Facebook)**

# Tugas 8 - Deteksi Komunitas Facebook

**Dataset bisa diunduh di link : https://rb.gy/3xwprm**

## 1. Install dan Import Library

```{code-cell}
!pip install networkx
```

```{code-cell}
!pip install python-louvain
```

```{code-cell}
import pandas as pd
import networkx as nx
import matplotlib.pyplot as plt
import community.community_louvain as community_louvain
from IPython.display import display
```

```{code-cell}
# debugging aja
dir(community_louvain)
```

## 2. Load Dataset

```{code-cell}
nama_file = "facebook_combined.txt"
data_facebook = pd.read_csv(
    nama_file,
    sep=' ',                             # file dipisahkan spasi
    names=['FromNode', 'ToNode'],        # memberi nama kolom
    header=None                          # file tidak punya header
)
print("Data Facebook:")
display(data_facebook)
```

## 3. Membuat Graph

```{code-cell}
graph = nx.from_pandas_edgelist(
    data_facebook,
    source='FromNode',
    target='ToNode',
    create_using=nx.Graph()
)

print("Jumlah Node :", graph.number_of_nodes())
print("Jumlah Edge :", graph.number_of_edges())
```

```{code-cell}
# Visualisasi Graph
plt.figure(figsize=(25, 25))

# Layout spring membutuhkan waktu, gunakan seed untuk konsisten
pos_full = nx.spring_layout(graph, seed=42)

# Gambar semua node
nx.draw_networkx_nodes(
    graph,
    pos_full,
    node_size=30,        # node diperkecil
    node_color='green'
)
# Gambar semua edge
nx.draw_networkx_edges(
    graph,
    pos_full,
    width=0.2,
    alpha=0.3,
    edge_color='black'
)
# Gambar semua label
nx.draw_networkx_labels(
    graph,
    pos_full,
    font_size=8,         # font kecil
    font_color='black'
)

plt.title("Graph Pertemanan Facebook", fontsize=14, fontweight='bold')
plt.axis("off")
plt.show()
```

## 4. Deteksi Komunitas (Algoritma Louvain)

```{code-cell}
print("Partisi komunitas menggunakan algoritma Louvain :")
partisi = community_louvain.best_partition(graph)

# Dapatkan semua label unik, urutkan
label = sorted(set(partisi.values()))
# Buat mapping lama --> baru
label_mapping = {old_label: new_label for new_label, old_label in enumerate(label)}
# Terapkan mapping
partition_relabel = {node: label_mapping[komunitas] for node, komunitas in partisi.items()}

# Simpan ke DataFrame
partisi_komunitas = pd.DataFrame(list(partition_relabel.items()), columns=['Node', 'Komunitas'])
display(partisi_komunitas)
```

```{code-cell}
# Visualisasi Deteksi Komunitas (Algoritma Louvain)
plt.figure(figsize=(25, 25))
# Layout spring (force-directed)
pos_louvain = nx.spring_layout(graph, seed=42)
# remap ID komunitas menjadi 0..n_komunitas-1
komunitas = sorted(set(partisi.values()))
mapping_komunitas = {c: i for i, c in enumerate(komunitas)}

warna_node = [mapping_komunitas[partisi[n]] for n in graph.nodes()] # warna beda tiap komunitas

# Gambar node dan warna komunitas
nx.draw(
    graph,
    pos_louvain,
    node_color=warna_node,
    cmap=plt.cm.tab20,
    node_size=30,
    with_labels=False  # label akan digambar terpisah
)
# Tambahkan label node
nx.draw_networkx_labels(
    graph,
    pos_louvain,
    labels={n: str(n) for n in graph.nodes()},  # semua node diberi label nama node
    font_size=8,       # kecil supaya tidak menumpuk
    font_color='black'
)

plt.title("Komunitas Facebook (Algoritma Louvain)", fontsize=14, fontweight='bold')
plt.axis('off')
plt.show()
```

## 5. Hitung Modularitas

```{code-cell}
# Langsung pakai library
modularitas = community_louvain.modularity(partisi, graph)
print(f"Modularitas (Algoritma Louvain): {modularitas:.4f}")
```

```{code-cell}
# Manual
# Convert partition dict → list komunitas
communities = {}
for node, comm in partisi.items():
    communities.setdefault(comm, []).append(node)
communities = list(communities.values())

# Parameter modularitas
weight = None
resolution = 1.0

# Mengecek apakah graf directed
directed = graph.is_directed()

# Hitung derajat
if directed:
    out_degree = dict(graph.out_degree(weight=weight))
    in_degree = dict(graph.in_degree(weight=weight))
    m = sum(out_degree.values())
    norm = 1 / m**2
else:
    out_degree = in_degree = dict(graph.degree(weight=weight))
    deg_sum = sum(out_degree.values())
    m = deg_sum / 2
    norm = 1 / deg_sum**2

# Fungsi kontribusi komunitas (sesuai rumus)
def community_contribution(community):
    comm = set(community)

    # L_c = jumlah edge internal komunitas
    L_c = sum(
        wt for u, v, wt in graph.edges(comm, data=weight, default=1)
        if v in comm
    )

    out_degree_sum = sum(out_degree[u] for u in comm)
    in_degree_sum  = sum(in_degree[u] for u in comm) if directed else out_degree_sum

    return (L_c / m) - resolution * out_degree_sum * in_degree_sum * norm

# Hitung total modularitas
modularitas_manual = sum(community_contribution(c) for c in communities)

print(f"Modularity Manual: {modularitas_manual:.4f}")
```

## 6. Code Tugas 8 - Deteksi Komunitas (Facebook)
- [PPW_Tugas8_DeteksiKomunitas(Facebook)](https://colab.research.google.com/drive/1qjNt_vC83UZzIIFyAcDEds1ucZRRHOeA?usp=sharing)