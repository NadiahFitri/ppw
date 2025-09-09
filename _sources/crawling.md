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

# **Crawling**

# Tugas 1 (Crawling SpringerNature)

## 1. Install dan Import Library
```{code-cell}
!pip install sprynger
```

```{code-cell}
import requests
import pandas as pd
```

## 2. Menyimpan API Key dan Alamat API
```{code-cell}
api_key = "0da1ccc0af5096386b64c11f74abe242"
# meta API v2
url = "https://api.springernature.com/meta/v2/json"
```

## 3. Input Kata Kunci dan Tampung Hasil Pencarian

```{code-cell}
# Kata kunci
keywords = [
    "web mining",
    "web usage mining",
    "web content mining",
    "web structure mining"
]

rows = []
```

## 4. Mengambil Informasi Dari Tiap Artikel

```{code-cell}
# Jumlah maksimal data per keyword
hasil_max = 100
per_page = 10
```

```{code-cell}
for keyword in keywords:
    print(f"\n🔎 Sedang crawling keyword: {keyword}")

    for start in range(1, hasil_max, per_page):
        params = {
            "q": keyword,
            "api_key": api_key,
            "p": per_page,
            "s": start
        }

        response = requests.get(url, params=params)

        if response.status_code == 200:
            data = response.json()

            # ambil informasi artikel
            for record in data.get('records', []):

                doi = record.get("doi", "N/A")
                title = record.get("title", "No title")
                abstract = record.get("abstract", "No abstract")
                # publicationName = record.get("publicationName", "N/A")
                # publicationDate = record.get("publicationDate", "N/A")
                # publisher = record.get("publisher", "N/A")
                # keywords_article = "; ".join(record.get("keyword", [])) if record.get("keyword") else "N/A"

                # keyword artikel list
                # ubah jadi string
                keywords_article = "; ".join(record.get("keyword", [])) if record.get("keyword") else "N/A"

                # url (list of dict → ambil HTML)
                url_list = record.get("url", [])
                if url_list and isinstance(url_list, list):
                    url_article = url_list[0].get("value", "N/A")
                else:
                    url_article = "N/A"

                # Tambahkan print untuk cek hasil
                print(f"DOI: {doi}")
                print(f"Title: {title}")
                print(f"Abstract: {abstract}\n")
                # print(f"Keywords: {keywords_article}")

                rows.append({
                    "search_keyword": keyword,
                    "doi": doi,
                    "title": title,
                    "abstract": abstract,
                    # "publicationName": publicationName,
                    # "publicationDate": publicationDate,
                    # "publisher": publisher,
                    # "keywords": keywords_article,
                    # "url": url_article
                })
        else:
            print("Error:", response.status_code, response.text)
```

## 5. Menyimpan dan Menampilkan Data

```{code-cell}
# Simpan ke DataFrame
df = pd.DataFrame(rows)

# Simpan ke CSV
df.to_csv("PPW_HasilCrawling_Tugas1(SpringerNature).csv", index=False, encoding="utf-8")

print("Data berhasil disimpan ke PPW_HasilCrawling_Tugas1(SpringerNature).csv")
print(f"Total data: {len(df)}")

# Tampilkan hanya 10 data pertama
print("\n 10 data pertama:")
print(df.head(10))
```

## 6. Code Crawling Tugas 1 (SpringerNature)
- [PPW_Crawling_Tugas1(SpringerNature)](https://colab.research.google.com/drive/1tNi7P0o_g7S_EX7nTZp8Bkt_FsVZyLFi?usp=sharing)



# Tugas 2 (A. Crawling PTA Trunojoyo)

## 1. Install dan Import Library

```{code-cell}
!pip install builtwith
```

```{code-cell}
!pip install requests
```

```{code-cell}
!pip install beautifulsoup4
```

```{code-cell}
import builtwith
import requests
from bs4 import BeautifulSoup
import pandas as pd
import os
from IPython.display import display
from urllib.parse import urljoin, urlparse
```

## 2. Analisis Teknologi Web PTA Trunojoyo

```{code-cell}
res = builtwith.parse('https://pta.trunojoyo.ac.id/')
print(res)
```

## 3. Menampilkan ID Semua Prodi

```{code-cell}
def pta_IDprodi2(url):
    try:
        response = requests.get(url)
        response.raise_for_status()  # Raise an error for bad status codes

        soup = BeautifulSoup(response.content, 'html.parser')

        # Ambil semua judul h1, h2, h3
        headings = soup.find_all(['h1', 'h2', 'h3'])
        for heading in headings:
            print(f"{heading.name}: {heading.get_text()}")

        # Ambil semua link
        links = soup.find_all('a', href=True)
        for link in links:
            href = link['href']
            text = link.get_text().strip()

            # Filter: hanya link yang ada '/c_search/byprod/'
            if "/c_search/byprod/" in href:
                print(f"URL: {href} | Teks: {text}")


    except requests.exceptions.RequestException as e:
        print(f"Terjadi kesalahan saat mengakses {url}: {e}")

# Gunakan fungsi
pta_IDprodi2("https://pta.trunojoyo.ac.id/welcome/index/2")
```

## 4. Mengambil Data Prodi

```{code-cell}
def daftar_prodi(url="https://pta.trunojoyo.ac.id/welcome/index/2"):
    mapping = {}
    try:
        response = requests.get(url)
        response.raise_for_status()
        soup = BeautifulSoup(response.content, "html.parser")

        links = soup.find_all("a", href=True)
        for link in links:
            href = link["href"]
            text = link.get_text().strip()

            if "/c_search/byprod/" in href:
                try:
                    prodi_id = int(href.split("/")[-1])
                    mapping[prodi_id] = text
                except:
                    continue
    except requests.exceptions.RequestException as e:
        print(f"Terjadi kesalahan saat ambil data prodi: {e}")

    return mapping
```

```{code-cell}
data_prodi = daftar_prodi(url="https://pta.trunojoyo.ac.id/welcome/index/2")
```

```{code-cell}
print(data_prodi)
```

## 5. Mengambil dan Menyimpan Data Karya Ilmiah

```{code-cell}
def pta_KaryaIlmiah2():
    data = {
        "no": [],
        "id_prodi": [],
        "nama_prodi": [],
        "penulis": [],
        "judul": [],
        "pembimbing_pertama": [],
        "pembimbing_kedua": [],
        "abstrak_bindonesia": [],
        "abstrak_binggris": []
    }

    # Ambil mapping prodi_id -> nama_prodi
    prodi_mapping = daftar_prodi()

    nomor = 1

    for prodi_id, nama_prodi in prodi_mapping.items():
        page = 1
        while True:
            url = f"https://pta.trunojoyo.ac.id/c_search/byprod/{prodi_id}/{page}"
            r = requests.get(url)
            soup = BeautifulSoup(r.content, "html.parser")
            jurnals = soup.select('li[data-cat="#luxury"]')

            if not jurnals:
                break

            for jurnal in jurnals:
                detail_url = jurnal.select_one("a.gray.button")["href"]
                response = requests.get(detail_url)
                soup1 = BeautifulSoup(response.content, "html.parser")

                isi = soup1.select_one("div#content_journal")
                judul = isi.select_one("a.title").text.strip()
                penulis = isi.select_one('span:contains("Penulis")').text.split(" : ")[1].strip()
                pembimbing_pertama = isi.select_one('span:contains("Dosen Pembimbing I")').text.split(" : ")[1].strip()
                pembimbing_kedua = isi.select_one('span:contains("Dosen Pembimbing II")').text.split(" :")[1].strip()

                # Ambil abstrak
                abstrak_paragraf = isi.find_all("p", align="justify")
                abstrak_bindonesia = abstrak_paragraf[0].get_text(strip=True) if len(abstrak_paragraf) > 0 else ""
                abstrak_binggris = abstrak_paragraf[1].get_text(strip=True) if len(abstrak_paragraf) > 1 else ""

                # Simpan ke data
                data["no"].append(nomor)
                data["id_prodi"].append(prodi_id)
                data["nama_prodi"].append(nama_prodi)
                data["penulis"].append(penulis)
                data["judul"].append(judul)
                data["pembimbing_pertama"].append(pembimbing_pertama)
                data["pembimbing_kedua"].append(pembimbing_kedua)
                data["abstrak_bindonesia"].append(abstrak_bindonesia)
                data["abstrak_binggris"].append(abstrak_binggris)

                nomor += 1

            print(f"Selesai ambil data Prodi {prodi_id}, Halaman {page}")
            page += 1

    df = pd.DataFrame(data)
    df.to_csv("PPW_HasilCrawling_Tugas2(PTATrunojoyo)_2.csv", index=False, encoding="utf-8-sig")
    return df
```

```{code-cell}
data_KaryaIlmiah = pta_KaryaIlmiah2()
```

## 6. Menampilkan Data Karya Ilmiah

```{code-cell}
# Drop kolom nomor, id_prodi, nama_prodi
tampilan_data = data_KaryaIlmiah.drop(columns=["no", "id_prodi", "nama_prodi"])
```

```{code-cell}
# Tampilan 10 data acak
display(tampilan_data.sample(10))
```

## 7. Code Crawling Tugas 2 (PTATrunojoyo)
- [PPW_Crawling_Tugas2(PTATrunojoyo)](https://colab.research.google.com/drive/1dd-SvzsoY2na8kmybPfOLlRWAnIWU_vB?usp=sharing)




# Tugas 2 (B. Crawling Berita Online)

## 1. Install dan Import Library

```{code-cell}
!pip install builtwith
```

```{code-cell}
!pip install requests
```

```{code-cell}
!pip install beautifulsoup4
```

```{code-cell}
!pip install matplotlib
```

```{code-cell}
!pip install networkx
```

```{code-cell}
!pip install pandas
```

```{code-cell}
import builtwith
import requests
from bs4 import BeautifulSoup
import pandas as pd
import os
from IPython.display import display
from urllib.parse import urljoin, urlparse
from urllib.request import urlopen
from tqdm import tqdm
import time
```

## 2. Analisis Teknologi Web Kompas

```{code-cell}
res = builtwith.parse('https://www.kompas.com/')
print(res)
```

## 3. Mengambil Link Yang Akan Dicrawling

```{code-cell}
# mengambil link yang akan dicrawling
def ekstrak_link(url):
    html = urlopen(url).read()
    soup = BeautifulSoup(html, 'html.parser')

    urls = soup.find_all("a", {"class": "paging__link"})
    urls = [url.get('href') for url in urls]

    return urls
```

## 4. Mengambil Isi Berita

```{code-cell}
# mengambil isi dari berita
def ambil_IsiBerita(url):
    html = urlopen(url).read()
    soup = BeautifulSoup(html, 'html.parser')

    div = soup.find("div", {"class": "read__content"})
    paragraph = div.find_all("p")

    isi = ''
    for p in paragraph:
        isi += p.text

    return isi
```

## 5. Mengambil dan Menyimpan Data Berita

```{code-cell}
# menampung hasil berita
hasil_berita = []

link = "https://indeks.kompas.com"

# mengetahui jumlah halaman terakhir indeks kompas
# indeks_akhir = ekstrak_link(link).pop()
# mengambil data berita otomatis sampai halaman akhir
# jumlah_halaman = indeks_akhir.split('=').pop()

# halaman manual agar jumlah data tidak terlalu besar
# 1 halaman = 15 artikel
jumlah_halaman = 134   # 134 = 2000 berita

urls = [link + '/?page=' + str(a) for a in range(1, int(jumlah_halaman) + 1)]

for nomor_halaman, url in enumerate(urls):
    html = urlopen(url).read()
    soup = BeautifulSoup(html, 'html.parser')

    # ambil data
    link_berita = soup.find_all("a", {"class": "article-link"})
    judul_berita = soup.find_all("h2", {"class": "articleTitle"})
    tanggal_berita = soup.find_all("div", {"class": "articlePost-date"})
    kategori_berita = soup.find_all("div", {"class": "articlePost-subtitle"})

    berita_per_halaman = len(link_berita)

    for elem in tqdm(range(berita_per_halaman)):
        berita = {}
        berita['id_berita'] = berita_per_halaman * nomor_halaman + (elem + 1)
        berita['judul_berita'] = judul_berita[elem].text
        berita['isi_berita'] = ambil_IsiBerita(link_berita[elem].get("href"))
        berita['tanggal_berita'] = tanggal_berita[elem].text
        berita['kategori_berita'] = kategori_berita[elem].text

        hasil_berita.append(berita)

        # jeda 1 detik tiap ambil berita
        time.sleep(1)

    # jeda tambahan setelah selesai 1 halaman
    time.sleep(3)
```

```{code-cell}
data_berita = pd.DataFrame(hasil_berita)
data_berita.to_csv("PPW_HasilCrawling_Tugas2(BeritaOnline).csv", index=False)
```

## 6. Menampilkan Data Berita

```{code-cell}
# drop kolom 'tanggal_berita'
tampilan_berita = data_berita.drop(columns=['tanggal_berita'])
```

```{code-cell}
# menampilkan 10 data acak
display(tampilan_berita.sample(10))
```

## 7. Code Crawling Tugas 2 (Berita Online)
- [PPW_Crawling_Tugas2(BeritaOnline)](https://colab.research.google.com/drive/1cfXg2YK3tVfojMbbY2v2AS8ppHgRPD54?usp=sharing)




# Tugas 2 (C. Crawling Link Dalam Page)

## 1. Install dan Import Library

```{code-cell}
!pip install builtwith
```

```{code-cell}
!pip install requests
```

```{code-cell}
!pip install beautifulsoup4
```

```{code-cell}
import builtwith
import requests
from bs4 import BeautifulSoup
import pandas as pd
import os
from IPython.display import display
from urllib.parse import urljoin, urlparse
```

```{code-cell}
import networkx as nx
import matplotlib.pyplot as plt
```

## 2. Analisis Teknologi Web ITS

```{code-cell}
res = builtwith.parse('https://www.its.ac.id/')
print(res)
```

## 3. Crawling Link Dalam Page

```{code-cell}
def crawling_website(link_awal, batas_halaman=50):
    sudah_dikunjungi = set()   # halaman yang sudah dikunjungi
    hasil = []                 # tempat menyimpan hasil crawling

    # ambil domain utama pakai netloc
    domain_utama = urlparse(link_awal).netloc.replace("www.", "")
    antrian = [link_awal]      # halaman awal untuk dimasukkan ke antrian

    nomor_id = 1

    while antrian and len(sudah_dikunjungi) < batas_halaman:
        link_sekarang = antrian.pop(0)

        if link_sekarang in sudah_dikunjungi:
            continue
        sudah_dikunjungi.add(link_sekarang)

        try:
            respon = requests.get(link_sekarang, timeout=10)
            respon.raise_for_status()
            halaman = BeautifulSoup(respon.content, "html.parser")

            # cari semua link di halaman
            semua_link = halaman.find_all("a", href=True)
            for tag_link in semua_link:
                href = tag_link["href"]
                link_lengkap = urljoin(link_sekarang, href)  # ubah jadi absolute URL
                link_terurai = urlparse(link_lengkap)

                # cek apakah domain sama persis dengan domain utama (bukan subdomain)
                if link_terurai.scheme in ["http", "https"]:
                    domain_link = link_terurai.netloc.replace("www.", "")
                    if domain_link == domain_utama:
                        # simpan ke hasil
                        hasil.append({
                            "id": nomor_id,
                            "page": link_sekarang,
                            "link_keluar": link_lengkap
                        })
                        nomor_id += 1

                        # masukkan ke antrian kalau belum pernah dikunjungi
                        if link_lengkap not in sudah_dikunjungi and link_lengkap not in antrian:
                            antrian.append(link_lengkap)

        except requests.exceptions.RequestException:
            # Lewati halaman yang error tanpa warning
            continue

    # simpan hasil ke CSV
    data_tabel = pd.DataFrame(hasil)
    data_tabel.to_csv("PPW_HasilCrawling_Tugas2(LinkDalamPage).csv", index=False, encoding="utf-8-sig")
    return data_tabel
```

```{code-cell}
tabel_hasil = crawling_website("https://www.its.ac.id/", batas_halaman=50)
display(tabel_hasil.head(10))
```

## 4. Graph Keterhubungan Antar Website

```{code-cell}
def Graph_Crawling(data_tabel):
    # Buat graph kosong
    G = nx.DiGraph()  # graf berarah

    # node & edge dari hasil crawling
    for _, baris in data_tabel.iterrows():
        halaman = baris["page"]
        link_keluar = baris["link_keluar"]

        G.add_node(halaman)
        G.add_node(link_keluar)
        G.add_edge(halaman, link_keluar)  # page -> link keluar

    return G
```

```{code-cell}
# graph dari hasil crawling
graf = Graph_Crawling(tabel_hasil)

# Gambar graph
plt.figure(figsize=(14, 10))
pos = nx.spring_layout(graf, k=0.3, seed=42)  # layout posisi node

nx.draw_networkx_nodes(graf, pos, node_size=200, node_color="green")
nx.draw_networkx_edges(graf, pos, arrowstyle="->", arrowsize=15, edge_color="black")
# kalau mau nampilkan link website, aktifkan :
# nx.draw_networkx_labels(graf, pos, font_size=8, font_family="sans-serif")

plt.title("Graph Keterhubungan Website (its.ac.id)", fontsize=14)
plt.axis("off")
plt.show()
```

## 5. Code Crawling Tugas 2 (Link Dalam Page)
- [PPW_Crawling_Tugas2(LinkDalamPage)](https://colab.research.google.com/drive/1jnj9QSYg4koMPHPKKtONW0zuVp0AtLvf?usp=sharing)