# OpenGL Nedir? 📐

Grafik programlama yolculuğumuza başlamadan önce, ilk olarak **OpenGL**'in tam olarak ne olduğunu tanımlamamız gerekir. 

Çoğu zaman OpenGL bir **API** (*Application Programming Interface - Uygulama Programlama Arayüzü*) olarak kabul edilir ve bize grafik ve görüntüleri işlemek için kullanabileceğimiz çok geniş bir fonksiyon koleksiyonu sunar. Ancak teknik olarak OpenGL'in kendisi doğrudan bir API değil; [Khronos Group](https://www.khronos.org/) tarafından geliştirilen ve bakımı yapılan bir **Spesifikasyondur (Şartnamedir)**.

![OpenGL Logosu](../img/getting-started/opengl.jpg){ align=right width=220 }

Bir şartname olarak OpenGL, her bir fonksiyonun tam olarak ne yapması gerektiğini ve hangi çıktıyı üretmesi gerektiğini kurala bağlar; ancak bu fonksiyonların arka planda *nasıl* çalıştırılacağını (nasıl kodlanacağını) belirlemez. Bu spesifikasyonu alıp gerçek kodlara dökmek, ekran kartı üreticilerinin (NVIDIA, AMD, Intel, Apple vb.) görevidir. 

Dolayısıyla bir ekran kartı satın aldığınızda ve sürücülerini kurduğunuzda, aslında o kart üreticisinin ilgili OpenGL şartnamesine göre bizzat C dilinde yazdığı kütüphaneleri (sürücüleri) sisteminize yüklemiş olursunuz.

!!! note "Ekran Kartı Sürücüleri ve Hatalar"
    OpenGL implementasyonu bizzat ekran kartı üreticisi tarafından yazıldığı için, bazen şartnamede belirtilen standartlar ile üreticinin yazdığı kod arasında ufak farklılıklar veya sürücü hataları (*driver bugs*) oluşabilir. Bu tür durumlarda genellikle ekran kartı sürücülerinizi en son sürüme güncellemek ilk çözüm yoludur.

---

## Çekirdek Profil ve Doğrudan Mod (Core-profile vs Immediate mode)

Eski günlerde OpenGL kullanmak **Doğrudan Mod** (*Immediate Mode* veya *Fixed Function Pipeline*) olarak bilinen bir yapıyla gerçekleştirilirdi. Bu yöntemde grafik çizimleri yapmak son derece kolaydı:

```c
// Eski OpenGL (Immediate Mode) Örneği - ARTIK KULLANILMIYOR:
glBegin(GL_TRIANGLES);
    glVertex3f(-0.5f, -0.5f, 0.0f);
    glVertex3f( 0.5f, -0.5f, 0.0f);
    glVertex3f( 0.0f,  0.5f, 0.0f);
glEnd();
```

Yukarıdaki kod yeni başlayan biri için çok basit ve anlaşılırdır; fakat **korkunç derecede verimsizdir**. Çünkü hesaplamaların büyük kısmı ekran kartında (GPU) değil işlemcide (CPU) döner ve her çizimde veriler CPU'dan GPU'ya teker teker aktarılır. Modern ekran kartları ise aynı anda milyonlarca matematiksel işlemi paralel yürütebilen devasa işlem canavarlarıdır.

Bu nedenle **OpenGL 3.2** sürümüyle birlikte Khronos Group, eski Immediate Mode mimarisini kullanımdan kaldırmaya (*deprecated*) karar verdi ve modern dünyayı temsil eden **Çekirdek Profil (Core-profile)** yapısını standart hale getirdi:

* **Core-profile (Çekirdek Profil):** Eski ve verimsiz fonksiyonların tamamen çöpe atıldığı, geliştiriciyi modern pratikleri (VBO, VAO, Shader'lar) kullanmaya zorlayan yapıdır.
* Core-profile ile çalıştığınızda, eski fonksiyonlardan herhangi birini (örneğin `glBegin`) çağırmaya çalışırsanız OpenGL bir hata bayrağı fırlatır ve çizim yapmayı reddeder.

Modern OpenGL'i öğrenmek ilk başta korkutucu görünebilir; çünkü ekrana tek bir üçgen çizmek için bile bellek tamponları tahsis etmeniz ve **GLSL (OpenGL Shading Language)** ile kendi gölgelendirici (*shader*) programlarınızı yazmanız gerekir. Fakat bu yaklaşım, modern GPU donanımı üzerinde size mutlak bir kontrol ve inanılmaz bir performans gücü sağlar.

!!! tip "Biz Hangi Sürümü Kullanacağız?"
    Bu rehber boyunca **OpenGL 3.3 (Core-profile)** standardını kullanacağız. 3.3 sürümü, modern OpenGL'in temel taşlarının oturduğu ve günümüzdeki tüm modern ekran kartları (hatta Intel dahili grafik kartları) tarafından eksiksiz desteklenen altın standarttır. 3.3'ü anladıktan sonra 4.x sürümlerine geçmek sadece birkaç yeni ek özelliği öğrenmekten ibarettir.

---

## Uzantılar (Extensions)

OpenGL'in en güçlü yönlerinden biri **Uzantı (Extension)** mekanizmasıdır. Bir ekran kartı üreticisi (örneğin NVIDIA) kartlarına yepyeni bir donanım özelliği veya optimizasyon geliştirdiğinde, yeni bir resmi OpenGL sürümünün Khronos tarafından onaylanmasını beklemek zorunda kalmaz.

Üretici bu özelliği bir uzantı olarak sürücülerine ekler. Eğer uygulamanız o kartın üzerinde çalışıyorsa, kodunuz bu uzantının varlığını sorgulayabilir:

```cpp
if (GL_NV_path_rendering) {
    // NVIDIA'nın özel path rendering uzantısını kullan!
} else {
    // Standart OpenGL yöntemiyle devam et.
}
```

Eğer bir uzantı sektörde çok sevilir ve diğer üreticiler tarafından da desteklenmeye başlarsa, Khronos Group o uzantıyı bir sonraki resmi OpenGL sürümünün çekirdek (*core*) parçası yapar.

---

## Durum Makinesi (State Machine)

OpenGL özünde devasa bir **Durum Makinesidir (State Machine)**. 

OpenGL'in o an nasıl davranacağını, ekrana ne çizeceğini veya hangi renkleri kullanacağını belirleyen değişkenler bütününe **OpenGL Bağlamı (OpenGL Context)** denir. OpenGL ile çalışırken genellikle şu döngüyü takip ederiz:

1. **Durumu Değiştir:** *"Şu dokuyu etkinleştir, arka planı koyu gri yap, derinlik testini aç."*
2. **Eylemi Gerçekleştir (Çizim Yap):** *"Mevcut aktif duruma göre üçgenleri çiz."*

```cpp
// 1. Durumu belirle (Durum Değiştirici Fonksiyon):
glClearColor(0.2f, 0.3f, 0.3f, 1.0f);

// 2. Durumu kullan (Durum Kullanıcı Fonksiyon):
glClear(GL_COLOR_BUFFER_BIT);
```

Yukarıdaki kodda `glClearColor`, OpenGL'in durum makinesine arka planı temizlerken hangi rengi kullanacağını bildirir (durum değişti). Ardından `glClear` çağrıldığında, tanımlanan o anki durum baz alınarak ekran temizlenir.

---

## Nesneler (Objects)

OpenGL kütüphaneleri C dilinde yazılmıştır; bu yüzden nesne yönelimli programlama dillerindeki (C++, C#) gibi `class` veya `struct` yapılarını doğrudan dışarı sunmaz. Bunun yerine **Nesne (Object)** soyutlamasını benzersiz kimlik numaraları (*ID*) üzerinden yürütür.

OpenGL'de bir nesne, GPU belleğindeki bir veri kümesini temsil eder. Tipik bir nesne yönetim modeli şöyledir:

```cpp
// 1. Nesne için bellekte bir kimlik numarası (ID) oluştur:
unsigned int objectId = 0;
glGenBuffers(1, &objectId);

// 2. Nesneyi OpenGL bağlamındaki hedef yuvaya bağla (Bind et):
glBindBuffer(GL_ARRAY_BUFFER, objectId);

// 3. Bağlanan nesnenin özelliklerini veya belleğini ayarla:
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

// 4. İşi bitince hedef yuvayı boşa çıkar (Unbind et):
glBindBuffer(GL_ARRAY_BUFFER, 0);
```

Bu model grafik dünyasının temelidir:
* Önce bir ID üretilir (`glGen...`).
* Ardından hedef yuvaya bağlanır (`glBind...`). Bir nesne bağlandığı andan itibaren yapılacak tüm ayar fonksiyonları doğrudan o nesneyi etkiler.
* İşlem tamamlandığında 0 atanarak bağ çözülür (`Unbind`).

---

## Özet ve Sıradaki Adım

Artık OpenGL'in:
* Khronos Group tarafından belirlenen bir **şartname**,
* Ekran kartı üreticileri tarafından sağlanan bir **sürücü**,
* Verimsiz Immediate Mode yerine modern **Core-profile** ile çalışan bir sistem,
* Ve temelde bir **Durum Makinesi** olduğunu öğrendik.

Artık teoriyi geride bırakıp kod yazmaya başlayabiliriz! Bir sonraki bölümde, OpenGL'in çizim yapabilmesi için gereken bir pencere ortamını (**GLFW**) ve fonksiyon işaretçilerini yükleyeceğimiz (**GLAD**) kütüphanelerini kurarak kendi ilk penceremizi oluşturacağız.

👉 **[Sıradaki Bölüm: Bir Pencere Oluşturma (Creating a window)](02-pencere-olusturma.md)**
