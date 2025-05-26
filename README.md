# Finansal İflas Riski Tahmini: Türk Şirketleri İçin Altman Z-Skoru Regresyon Modeli

---

## Giriş

Bu proje, Akbank'ın düzenlediği Global AI bootcamp kapsamında geliştirilmiştir. Amacımız, Türkiye'deki halka açık şirketlerin finansal verilerini kullanarak **Altman Z-Skoru'nu tahmin eden bir makine öğrenmesi modeli** oluşturmaktır. Altman Z-Skoru, bir şirketin finansal sağlığını ve potansiyel iflas riskini değerlendirmek için kullanılan önemli bir metriktir. Bu çalışma, bankacılık ve finans sektöründe risk yönetimi ve proaktif karar alma süreçlerine değer katmayı hedeflemektedir.

---

## Veri Seti

Çalışmada kullanılan veri seti, Kaggle üzerinden temin edilen "Audit opinions of Turkish Public Companies" adlı finansal verilerdir. Veri setine aşağıdaki bağlantıdan ulaşılabilir:

[Kaggle Veri Seti Bağlantısı](https://www.kaggle.com/datasets/agrafintech/financial-data-of-turkish-public-companies)

Bu veri seti, şirketlerin finansal tablolarından türetilmiş çeşitli rasyoları ve iflas tahmin skorlarını içermektedir.

---

## Metrikler

Bu projede, modelin performansını ölçmek ve finansal veriler üzerindeki etkinliğini yorumlamak büyük önem taşımaktadır. Altman Z-Skoru'nun sayısal bir değer olmasından dolayı, bir **regresyon problemi** üzerinde çalıştık. Bu nedenle model performansını değerlendirmek için aşağıdaki metrikleri kullandık:

* **Ortalama Mutlak Hata (MAE):** Tahminlerin gerçek değerlerden ortalama mutlak sapmasını gösterir. Düşük MAE, daha doğru tahminlere işaret eder.
* **Ortalama Kare Hata (MSE):** Hataların karelerinin ortalamasıdır. Büyük hataları daha fazla cezalandırır.
* **Kök Ortalama Kare Hata (RMSE):** MSE'nin kareköküdür ve hatayı orijinal birimlerde ifade eder. Yorumlanması daha kolaydır.
* **R-kare Skoru (R2 Score):** Modelin, hedef değişkendeki varyansın ne kadarını açıklayabildiğini gösterir. 0 ile 1 arasında değişir; 1'e ne kadar yakınsa model o kadar iyidir.

### Problem Tanımı ve Yaklaşım Değişikliği

Başlangıçta Altman Z-Skoru'nu iflas risk kategorilerine ayırarak bir **sınıflandırma problemi** olarak ele alma fikri olsa da, Z-Skorunun doğası gereği (sürekli sayısal bir değer olması) bu yaklaşımın veriyi basite indirgeyerek modelin öğrenme kapasitesini kısıtladığı anlaşıldı. Z-skorunun kendisi zaten bir risk göstergesi olduğu için, onu kategorize etmek yerine **doğrudan sayısal değerini tahmin etmek** çok daha anlamlı ve işlevsel bir yaklaşım sunmaktadır. Bu nedenle proje kapsamı, diğer finansal göstergeleri kullanarak **Altman Z-Skoru'nun sayısal değerini tahmin etmeye yönelik bir regresyon modeli** geliştirmeye odaklanmıştır. Bu değişiklik, modelin finansal veriler ve risk seviyesi arasındaki daha derinlemesine ilişkileri yakalamasına olanak tanımıştır.

### Veri Ön İşleme ve Özellik Mühendisliği

Veri setinde kapsamlı bir ön işleme ve özellik mühendisliği adımı izlenmiştir:

* **Kategorik Değişken İşleme:** 'Periyot' sütunu ilgili ay sayısı ile etiketlenirken, 'Şirket Kodu' sütunu Label Encoding ile dönüştürülmüştür. 'Şirket Adı' sütunu, 'Şirket Kodu'nun yeterli bilgi sağlaması nedeniyle kaldırılmıştır.
* **Eksik Değer Analizi:** 'Görüş Tipi' gibi kategorik değişkenlerdeki eksik değerlerin model için anlamlılığı değerlendirilmiş, genel veri dağılımına etkisi göz önünde bulundurulmuştur.
* **Aykırı Değer Analizi ve Kararı:** Finansal verilerde yaygın olan aykırı değerler, Boxplot görselleştirmeleriyle tespit edilmiştir. Bu değerlerin verinin doğal yapısından kaynaklandığı ve finansal risk açısından anlamlı olabileceği düşünülerek, aykırı değerleri veri setinde **tutma kararı** alınmıştır. Bu yaklaşım, aykırı değerlere dayanıklı regresyon modellerinin tercih edilmesiyle desteklenmiştir.
* **Veri Dağılımı ve Çarpıklık:** Finansal oranlardaki belirgin sağa çarpıklıklar ve yüksek varyanslar da verinin doğal bir özelliği olarak kabul edilmiştir. Bu nedenle, modelin yorumlanabilirliğini korumak ve önemli finansal sinyalleri kaybetmemek adına **herhangi bir değişken dönüştürme işlemi yapılmamıştır.**
* **Korelasyon Analizi ve Özellik Seçimi:** Altman Z-Skoru ile diğer değişkenler arasındaki korelasyonlar incelenmiştir. Likidite oranlarının (Cari Oran, Asit Test Oranı, Nakit Oranı) pozitif korelasyon gösterdiği, diğer bazı iflas skorlarının ise negatif korelasyon sergilediği tespit edilmiştir. Korelasyon katsayısı mutlak değer olarak $0.01$'den küçük olan değişkenler, modele anlamlı katkı sağlamadıkları için özellik mühendisliği aşamasında **çıkarılmıştır**. Bu sayede model daha sade ve etkili hale getirilmiştir.

### Model Seçimi ve Eğitimi: Random Forest Regressor

Altman Z-Skoru gibi sürekli ve geniş bir aralığa yayılan, ayrıca yüksek varyans ve aykırı değerler içeren bir hedef değişkeni tahmin etmek için **Random Forest Regressor (Rassal Orman Regresyonu)** modeli tercih edilmiştir. Bu modelin seçilme nedenleri şunlardır:

* **Aykırı Değerlere Dayanıklılık:** Ağaç tabanlı bir model olduğu için aykırı değerlere karşı doğal bir sağlamlık sunar.
* **Doğrusal Olmayan İlişkileri Yakalama:** Finansal verilerdeki karmaşık ve doğrusal olmayan ilişkileri başarıyla öğrenme yeteneğine sahiptir.
* **Aşırı Öğrenmeye Karşı Direnç:** Birden fazla karar ağacını birleştirerek aşırı öğrenmeyi azaltır ve genellenebilirliği artırır.

Model eğitimi için, özellikler (`X`) olarak korelasyon analizinde belirlenen anlamlı değişkenler kullanılmış, hedef değişken (`y`) ise doğrudan **Altman Z-Skoru'nun sayısal değeri** olmuştur.

### Model Değerlendirmesi ve Optimizasyonu

Başlangıçta geliştirdiğimiz Random Forest regresyon modelinin oldukça yüksek bir performans gösterdiğini görmüştük. Tekil test setinde $0.9903$ gibi etkileyici bir R-kare skoru elde etmiş, ardından çapraz doğrulama ile bu performansın genellenebilirliğini ($0.9693$ Ortalama R-kare) teyit etmiştik. Bu sonuçlar, modelin Altman Z-Skoru'nu güvenilir bir şekilde tahmin ettiğini göstermişti.

Modelimizin performansını daha da optimize etmek ve en iyi hiperparametre kombinasyonunu bulmak amacıyla **GridSearchCV** yöntemini kullandık. Bu işlem, tanımladığımız parametre aralıkları içinde en iyi performansı (en düşük RMSE) veren kombinasyonu sistematik olarak aramıştır.

**GridSearchCV Sonuçları:**

GridSearchCV, model için en uygun hiperparametreleri aşağıdaki gibi belirlemiştir:

* **En İyi Hiperparametreler:** `{'max_depth': 20, 'max_features': 1.0, 'min_samples_leaf': 1, 'min_samples_split': 2, 'n_estimators': 100}`
* **En İyi Ortalama RMSE (GridSearchCV Sonucu):** $7.0972$

Bu sonuçlar, çapraz doğrulama aşamasında modelin bu parametrelerle en iyi hata oranını yakaladığını göstermektedir.

**Optimize Edilmiş Modelin Nihai Test Seti Performansı:**

GridSearchCV tarafından belirlenen en iyi hiperparametrelerle modelimizi yeniden eğitip, daha önce hiç görmediği test seti üzerinde değerlendirdiğimizde aşağıdaki sonuçları elde ettik:

* **Ortalama Mutlak Hata (MAE):** $0.0960$
* **Ortalama Kare Hata (MSE):** $11.6220$
* **Kök Ortalama Kare Hata (RMSE):** $3.4091$
* **R-kare Skoru (R2 Score):** $0.9881$

**Değerlendirme:**

Optimize edilmiş modelin test seti performans metrikleri, başlangıçtaki modelin yüksek performansını koruduğunu ve hatta bazı metriklerde iyileşme sağladığını göstermektedir:

* **R-kare Skoru** $0.9881$ ile hala **son derece yüksek** bir seviyededir. Bu, modelin Altman Z-Skoru'ndaki varyansın neredeyse %99'unu açıklayabildiğini ve tahmin gücünü koruduğunu gösterir.
* **MAE ($0.0960$) ve RMSE ($3.4091$)** değerleri, önceki çapraz doğrulama ortalamalarından (Ortalama MAE: $0.3937$, Ortalama RMSE: $7.0972$) belirgin şekilde daha düşüktür. Bu iyileşme, **hiperparametre optimizasyonunun modelin genelleme performansını başarıyla artırdığını** ve tahmin hatalarını minimize ettiğini kanıtlamaktadır.

Sonuç olarak, Random Forest Regressor modeliniz, **Altman Z-Skoru'nu olağanüstü bir doğruluk ve güvenilirlikle tahmin edebilen, sağlam ve optimize edilmiş bir yapıya** kavuşmuştur. Bu model, finansal risk değerlendirmesi ve şirketlerin finansal sağlığının öngörülmesi gibi kritik iş senaryolarında etkin bir şekilde kullanılabilir.

---

## Sonuç ve Gelecek Çalışmalar

Bu proje kapsamında geliştirilen Random Forest regresyon modeli, Türk şirketlerinin finansal verileri üzerinden **Altman Z-Skoru'nu tahmin etmede üstün bir performans** sergilemiştir. Elde edilen yüksek R-kare skoru ve düşük hata metrikleri, modelin finansal risk değerlendirmesi için güçlü ve pratik bir araç olabileceğini göstermektedir. Bu çalışma, şirketlerin finansal sağlığını öngörmede ve potansiyel iflas risklerini proaktif olarak yönetmede bankacılık sektörüne değerli içgörüler sunabilir.

### Gelecek Çalışmalar İçin Vizyon

Bu proje, bir başlangıç noktası olup gelecekte birçok yönde geliştirilebilir:

* **Dinamik Veri Toplama:** Mevcut statik veri setini dinamik, gerçek zamanlı finansal veri akışlarıyla entegre ederek modelin sürekli güncel kalmasını sağlamak. Bu, API'lar aracılığıyla borsadan veya finansal veri sağlayıcılardan otomatik veri çekmeyi içerebilir.
* **Model Genişletme:** Sadece Altman Z-Skoru değil, Springate, Zmijewski gibi diğer iflas skorlarını da tahmin eden veya bu skorları bir araya getirerek daha kapsamlı bir risk puanı oluşturan modeller geliştirmek.
* **Derin Öğrenme Yaklaşımları:** Özellikle zaman serisi verileri için LSTM veya Transformer gibi derin öğrenme modellerini deneyerek finansal trendleri ve dinamikleri daha iyi yakalamayı hedeflemek.
* **Arayüz Entegrasyonu:** Modelin tahminlerini kolayca erişilebilir kılmak için basit bir web arayüzü (örneğin Flask veya Streamlit kullanarak) geliştirmek. Bu, finans analistlerinin veya bankacıların anında risk değerlendirmesi yapmasına olanak tanır.
* **Açıklanabilir Yapay Zeka (XAI):** Modelin neden belirli bir Z-Skoru tahmininde bulunduğunu açıklamak için SHAP veya LIME gibi XAI araçlarını entegre etmek. Bu, modelin güvenilirliğini ve iş birimleri tarafından benimsenmesini artıracaktır.
* **Makroekonomik Faktörlerin Entegrasyonu:** Şirket özelindeki verilerin yanı sıra enflasyon, faiz oranları, GSYİH gibi makroekonomik göstergeleri de modele dahil ederek tahminlerin doğruluğunu ve sağlamlığını artırmak.

Bu proje, benim için makine öğrenimi alanındaki yetkinliklerimi geliştirmem ve gerçek dünya problemleri üzerine uygulamalı bir bakış açısı kazanmam açısından önemli bir adımdır. Gelecekte, finansal teknolojiler (FinTech) ve yapay zeka kesişimindeki kariyer hedefime ulaşmak için bu tür projelere devam etmeyi ve öğrendiğim yeni teknolojileri entegre etmeyi planlıyorum.

---

## Linkler

* [Kaggle Not Defteri Bağlantısı](https://www.kaggle.com/code/ariffurkanaytekin/work-of-akbank-bootcamp)

---
