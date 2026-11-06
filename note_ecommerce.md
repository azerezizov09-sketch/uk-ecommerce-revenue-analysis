# Revenue Analysis and Customer-Level Aggregation on UK E-Commerce Data

## 1. Dataset və alətlər

- **Dataset:** `data.csv` (Kaggle: carrie1/ecommerce-data), **541 909 sətir, 8 sütun** (`InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country`). Dövr: **2010-12-01 – 2011-12-09**, 38 ölkə.
- **Alətlər:** R (`tidyverse`: `dplyr`, `tidyr`, `readr`, `ggplot2`; `lubridate`; `scales`), R Markdown ilə hazırlanmış hesabat. Tapşırıqda göstərilən pandas/matplotlib/seaborn əvəzinə R istifadə olunub, funksionallıq eynidir.
- **Vahid:** gəlir funt sterlinqlə (£).

## 2. Metodologiya

1. **Yükləmə:** fayl `ISO-8859-1` kodlaşması ilə oxundu. `InvoiceNo` və `CustomerID` mətn kimi saxlanıldı (`InvoiceNo`-da `C` hərfi var).
2. **Data problemləri:**
   - `CustomerID` **135 080** sətirdə boşdur (təxminən 25%), `Description` 1 454 sətirdə boşdur.
   - **Ləğv edilmiş sifarişlər:** `InvoiceNo` `C` ilə başlayır. **9 288 sətir**, **3 836 unikal qaimə**. Bütün unikal qaimə sayı 25 900-dür.
   - **Mənfi `Quantity`:** 10 624 sətir. Bunların 9 288-i ləğvlərdir, qalan 1 336-sı `C` ilə başlamayan stok düzəlişləridir.
   - `UnitPrice` 2 515 sətirdə 0, 2 sətirdə mənfidir.
3. **Təmizləmə qaydaları:**
   - Ləğvlər ayrıca `cancelled` cədvəlinə çıxarıldı.
   - Gəlir hesablaması üçün `sales` cədvəli: ləğv edilməmiş, `Quantity > 0` və `UnitPrice > 0` olan sətirlər (**530 104 sətir**).
   - **Müştəri üzrə analiz** üçün `sales` içindən boş `CustomerID` sətirləri silindi (**397 884 sətir**, **4 338 müştəri**).
   - Ölkə, aylıq və məhsul analizi `CustomerID`-dən asılı olmadığı üçün bütün `sales` sətirləri ilə aparıldı (daha tam mənzərə).
   - Ləğvlər və mənfi `Quantity` gəlirdən çıxarıldı, amma return rate üçün saxlanıldı.
4. **Gəlir mühəndisliyi:** `Revenue = Quantity * UnitPrice`.
5. **Müştəri üzrə aqreqasiya:** ümumi gəlir, sifariş sayı (unikal `InvoiceNo`), orta sifariş dəyəri. Əlavə olaraq `net_revenue` (ləğvlər çıxıldıqdan sonrakı gəlir).
6. **Ölkə üzrə aqreqasiya:** ümumi gəlir, sifariş sayı, orta sifariş dəyəri və ümumi gəlirdəki pay (%).
7. **Return rate:** ləğv edilmiş unikal sifarişlər ÷ bütün unikal sifarişlər (hər ölkə üçün). Kiçik ölkələrdə faiz yanıldıcı olduğu üçün **yüksək iadəli bazar** şərti: ən azı 30 sifariş **və** return rate ümumi rate-in 1.5 mislindən çox.
8. **Bonus:** top 10 müştərinin aylıq xərcləməsi və ölkə üzrə basket size (qaimə başına orta unikal `StockCode` sayı).
9. **Qrafiklər** `plots/` qovluğuna saxlandı: aylıq gəlir, top 10 ölkə, top 20 məhsul, return rate, top 10 müştəri trendi, basket size.

## 3. Əsas nəticələr

| Göstərici | Nəticə |
|---|---|
| Ümumi gəlir (`sales`) | £10 666 685 |
| Müştəri bazasında gəlir (`sales_cust`) | £8 911 408 (4 338 müştəri, 18 532 sifariş) |
| Orta sifariş dəyəri (müştəri bazasında) | £480.87 (müştəri üzrə median: £293.90) |
| Təkrar alıcı payı | 65.6% (4 338 müştərinin 2 845-i bir neçə sifariş verib) |
| Pik ay | Noyabr 2011: £1 509 496 (ümumi gəlirin 14.2%-i) |
| Ümumi return rate | 14.8% (3 836 ləğv / 25 900 sifariş) |
| Ləğvlərin dəyəri | £896 812 (ümumi gəlirin 8.4%-i) |
| Orta basket size | 26.0 unikal məhsul / qaimə |

**Top 5 ölkə (gəlirə görə):**

| Ölkə | Gəlir | Pay | Sifariş | Orta sifariş dəyəri |
|---|---|---|---|---|
| United Kingdom | £9 025 222 | 84.6% | 18 019 | £501 |
| Netherlands | £285 446 | 2.7% | 94 | £3 037 |
| EIRE | £283 454 | 2.7% | 288 | £984 |
| Germany | £228 867 | 2.1% | 457 | £501 |
| France | £209 715 | 2.0% | 392 | £535 |

**Aylıq gəlir (ən yüksək 3 ay):** Noyabr 2011 (£1.51M), Oktyabr 2011 (£1.15M), Sentyabr 2011 (£1.06M). Dekabr 2011 natamam aydır (data 9 dekabrda bitir).

**Ən çox satılan 5 məhsul (say üzrə):** PAPER CRAFT, LITTLE BIRDIE (80 995), MEDIUM CERAMIC TOP STORAGE JAR (78 033), WORLD WAR 2 GLIDERS ASSTD DESIGNS (55 047), JUMBO BAG RED RETROSPOT (48 474), WHITE HANGING HEART T-LIGHT HOLDER (37 891).

**Top 5 müştəri (ümumi gəlir):** 14646 (£280 206, 73 sifariş), 18102 (£259 657, 60 sifariş), 17450 (£194 551, 46 sifariş), 16446 (£168 473, 2 sifariş), 14911 (£143 825, 201 sifariş).

## 4. 5 biznes insight-ı

1. **Gəlir payızda və qışa doğru kəskin artır.** Sentyabr–Noyabr aylarının cəmi 13 aylıq dövrün gəlirinin **34.9%**-ni verir, Noyabr tək **14.2%**-dir. Bu, ehtimal ki, Milad mövsümünə hazırlıq alışlarının təsiridir (bunu data birbaşa göstərmir). Stok və marketinq planlaşdırmasını bu pikə uyğun əvvəlcədən qurmaq məsləhətdir.

2. **Gəlir bir bazara çox bağlıdır, amma bəzi kiçik bazarlar çox dəyərlidir.** United Kingdom gəlirin **84.6%**-ni verir. Netherlands cəmi 94 sifarişlə £285K gəlir gətirib: orta sifariş dəyəri **£3 037**-dir, UK-dəki £501-dən 6 dəfə çoxdur. EIRE-də də bu rəqəm £984-dür. Bu bazarlar az sifarişlə böyük həcm gətirir, onlara fərdi münasibət (məsələn həcm endirimləri) fayda verə bilər.

3. **Müştəri bazası konsentrasiya olunub, amma təkrar alıcılar əsasdır.** Top 10% müştəri (433 nəfər) müştəri bazasının gəlirinin **61.3%**-ni verir, top 5 müştəri isə **11.7%**-ni (top 10 müştəri **17.3%**). Eyni zamanda müştərilərin **65.6%**-i təkrar alıcıdır (median 2 sifariş). Yəni gəlir həm bir neçə böyük müştəridən, həm də sadiq təkrar alıcılardan asılıdır, hər iki qrupu saxlamaq vacibdir.

4. **Germany, Switzerland və Italy yüksək iadəli bazarlardır.** Ümumi return rate 14.8%-dir, həddi (1.5 misli, 22.2%) aşanlar: **Italy 30.9%**, **Switzerland 27.0%**, **Germany 24.2%**. Germany 457 sifarişlə dördüncü ən böyük bazardır, ona görə onun yüksək ləğv nisbəti əhəmiyyətlidir. Əksinə, Netherlands cəmi 5.9% ilə ən aşağı rate-ə malikdir. Yüksək iadəli bazarlarda sifariş təsdiqi, məhsul təsviri və çatdırılma şərtlərini yoxlamaq lazımdır.

5. **Ümumi gəlir ləğvlərin təsirini gizlədir, müştəri reytinqi xalis gəlirlə qurulmalıdır.** Ləğv edilmiş sifarişlərin dəyəri **£896 812**-dir (ümumi gəlirin **8.4%**-i). Ümumi gəlirə görə top 10 müştəriyə 16446 və 12346 daxildir, amma onların böyük sifarişləri (müvafiq olaraq 80 995 və 74 215 ədəd) sonradan tam ləğv edilib, xalis gəlirləri təxminən sıfırdır. Eyni səbəbdən ən çox satılan iki məhsul (PAPER CRAFT, LITTLE BIRDIE və MEDIUM CERAMIC TOP STORAGE JAR) təkcə bu iki ləğv edilmiş sifarişin hesabına birinci və ikinci yerdədir. Reytinqləri xalis gəlirlə qurmaq daha düzgün nəticə verir.

**Bonus müşahidələr:**
- **Basket size:** qaimə başına orta unikal məhsul sayı Switzerland-da ən yüksəkdir (36.0), Norway 29.7, UK 26.4. France (21.4), Belgium (20.7) və Australia (20.7) ən aşağıdır.
- **Top müştərilərin trendi:** 14646 iyul istisna olmaqla hər ay alış edir (£3K–£40K), 18102 və 17450 isə bəzi aylarda çox iri sifarişlər verir (məsələn 17450 sentyabr 2011-də £75K). 12346 yalnız yanvar 2011-də, 16446 yalnız dekabr 2011-də alış edib (hər ikisi ləğv olunub).

## 5. Məhdudiyyətlər

- Dataset 13 aylıq dövrü əhatə edir, bir illik mövsümilik tam təsdiqlənə bilməz (dekabr yalnız bir dəfə, 2010-da tam ay kimi var).
- Dekabr 2011 natamamdır (9 dekabra qədər), bu aydakı aşağı gəlir real azalma deyil.
- `CustomerID` 25% sətirdə boşdur, bu sətirlər müştəri analizindən çıxarılıb. Bu sətirlərin çoxu UK-ya aiddir, ona görə UK-nin payı müştəri bazasında (82.0%) bütün satışlardakından (84.6%) azdır.
- Mənfi `Quantity` olan, amma `C` ilə başlamayan 1 336 sətir (stok düzəlişləri) nə ləğv, nə satış sayılıb, gəlirdən çıxarılıb.
- `DOTCOM POSTAGE`, `POST`, `M` (manual) kimi məhsul olmayan xidmət kodları gəlirə daxildir. Məsələn, `DOTCOM POSTAGE` təxminən £206K gəlir verir.
- Return rate sifariş sayına əsaslanır (dəyərə yox). Kiçik ölkələrdə (30-dan az sifariş) faiz etibarsızdır, ona görə hesabatda göstərilmir.
