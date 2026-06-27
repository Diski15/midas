# Panduan Interpretasi Output MIDAS

## 1. Summary Performance Estimates

Setelah menjalankan `midas tp fp fn tn, res(sum) nip(1)`, kamu akan mendapatkan tabel seperti ini:

```
SUMMARY PERFORMANCE ESTIMATES
Parameter              Estimate    95% CI
Sensitivity            0.73        [0.63, 0.80]
Specificity            0.96        [0.92, 0.97]
Positive Likelihood Ratio  16.2   [9.7, 27.3]
Negative Likelihood Ratio  0.29   [0.21, 0.39]
Diagnostic Odds Ratio  57          [30, 107]
```

### Sensitivitas & Spesifisitas
- **Sensitivitas**: proporsi kasus positif yang terdeteksi benar oleh uji → seberapa baik uji mendeteksi penyakit
- **Spesifisitas**: proporsi kasus negatif yang teridentifikasi benar → seberapa baik uji menyingkirkan penyakit

### Likelihood Ratio (LR)
| LR Positif | LR Negatif | Interpretasi |
|---|---|---|
| > 10 | < 0.1 | Sangat informatif (konfirmasi & eksklusi) |
| 5–10 | 0.1–0.2 | Cukup informatif |
| 2–5 | 0.2–0.5 | Sedikit informatif |
| < 2 | > 0.5 | Tidak informatif |

### Diagnostic Odds Ratio (DOR)
- Rasio odds hasil positif sejati terhadap hasil positif palsu
- Nilai tinggi = performa diagnostik lebih baik
- Independen dari prevalensi penyakit

---

## 2. Area Under the Curve (AUC — SROC)

| Rentang AUC | Akurasi Diagnostik |
|---|---|
| 0.5 – 0.70 | Rendah |
| 0.70 – 0.90 | Sedang |
| 0.90 – 1.00 | **Tinggi** ✓ |

Pada contoh dataset (`AUC = 0.95`): akurasi FDG-PET **tinggi**.

---

## 3. Heterogenitas (I²)

```
HETEROGENEITY STATISTICS
Inconsistency (I-square): LRT_I2 = 99, 95% CI = [98–99]
Proportion due to threshold effect = 0.11
ICC_SEN = 0.31, ICC_SPE = 0.29
```

| Nilai I² | Interpretasi |
|---|---|
| 0% | Tidak ada heterogenitas |
| 25% | Heterogenitas rendah |
| 50% | Heterogenitas sedang |
| ≥ 75% | Heterogenitas tinggi |

**Threshold effect**: proporsi heterogenitas yang disebabkan oleh variasi cut-off antar studi. Diuji dengan Spearman's rho dari korelasi logit sensitivitas dan logit spesifisitas.

---

## 4. Forest Plot

- **Kotak (box)**: estimasi performa studi individual
- **Lebar box**: ukuran sampel studi (makin lebar = sampel makin besar)
- **Error bar**: 95% confidence interval
- **Diamond (berlian)**: summary estimate keseluruhan

---

## 5. Fagan Plot (Bayes Nomogram)

Menghitung probabilitas post-test berdasarkan probabilitas pre-test dan likelihood ratio:

```
PTP = LR × pre-test probability / [(1 - pre-test probability) × (1 - LR)]
```

Contoh dengan prior = 25%:
- LR+ = 16 → Post-test probability positif = **84%**
- LR− = 0.29 → Post-test probability negatif = **9%**

---

## 6. Likelihood Ratio Matrix (Kuadran)

| Kuadran | LR+ | LR− | Makna |
|---|---|---|---|
| Kiri Atas (LUQ) | > 10 | < 0.1 | Konfirmasi **dan** eksklusi |
| Kanan Atas (RUQ) | > 10 | > 0.1 | Konfirmasi saja |
| Kiri Bawah (LLQ) | < 10 | < 0.1 | Eksklusi saja |
| Kanan Bawah (RLQ) | < 10 | > 0.1 | Tidak informatif |

---

## 7. Publication Bias (Deeks' Test)

```
Bias Coef = -3.21, p = 0.464
```

- p < 0.10 → asymmetry signifikan → kemungkinan ada bias publikasi
- p ≥ 0.10 → tidak ada bukti bias publikasi
- **Catatan**: uji ini memiliki statistical power rendah pada jumlah studi kecil

---

## Tips Pelaporan

Saat melaporkan hasil MIDAS dalam publikasi ilmiah:

1. Sebutkan model yang digunakan: *bivariate random-effects model*
2. Laporkan: sensitivitas, spesifisitas, LR+, LR−, DOR, AUC beserta 95% CI
3. Laporkan statistik heterogenitas: I², Cochran Q, p-value
4. Jika ada heterogenitas substansial, lakukan dan laporkan meta-regresi
5. Sertakan SROC curve dan Fagan plot untuk utilitas klinis
6. Laporkan hasil Deeks' test untuk bias publikasi
