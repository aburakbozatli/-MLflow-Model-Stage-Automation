# 🚀 MLflow Model Stage Automation

## 📌 Proje Özeti
Bu repo, MLflow Model Registry’de kayıtlı modellerin **stage geçişlerini (None → Staging → Production → Archived)** güvenli ve otomatik şekilde yönetmek için hazırlanmış bir Jupyter Notebook içerir.  
Amaç: Model versiyonunu seçip güvenli şekilde stage’ini değiştirmek, işlem öncesi yedek almak ve gerekirse rollback yapabilmek.

---

## 📖 İçindekiler / Neler Yaptık?
1. **Bağlantı Ayarları**
   - MLflow Tracking URI (`file://.../mlruns`) belirlendi.
   - Notebook MLflow registry’ye bağlandı.
2. **Model ve Versiyon Seçimi**
   - `model_name` ve `target_version` tanımlandı.
   - Geçiş yapılacak hedef stage (`Staging` / `Production`) ayarlandı.
3. **Yedekleme**
   - Stage güncellemesi öncesi `meta.yaml` dosyası timestamp ile yedeklendi.
4. **Stage Güncelleme**
   - Model versiyonu hedef stage’e geçirildi.
   - İşlem idempotent: Aynı stage’e tekrar geçiş istenirse değişiklik yapılmadı.
5. **Doğrulama**
   - Güncelleme sonrası stage bilgisi registry’den tekrar çekildi.
   - Log çıktıları ile işlem kontrol edildi.
6. **Rollback**
   - Hata durumunda alınan `.bak` dosyası kullanılarak eski stage bilgisi geri yüklendi.
7. **Örnek Çıktılar**
   - “[ok] yedek alındı…”, “[ok] meta.yaml güncellendi…”, “[verify] current_stage…” şeklinde loglar üretildi.

---

## ⚙️ Gereksinimler
- Python >= 3.9
- MLflow kurulu (lokal `file://` veya remote server)
- Ek paket:  
  ```bash
  pip install pyyaml
  ```

---

## 🚀 Kullanım
1. Notebook’u aç (`mlflow.ipynb`).  
2. Değişkenleri ayarla:  
   ```python
   registry_uri   = "file:///Users/<username>/mlruns"
   model_name     = "logistics-on-time"
   target_version = 2
   target_stage   = "Staging"
   ```
3. Hücreleri sırayla çalıştır.  
4. Log çıktıları ve MLflow UI üzerinden sonucu doğrula:  
   ```
   http://127.0.0.1:5000/#/models/logistics-on-time/versions/2
   ```

---

## 🛡️ Rollback
Stage değişikliğinden önce otomatik yedek alındığı için rollback çok basit:  
```bash
cp meta.yaml.bak-YYYYMMDD-HHMMSS meta.yaml
```

---

## 📊 Örnek Log Çıktısı
```
[ok] yedek alındı → /mlruns/models/logistics-on-time/version-2/meta.yaml.bak-20250916-173743
[ok] meta.yaml güncellendi → stage='Staging'
[verify] current_stage: Staging | version: 2
```

---

## 📈 Yol Haritası
- [ ] CLI script desteği
- [ ] GitHub Actions entegrasyonu
- [ ] Bildirim desteği (Slack/Teams)
- [ ] Docker container kullanımı

---
