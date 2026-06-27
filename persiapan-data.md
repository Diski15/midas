# Panduan Persiapan Data untuk MIDAS

## Format Data yang Diperlukan

MIDAS memerlukan data dalam format **rectangular (wide)** — satu baris per studi — dengan minimal 4 variabel dari tabel 2×2:

```
+------------------+-------------------+-------------------+
|                  | Reference Test +  | Reference Test -  |
+------------------+-------------------+-------------------+
| Index Test +     | a = True Positive | c = False Negative|
| Index Test -     | b = False Positive| d = True Negative |
+------------------+-------------------+-------------------+
```

## Contoh Data Minimum

```stata
input str13 author int year int tp fp fn tn
"StudyA"  2010  45  5   10  90
"StudyB"  2012  30  3   8   75
"StudyC"  2015  60  8   15  120
end
```

## Variabel Tambahan yang Dianjurkan

| Variabel | Tipe | Keterangan |
|---|---|---|
| `author` | string | Nama penulis pertama |
| `year` | integer | Tahun publikasi |
| `sampsize` | integer | Total sampel per studi |
| `pmet` | float | Prevalensi penyakit (0–1) |

## Variabel QUADAS (Quality Assessment)

Variabel biner (0/1 atau Yes/No) untuk setiap item QUADAS:

| Variabel | Item QUADAS |
|---|---|
| `prodesign` | Desain prospektif |
| `fulverif` | Full verification of test results |
| `testdescr` | Deskripsi index test memadai |
| `refdescr` | Deskripsi reference test memadai |
| `clindescr` | Informasi klinis tersedia |
| `report` | Pelaporan hasil memadai |
| `spectrum` | Spektrum penyakit representatif |
| `blindref` | Blinded reference test interpretation |
| `blindtest` | Blinded index test interpretation |

## Menangani Zero Cells

Beberapa studi mungkin memiliki sel nol (misalnya `fp = 0`). MIDAS secara otomatis menambahkan koreksi kontinuitas 0.5 pada setiap sel yang nol. Kamu bisa mengubah nilai koreksi dengan opsi `zcf(#)`:

```stata
* Koreksi kontinuitas 0.1 (bukan default 0.5)
midas tp fp fn tn, zcf(0.1)
```

## Import dari Excel/CSV

```stata
* Dari CSV
import delimited "data_studi.csv", clear varnames(1)

* Dari Excel
import excel "data_studi.xlsx", sheet("Sheet1") firstrow clear

* Pastikan tipe data benar
destring tp fp fn tn, replace
```

## Cek Data Sebelum Analisis

```stata
* Cek missing values
misstable summarize tp fp fn tn

* Cek nilai negatif atau tidak wajar
assert tp >= 0 & fp >= 0 & fn >= 0 & tn >= 0

* Hitung prevalensi per studi
generate prev = (tp + fn) / (tp + fp + fn + tn)
summarize prev

* Hitung sensitivitas & spesifisitas kasar per studi
generate sens_raw = tp / (tp + fn)
generate spec_raw = tn / (tn + fp)
tabstat sens_raw spec_raw, stats(mean sd min max)
```
