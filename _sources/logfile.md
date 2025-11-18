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

# **LogFile**

# Tugas 7 - Analisis Log File (Data Web Usage Mining)

## 1. Import Library

```{code-cell}
import pandas as pd
import numpy as np
from datetime import datetime, timedelta
```

## 2. Load Dataset

### 1. Baca Data

```{code-cell}
nama_file = 'web_usage_mining.csv'
datalog_web = pd.read_csv(nama_file)
```

```{code-cell}
display(datalog_web)
```

```{code-cell}
datalog_web.info()
```

## 3. Filter Data

```{code-cell}
# request method = GET
# Status = 200
# halaman .html
filter_datalog_web = datalog_web[
    (datalog_web['Request method'] == 'GET') &
    (datalog_web['Status'] == 200) &
    (datalog_web['Request URI'].str.endswith('.html'))
].copy()  # penting untuk menghindari SettingWithCopyWarning
```

```{code-cell}
display(filter_datalog_web)
```

## 4. Konversi Waktu ke Format Datetime

```{code-cell}
filter_datalog_web['Request time'] = pd.to_datetime(
    filter_datalog_web['Request time'],
    errors='coerce',
    utc=True,
    infer_datetime_format=True
)

# Hapus data yang gagal dikonversi (NaT)
datalog_web_bersih = filter_datalog_web.dropna(subset=['Request time'])
```

```{code-cell}
# Urutkan data berdasarkan IP dan waktu
datalog_web_sorted = datalog_web_bersih.sort_values(by=['Remote host', 'Request time']).reset_index(drop=True)
```

```{code-cell}
display(datalog_web_sorted)
```

## 5. Sessionization (Identifikasi Sesi User)

### 1. Sesi Setiap IP

```{code-cell}
session_timeout = timedelta(minutes=30)
datalog_web_sorted['Session Number'] = 0
datalog_web_sorted['Session ID'] = ""

# Hitung sesi per IP berdasarkan selisih waktu antar akses
for ip, group in datalog_web_sorted.groupby('Remote host'):
    group = group.sort_values('Request time').copy()
    waktu_akses = group['Request time'].diff().fillna(pd.Timedelta(seconds=0))

    # Jika selisih waktu antar akses > 30 menit → sesi baru
    jumlah_sesi = (waktu_akses > session_timeout).cumsum() + 1

    # Simpan hasil sesi ke dataframe utama
    datalog_web_sorted.loc[group.index, 'Session Number'] = jumlah_sesi
    datalog_web_sorted.loc[group.index, 'Session ID'] = ip + "_S" + jumlah_sesi.astype(str)

# jumlah sesi = integer
datalog_web_sorted['Session Number'] = datalog_web_sorted['Session Number'].astype(int)
```

```{code-cell}
display(datalog_web_sorted)
```

### 2. Total Sesi Setiap IP

```{code-cell}
total_sesi = (
    datalog_web_sorted.groupby('Remote host')['Session ID']
    .nunique()
    .reset_index()
    .rename(columns={'Session ID': 'Total Sessions'})
)

datalog_web_baru = pd.merge(datalog_web_sorted, total_sesi, on='Remote host', how='left')
```

```{code-cell}
# Urutkan berdasarkan jumlah sesi terbanyak
datalog_web_baru = datalog_web_baru.sort_values(
    by=['Total Sessions', 'Remote host', 'Request time'],
    ascending=[False, True, True]
).reset_index(drop=True)
```

```{code-cell}
display(datalog_web_baru)
```

## 6. Kunjungan Web

```{code-cell}
# kunjungan 0/1
kunjungan = (
    datalog_web_baru.groupby(['Remote host', 'Request URI'])
    .size()
    .unstack(fill_value=0)
)
kunjungan = (kunjungan > 0).astype(int)  # ubah jadi 0/1 (boolean visit)
```

```{code-cell}
# Durasi antar halaman dalam menit
datalog_web_baru['Next Time'] = datalog_web_baru.groupby(['Remote host', 'Session Number'])['Request time'].shift(-1)
datalog_web_baru['Duration (minutes)'] = (datalog_web_baru['Next Time'] - datalog_web_baru['Request time']).dt.total_seconds() / 60
datalog_web_baru['Duration (minutes)'] = datalog_web_baru['Duration (minutes)'].fillna(30)  # default 5 menit untuk halaman terakhir
```

```{code-cell}
# Durasi kunjungan
durasi_kunjungan = datalog_web_baru.pivot_table(
    index='Remote host',
    columns='Request URI',
    values='Duration (minutes)',
    aggfunc='sum',
    fill_value=0
)
```

## 7. Datalog Filter + Durasi

```{code-cell}
# data
kolom_datalog_web = []
for page in kunjungan.columns:
    kolom_datalog_web.append(page)
    kolom_datalog_web.append(f"Lama waktu akses di {page} (menit)")

datalog = pd.DataFrame(index=kunjungan.index)

for page in kunjungan.columns:
    datalog[page] = kunjungan[page]
    datalog[f"Lama waktu akses di {page} (menit)"] = durasi_kunjungan[page]

datalog = datalog.reset_index()
```

```{code-cell}
# Tambahkan Total Sessions dari data utama
total_sesi_ip = datalog_web_baru[['Remote host', 'Total Sessions']].drop_duplicates()
datalog = datalog.merge(total_sesi_ip, on='Remote host', how='left')
```

```{code-cell}
# Urutkan berdasar Total Sessions
datalog = datalog.sort_values(by='Total Sessions', ascending=False).reset_index(drop=True)
```

```{code-cell}
# Datalog web sebelum pengelompokkan user
display(datalog)
```

```{code-cell}
# Datalog web setelah pengelompokkan user

# Buat mapping IP unik ke User ID
ip_user = {ip: f"User {i+1}" for i, ip in enumerate(datalog['Remote host'].unique())}

# Tambahkan kolom 'User' berdasarkan mapping
datalog['User'] = datalog['Remote host'].map(ip_user)

# Urutkan supaya kolom 'User' muncul di paling kiri
kolom = ['User'] + [col for col in datalog.columns if col != 'User']
datalog_web_final = datalog[kolom]

display(datalog_web_final)
```

```{code-cell}
# simpan ke csv
datalog_web_final.to_csv('PPW_Tugas7_LogFile(WebUsageMining).csv', index=False)
```

## 8. Code Tugas 7 - LogFile (Data Web Usage Mining)
- [PPW_Tugas7_LogFile(WebUsageMining)](https://colab.research.google.com/drive/1MU92FmLjoEgAHjWQFGAYzQQXsj2djZPi?usp=sharing)


# Tugas 7 - Analisis LogFile (Data Nasa Bulan Juli)

## 1. Import Library

```{code-cell}
import re
import pandas as pd
from IPython.display import display
```

## 2. Load Dataset

### 1. Baca Data

```{code-cell}
nama_file = "access_log_Jul95"
```

```{code-cell}
with open(nama_file, 'r', encoding='latin-1') as f:
    datalog_nasa = f.readlines()
```

```{code-cell}
display(datalog_nasa[:5])
```

## 2. Parse Data

```{code-cell}
log_pattern = re.compile(r'(\S+) - - \[(.*?)\] "(\S+) (.*?) (\S+)" (\S+) (\S+)')
parsed_data = []

for baris in datalog_nasa:
    match = log_pattern.match(baris)
    if match:
        hostname, timestamp, request_type, path, protocol, status_code, size = match.groups()
        parsed_data.append([hostname, timestamp, request_type, path, status_code, size])
    else:
        # Optional: Log lines that don't match the pattern
        # print(f"Skipping line: {line.strip()}")
        pass

display(parsed_data[:5])
```

## 3. DataFrame

```{code-cell}
df_lognasa = pd.DataFrame(parsed_data, columns=['hostname', 'timestamp', 'request_type', 'path', 'status_code', 'size'])
display(df_lognasa)
```

```{code-cell}
df_lognasa.info()
```

## 3. Jumlah IP Unik

```{code-cell}
jumlah_ip_unik = df_lognasa["hostname"].value_counts()
print("Jumlah kemunculan setiap IP:")
display(jumlah_ip_unik.head(10))  # 10 IP teratas
```

## 4. Konversi Waktu ke Datetime

```{code-cell}
df_lognasa['timestamp'] = pd.to_datetime(df_lognasa['timestamp'], format='%d/%b/%Y:%H:%M:%S %z')
# urut berdasar host dan waktu
df_sorted = df_lognasa.sort_values(by=['hostname', 'timestamp'])
display(df_sorted.head())
```

## 5. Jumlah request_type Unik

```{code-cell}
jumlah_request_unik = df_lognasa['request_type'].value_counts()
print("Nilai unik dalam kolom 'request_type' beserta jumlahnya:")
display(jumlah_request_unik)
```

## 6. Jumlah status_code Unik

```{code-cell}
jumlah_status_unik = df_lognasa['status_code'].value_counts()
print("Nilai unik dalam kolom 'status_code' beserta jumlahnya:")
display(jumlah_status_unik)
```

## 7. Filter Data

```{code-cell}
filter_datalog_nasa = df_lognasa[(df_lognasa['request_type'] == 'GET') & (df_lognasa['path'].str.endswith('.html')) & (df_lognasa['status_code'] == '200')]
display(filter_datalog_nasa)
```

## 8. Urutkan Hostname Yang Sama Berdasarkan Waktu

```{code-cell}
datalog_nasa_sorted = filter_datalog_nasa.sort_values(by=['hostname', 'timestamp'])
display(datalog_nasa_sorted)
```

```{code-cell}
datalog_nasa_sorted.to_csv('PPW_Tugas7_LogFile(Nasa).csv', index=False)
```

## 9. Code Tugas 7 - LogFile (Data NASA)
- [PPW_Tugas7_LogFile(NASA)](https://colab.research.google.com/drive/1jvt713uUrpHqrMaXU0J-SyJgggCsyf_h?usp=sharing)