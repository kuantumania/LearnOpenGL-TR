# Katkıda Bulunma Kılavuzu 🤝

**LearnOpenGL-TR** projesine katkıda bulunmak istediğiniz için çok teşekkürler! Bu proje, Türkiye'deki tüm geliştiricilerin özgürce faydalanabileceği yaşayan ve dinamik bir topluluk projesidir.

---

## Nasıl Katkı Sağlayabilirsiniz?

1. **Yeni Bölüm Çevirisi:** Henüz çevrilmemiş bölümleri Türkçeye kazandırabilirsiniz.
2. **Düzeltme & İyileştirme:** Mevcut çevirilerdeki anlatım bozukluklarını, imla hatalarını veya teknik terim uyumsuzluklarını giderebilirsiniz.
3. **Kod Örnekleri & Test:** Bölümlerdeki kod örneklerini test edip modern derleyicilerde (C++17/20, son sürüm GLFW/GLAD) denenmiş alternatifler sunabilirsiniz.

---

## Terminoloji Prensiplerimiz

* **Kod öğeleri orijinal kalır:** Fonksiyon adları, API çağrıları, değişkenler ve GLSL anahtar kelimeleri (`glDrawArrays`, `uniform`, `sampler2D` vb.) asla Türkçeleştirilmez.
* **Sektör Standartları Korunur:** Bazı kökleşmiş kavramlar parantez içinde orijinal haliyle verilir:
  * Örn: *Tepe Noktası Gölgelendiricisi (Vertex Shader)*
  * Örn: *Piksel/Parça Gölgelendiricisi (Fragment Shader)*
  * Örn: *Çerçeve Belleği (Framebuffer)*
  * Örn: *Doku (Texture)*

---

## GitHub Üzerinden PR (Pull Request) Açma

1. Repoyu Fork'layın: `https://github.com/kuantumania/LearnOpenGL-TR`
2. Kendi yerel makinenize klonlayın:
   ```bash
   git clone https://github.com/KULLANICI_ADINIZ/LearnOpenGL-TR.git
   ```
3. Yeni bir dal (branch) açın:
   ```bash
   git checkout -b cevir/02-pencere-olusturma
   ```
4. Değişikliklerinizi yapıp yerelde `mkdocs serve` ile kontrol edin.
5. Commit atıp kendi deponuza push'layın ve GitHub üzerinden **Pull Request** gönderin.
