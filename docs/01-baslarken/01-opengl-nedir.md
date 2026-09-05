# OpenGL Mimarisi ve Temel Kavramlar 📐

Bilgisayar grafikleri ve donanım hızlandırmalı görselleştirme alanındaki çalışmalara başlamadan önce, **OpenGL**'in yapısal tanımının doğru yapılması gerekmektedir. 

Yaygın kabule göre OpenGL bir **API** (*Application Programming Interface - Uygulama Programlama Arayüzü*) olarak nitelendirilmekte ve iki/üç boyutlu grafik verilerini manipüle etmek amacıyla kullanılan kapsamlı bir fonksiyon kümesi olarak tanımlanmaktadır. Ne var ki OpenGL, teknik açıdan doğrudan bir yazılım kütüphanesi değil; kâr amacı gütmeyen [Khronos Group](https://www.khronos.org/) konsorsiyumu tarafından geliştirilen ve denetlenen bir **teknik şartnamedir (specification)**.

![OpenGL Logosu](../img/getting-started/opengl.jpg){ align=right width=220 }

Bir şartname olarak OpenGL; grafik işlem hattındaki her bir fonksiyonun alması gereken parametreleri, sergileyeceği kesin davranış biçimini ve üreteceği matematiksel çıktıları bağlayıcı standartlarla tanımlar. Buna karşın, bahsi geçen fonksiyonların donanım seviyesinde *nasıl* yürütüleceği şartname kapsamında yer almaz. Şartnamenin donanıma özgü olarak gerçeklenmesi (**implementation**), donanım üreticilerinin (NVIDIA, AMD, Intel, Apple vb.) sorumluluğundadır.

Sonuç olarak; bir grafik donanımı temin edildiğinde ve sürücü yazılımları sisteme kurulduğunda, aslında ilgili üreticinin OpenGL şartnamesine bağlı kalarak geliştirdiği donanıma özgü sürücü kütüphaneleri sisteme dahil edilmiş olur.

!!! note "Üretici Sürücüleri ve Donanım Uyumluluğu"
    OpenGL standartlarının somut uygulaması üreticiler tarafından yazıldığından, zaman zaman şartname standartları ile sürücü kodları arasında mikro düzeyde farklılıklar ya da yazılımsal hatalar (*driver bugs*) meydana gelebilmektedir. Grafik geliştirme süreçlerinde karşılaşılan beklenmedik donanım anomalilerinde, sürücü yazılımlarının güncelliğinin kontrol edilmesi ilk teknik adımdır.

---

## Çekirdek Profil ve Doğrudan Yürütüm Modu (Core-profile vs. Immediate Mode)

OpenGL'in erken sürümlerinde geliştirme süreçleri, **Doğrudan Yürütüm Modu** (*Immediate Mode* ya da *Fixed Function Pipeline - Sabit Fonksiyon İşlem Hattı*) adı verilen mimari üzerinden yürütülmekteydi. Bu yapıda çizim komutları yüksek seviyeli ve doğrudan çağrılarla gerçekleştirilmekteydi:

```c
// Geleneksel OpenGL (Immediate Mode) Mimarisi - KULLANIMDAN KALDIRILMIŞTIR:
glBegin(GL_TRIANGLES);
    glVertex3f(-0.5f, -0.5f, 0.0f);
    glVertex3f( 0.5f, -0.5f, 0.0f);
    glVertex3f( 0.0f,  0.5f, 0.0f);
glEnd();
```

Bu yaklaşım pedagojik açıdan anlaşılır görünmekle birlikte, modern donanım kaynaklarını optimize etmekten uzaktır. Zira geometri verileri her karede ana bellekten (RAM / CPU) grafik belleğine (VRAM / GPU) tekrar tekrar transfer edilmekte, bu durum veri yolu üzerinde ciddi bant genişliği darboğazlarına (*bus bottleneck*) sebebiyet vermektedir. Modern Grafik İşlem Birimleri (GPU), geniş çapta paralelleştirilmiş SIMD (*Single Instruction, Multiple Data*) hesaplama modelleri üzerine kuruludur ve en yüksek başarım oranına verinin yerel GPU belleğinde toplu olarak işlenmesiyle ulaşır.

Bu teknik zorunluluklar doğrultusunda **OpenGL 3.2** sürümü itibarıyla Khronos Group, Sabit Fonksiyon İşlem Hattı'nı resmi olarak **yürürlükten kaldırmış (deprecated)** ve çağdaş grafik programlamanın temelini oluşturan **Çekirdek Profil (Core-profile)** mimarisini zorunlu standart olarak ilan etmiştir:

* **Çekirdek Profil (Core-profile):** Eski ve verimsiz fonksiyonların kütüphaneden arındırıldığı, geliştiriciyi modern bellek yönetimi yapılarını (**VBO**, **VAO**) ve programlanabilir gölgelendiricileri (**GLSL**) kullanmaya zorlayan standarttır.
* Çekirdek profil altında yapılandırılan bir bağlamda, yürürlükten kaldırılmış fonksiyonların (örneğin `glBegin` / `glEnd`) çağrılması durumunda OpenGL çalışma zamanı (*runtime*) hata üretecek ve çizim komutunu reddedecektir.

Modern OpenGL mimarisi, ekrana en temel geometrik ilkel olan üçgenin çizilmesinde dahi geliştiricinin bellek tahsisi yapmasını ve kendi gölgelendirici (*shader*) programlarını derlemesini gerektirmektedir. Bu yapı, öğrenme eğrisini dikleştirmekle beraber grafik donanımı üzerinde tam denetim, deterministik davranış ve azami hesaplama performansı sağlamaktadır.

!!! tip "Referans Standart: OpenGL 3.3 (Core Profile)"
    Bu çalışmada referans sürüm olarak **OpenGL 3.3 (Core-profile)** esas alınacaktır. 3.3 sürümü, programlanabilir işlem hattının standartlaştığı ve günümüzdeki neredeyse tüm grafik donanımları (entegre grafik yongaları dahil) tarafından donanımsal düzeyde desteklenen evrensel temeldir. 3.3 sürümünün mimari prensipleri kavrandığında, OpenGL 4.x standartlarına ya da modern düşük seviyeli API'lara (Vulkan, DirectX 12) geçiş metodolojik olarak kolaylaşacaktır.

---

## Genişletilebilirlik ve Uzantı Mimarisi (Extensions)

OpenGL spesifikasyonunun en önemli yapısal avantajlarından biri **Uzantı (Extension)** mekanizmasıdır. Donanım üreticileri, geliştirdikleri yeni bir donanımsal kabiliyet veya mikro-mimari optimizasyonunu Khronos Group'un konsensüs sürecine bağlı kalmaksızın derhal sektörün kullanımına sunabilmektedir.

Yeni bir yetenek, sürücü seviyesinde bir uzantı olarak tanımlanır. Yazılım geliştirici, çalışma zamanında uygulamanın çalıştığı donanımın ilgili uzantıyı destekleyip desteklemediğini sorgulayabilir:

```cpp
if (GL_NV_path_rendering) {
    // Üreticiye özgü optimize edilmiş yol tarama uzantısını çalıştır
} else {
    // Standart OpenGL işlem adımlarını yürüt
}
```

Yaygın kabul gören ve sektör genelinde donanım standardına dönüşen üretici uzantıları, Khronos Group tarafından incelenerek sonraki resmi OpenGL sürümlerinin çekirdek (*core*) standardına dahil edilir.

---

## Durum Makinesi Mimarisi (State Machine)

OpenGL, kuramsal olarak devasa bir **Durum Makinesi (State Machine)** prensibiyle çalışır. 

Sistemin belirli bir anda nasıl davranacağını, rasterizasyon kurallarını, harmanlama (*blending*) fonksiyonlarını ve aktif tamponları saklayan değişkenler bütünü **OpenGL Bağlamı (OpenGL Context)** olarak adlandırılır. OpenGL mimarisi genelinde yürütülen operasyonlar iki ana kategoriye ayrılır:

1. **Durum Değiştirici Fonksiyonlar (*State-Changing Functions*):** Bağlamın mevcut durum parametrelerini günceller.
2. **Durum Kullanıcı Fonksiyonlar (*State-Using Functions*):** O anki bağlam durumuna göre işlem hattını tetikler.

```cpp
// 1. Durum Parametresinin Tanımlanması (Durum Değiştirici):
glClearColor(0.2f, 0.3f, 0.3f, 1.0f);

// 2. Aktif Durum Üzerinden Eylemin Yürütülmesi (Durum Kullanıcı):
glClear(GL_COLOR_BUFFER_BIT);
```

Yukarıdaki kod kesitinde `glClearColor` çağrısı, OpenGL bağlamına ekran temizleme rengini durum parametresi olarak işler. Akabinde yürütülen `glClear` çağrısı ise bağlamda saklanan bu güncel rengi kullanarak renk tamponunu temizler.

---

## Nesne Yönelimli Soyutlama Modeli (Objects)

OpenGL kütüphaneleri C standardında geliştirilmiştir; dolayısıyla C++ veya C# gibi dillerdeki yüksek seviyeli nesne yönelimli sınıfları (*classes*) bünyesinde barındırmaz. Bunun yerine bellek kaynaklarının yönetimi, **OpenGL Nesneleri (Objects)** adı verilen tanıtıcı kimlikler (*Identifiers / IDs*) üzerinden soyutlanır.

OpenGL mimarisinde nesne yönetimi genel olarak şu şablonu takip eder:

```cpp
// 1. Bellek nesnesi için tanıtıcı bir kimlik (ID) tahsis edilmesi:
unsigned int objectId = 0;
glGenBuffers(1, &objectId);

// 2. Nesnenin bağlamdaki ilgili hedef yuvaya bağlanması (Binding):
glBindBuffer(GL_ARRAY_BUFFER, objectId);

// 3. Aktif yuvaya bağlanan nesnenin parametrelerinin ve verisinin yapılandırılması:
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

// 4. İşlem tamamlandığında nesne bağlantısının çözülmesi (Unbinding):
glBindBuffer(GL_ARRAY_BUFFER, 0);
```

Bu mekanizma, tüm modern grafik programlama modellerinin temel çalışma prensibidir:
* Bellek nesnesi oluşturulur (`glGen...`),
* Bağlama işlemiyle (`glBind...`) bağlamdaki aktif yuva ile ilişkilendirilir,
* İlişkilendirilmiş yuva üzerinden veri transferi ve parametre konfigürasyonu yapılır,
* Başka bir nesnenin kazara manipüle edilmesini önlemek amacıyla yuva bağlantısı sıfırlanır (`glBind...(0)`).

---

## Özet ve Sonraki Aşama

Bu bölümde;
* OpenGL'in bir kütüphane değil, Khronos Group tarafından denetlenen bağlayıcı bir **teknik şartname** olduğu,
* Şartnamenin donanımsal gerçeklemesinin ekran kartı üreticilerinin **sürücü yazılımları** vasıtasıyla sağlandığı,
* Yüksek başarım ve tam donanım denetimi için **Çekirdek Profil (Core-profile)** standardının zorunluluğu,
* Sistem mimarisinin temelinde **Durum Makinesi** ve **Nesne Bağlama** modellerinin yattığı teorik düzeyde incelenmiştir.

Bir sonraki bölümde, grafik komutlarının icra edileceği platformlar arası pencere ve bağlam yönetim kütüphanesi olan **GLFW** ile donanıma özgü fonksiyon işaretçilerini yükleyeceğimiz **GLAD** kütüphanelerinin kurulumu ele alınacaktır.

👉 **[Sonraki Bölüm: Geliştirme Ortamı ve Pencere Oluşturma (Creating a window)](02-pencere-olusturma.md)**
