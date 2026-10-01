# Active Learning SVM dengan Margin Sampling untuk Analisis Sentimen YouTube MBG

Implementasi *pool-based Active Learning* (SVM + Margin Sampling, Human-in-the-Loop) untuk klasifikasi sentimen komentar YouTube terkait program Makan Bergizi Gratis (MBG). Membandingkan tiga kondisi — **AL-HITL**, **Random Sampling**, dan **Simulated AL (oracle)** — selama 15 iterasi.

📄 Paper: **Analisis Active Learning SVM berbasis Margin Sampling pada Sentimen YouTube MBG**
FAHMA: Jurnal Informatika Komputer, Bisnis dan Manajemen, Vol. 24 No. 2 (2026), Sinta 4
DOI: [10.61805/fahma.v24i2.203](https://doi.org/10.61805/fahma.v24i2.203)
Alvian Putra Hardiadi¹, Minarwati² — Informatika, STMIK El Rahma Yogyakarta

## Hasil Utama

| Kondisi | F1-Macro (baseline → akhir) | Sampel ditambahkan |
|---|---|---|
| **AL-HITL (Margin Sampling)** | 0.5154 → **0.5389** (+0.0235) | 50 (dari 75 diajukan, skip rate 33.3%) |
| Simulated AL (oracle) | 0.5154 → 0.4950 (−0.0204) | 75 |
| Random Sampling | 0.5154 → 0.4977 (−0.0177) | 75 |

AL-HITL adalah satu-satunya kondisi yang meningkat dari baseline — mekanisme *skip* pada proses HITL berfungsi implisit sebagai *quality filter* yang mencegah *noisy label* masuk ke set latih.

## Dataset

- `sample_1000_labeled.csv` — 999 komentar YouTube berlabel (seed dataset: 599 initial train, 200 oracle pool, 200 fixed test; stratified 60/20/20). Distribusi kelas: Negatif 40.7%, Netral 39.2%, Positif 20.0%.
- `clean_comments_mbg.csv` — 7,967 komentar mentah (pool tambahan, belum berlabel).

Kolom identitas komentator (`author`) telah dihapus dari kedua file sebelum dipublikasikan di repo ini. Kolom `Column1` pada `sample_1000_labeled.csv` adalah artefak/kolom QC dari proses pelabelan manual dan tidak digunakan dalam analisis.

## Metodologi Singkat

1. **Preprocessing** — lowercase, hapus URL/mention/hashtag/emoji/tanda baca/angka
2. **Representasi fitur** — TF-IDF (`max_features=5000`, `ngram_range=(1,2)`), di-*fit* hanya pada initial train set
3. **Model baseline** — SVM linear (`C=1.0`, `class_weight='balanced'`)
4. **Query strategy** — Margin Sampling (`QUERY_SIZE=5`, `N_ITER=15`), peneliti bertindak sebagai oracle (HITL) dengan opsi *skip* untuk sampel yang terlalu ambigu
5. **Evaluasi** — dibandingkan terhadap Random Sampling dan Simulated AL (oracle tanpa noise) pada fixed test set yang sama

> **Catatan:** Notebook berisi mode HITL nyata (`SIMULATION_MODE = False`), yang membutuhkan input pelabelan manual secara live saat dijalankan — bukan skrip yang bisa langsung "Run All" tanpa interaksi. Untuk eksperimen reproduksi otomatis, gunakan jalur Simulated AL.

## Keterbatasan

Penelitian ini diposisikan sebagai studi pendahuluan. Oracle pool berasal dari partisi data yang sudah berlabel tersembunyi (bukan pool yang benar-benar tidak berlabel), dan pelabelan dilakukan oleh satu annotator tunggal. Detail lengkap ada di bagian Kesimpulan pada paper.

## Cara Menjalankan

```bash
pip install -r requirements.txt
jupyter notebook AL_Sentiment_MBG_YouTube.ipynb
```

## Struktur

```
.
├── AL_Sentiment_MBG_YouTube.ipynb   # notebook utama (pipeline lengkap)
├── sample_1000_labeled.csv          # seed dataset berlabel
├── clean_comments_mbg.csv           # pool komentar mentah
├── requirements.txt
└── README.md
```

## Sitasi

Jika memanfaatkan kode atau dataset ini, mohon sitasi paper di atas.
