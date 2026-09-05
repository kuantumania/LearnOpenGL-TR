# Modern OpenGL ve Bilgisayar Grafikleri 🏛️

Çağdaş bilgisayar grafikleri ve gerçek zamanlı render sistemleri literatürünün en prestijli eğitim kaynağı olan **LearnOpenGL**'in Türkçe edisyonuna hoş geldiniz.

Bu kaynak; çağdaş grafik işlem birimlerinin (GPU) çalışma mimarisini, **OpenGL 3.3+ (Çekirdek Profil)** standartlarını, **GLSL (OpenGL Shading Language)** programlanabilir gölgelendirici işlem hattını ve modern oyun/simülasyon motorlarının temelini oluşturan aydınlatma algoritmalarını (Phong, PBR, IBL) akademik ve metodolojik bir yaklaşımla ele almak üzere yapılandırılmıştır.

<div class="admonition note">
<p class="admonition-title">Proje ve Yayın Hakkında</p>
<p>Bu çalışma, <strong>Batuhan Dev</strong> editörlüğünde; Türkiye bilgisayar mühendisliği, oyun tasarımı ve teknik sanat (Tech Art) ekosistemine dünya standartlarında, terminolojik açıdan tutarlı ve akademik yetkinliğe sahip kalıcı bir Türkçe başvuru kaynağı kazandırmak amacıyla yürütülmektedir.</p>
</div>

---

## 📚 Müfredat ve Kapsam

Dokümantasyon, temel prensiplerden ileri seviye fiziksel tabanlı render mimarilerine kadar birbirini izleyen 6 ana modülden oluşmaktadır:

1. **Modül I: Temel Prensipler ve Grafik İşlem Hattı:**
   OpenGL şartnamesi, durum makinesi yapısı, GLFW ve GLAD bağlam yönetimi, bellek tamponları (VBO, VAO, EBO), GLSL gölgelendirici mimarisi, doku haritalama (Texture Mapping), afin dönüşümler, homojen koordinat sistemleri ve serbest yönelimli 3B kamera matrisleri.

2. **Modül II: Aydınlatma Modelleri (Lighting):**
   Fotometrik renk kuramı, Phong ve Blinn-Phong aydınlatma modelleri, ortam (ambient), yayılma (diffuse) ve speküler (specular) yansıma bileşenleri, materyal özellikleri, ışık haritaları (diffuse/specular maps) ve çoklu ışık matematiği (yönlü ışık, nokta ışık ve projektör ışık kaynakları).

3. **Modül III: Geometrik Model Entegrasyonu:**
   Harici 3B varlıkların (`.obj`, `.fbx`, `.gltf`) sahneye dahil edilmesi, Assimp (*Open Asset Import Library*) kütüphanesinin entegrasyonu, veri yapıları ve nesne yönelimli Mesh / Model sınıf mimarisi.

4. **Modül IV: İleri Düzey OpenGL Teknikleri:**
   Z-Tamponu ve derinlik testi (Depth Testing), şablon testi (Stencil Testing), alfa harmanlama ve saydamlık (Blending), yüzey kırpma optimizasyonları (Face Culling), çerçeve tamponları (Framebuffers) ile ekran sonrası işlemler (Post-processing) ve kübik doku haritalama (Cubemaps / Skybox).

5. **Modül V: İleri Seviye Aydınlatma ve Görsel Efektler:**
   Gama düzeltmesi (Gamma Correction), derinlik haritası üzerinden gölge projeksiyonu (Shadow Mapping), teğet uzayı ve normal eşleme (Normal Mapping), paralaks eşleme (Parallax Mapping), Yüksek Dinamik Aralık (HDR), kamaşma (Bloom) ve Ekran Uzayı Ortam Karartması (SSAO).

6. **Modül VI: Fiziksel Tabanlı Render (PBR - Physically Based Rendering):**
   Mikroyüzey teorisi, Fresnel denklemleri, Cook-Torrance BRDF matematiksel modeli, radyometri ve Görüntü Tabanlı Aydınlatma (IBL - Image-Based Lighting).

---

## 🎯 Başlarken

Grafik programlama alanındaki teorik ve pratik eğitiminize başlamak için sol panelde yer alan **[Modül I: OpenGL Nedir?](01-baslarken/01-opengl-nedir.md)** bölümünü inceleyebilirsiniz.
