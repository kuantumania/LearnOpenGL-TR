# OpenGL Nedir? 📐

Bu heyecan verici grafik programlama yolculuğumuza başlamadan önce, ilk olarak **OpenGL**'in gerçekte ne olduğunu tanımlamamız gerekir. 

Çoğu zaman OpenGL bir **API** (*Application Programming Interface - Uygulama Programlama Arayüzü*) olarak düşünülür ve bize grafikler ve görüntüler oluşturmak için kullanabileceğimiz devasa bir fonksiyon koleksiyonu sunar. Ancak teknik olarak OpenGL tek başına bir API değil; kâr amacı gütmeyen [Khronos Group](https://www.khronos.org/) tarafından geliştirilen ve sürekli güncellenen bir **teknik şartnamedir (specification)**.

![OpenGL Logosu](../img/getting-started/opengl.jpg){ align=right width=220 }

Bir şartname olarak OpenGL, her bir fonksiyonun tam olarak ne yapması gerektiğini, hangi parametreleri alıp ne sonuç vermesi gerektiğini katı kurallarla belirler; fakat bu fonksiyonların arka planda *nasıl* çalıştırılacağını söylemez. Bu şartnameyi alıp gerçek çalışan kodlara dökmek, ekran kartı üreticilerinin (NVIDIA, AMD, Intel, Apple vb.) görevidir.

Dolayısıyla yeni bir ekran kartı alıp sürücülerini kurduğunuzda, aslında o kart üreticisinin ilgili OpenGL şartnamesine bağlı kalarak C dilinde bizzat yazdığı kütüphaneleri sisteminize yüklemiş olursunuz.

!!! note "Sürücüler ve Hatalar"
    OpenGL implementasyonunu bizzat ekran kartı üreticileri yazdığı için, zaman zaman şartnamede belirtilen standartlar ile üreticinin kodu arasında ufak farklılıklar ya da sürücü hataları (*driver bugs*) ortaya çıkabilir. Grafik geliştirirken beklenmedik donanım gariplikleriyle karşılaşırsanız, ekran kartı sürücülerinizi en son sürüme güncellemek genellikle ilk adımdır.

---

## Çekirdek Profil ve Doğrudan Mod (Core-profile vs. Immediate Mode)

Eski günlerde OpenGL kullanmak **Doğrudan Mod** (*Immediate Mode* ya da *Fixed Function Pipeline - Sabit Fonksiyon İşlem Hattı*) olarak bilinen yöntemle yapılırdı. Bu yöntemle ekrana grafik çizdirmek gerçekten çok kolaydı:

```c
// Eski OpenGL (Immediate Mode) - ARTIK KULLANILMIYOR:
glBegin(GL_TRIANGLES);
    glVertex3f(-0.5f, -0.5f, 0.0f);
    glVertex3f( 0.5f, -0.5f, 0.0f);
    glVertex3f( 0.0f,  0.5f, 0.0f);
glEnd();
```

Bu yöntem yeni başlayanlar için çok pratik ve anlaşılır görünse de **oldukça verimsizdir**. Çünkü hesaplamaların büyük çoğunluğu ekran kartı (GPU) yerine işlemci (CPU) üzerinde yapılır ve veriler her karede CPU'dan GPU'ya teker teker aktarılır. Oysa modern ekran kartları, aynı anda binlerce matematiksel işlemi paralel olarak yürütebilen devasa donanımlardır ve veriyi kendi yerel belleklerinde topluca işlemeyi severler.

Bu nedenle **OpenGL 3.2** sürümüyle birlikte Khronos Group, eski Doğrudan Mod mimarisini kullanımdan kaldırmaya (*deprecated*) karar verdi ve modern yaklaşımı temsil eden **Çekirdek Profil (Core-profile)** yapısını zorunlu hale getirdi:

* **Çekirdek Profil (Core-profile):** Eski ve verimsiz yöntemlerin tamamen geride bırakıldığı, bizi modern pratikleri (VBO, VAO bellek tamponları) ve programlanabilir gölgelendiricileri (**GLSL**) kullanmaya teşvik eden standarttır.
* Çekirdek profil ile çalışırken eski fonksiyonlardan birini (örneğin `glBegin`) çağırmaya kalkarsanız, OpenGL bir hata fırlatır ve çizim yapmayı reddeder.

Modern OpenGL'i öğrenmek ilk başta gözünüzü korkutabilir; çünkü ekrana basit bir üçgen çizmek için bile bellek tamponları tahsis etmeniz ve kendi küçük gölgelendirici (*shader*) programlarınızı yazmanız gerekir. Fakat bana güvenin: Bu yaklaşım size hem modern GPU donanımı üzerinde mutlak bir kontrol kazandıracak hem de hayal ettiğiniz yüksek performansı sunacaktır!

!!! tip "Biz Hangi Sürümü Kullanacağız?"
    Bu rehber boyunca **OpenGL 3.3 (Core-profile)** standardını kullanacağız. 3.3 sürümü, modern OpenGL'in temel taşlarının oturduğu ve günümüzdeki neredeyse tüm ekran kartları (hatta Intel dahili grafik kartları) tarafından eksiksiz desteklenen altın standarttır. 3.3'ün mantığını kavradığınızda, 4.x sürümlerine veya Vulkan gibi yeni nesil API'lara geçmek çocuk oyuncağı olacaktır.

---

## Uzantılar (Extensions)

OpenGL'in en harika özelliklerinden biri **Uzantı (Extension)** desteğidir. Bir ekran kartı üreticisi (örneğin NVIDIA) kartları için yepyeni bir donanım kabiliyeti veya optimizasyon geliştirdiğinde, yeni bir resmi OpenGL sürümünün onaylanmasını beklemek zorunda kalmaz.

Üretici bu yeni özelliği sürücüsüne bir uzantı olarak ekler. Kodunuz çalışırken ilgili donanımın bu uzantıyı destekleyip desteklemediğini tek bir satırla sorgulayabilirsiniz:

```cpp
if (GL_NV_path_rendering) {
    // NVIDIA'nın özel hızlı path rendering uzantısını kullan!
} else {
    // Standart OpenGL yöntemiyle devam et.
}
```

Eğer bu uzantı geliştiriciler tarafından çok sevilir ve diğer üreticiler tarafından da benimsenirse, Khronos Group bu uzantıyı sonraki resmi OpenGL sürümlerinin çekirdek (*core*) bir parçası haline getirir.

---

## Durum Makinesi (State Machine)

OpenGL, özünde devasa bir **Durum Makinesidir (State Machine)**. 

OpenGL'in o anda nasıl davranacağını, ekrana ne çizeceğini veya hangi ayarları kullanacağını belirleyen değişkenler bütününe **OpenGL Bağlamı (OpenGL Context)** denir. OpenGL ile çalışırken adımlarımız genellikle çok nettir:

1. **Durumu Değiştir:** *"Şu dokuyu aktif yap, arka plan temizleme rengini koyu yeşil yap."*
2. **Durumu Kullan (Çiz):** *"Mevcut aktif ayarlara göre üçgenleri ekrana çiz."*

```cpp
// 1. Durumu belirle (Durum Değiştirici Fonksiyon):
glClearColor(0.2f, 0.3f, 0.3f, 1.0f);

// 2. Durumu kullan (Durum Kullanıcı Fonksiyon):
glClear(GL_COLOR_BUFFER_BIT);
```

Yukarıdaki kodda `glClearColor`, OpenGL'in durum makinesine ekranı temizlerken hangi rengi kullanacağını söyler. Ardından `glClear` çağrıldığında, tanımladığımız o güncel durum rengi kullanılarak ekran temizlenir.

---

## Nesneler (Objects)

OpenGL kütüphaneleri C dilinde yazılmıştır. Bu yüzden C++ veya C# gibi dillerdeki yüksek seviyeli `class` yapıları doğrudan bulunmaz. Bunun yerine OpenGL, geliştiricilerin hayatını kolaylaştırmak için **Nesne (Object)** soyutlamasını kullanır.

OpenGL'de bir nesne, GPU belleğindeki belirli bir veri kümesini veya ayar grubunu temsil eder. Tipik bir nesne kullanım şablonu şöyledir:

```cpp
// 1. Nesne için bellekte benzersiz bir kimlik numarası (ID) oluştur:
unsigned int objectId = 0;
glGenBuffers(1, &objectId);

// 2. Nesneyi ilgili hedef yuvaya bağla (Bind et):
glBindBuffer(GL_ARRAY_BUFFER, objectId);

// 3. Bağlanan nesnenin özelliklerini veya belleğini ayarla:
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

// 4. İşi bitince hedef yuvayı boşa çıkar (Unbind et):
glBindBuffer(GL_ARRAY_BUFFER, 0);
```

Bu model grafik dünyasının kalbidir:
* Önce nesnenin kimliği üretilir (`glGen...`),
* Ardından hedef yuvaya bağlanır (`glBind...`). Bir nesne bağlandığı andan itibaren yapacağınız tüm ayarlar doğrudan o nesneyi etkiler,
* İşlem bitince başka bir nesneyi yanlışlıkla değiştirmemek için yuva boşa çıkarılır (`glBind...(0)`).

---

## Haydi Başlayalım! 🚀

Artık OpenGL'in ne olduğuna dair temel bir fikriniz olduğuna göre, **muhtemelen kod yazmaya başlamak için sabırsızlanıyorsunuzdur!** 

Bir sonraki bölümde, OpenGL komutlarımızı çalıştırabilmemiz için gereken pencere ortamını (**GLFW**) ve donanıma özel fonksiyonları yükleyeceğimiz (**GLAD**) kütüphanelerini kurarak kendi ilk penceremizi oluşturacağız.

👉 **[Sonraki Bölüm: Bir Pencere Oluşturma (Creating a window)](02-pencere-olusturma.md)**
