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
- **Session Identification  :** Halaman yang diakses dibagi ke dalam sesi tertentu agar tercipta satu sesi untuk setiap user yang mengakses.
- **Path Completion :** melengkapi path yang mungkin belum lengkap karena tersimpan pada file log.
- **Transaction Identification :** mengindentifikasi sejumlah sesi tertentu agar dapat menunjukkan proses transaksi yang dilakukan oleh user.


##### 2. Pattern Discovery
Pattern Discovery adalah pencarian pola akses yang dilakukan oleh user aplikasi.
- **Statistical Analysis :** teknik untuk mendapatkan informasi atau pengetahuan dari pola akses pengguna. Contohnya pola akses pengguna dilihat dari waktu akses untuk setiap harinya.
- **Association Rules :** Output yang dihasilkan berupa pola akses terhadap halaman-halaman web. Dari pola tersebut dapat diketahui halaman mana saja yang selalu diakses secara bersamaan oleh user.
- **Clustering :** teknik mengelompokan sekumpulan objek fisik maupun abstrak ke dalam kelas tertentu berdasarkan kesamaannya.
- **Classification :** proses pengelompokan berdasarkan kelas yang sudah didefinisikan sebelumnya.
- **Sequential Pattern :** teknik analisis pola urutan akses halaman web oleh user.
- **Dependency Modeling :** teknik yang mencari hubungan antara satu variabel dengan variabel yang lain dalam web untuk prediksi pola di masa depan.


##### 3. Pattern Analysis
Pattern Analysis adalah proses visualisasi hasil analisis pola yang telah dilakukan. Dari hasil visualisasi ini, dapat dibuat suatu keputusan seperti mengubah tampilan situs web, mengoptimalkan navigasi website, atau meningkatkan kemampuan website dengan melakukan caching halaman-halaman tertentu yang sering dikunjungi.



#### Clickstream Analysis
Analisis Clickstream adalah ruang lingkup untuk analisis frekuensi dalam kumpulan data kunjungan atau analisis pola penggunaan. Clickstream adalah catatan perilaku pelanggan di Internet, termasuk setiap situs dan setiap halaman web dari setiap situs web yang dikunjungi pengguna, berapa lama pengguna berada di sebuah halaman atau situs, dalam struktur apa situs tersebut diperiksa, setiap grup berita yang diikuti pengguna, dan bahkan alamat e-mail dari surat yang dikirim dan diterima pengguna.



#### Aplikasi Web Usage Mining
- Rekomendasi produk : fitur yang memprediksi dan merekomendasikan produk paling mungkin diminati oleh seorang pengguna, berdasarkan data seperti perilaku, preferensi, atau kesamaan dengan pengguna lain.
- Pencarian yang dipersonalisasi : pencarian informasi di mana hasil pencarian disesuaikan dengan preferensi, perilaku, dan konteks pengguna individu.
- Clickstream analysis : ruang lingkup untuk analisis frekuensi dalam kumpulan data kunjungan atau analisis pola penggunaan (6). 
- User profiling : data interaksi web dapat digunakan untuk membentuk profil pengguna, yang mencakup preferensi, minat, dan kebiasaan. Profil ini bermanfaat bagi perusahaan untuk segmentasi pelanggan dan optimalisasi strategi pemasaran.




### 3. Web Structure Mining
Web Structure Mining adalah teknik  menemukan struktur link dari hyperlink supaya mengetahui keterkaitan antara suatu halaman web dengan halaman web lainnya, kemudian akan digunakan untuk membangun rangkuman website dan halaman web.



#### Sumber Graph Web
- Pencarian web (web crawls) termasuk halaman HTML dan tautan (hyperlinks),
- Jaringan sosial yang mencakup hubungan eksplisit antar pengguna (contoh: jaringan teman di Facebook),
- Jenis data komunitas lainnya (forum diskusi, percakapan email, dan lainnya).



#### Aplikasi Web Structure Mining
Grafik Web adalah representasi visual dari halaman web sebagai simpul (node) dan hyperlink sebagai sisi (edge). Simpul (node) merepresentasikan halaman web atau aktor dalam jejaring sosial, sedangkan sisi (edge) menggambarkan hubungan atau tautan antar simpul. Identifikasi simpul penting bertujuan menemukan node yang memiliki peran dominan atau pengaruh besar dalam jaringan. Konsep ini erat kaitannya dengan centrality, yaitu ukuran yang digunakan untuk menilai sejauh mana sebuah node menempati posisi strategis dalam jaringan. Simpul penting berguna untuk mencari halaman penting (seperti situs berita utama), mengidentifikasi pengguna influencer di media social dan mendeteksi sumber informasi terpercaya. Kegunaan analisis struktur web diantaranya menentukan otoritas halaman (seperti PageRank), mendeteksi komunitas online, menganalisis jaringan sosial digital.



#### Implementasi Web Structure Mining
- Google Search: Menggunakan PageRank untuk menentukan urutan hasil pencarian.
- Twitter/Instagram: Menampilkan akun influencer pada rekomendasi.
- Analisis Jaringan Sosial: Mencari pemimpin komunitas atau penyebar berita palsu.




## Proses Web Mining
- **Data Gathering and Exploration**

  Tahap pengumpulan data dari web melalui web crawling, penggunaan web APIs, atau ekstraksi langsung dari dokumen web. Setelah data terkumpul, dilakukan eksplorasi untuk memahami data, ringkasan statistic data web dan visualisasi data agar pola awal lebih mudah diamati.

- **Preprocessing and Transformation**

  Pembersihan dan tranformasi data agar sesuai metode analisis. Tahapannya meliputi reduksi dimensi, seleksi fitur, diskritisasi, binarisasi, serta transformasi teks ke dalam bentuk vektor (embeddings).

- **Actual Data Mining**

  Tahap membangun model dan menemukan pola dari data yang telah di proses sebelumnya. Berbagai metode data mining dapat diterapkan, seperti klasifikasi, clustering, asosiasi, maupun prediksi. Prosesnya iteratif, karena memerlukan eksperimen hyperparameter, perbaikan praproses, dan peningkatan jumlah serta kualitas data latih.

- **Evaluation and Interpretation**

  Tahap evaluasi hasil model atau pola yang ditemukan untuk memastikan model dan data layak, valid, bermanfaat, dan sesuai dengan tujuan analisis. Evaluasi dapat dilakukan dengan pengukuran akurasi, cross validation, atau analisis kualitas pola.





# Text Mining
Text mining adalah penemuan informasi baru dan berkualitas oleh komputer dengan secara otomatis mengekstrak informasi dari sumber-sumber yang berbeda. Kunci dari proses ini adalah menggabungkan informasi yang berhasil diekstraksi dari berbagai sumber.

## Tugas Text Mining
- Ekstraksi Informasi (Information Extraction) : Idetifikasi frasa kunci dan hubungan dalam teks dengan melihat urutan tertentu melalui pencocokan pola.
- Pelacakan Topik (Toping Tracking) : Penentuan dokumen lain yang menarik pengguna berdasarkan profil dan dokumen yang dilihat pengguna tersebut.
- Perangkuman (Summarization) : Pembuatan rangkuman dokumen.
- Kategorisasi (Categorization) : Penentuan tema suatu teks dan pengelompokan teks ke dalam kategori yang telah ditentukan berdasarkan tema tersebut.
- Penggugusan (Clustering) : Pengelompokan dokumen yang serupa tanpa penentuan kategori sebelumnya.
- Penjawaban Pertanyaan (Question Answering) : Pencocokan pola berdasarkan pengetahuan untuk memberikan jawaban terbaik terhadap suatu pertanyaan (7).