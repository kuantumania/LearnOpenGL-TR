# LearnOpenGL TR 🇹🇷

<p align="center">
  <img src="docs/img/getting-started/opengl.jpg" alt="LearnOpenGL TR" width="200" style="border-radius: 12px;"/>
</p>

<p align="center">
  <strong>Joey de Vries'in efsanevi LearnOpenGL kaynağının en kapsamlı, modern ve eksiksiz Türkçe çevirisi.</strong><br>
  <em>Bilgisayar Grafikleri, Shader Matematiği, Oyun Motoru Mimarisi ve PBR Rehberi</em>
</p>

<p align="center">
  <a href="https://kuantumania.github.io/LearnOpenGL-TR/"><img src="https://img.shields.io/badge/Canlı%20Site-Yayında-brightgreen?style=for-the-badge&logo=google-chrome" alt="Live Website"/></a>
  <a href="https://github.com/kuantumania/LearnOpenGL-TR/stargazers"><img src="https://img.shields.io/github/stars/kuantumania/LearnOpenGL-TR?style=for-the-badge&logo=github&color=gold" alt="GitHub Stars"/></a>
  <a href="https://github.com/kuantumania/LearnOpenGL-TR/blob/main/LICENSE"><img src="https://img.shields.io/badge/Lisans-CC%20BY%204.0-blue?style=for-the-badge" alt="License"/></a>
  <img src="https://img.shields.io/badge/C%2B%2B-17%20%2F%2020-00599C?style=for-the-badge&logo=cplusplus" alt="C++"/>
  <img src="https://img.shields.io/badge/OpenGL-3.3%2B%20Core-5586A4?style=for-the-badge&logo=opengl" alt="OpenGL"/>
</p>

---

## 🌟 Proje Hakkında

**LearnOpenGL TR**, Joey de Vries tarafından hazırlanan ve dünya çapında oyun stüdyolarının, üniversitelerin ve teknik sanatçıların bir numaralı başvuru kaynağı olan **[LearnOpenGL.com](https://learnopengl.com)** rehberinin **48 bölümden oluşan tüm çekirdek müfredatını** Türkçeye kazandıran açık kaynaklı bir dokümantasyon projesidir.

* 🌐 **Canlı Web Sitesi:** [kuantumania.github.io/LearnOpenGL-TR](https://kuantumania.github.io/LearnOpenGL-TR/)
* 👨‍💻 **Proje Lideri & Çevirmen:** [Batuhan Dev (@kuantumania)](https://github.com/kuantumania)

---

## 📚 Tam Müfredat (48 Bölüm / 7 Modül)

### 1. Başlarken (Getting Started)
- OpenGL Nedir? (Çekirdek Profil, Durum Makinesi)
- Bir Pencere Oluşturma (GLFW & GLAD Kurulumu)
- Merhaba Pencere (İlk Çizim Döngüsü)
- Merhaba Üçgen (Grafik İşlem Hattı, VBO, VAO, EBO)
- Shader'lar (GLSL, Renk Enterpolasyonu, Shader Sınıfı)
- Dokular (UV Koordinatları, Filtreleme, Mipmaps, Çift Doku)
- Dönüşümler (GLM, Vektör/Matris Matematiği)
- Koordinat Sistemleri (MVP Hattı, Z-Buffer Derinlik Testi, 10 Küp)
- Kamera (LookAt Matrisi, Euler Açıları, Serbest FPS Kamerası)
- 1. Modül Özeti ve Terimler Sözlüğü

### 2. Aydınlatma (Lighting)
- Renkler ve Işık Fiziği
- Temel Aydınlatma (Phong Modeli: Ambient, Diffuse, Specular, Normaller)
- Malzemeler (GLSL Yapıları, Gerçek Dünya Malzeme Tablosu)
- Işık Haritaları (Diffuse & Specular Dokuları)
- Işık Kaynakları (Yönlü Işık, Noktasal Zayıflama, Yumuşak El Feneri)
- Çoklu Işık Kaynakları (Uber Shader Mimarisi)
- Aydınlatma Özeti ve Sözlük

### 3. Model Yükleme (Model Loading)
- Assimp Kütüphanesine Giriş (aiScene, aiMesh, Optimizasyon Bayrakları)
- Mesh Sınıfı Tasarımı
- Model Sınıfı Tasarımı ve 3B Model (Backpack) Renderı

### 4. İleri Düzey OpenGL (Advanced OpenGL)
- Derinlik Testi ve Z-Fighting Çözümleri
- Kalıp Testi (Stencil Testing & Nesne Vurgulama / Outlining)
- Harmanlama (Blending & Şeffaflık)
- Yüzey Ayıklama (Face Culling & Winding Order)
- Çerçeve Tamponları (Framebuffers & Post-Processing Efektleri)
- Küp Haritaları (Cubemaps, Skybox, Ayna Yansıması & Kırılma)
- İleri Düzey Veri Yönetimi
- İleri Düzey GLSL (Uniform Buffer Objects - UBO)
- Geometri Shader (Patlama Efekti & Normalleri Çizme)
- Örnekleme (Instancing - 100.000 Asteroit Renderı)
- Kenar Yumuşatma (Anti-Aliasing - MSAA)

### 5. İleri Düzey Aydınlatma (Advanced Lighting)
- Blinn-Phong Aydınlatma Modeli (Halfway Vector)
- Gama Düzeltmesi (Gamma Correction & sRGB)
- Gölge Haritalama (Shadow Mapping & PCF Yumuşak Gölgeler)
- Noktasal Gölgeler (Omnidirectional Shadow Cubemaps)
- Normal Haritalama (Normal Mapping & TBN Matrisi)
- Paralaks Haritalama (Parallax Occlusion Mapping - POM)
- Yüksek Dinamik Aralık (HDR & Ton Eşleme)
- Bloom Efekti (Gauss Bulanıklığı)
- Ertelenmiş Gölgelendirme (Deferred Shading & G-Buffer)
- Ekran Uzayı Ortam Kapatma (SSAO)

### 6. Fiziksel Tabanlı İşleme (PBR)
- PBR Teorisi (Mikro Yüzeyler, Enerjinin Korunumu, Cook-Torrance BRDF)
- Doğrudan PBR Aydınlatma Shader'ı
- IBL: Yaygın Işınım (Diffuse Irradiance)
- IBL: Aynasal Yansıma (Specular IBL & Split-Sum)

### 7. Uygulamada (In Practice)
- Profesyonel Grafik Hata Ayıklama (RenderDoc & Nsight)
- FreeType ile Metin Çizimi
- 2B Breakout Oyunu (Fizik, Çarpışma, Parçacıklar, Seviyeler)

---

## 🛠️ Yerel Geliştirme

```bash
# Projeyi klonlayın:
git clone https://github.com/kuantumania/LearnOpenGL-TR.git
cd LearnOpenGL-TR

# Bağımlılıkları yükleyin:
pip install mkdocs-material pymdown-extensions

# Yerel geliştirme sunucusunu başlatın:
mkdocs serve
```

---

## 🤝 Katkıda Bulunma

Her türlü katkı, teknik inceleme ve geri bildirim memnuniyetle karşılanır! Detaylar için [Katkıda Bulunma Rehberi](docs/katkida-bulunma.md)'ne göz atabilirsiniz.

---

## 📜 Lisans & Telif

* Orijinal İçerik: **Joey de Vries** - [LearnOpenGL.com](https://learnopengl.com) (CC BY 4.0)
* Türkçe Çeviri, Uyarlama ve Mimari: **[Batuhan Dev](https://github.com/kuantumania)**
