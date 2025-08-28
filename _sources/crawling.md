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

# Crawling

## 1 Install dan Import Library
```{code-cell}
!pip install sprynger
```

```{code-cell}
import requests
import pandas as pd
```

## 2. Menyimpan API key dan alamat API spring
```{code-cell}
api_key = "0da1ccc0af5096386b64c11f74abe242"
# meta API v2
url = "https://api.springernature.com/meta/v2/json"
```

## 3. Input kata kunci dan tampung hasil pencarian

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

## 4. Mengambil informasi dari tiap artikel

```{code-cell}
# Jumlah maksimal data per keyword
hasil_max = 50
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

                # 🔹 Tambahkan print untuk cek hasil
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

## 5. Menyimpan dan menampilkan data

```{code-cell}
# Simpan ke DataFrame
df = pd.DataFrame(rows)

# Simpan ke CSV
df.to_csv("hasil_crawling_tugas2.csv", index=False, encoding="utf-8")

print("Data berhasil disimpan ke hasil_crawling_tugas2.csv")
print(f"Total data: {len(df)}")

# Tampilkan hanya 10 data pertama
print("\n 10 data pertama:")
print(df.head(10))
```

## Link Google Colab Crawling :
- [Crawling_SpringNature_PPW](https://colab.research.google.com/drive/1tNi7P0o_g7S_EX7nTZp8Bkt_FsVZyLFi?usp=sharing)