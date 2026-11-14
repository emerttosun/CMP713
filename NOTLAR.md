# CMP713 Assignment 1: Notlar

Yeni bir Claude oturumunda: "NOTLAR.md dosyasını oku ve oradan devam edelim" demen yeterli.

## Ödev
- Ders: CMP713 Data Mining (Burkay Genç), Assignment 1.
- Sayfa: https://web.cs.hacettepe.edu.tr/~bgenc/courses/cmp713/2627F/assignment1/for_students/assignment1.html
- **Son teslim: 15/11/2026 23:59.** Teslim: sadece `assignment1.Rmd`, kurs sayfasındaki SUBMIT linkiyle.
- Veri: `meltem_daily.csv` (12 türbin × 1827 gün = 21.924 satır, 34 sütun). Sözlük: `meltem_dictionary.md`.
  Hocanın sitesindeki dosyalarla bayt bayt aynı (SHA-256 ile kontrol edildi).
- Kurallar: Sadece `student_name`, `student_id` ve Answer/Explanation bölümleri değiştirilebilir.
  Yalnızca dplyr, tidyr, readr, ggplot2, DMwR2, mice ve base R kullanılabilir. `install.packages` yazılmaz.
  Knit süresi 5 dakikanın altında olmalı. Her açıklama en fazla 250 kelime ve bu verideki sayılara dayanmalı.

## Durum: teslime hazır
- Rmd baştan sona hatasız knit ediliyor (yaklaşık 41 saniye), uyarı yok, sondaki tabloda iki satır da "yes".
- Sabit kısımlar hocanın orijinal şablonuyla karşılaştırıldı: tek fark isim ve numara satırları.
- Açıklamalar kullanıcının kendi cümleleriyle yazıldı. Kelime sayıları: 242 / 239 / 242 / 244.
- Açıklamalardaki her sayı knit çıktısında görünüyor. Kodda ve açıklamalarda ders adı ("Lecture …") geçmiyor.
- Kod kısa tutuldu. Anlaşılmayan satırlar kodu uzatmak yerine sohbette açıklanıyor.

### Öğrenci numarası
Gerçek numara **N25123091**. Fixed code `as.numeric(student_id)` yaptığı için "N" harfiyle knit duruyor.
Rmd'de `student_id = "25123091"` yazıyor. Hocanın şablonundaki örnek de sadece rakam ("11111111111"),
bu yüzden rakamla yazmak büyük olasılıkla doğru. İstenirse hocaya teyit ettirilebilir.

## Görev özetleri (knit çıktısındaki sayılar)
**T1, eksik değerler** (`imputed`, meltem'in kopyası):
- `wind_ms`: NA yok ama 1113 tane `-9999` var. `na_if` ile NA yapılıyor. MCAR (her ayda %4–6).
  Yöntem: lm + aynı gün diğer türbinlerin ortalama artığı (`same_day`, `ave`).
- `gen_temp_c`: 1381 NA. MAR: Ocak %22, Haziran %0, bloklar hâlinde, dış hava soğukken (6,9 / 15,4 °C).
  Yöntem: lm + artığın türbin içinde zamana göre enterpolasyonu (`in_time`, `approx`).
- `vib_gbx_mm_s`: 990 NA. MNAR: sensör 0–9 ölçüyor, en büyük değer tam 9, eksikler yüksek üretim günlerinde (40 / 18 MWh).
  Yöntem: lm + `pmax(tahmin, 9)`.
- Değer gizleme testi (RMSE). gen_temp blokları `unique()` ile tekrarsız sayılıyor:
  wind: median 2,07 / kNN 0,55 / lm 0,56 / **seçilen 0,34**.
  gen: median 7,00 / kNN 1,84 / lm 1,22 / **seçilen 0,90**.
  vib (8'in üstü gizlenerek simülasyon): lm 0,90 → `pmax` ile **0,45**, median 1,88.
- mice karşılaştırmadan çıkarıldı (kNN ile aynı rolü oynuyordu ve daha kötüydü). Tobit, derste olmadığı için kullanılmadı.

**T2, split** (`split`: date, turbine, set): Zamana göre bölme. Train 2020-01-01..2023-12-24 (17.448 satır),
test 2024 (4.392 satır, %20,1). 2023'ün son 7 günü (84 satır) atılıyor, çünkü etiket 7 gün ileriye, 2024'e bakıyor.
Pozitifler: train 374 (%2,14), test 70 (%1,59). Gerekçe: T01 hedef ACF'si 0,86 (lag 1) → 0,03 (lag 7);
T01 ile T03 enerji korelasyonu 0,93.

**T3, STL:** 60 değer, Ocak 2020 = 119,9. `stl(s.window = "periodic")`.
Mevsimsel: Temmuz +118, Ağustos +92, Aralık −85 (Mayıs–Eylül pozitif). Trend yıllık ortalamaları: 189, 211, 245, 246, 251.
Standart sapmalar: mevsimsel 66,4 / trend 25,5 / kalıntı 35,1.
Kalıntı ACF'si: lag 2 −0,37, lag 3 −0,26, lag 12 −0,40 (sabit mevsim örüntüsü yıllar arası farkı yakalayamıyor).

**T4, EDA:** Büyük model T02, T05, T06, T09, T11 (maksimum enerji 64–70, diğerlerinde 43–48; ortalama 6,6–6,9 rpm, diğerlerinde 10,7–11,1).
rotor_rpm ile gbx_oil_c korelasyonu: tüm satırlarda −0,37, büyük modelde +0,65, küçük modelde +0,67. Simpson paradoksu.
Öneri: yağ sıcaklığı için model başına alarm sınırı.

## Mac'te knit
RStudio'da Knit düğmesi yeterli. Terminalden knit için RStudio'nun pandoc'u gerekiyor:
```bash
RSTUDIO_PANDOC=/Applications/RStudio.app/Contents/Resources/app/quarto/bin/tools/aarch64 Rscript -e 'rmarkdown::render("assignment1.Rmd")'
```

## Kalan adım
`assignment1.Rmd`'yi SUBMIT linkinden teslim et (istersen önce numarayı hocaya teyit ettir).
