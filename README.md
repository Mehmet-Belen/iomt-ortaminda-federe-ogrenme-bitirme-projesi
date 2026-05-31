# Federe Öğrenme ile IoMT Ortamlarında Gizlilik Dostu Anomali Tespiti

Bu depo, **Tıbbi Nesnelerin İnterneti (IoMT)** ortamlarında gizlilik dostu anomali tespiti yapmak amacıyla geliştirilen bitirme projesine ait eğitim notebook dosyalarını içerir.

Projede; merkezi veri toplama ihtiyacını azaltan **Federe Öğrenme**, zamansal ağ trafiği örüntülerini öğrenen **LSTM tabanlı otokodlayıcılar**, global bağlamı yakalayan **Multi-Head Attention**, saldırılara dayanıklılık için **Trimmed Mean savunması**, kaynak kısıtlı cihazlar için **Knowledge Distillation** ve model kararlarını açıklamak için **SHAP tabanlı açıklanabilir yapay zekâ** yaklaşımları kullanılmıştır.

---

## Proje Başlığı

**Federe Öğrenme ile IoMT Ortamlarında Gizlilik Dostu Anomali Tespiti**

---

## Proje Özeti

IoMT sistemleri; hasta monitörleri, giyilebilir sensörler, tıbbi IoT ağ geçitleri ve hastane ağ bileşenleri gibi birçok cihazdan sürekli ağ trafiği üretir. Bu trafik, siber saldırıların tespit edilmesi için değerli olsa da sağlık verilerinin merkezi bir sunucuya aktarılması gizlilik ve güvenlik açısından risk oluşturur.

Bu proje, bu problemi çözmek için **Federe Öğrenme** yaklaşımını kullanır. Ham veriler istemcilerde kalır; merkezi sunucuya yalnızca model ağırlıkları gönderilir. Böylece veri gizliliği korunurken IoMT ağlarındaki anormal trafikler tespit edilebilir.

Projede önerilen ana model, **MHA-LSTM-AE** adı verilen hibrit bir otokodlayıcı mimarisidir. Model, normal trafik örüntülerini öğrenir ve yüksek yeniden yapılandırma hatasına sahip örnekleri anomali olarak işaretler.

---

## Amaçlar

Bu projenin temel amaçları şunlardır:

- IoMT ağ trafiğinde siber anomalileri tespit etmek.
- Ham verileri merkezi sunucuya göndermeden gizlilik dostu eğitim yapmak.
- Merkezi öğrenme ve federe öğrenme yaklaşımlarını karşılaştırmak.
- Non-IID istemci dağılımlarında modelin performansını test etmek.
- Federe öğrenmede model zehirleme saldırısını simüle etmek.
- Zehirleme saldırılarına karşı Trimmed Mean savunmasını değerlendirmek.
- İstemci düşme senaryosunda sistem dayanıklılığını ölçmek.
- Knowledge Distillation ile daha küçük ve hafif bir öğrenci model geliştirmek.
- SHAP ile model kararlarını açıklanabilir hale getirmek.

---

## Kullanılan Veri Seti

Projede **CICIoMT2024** veri seti kullanılmıştır.

Bu veri seti, gerçekçi IoMT ağ ortamlarında elde edilen normal ve saldırı trafiği örneklerini içerir. Wi-Fi ve MQTT protokollerine ait trafikler üzerinden çeşitli siber saldırı türleri değerlendirilmiştir.

Veri setinde yer alan saldırı türlerinden bazıları:

- DDoS
- DoS
- Botnet
- Port Tarama
- Veri Sızdırma
- Fidye Yazılımı Trafiği

---

## Yöntem

Projenin genel iş akışı aşağıdaki adımlardan oluşur:

1. CICIoMT2024 veri setinin yüklenmesi
2. Gereksiz ve sabit özelliklerin temizlenmesi
3. Verilerin standartlaştırılması
4. PCA ile boyut indirgeme uygulanması
5. Ağ trafiğinin zaman pencereleri hâline getirilmesi
6. Otokodlayıcı tabanlı derin öğrenme modellerinin eğitilmesi
7. Merkezi ve federe öğrenme senaryolarının karşılaştırılması
8. Rekonstrüksiyon hatasına göre anomali skorlarının hesaplanması
9. F1 ve Precision-Recall tabanlı eşik stratejilerinin uygulanması
10. SHAP ile açıklanabilirlik analizi yapılması

---

## Önerilen Model Mimarisi

Ana model olarak **MHA-LSTM-AE** kullanılmıştır.

Bu model aşağıdaki bileşenlerden oluşur:

- **Conv1D:** Yerel zamansal örüntülerin çıkarılması
- **BiLSTM:** Çift yönlü zamansal bağımlılıkların öğrenilmesi
- **Multi-Head Attention:** Ağ trafiğindeki önemli zaman adımlarına odaklanılması
- **LSTM Bottleneck:** Sıkıştırılmış latent temsilin oluşturulması
- **Decoder:** Girdinin yeniden yapılandırılması
- **TimeDistributed Dense:** Çıkış boyutunun giriş boyutuna eşlenmesi

Model yalnızca sınıflandırma mantığıyla değil, yeniden yapılandırma hatası üzerinden çalışır. Normal trafik daha düşük hata ile yeniden oluşturulurken, saldırı veya anormal trafik daha yüksek hata üretir.

---

## Federe Öğrenme Yapısı

Projede istemci-sunucu temelli bir federe öğrenme mimarisi kullanılmıştır.

Her istemci farklı bir IoMT ortamını temsil eder:

- Wi-Fi hastane trafiği istemcisi
- MQTT IoMT sunucusu istemcisi
- Karma / ambulans trafiği istemcisi
- Non-IID senaryolar için ek istemciler

Federe öğrenme sürecinde:

1. Sunucu global modeli istemcilere gönderir.
2. Her istemci kendi yerel verisiyle modeli eğitir.
3. İstemciler ham verilerini paylaşmadan yalnızca model ağırlıklarını sunucuya gönderir.
4. Sunucu, gelen ağırlıkları **Weighted FedAvg** algoritmasıyla birleştirir.
5. Güncellenen global model tekrar istemcilere dağıtılır.

Bu yapı sayesinde ham IoMT verileri yerel ortamda kalır ve gizlilik korunur.

---

## Deneysel Senaryolar

Bu depodaki notebook dosyaları farklı deneysel senaryoları kapsamaktadır.

### Senaryo 1: Merkezi Öğrenme ve Federe Öğrenme Karşılaştırması

Bu senaryoda aynı model mimarisi kullanılarak merkezi öğrenme ile federe öğrenme karşılaştırılmıştır.

Amaç, ham verileri merkezi bir yerde toplamadan federe öğrenmenin yüksek başarı sağlayıp sağlayamayacağını test etmektir.

---

### Senaryo 2: Non-IID Federe Öğrenme

Bu senaryoda istemciler arasında farklı veri miktarları ve farklı protokol dağılımları oluşturulmuştur.

Amaç, gerçek dünyadaki heterojen IoMT ortamlarında modelin performansını ölçmektir.

---

### Senaryo 3: Model Zehirleme Saldırısı ve Trimmed Mean Savunması

Bu senaryoda bazı istemciler kötü niyetli olarak modellenmiş ve global modeli bozacak şekilde zehirli ağırlıklar göndermiştir.

Bu saldırıya karşı **Trimmed Mean** agregasyon yöntemi kullanılmıştır. Bu yöntem, aşırı sapan ağırlıkları kırparak daha dayanıklı bir global model oluşturmayı amaçlar.

---

### Senaryo 4: İstemci Düşme Senaryosu

Gerçek IoMT ortamlarında bazı cihazlar veya istemciler bağlantı problemi yaşayabilir.

Bu senaryoda, bazı istemcilerin eğitim sürecinde geçici olarak sistemden düşmesi simüle edilmiştir. Amaç, federe modelin eksik istemci katılımı altında ne kadar kararlı kalabildiğini test etmektir.

---

### Senaryo 5: Knowledge Distillation ile Hafif Öğrenci Model

Bu senaryoda büyük öğretmen modelden daha küçük bir öğrenci modele bilgi aktarımı yapılmıştır.

Öğretmen model: **MHA-LSTM-AE**  
Öğrenci model: **BiLSTM-AE**

Amaç, IoMT edge cihazlarında çalışabilecek daha hafif ve düşük iletişim maliyetli bir model geliştirmektir.

---

## Başlıca Sonuçlar

Deneysel sonuçlara göre:

- Federe öğrenme yaklaşımı, yüksek anomali tespit başarımı sağlamıştır.
- Ana federe model yaklaşık **ROC AUC = 0.9990** değerine ulaşmıştır.
- Saldırı F1 skoru yaklaşık **%99.871** olarak elde edilmiştir.
- Trimmed Mean savunması, model zehirleme saldırılarına karşı dayanıklılığı artırmıştır.
- Knowledge Distillation ile geliştirilen öğrenci model, iletişim maliyetini yaklaşık **%81.4** azaltmıştır.
- Hafif öğrenci model yaklaşık **ROC AUC = 0.9867** performansını korumuştur.
- SHAP analizi, `fin_count` ve `Rate` gibi özelliklerin DDoS saldırılarında belirleyici olduğunu göstermiştir.

---

## Depo İçeriği

Bu depoda şu anda yalnızca eğitim ve deney notebook dosyaları bulunmaktadır.

Örnek depo yapısı:

```text
.
├── README.md
├── scenario_1_centralized_vs_federated.ipynb
├── scenario_2_non_iid_federated_learning.ipynb
├── scenario_3_poisoning_trimmed_mean.ipynb
├── scenario_4_client_dropout.ipynb
└── scenario_5_knowledge_distillation.ipynb
```

> Not: Notebook dosya adları repodaki gerçek dosya isimlerine göre farklılık gösterebilir.

---

## Kurulum

Depoyu klonlamak için:

```bash
git clone https://github.com/kullanici-adi/repo-adi.git
cd repo-adi
```

Gerekli kütüphaneleri kurmak için:

```bash
pip install numpy pandas scikit-learn tensorflow matplotlib seaborn shap
```

Notebook dosyaları aşağıdaki ortamlarda çalıştırılabilir:

- Jupyter Notebook
- JupyterLab
- Google Colab
- Kaggle Notebook

GPU destekli bir ortam kullanılması önerilir.

---

## Gereksinimler

Önerilen çalışma ortamı:

- Python 3.10+
- TensorFlow / Keras
- NumPy
- Pandas
- Scikit-learn
- Matplotlib
- Seaborn
- SHAP
- GPU destekli çalışma ortamı

---

## Önemli Notlar

Bu depo şu anda yalnızca deneysel eğitim notebook dosyalarını içermektedir.

Depoda şu bileşenler bulunmamaktadır:

- Hazır Python paketi
- Web arayüzü
- REST API
- Önceden eğitilmiş model ağırlıkları
- Veri seti dosyaları
- Deployment scriptleri

Bu nedenle depo, daha çok akademik araştırma ve deneysel model eğitimi amacıyla hazırlanmıştır.

---

## Proje Ekibi

- Mehmet Belen
- Ali Özen
- Merve Keleş

Danışman:

- Dr. Öğr. Üyesi Merve Sedef Demirci

Kurum:

- Gazi Üniversitesi
- Bilgisayar Mühendisliği Bölümü

---

## Anahtar Kelimeler

Federe Öğrenme, IoMT, Anomali Tespiti, Otokodlayıcı, LSTM, Multi-Head Attention, Model Zehirleme, Trimmed Mean, Knowledge Distillation, SHAP, Açıklanabilir Yapay Zekâ, Siber Güvenlik

---

## Akademik Atıf

Bu projeyi akademik bir çalışmada kullanmak veya referans göstermek isterseniz aşağıdaki şekilde atıf verebilirsiniz:

```text
Belen, M., Özen, A. ve Keleş, M. (2026).
Federe Öğrenme ile IoMT Ortamlarında Gizlilik Dostu Anomali Tespiti.
Gazi Üniversitesi, Bilgisayar Mühendisliği Bölümü.
```

---

## Lisans

Bu çalışma akademik ve eğitim amaçlı hazırlanmıştır. Notebook dosyalarını veya deneysel sonuçları başka bir akademik ya da ticari çalışmada kullanmadan önce proje ekibiyle iletişime geçilmesi önerilir.
