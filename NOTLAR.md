# CMP713 Assignment 1: Devir Notu

Bu not, bulut oturumunda yapılan işi yerel (Mac) oturumuna aktarmak için yazıldı.
Yeni bir Claude oturumunda: "NOTLAR.md dosyasını oku ve oradan devam edelim" demen yeterli.

## Ödev
- Ders: CMP713 Data Mining (Burkay Genç), Assignment 1.
- Sayfa: https://web.cs.hacettepe.edu.tr/~bgenc/courses/cmp713/2627F/assignment1/for_students/assignment1.html
- **Son teslim: 15/11/2026 23:59.** Teslim: sadece `assignment1.Rmd`, forms.gle linkiyle (kurs sayfasındaki SUBMIT).
- Veri: `meltem_daily.csv` (12 türbin × 1827 gün = 21.924 satır, 34 sütun). Sözlük: `meltem_dictionary.md`.
- Kurallar: Sadece `student_name`, `student_id` ve Answer/Explanation bölümleri değiştirilebilir.
  Yalnızca dplyr, tidyr, readr, ggplot2, DMwR2, mice ve base R kullanılabilir. `install.packages` yazılmaz.
  Knit süresi 5 dakikanın altında olmalı. Her açıklama en fazla 250 kelime ve bu verideki sayılara dayanmalı.
- Ödev Ders 4–7'ye dayanıyor: L4 Cleaning Data (eksik değerler), L5 Feature Engineering (split),
  L6 Dependency & Data Reduction (stl, acf, zaman sıralı split), L7 EDA (Simpson, boxplot).

## Durum
Dört görevin kodu ve açıklaması `assignment1.Rmd` içinde hazır. Rmd baştan sona hatasız knit ediliyor
(yaklaşık 1,5 dakika) ve sondaki tabloda iki satır da "yes". Kod, kullanıcının isteğiyle slayt yöntemlerine uyarlandı.

### Açık konu: öğrenci numarası
Gerçek numara **N25123091**. Fixed code `as.numeric(student_id)` yaptığı için "N" harfiyle NA çıkıyor,
`set.seed(NA)` hata veriyor ve knit duruyor. Rmd'de şimdilik `student_id = "25123091"` yazıyor.
**Hocaya sorulacak.**

## Görev özetleri ve beklenen çıktılar
**T1, eksik değerler** (`imputed`, meltem'in kopyası):
- `wind_ms`: NA yok ama 1113 tane `-9999` var. `na_if` ile NA yapılıyor. Eksiklik MCAR (her ayda yaklaşık %5).
  Yöntem: diğer tam sütunlardan lm + aynı gün diğer türbinlerin ortalama artığı (`ave` ile).
- `gen_temp_c`: 1381 NA, MAR (Ocak %22, Haziran %0, bloklar halinde, sargılar soğukken).
  Yöntem: lm + artığın türbin içinde zamana göre enterpolasyonu (`approx`).
- `vib_gbx_mm_s`: 990 NA, MNAR. Sensör aralığı 0–9 ve maksimum tam 9; eksikler rüzgârlı yaz günlerinde.
  Yöntem: lm + `pmax(tahmin, 9)`.
- Değer gizleme testi (RMSE): wind: median 2,07 / kNN 0,55 / mice 0,61 / lm 0,56 / **seçilen 0,34**.
  gen: median 6,99 / kNN 1,84 / mice 2,15 / lm 1,23 / **seçilen 0,91**.
  vib (8'in üstü gizlenerek yapılan simülasyon): lm 0,90 → `pmax` ile **0,45**.
- Bulutta ilk denenen ama sonra çıkarılan yöntem: Tobit (sansürlü regresyon) vib için 0,33 veriyordu. Slaytlarda olmadığı için kaldırıldı.

**T2, split** (`split`: date, turbine, set): Zaman sıralı ayrım. Train 2020-01-01..2023-12-24 (17.448 satır),
test 2024 (4.392 satır, %20,1). 2023'ün son 7 günü atılıyor, çünkü etiket 7 gün ileriye bakıyor.
Pozitifler: train 374 (%2,14), test 70 (%1,59). Gerekçe: T01 için fault_next7'nin lag-1 ACF'si 0,86; T01 ile T03 enerji korelasyonu 0,93.

**T3, STL:** 60 değer, Ocak 2020 = 119,9. `stl(s.window = "periodic")`.
Mevsimsel bileşen Temmuz +118, Aralık −85. Trendin yıllık ortalamaları 189, 211, 245, 246, 251.
Bileşenlerin standart sapmaları: mevsimsel 66,4 / trend 25,5 / kalıntı 35,1. Kalıntı ACF'si lag 1–3'te 0,14, −0,37, −0,26.

**T4, EDA:** Büyük model T02, T05, T06, T09, T11 (maksimum enerji 64–70, diğerlerinde 43–48; yaklaşık 6,8 rpm, diğerlerinde yaklaşık 11).
rotor_rpm ile gbx_oil_c korelasyonu: tüm satırlarda −0,37, büyük modelde 0,65, küçük modelde 0,67. Simpson tersine dönmesi.

## Mac kurulumu (yapıldı)
```r
install.packages(c("dplyr","tidyr","readr","ggplot2","mice","rmarkdown","knitr",
                   "xts","zoo","rpart","class","quantmod","TTR","DBI"))
install.packages("https://cloud.r-project.org/src/contrib/Archive/DMwR2/DMwR2_0.0.2.tar.gz",
                 repos = NULL, type = "source")
```

## Kalan adımlar
1. RStudio'da `assignment1.Rmd`'yi aç. Session → Set Working Directory → To Source File Location.
2. Chunk'ları sırayla çalıştır, çıktıları yukarıdaki sayılarla karşılaştır. Sonra Knit.
3. Açıklamaları oku ve kendi cümlelerinle düzenle.
4. Numara konusunu hocaya sor ve teslim et.
