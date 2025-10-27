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

# **PageRank**

# Tugas 6 - PageRank (Data Web Google 10k)

## 1. Import Library

```{code-cell}
import pandas as pd
import numpy as np
import networkx as nx
import matplotlib.pyplot as plt
from IPython.display import display
```

## 2. Load Data

```{code-cell}
nama_file = 'web-Google_10k.txt'
```

```{code-cell}
data_node = pd.read_csv(
    nama_file,
    sep='\t',          # data dipisahkan oleh tab
    comment='#',       # abaikan baris yang diawali tanda #
    names=['FromNodeId', 'ToNodeId'],  # nama kolom
    # header=0           # tidak ada header di baris pertama setelah komentar
)
```

```{code-cell}
display(data_node)
```

## 3. Membangun Graph Menggunakan NetworkX

```{code-cell}
graph = nx.DiGraph()
edge = list(zip(data_node['FromNodeId'], data_node['ToNodeId']))
graph.add_edges_from(edge)

print(f"Jumlah node : {graph.number_of_nodes()}")
print(f"Jumlah edge : {graph.number_of_edges()}")
```

```{code-cell}
# Visualisasi Graph
plt.figure(figsize=(16, 12))
plt.title("Visualisasi Graph (10.000 Node dengan 78.323 Edge)", fontsize=14, fontweight='bold')

# layout efisien untuk dataset besar
pos = nx.spring_layout(graph, seed=42, k=0.15, iterations=20)

# gambar node dan edge
nx.draw_networkx_nodes(graph, pos, node_color='skyblue', node_size=10, alpha=0.8)
# nx.draw_networkx_edges(graph, pos, edge_color='black', arrows=False, alpha=0.3)
nx.draw_networkx_edges(graph, pos, edge_color='black', arrowstyle='->', arrowsize=4, alpha=0.3)

# label, bisa ditampilkan (resiko crash atau hang karena 10.000 node). aktifkan :
# nx.draw_networkx_labels(graph, pos, font_size=6, font_color='black')

plt.axis('off')
plt.show()
```

## 4. Membentuk Matriks Adjacency (A)

```{code-cell}
# note: all matriks berukuran 10.000 x 10.000, sangat besar untuk ditampilkan
# jadi ditampilkan sebagian (misalnya 30x30)
node = list(graph.nodes())[:30]
A = nx.to_numpy_array(graph, nodelist=node, dtype=int)

data_node_A = pd.DataFrame(A, index=node, columns=node)
print("Matriks Adjacency (A) [30x30]:")
display(data_node_A)
```

## 5. Membentuk Matriks Probabilitas Transisi Baris-Stokastik (P)

```{code-cell}
adj_full = nx.to_numpy_array(graph, dtype=float)
n = adj_full.shape[0]

# tangani dangling node (node tanpa outlink)
out_degree = adj_full.sum(axis=1)
for i in range(n):
    if out_degree[i] == 0:
        adj_full[i, :] = 1.0

# matriks transisi baris-stokastik
P = adj_full / adj_full.sum(axis=1, keepdims=True)

# tampilan sebagian matriks P
data_node_P = pd.DataFrame(P[:30, :30], columns=range(30), index=range(30))
print("Matriks Transisi (P) [30x30]:")
display(data_node_P)
```

## 6. Membentuk Matriks Kolom-Stokastik (M)

```{code-cell}
# M = Pᵀ (kolom-stokastik)
M = P.T
data_node_M = pd.DataFrame(M[:30, :30], columns=range(30), index=range(30))
print("Matriks M = Pᵀ [30x30]:")
display(data_node_M)
```

## 7. Menghitung PageRank Iteratif (Manual)

```{code-cell}
def pagerank_iteratif(M, nodelist, d=0.85, max_iter=100, tol=1e-6):
    n = M.shape[0]
    r = np.ones(n) / n
    teleport = (1 - d) / n

    for i in range(max_iter):
        r_new = d * M @ r + teleport
        # debugging aja
        indeks_top_node = np.argmax(r_new)  # indeks node dengan PageRank tertinggi
        top_node = nodelist[indeks_top_node] # node dengan score pagerank tertinggi
        print(f"Iterasi {i+1}: Node {top_node} dengan PageRank {r_new[indeks_top_node]:.6f}")
        if np.linalg.norm(r_new - r, 1) < tol:
            # debugging aja
            print(f"Konvergen setelah {i+1} iterasi")
            break
        r = r_new
    return r

print("Menghitung PageRank (butuh waktu karena 10.000 node):")

urutan_node = list(graph.nodes())
r = pagerank_iteratif(M, nodelist=urutan_node, d=0.85, max_iter=100, tol=1e-6)
```

## 8. Menampilkan dan Menyimpan Hasil PageRank

### 1. Semua Node

```{code-cell}
score_pagerank = pd.DataFrame({
    'node': list(graph.nodes()),
    'pagerank': r
})

# mengurutkan node dengan nilai PageRank tertinggi ke terendah
data_pagerank_node = score_pagerank.sort_values(by='pagerank', ascending=False).reset_index(drop=True)
print("Data Node dan Score PageRank:")
display(data_pagerank_node)
```

```{code-cell}
data_pagerank_node.to_csv("PPW_Tugas6_WebGoogle10K_PageRank(Manual).csv", index=False)
```

### 2. 5 Node

```{code-cell}
lima_node_penting = data_pagerank_node.head(5)
print("5 Node dengan Nilai PageRank Tertinggi:")
display(lima_node_penting)
```

## 9. Perbandingan: Menghitung dan Menampilkan PageRank Menggunakan NetworkX

### 1. Semua Node

```{code-cell}
score_pagerank_networkx = nx.pagerank(graph, alpha=0.85)
data_pagerank_node_networkx = pd.DataFrame(list(score_pagerank_networkx.items()), columns=['node', 'pagerank'])

# node score pagerank tertinggi ke terendah
data_node_pagerank_networkx_urut = data_pagerank_node_networkx.sort_values(by='pagerank', ascending=False).reset_index(drop=True)
print("Perbandingan Menggunakan NetworkX - Data Node dan Score PageRank:")
display(data_node_pagerank_networkx_urut)
```

```{code-cell}
# OPSIONAL
# kalau mau simpan hasil data node dan score pagerank pembanding menggunakan NetworkX, aktifkan :
# data_node_pagerank_networkx_urut.to_csv("PPW_Tugas6_WebGoogle10K_PageRank(NetworkX).csv", index=False)
```

### 2. 5 Node

```{code-cell}
lima_node_penting_networkx = data_node_pagerank_networkx_urut.head(5)

print("Perbandingan Menggunakan NetworkX - 5 Node dengan Nilai PageRank Tertinggi:")
display(lima_node_penting_networkx)
```

## 10. Visualisasi Hubungan Node Penting dengan Node Terhubung

```{code-cell}
# simpan node dengan PageRank tertinggi ke variabel
node_penting = int(lima_node_penting.iloc[0]['node'])
print(f"Node dengan nilai PageRank tertinggi: {node_penting}")

# node yang terhubung dengan node penting (incoming dan outgoing)
neighbors_out = list(graph.successors(node_penting))   # node yang ditaut oleh node_penting
neighbors_in = list(graph.predecessors(node_penting))  # node yang menaut ke node_penting

# menggabungkan semua node yang relevan
node_terhubung = set(neighbors_out + neighbors_in + [node_penting])

# buat subgraph dari node-node tersebut
subgraph_node_terhubung = graph.subgraph(node_terhubung)
```

```{code-cell}
# Visualisasi Graph
plt.figure(figsize=(12, 9))
plt.title(f"Graph Keterhubungan Node Penting ({node_penting})", fontsize=14, fontweight='bold')

pos = nx.spring_layout(subgraph_node_terhubung, seed=42, k=0.3)

# warna berbeda untuk node penting
warna_node = ['red' if node == node_penting else 'skyblue' for node in subgraph_node_terhubung.nodes()]

nx.draw_networkx_nodes(subgraph_node_terhubung, pos, node_color=warna_node, node_size=500, alpha=0.9)
nx.draw_networkx_edges(subgraph_node_terhubung, pos, edge_color='black', arrowstyle='->', arrowsize=7, alpha=0.6)
nx.draw_networkx_labels(subgraph_node_terhubung, pos, font_size=7, font_color='black')

plt.axis('off')
plt.show()
```

```{code-cell}
# Data Keterhubungan Node Penting
print(f"Jumlah total node yang terhubung dengan {node_penting}: {len(node_terhubung)}")

# DataFrame untuk incoming dan outgoing edges
df_in = pd.DataFrame({
    'FromNodeId': neighbors_in,
    'ToNodeId': [node_penting] * len(neighbors_in)
})
df_out = pd.DataFrame({
    'FromNodeId': [node_penting] * len(neighbors_out),
    'ToNodeId': neighbors_out
})

# gabungkan kedua arah hubungan
data_node_terhubung = pd.concat([df_in, df_out], ignore_index=True)

print(f"Daftar Node yang Terhubung dengan Node {node_penting} (Incoming dan Outgoing):")
display(data_node_terhubung)
```

## 11. Code Tugas 6 - PageRank (Data Web Google 10k)
- [PPW_Tugas6_WebGoogle10K_PageRank](https://colab.research.google.com/drive/1xYdyDb77WSdyIoWa7HYgvs-uN_aTpgQi?usp=sharing)


# Tugas 6 - PageRank (Data Link Dalam Page)

## 1. Import Library

```{code-cell}
import pandas as pd
import numpy as np
import networkx as nx
import matplotlib.pyplot as plt
from IPython.display import display
```

## 2. Load Data

```{code-cell}
nama_file = 'PPW_HasilCrawling_Tugas2(LinkDalamPage).csv'
data_page = pd.read_csv(nama_file)
```

```{code-cell}
display(data_page)
```

## 3. Membangun Graph Menggunakan NetworkX

```{code-cell}
graph = nx.DiGraph()
edge = list(zip(data_page['page'], data_page['link_keluar']))
graph.add_edges_from(edge)

print(f"Jumlah node (page unik): {graph.number_of_nodes()}")
print(f"Jumlah edge (tautan): {graph.number_of_edges()}")
```

```{code-cell}
# Visualisasi Graph
plt.figure(figsize=(16, 12))
plt.title("Visualisasi Graph (662 Node dengan 3.561 Edge)", fontsize=14, fontweight='bold')

# layout efisien untuk dataset besar
pos = nx.spring_layout(graph, seed=42, k=0.15, iterations=20)

# gambar node dan edge
nx.draw_networkx_nodes(graph, pos, node_color='skyblue', node_size=25, alpha=0.8)
# nx.draw_networkx_edges(graph, pos, edge_color='black', arrows=False, alpha=0.3)
nx.draw_networkx_edges(graph, pos, edge_color='black', arrowstyle='->', arrowsize=12, alpha=0.3)

# label, bisa ditampilkan (resiko visualisasi ga jelas karena link panjang). aktifkan :
# nx.draw_networkx_labels(graph, pos, font_size=6, font_color='black')

plt.axis('off')
plt.show()
```

## 4. Membentuk Matriks Adjacency (A)

```{code-cell}
# note: all matriks berukuran 662 x 662, sangat besar untuk ditampilkan
# jadi ditampilkan sebagian (misalnya 30x30)
page = list(graph.nodes())[:30]
A = nx.to_numpy_array(graph, nodelist=page, dtype=int)

data_page_A = pd.DataFrame(A, index=page, columns=page)
print("Matriks Adjacency (A) [30x30]:")
display(data_page_A)
```

## 5. Membentuk Matriks Probabilitas Transisi Baris-Stokastik (P)

```{code-cell}
adj_full = nx.to_numpy_array(graph, dtype=float)
n = adj_full.shape[0]

# tangani dangling node (page tanpa link_keluar)
out_degree = adj_full.sum(axis=1)
for i in range(n):
    if out_degree[i] == 0:
        adj_full[i, :] = 1.0

# matriks transisi baris-stokastik
P = adj_full / adj_full.sum(axis=1, keepdims=True)

# tampilan sebagian matriks P
data_page_P = pd.DataFrame(P[:30, :30], columns=range(30), index=range(30))
print("Matriks Transisi (P) [30x30]:")
display(data_page_P)
```

## 6. Membentuk Matriks Kolom-Stokastik (M)

```{code-cell}
# M = Pᵀ (kolom-stokastik)
M = P.T
data_page_M = pd.DataFrame(M[:30, :30], columns=range(30), index=range(30))
print("Matriks M = Pᵀ [30x30]:")
display(data_page_M)
```

## 7. Menghitung PageRank Iteratif (Manual)

```{code-cell}
def pagerank_iteratif(M, pagelist, d=0.85, max_iter=100, tol=1e-6):
    n = M.shape[0]
    r = np.ones(n) / n
    teleport = (1 - d) / n

    for i in range(max_iter):
        r_new = d * M @ r + teleport
        # debugging aja
        indeks_top_page = np.argmax(r_new)  # indeks page dengan PageRank tertinggi
        top_page = pagelist[indeks_top_page] # page dengan score pagerank tertinggi
        print(f"Iterasi {i+1}: Page {top_page} dengan PageRank {r_new[indeks_top_page]:.6f}")
        if np.linalg.norm(r_new - r, 1) < tol:
            # debugging aja
            print(f"Konvergen setelah {i+1} iterasi")
            break
        r = r_new
    return r

print("Menghitung PageRank:")

urutan_page = list(graph.nodes())
r = pagerank_iteratif(M, pagelist=urutan_page, d=0.85, max_iter=100, tol=1e-6)
```

## 8. Menampilkan dan Menyimpan Hasil PageRank

### 1. Semua Page

```{code-cell}
score_pagerank = pd.DataFrame({
    'page': list(graph.nodes()),
    'pagerank': r
})

# mengurutkan nilai PageRank tertinggi ke terendah
data_pagerank_page = score_pagerank.sort_values(by='pagerank', ascending=False).reset_index(drop=True)
print("Data Page dan Score PageRank:")
display(data_pagerank_page)
```

```{code-cell}
data_pagerank_page.to_csv("PPW_Tugas6_LinkDalamPage_PageRank(Manual).csv", index=False)
```

### 2. 5 Page

```{code-cell}
lima_page_penting = data_pagerank_page.head(5)
print("5 Page dengan Nilai PageRank Tertinggi:")
display(lima_page_penting)
```

## 9. Perbandingan: Menghitung dan Menampilkan Hasil PageRank Menggunakan NetworkX

### 1. Semua Page

```{code-cell}
score_pagerank_networkx = nx.pagerank(graph, alpha=0.85)
data_pagerank_page_networkx = pd.DataFrame(list(score_pagerank_networkx.items()), columns=['page', 'pagerank'])

# page score pagerank tertinggi ke terendah
data_page_pagerank_networkx_urut = data_pagerank_page_networkx.sort_values(by='pagerank', ascending=False).reset_index(drop=True)
print("Perbandingan Menggunakan NetworkX - Data Page dan Score PageRank:")
display(data_page_pagerank_networkx_urut)
```

```{code-cell}
# OPSIONAL
# kalau mau simpan hasil data page dan score pagerank pembanding menggunakan NetworkX, aktifkan :
# data_page_pagerank_networkx_urut.to_csv("PPW_Tugas6_LinkDalamPage_PageRank(NetworkX).csv", index=False)
```

### 2. 5 Page

```{code-cell}
lima_page_penting_networkx = data_page_pagerank_networkx_urut.head(5)

print("5 Page dengan Nilai PageRank Tertinggi (NetworkX):")
display(lima_page_penting_networkx)
```

## 10. Visualisasi Hubungan Page Penting dengan Page Terhubung

```{code-cell}
page_penting = lima_page_penting.iloc[0]['page']
print(f"Page dengan nilai PageRank tertinggi: {page_penting}")

neighbors_out = list(graph.successors(page_penting))
neighbors_in = list(graph.predecessors(page_penting))
page_terhubung = set(neighbors_out + neighbors_in + [page_penting])
subgraph_page_terhubung = graph.subgraph(page_terhubung)
```

```{code-cell}
# Visualisasi Graph
plt.figure(figsize=(12, 9))
plt.title(f"Graph Keterhubungan Page Penting:\n{page_penting}", fontsize=14, fontweight='bold')

pos = nx.spring_layout(subgraph_page_terhubung, seed=42, k=0.3)
warna_page = ['red' if page == page_penting else 'skyblue' for page in subgraph_page_terhubung.nodes()]

nx.draw_networkx_nodes(subgraph_page_terhubung, pos, node_color=warna_page, node_size=600, alpha=0.9)
nx.draw_networkx_edges(subgraph_page_terhubung, pos, edge_color='black', arrowstyle='->', arrowsize=8, alpha=0.6)
nx.draw_networkx_labels(subgraph_page_terhubung, pos, font_size=7, font_color='black')

plt.axis('off')
plt.show()
```

```{code-cell}
# Data Keterhubungan Page Penting
print(f"Jumlah total page yang terhubung dengan page penting: {len(page_terhubung)}")

# DataFrame untuk incoming dan outgoing edges
df_in = pd.DataFrame({
    'page': neighbors_in,
    'link_keluar': [page_penting] * len(neighbors_in)
})
df_out = pd.DataFrame({
    'page': [page_penting] * len(neighbors_out),
    'link_keluar': neighbors_out
})

# gabungkan kedua arah hubungan
data_page_terhubung = pd.concat([df_in, df_out], ignore_index=True)

print(f"Daftar Page yang Terhubung dengan Page Penting:")
display(data_page_terhubung)
```

## 11. Code Tugas 6 - PageRank (Data Link Dalam Page)
- [PPW_Tugas6_LinkDalamPage_PageRank](https://colab.research.google.com/drive/1EMbIFVQ4bF0niWB1klrIFjVgnZnY3qjL?usp=sharing)
