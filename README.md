# 131 Fon, 1 Soru: Sinyaller Önceden Görünüyor muydu?

**Fon krizinin veri röntgeni:** Eylül 2026'da Türkiye'de 131 yatırım fonunun tasfiyeye alındığı krizden önce, kamuya açık TEFAS fiyat verisinde bir uyarı var mıydı? Bu proje bir yapay zeka modeliyle her fona her gün 0–100 arası bir **risk skoru** veriyor ve sonucu Power BI'da bir **erken uyarı paneline** dönüştürüyor.

## Bulgular
| | |
|---|---|
| Analiz edilen fon | 1.049 (TEFAS, yatırım fonları) |
| TEFAS'ta bulunan kriz fonu | 8 (fiyatı 16–25 Eylül'de sıfıra düşen) |
| Temerrütten 1 gün önce ilk 50'deki kriz fonu | **6 / 8** (en riskli %5) |
| Rastgele seçime göre isabet | **~15 kat** |
| En erken kırmızı alarm | Temerrütten ~37 hafta önce (TLY ve DFI, 2 Ocak 2026) |

Model çöküşü hiç görmedi: skorlar yalnızca **15 Eylül 2026'ya kadarki** veriyle hesaplandı, model ise "normal" davranışı Mart 2026 öncesinden öğrendi.

## Yöntem
1. **Veri toplama** (`fon_veri_cek_v2.py`): TEFAS'ın kamuya açık API'sinden 1.071 fonun 3 yıllık günlük fiyatı.
2. **Sinyaller** (`analiz.py`, 60 iş günü kayan pencere):
   - Kendi fon türünden ayrışan getiri
   - "Pürüzsüzlük": türüne göre fazla getirinin ortalama ÷ oynaklık oranı (hiç düşmeden yükseliş)
   - Pozitif gün oranı
   - Oynaklık
3. **Yapay zeka:** scikit-learn **Isolation Forest** ile anomali skoru → 0–100 ölçek → 10 günlük ortalama → alarm (70+ kırmızı, 50+ sarı).
4. **Power BI:** yıldız şema (`dim_Fon` + `fact_Risk`, `fact_KrizFiyat`, `kriz_Sonuc`), DAX ölçüleri, koyu tema.

## Sınırlar
- Tasfiyeye alınan 131 fondan yalnızca 8'i TEFAS listesinde.
- Yatırımcı sayısı ve fon büyüklüğü TEFAS'ın yeni sisteminde kamuya açık değil; model yalnızca fiyatı görüyor.
- Para piyasası fonlarından ikisi (DOH, TLV) yakalanamadı.
- Kırmızı listedeki fonların çoğu kriz fonu değil: model "suçlu" değil, **"önce buna bak" listesi** üretir.

## Uyarı
Bu çalışma bir suçlama aracı ya da yatırım tavsiyesi değildir. Soruşturma sürmektedir; proje yalnızca kamuya açık fiyat verisinin ne gösterdiğini inceler.

**Araçlar:** Python (pandas, scikit-learn, requests) · Power BI (Power Query, DAX) · TEFAS
