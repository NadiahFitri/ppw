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

# **Pengantar Web Mining**

# Web Mining
Web mining adalah proses menemukan dan mengekstrak informasi dari internet menggunakan berbagai teknik penambangan data. Informasi yang dihasilkan akan berguna untuk pengambilan keputusan yang lebih efektif bagi perusahaan. Contoh penerapan web mining yaitu untuk membantu meningkatkan kualitas search engine dengan mengklasifikasi dokumen web dan mengidetifikasi halaman web. Beberapa bidang ilmu yang mendasari web mining adalah Natural Language Processing (NLP), Machine Learning, Database Systems, Social Network Analysis dan Graph Mining.




## Definisi Web Mining
Berikut adalah definisi web mining menurut beberapa ahli :
1.	Menurut Etzioni, 1996; CACM 39. Web Mining yaitu penggunaan teknik penambangan data untuk menemukan dan mengekstrak informasi dari layanan web secara otomatis.
2.	Menurut Bing Liu, 2007, Web Data Mining. Tujuan web mining yaitu menemukan pola-pola pengetahuan dari struktur hyperlink web, isi halaman web, dan perilaku pengguna.




## Tantangan Pemrosesan Data Web
Website adalah sekumpulan halaman web saling berhubungan yang umumnya berada pada peladen yang sama berisikan kumpulan informasi yang disediakan oleh individu, kelompok, atau organisasi. Ditulis sebagai berkas plain text yang diatur dan dikombinasikan dengan instruksi berbasis HTML atau XHTML. Tantangan dalam pemrosesan data web adalah web itu sangat besar, kompleks, dinamis dan bukan domain spesifik sehingga sulit untuk dilakukan penambangan data.




## Taksonomi Web Mining
### 1. Web Content Mining
Proses otomatis untuk menemukan informasi yang bermanfaat dari dokumen web dengan mengekstraksi keyword pada dokumen disebut sebagai Web Content Mining. Fokus pada konten halaman web seperti teks, gambar, audio, video, metadata dan hyperlink.


#### Aplikasi Text Mining dalam Web Content Mining
- **Ekstraksi Informasi**

Ekstraksi Informasi bertujuan mengambil informasi terstruktur dari konten web yang umumnya tidak terstruktur atau semi-terstruktur, seperti artikel, ulasan, komentar, tabel HTML, atau data yang tertanam dalam dokumen web. Ekstraksi informasi fokus pada identifikasi entitas, hubungan antar entitas, serta atribut penting dari teks. Proses ini memungkinkan data mentah yang tersebar di web diubah jadi informasi yang lebih mudah dianalisis dan digunakan kembali.

- **Topic Modelling**

Topic Modelling adalah pendekatan text mining untuk mengidentifikasi topik-topik yang muncul dalam suatu data teks berdasarkan kemiripannya tanpa harus memiliki label/kategori sebelumnya. Pendekatan ini menggunakan model statistik untuk menemukan topik dalam dataset teks, kata mana yang berkontribusi ke suatu topik, dan topik mana yang berkontribusi pada suatu dokumen. Tujuan topic modelling adalah agar tanpa membaca semua isi teks, user tetap bisa memahami isinya.

- **Peringkasan Dokumen**

Peringkasan dokumen bertujuan menghasilkan ringkasan otomatis dari dokumen sehingga hanya menyajikan informasi utama tanpa menghilangkan makna penting yang terkandung di dalam teks asli. peringkasan dokumen dapat mengatasi kelebihan informasi di web, karena dapat menyajikan konten yang ringkas, relevan, dan mudah dipahami pengguna sehingga membantu pengguna memahami isi utama dari artikel, berita, ulasan, atau laporan tanpa harus membaca seluruh isi dokumen.

- **Klasifikasi Dokumen (Sentiment Analisis, Opinion Mining)**

Dalam konteks web content mining, salah satu bentuk penerapan klasifikasi adalah sentiment analysis atau opinion mining, yaitu proses menganalisis teks untuk menentukan sikap, opini, atau emosi penulis terhadap suatu objek. Tujuan kalsifikasi dokumen agar dokumen atau gambar yang belum pernah dilihat dikategorikan dalam kelas dengan akurat. Sentiment analysis mengklasifikasikan polaritas teks (positif, negative, netral) yang diberikan pada tingkat dokumen, kalimat, atau fitur/atribut seperti ulasan. Opinion mining tidak hanya mengukur polaritas, tetapi juga menggali aspek yang menjadi perhatian, seperti kualitas layanan. Klasifikasi dokumen betujuan membantu penyedia layanan memahami persepsi pengguna dan  mendukung pengambilan keputusan berbasis data.

- **Pengelompokan Dokumen (Sistem Rekomendasi)**

Pengelompokan dokumen bertujuan mengelompokkan sekumpulan dokumen dalam beberapa kelompok berdasarkan tingkat kesamaan isi, sehingga dokumen dalam satu kelompok lebih mirip satu sama lain dibandingkan dengan dokumen dari kelompok lain. Dalam konteks sistem rekomendasi, pengelompokan dokumen berguna untuk menyajikan informasi relevan sesuai minat atau kebutuhan pengguna. Contohnya pada mesin pencari, hasil pencarian dikelompokkan ke dalam topik tertentu agar mudah dipahami pengguna.

- **Ektraksi Kata Kunci**

Ekstraksi kata kunci bertujuan mengidentifikasi kata atau frasa yang paling mewakili isi dokumen sehingga dapat digunakan sebagai ringkasan singkat atau penanda topik utama dari suatu teks. Web content mining mengekstraksi kata kunci yang terkandung pada dokumen. Dengan adanya kata kunci yang relevan, sistem dapat lebih mudah melakukan pengindeksan, pencarian, maupun rekomendasi konten yang sesuai kebutuhan pengguna.



### 2. Web Usage Mining
Web Usage Mining mengekstraksi informasi dari data yang dihasilkan pengguna saat mengakses halaman web yang ditandai melalui informasi dari log, profil pengguna, click stream, cookies, metadata dan query. Manfaat web usage mining yaitu memodifikasi halaman berdasarkan profil pengguna, mengidentifikasi ketertarikan pelanggan terhadap produk tertentu, dan menentukan target pasar yang tepat.


#### Proses Web Usage Mining
##### 1. Preprocessing
Preprocessing bertujuan untuk melakukan standarisasi data dan menghilangkan bagian–bagian data yang tidak diperlukan dalam proses mining.
- **Data Cleaning :** membersihkan file log tidak relevan dari data dengan proses mining.
- **User Identification :** proses pengidentifikasian user.
**Session Identification  :** Halaman yang diakses dibagi ke dalam sesi tertentu agar tercipta satu sesi untuk setiap user yang mengakses.
- **Path Completion :** melengkapi path yang mungkin belum lengkap karena tersimpan pada file log.
- **Transaction Identification :** mengindentifikasi sejumlah sesi tertentu agar dapat menunjukkan proses transaksi yang dilakukan oleh user.

##### 2. Pattern Discovery
Pattern Discovery adalah pencarian pola akses yang dilakukan oleh user aplikasi.
- **Statistical Analysis :** teknik untuk mendapatkan informasi atau pengetahuan dari pola akses pengguna. Contohnya pola akses pengguna dilihat dari waktu akses untuk setiap harinya.
