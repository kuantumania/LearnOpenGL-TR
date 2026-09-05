# 2B Breakout Oyunu (Baştan Sona Oyun Geliştirme) 🕹️🧱

Öğrendiğimiz tüm bilgileri (Shader'lar, Dokular, Matrisler, Parçacıklar, Metin Çizimi ve Ses) taçlandırmanın en iyi yolu **tam teşekküllü bir video oyunu** geliştirmektir!

Bu bölümde 1976 Atari klasiği olan **Breakout (Tuğla Kırma Oyunu)**'nu modern C++ ve OpenGL ile sıfırdan inşa edeceğiz:

![Breakout Kapak](../img/in-practice/cover.png)

---

## Oyun Mimarisi 🏛️

Profesyonel bir oyun mimarisi şu sınıflardan oluşur:
1. **Game Sınıfı:** Oyun durumlarını (`GAME_ACTIVE`, `GAME_MENU`, `GAME_WIN`), seviyeleri ve tuş girdilerini yönetir.
2. **SpriteRenderer Sınıfı:** 2B dokulu kareleri tek bir shader ile ekranda istenen konum, boyut ve açıda çizer.
3. **GameObject Sınıfı:** Oyuncunun raketi, tuğlalar ve top gibi varlıkların konum ve hız vektörlerini saklar.
4. **Çarpışma Algılama (Collision Detection):**
   * **AABB - AABB Çarpışması:** Kutu ile kutu çarpışması.
   * **Daire - AABB Çarpışması:** Topun tuğlaya veya rakete hangi kenarından çarptığını tam olarak hesaplar.
5. **Parçacık Sistemi (Particle Generator):** Top hareket ederken arkasında sönen alev kuyruğu efekti bırakır!
6. **Post-Processing:** Top belirli tuğlaları kırdığında ekran sallantısı (*Chaos effect*) ve renk bozulması efektleri devreye girer.

---

## Sonuç: Kendi Oyununuzu Oynayın! 🎮

Tüm parçaları bir araya getirdiğinizde ses efektleriyle, seviye geçişleriyle, parçacık efektleriyle dolu bağımlılık yapıcı bir 2B oyun ortaya çıkar!

---

# BÜYÜK FİNAL: LearnOpenGL TR Tamamlandı! 🎓✨

**İnanılmaz bir maratonun sonuna geldiniz.** 

İlk sayfada bir pencere açmayı bile bilmezken, şu anda:
* Modern grafik işlem hattını,
* 3B koordinat sistemlerini ve serbest kamera matematiğini,
* Phong ve Blinn-Phong aydınlatma modellerini,
* Assimp ile 3B model yüklemeyi,
* Gölge haritalamayı ve normal haritalamayı,
* Deferred Shading ve SSAO gibi ileri seviye teknikleri,
* Ve AAA oyun motorlarının kullandığı **Fiziksel Tabanlı İşleme (PBR)** teorisini baştan sona kavradınız!

Artık kendi oyun motorunuzu yazabilir, modern grafik programlama dünyasında prestijli bir yer edinebilir ve bilgisayar grafiklerinin büyülü evreninde sınır tanımadan üretebilirsiniz!
