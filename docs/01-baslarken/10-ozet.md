# 1. Modül Özeti ve Terimler Sözlüğü (Review & Glossary) 📚

**Tebrikler!** "Başlarken" (*Getting Started*) modülünün sonuna başarıyla ulaştınız. Sıfırdan başladığımız bu yolculukta, modern bilgisayar grafiklerinin en temel ve en kritik yapı taşlarını tek tek inşa ettik.

Şu anda elinizde:
1. İşletim sisteminden bağımsız modern bir pencere açabilen (**GLFW** & **GLAD**),
2. GPU belleğinde tamponlar oluşturup yönetebilen (**VBO**, **VAO**, **EBO**),
3. Kendi **Vertex ve Fragment Shader**'larını derleyip bağlayabilen,
4. Çift doku katmanını (*texture units*) filtreleme ve mipmap teknikleriyle harmanlayabilen,
5. 3B uzayda **MVP (Model-View-Projection)** matris dönüşümlerini uygulayabilen,
6. **Z-Buffer** ile derinlik testini hatasız gerçekleştiren,
7. Ve klavye-fare ile serbestçe gezinebildiğiniz 60+ FPS bir **Kamera Sınıfına** sahip tam teşekküllü bir C++ / OpenGL altyapınız var!

Bir sonraki modül olan **Aydınlatma (Lighting)** dünyasına geçmeden önce, öğrendiğimiz tüm kavramları kristalize edecek kapsamlı bir **Terimler Sözlüğü** ve karşılaştığınızda hayat kurtaracak **Hata Ayıklama (Debugging) İpuçları** hazırladık.

---

## Grafik Programlama Terimler Sözlüğü (Glossary) 📖

Aşağıdaki tablo, uluslararası oyun stüdyolarında ve grafik mühendisliğinde kullanılan standart İngilizce terimler ile Türkçe karşılıklarını ve açıklamalarını içerir:

| Terim (EN) | Türkçe Karşılığı | Açıklama |
| :--- | :--- | :--- |
| **OpenGL** | Açık Grafik Kütüphanesi | Grafik donanımını kontrol etmek için fonksiyonların imzasını ve çıktısını belirleyen resmi grafik API şartnamesi. |
| **GLAD** | Uzantı Yükleyici Kütüphane | İşletim sistemine ve GPU sürücüsüne özgü modern OpenGL fonksiyon işaretçilerini (*function pointers*) çalışma anında yükleyen kütüphane. |
| **Viewport** | Çizim / Görünüm Alanı | Pencere içerisinde sahnenin çizdirildiği 2B piksel bölgesi (`glViewport`). |
| **Graphics Pipeline** | Grafik İşlem Hattı | Tepe noktalarının ekranda nihai piksellere dönüşene kadar geçtiği tüm donanımsal ve yazılımsal aşamalar bütünü. |
| **Shader** | Gölgelendirici | Doğrudan grafik kartı (GPU) üzerinde paralel çalışan küçük, özelleştirilmiş C benzeri programlar (GLSL). |
| **Vertex** | Tepe Noktası / Köşe | 3B uzayda bir konumu ve ona ait ek verileri (renk, normal, UV koordinatı) temsil eden veri paketi. |
| **NDC (Normalized Device Coordinates)** | Normalleştirilmiş Cihaz Koordinatları | Kırpma uzayındaki koordinatların $w$'ya bölünmesiyle elde edilen, $[-1.0, 1.0]$ aralığındaki standartlaştırılmış koordinat uzayı. |
| **VBO (Vertex Buffer Object)** | Tepe Noktası Tampon Nesnesi | GPU video belleğinde (VRAM) yer ayıran ve binlerce köşe noktası verisini saklayan tampon bellek nesnesi. |
| **VAO (Vertex Array Object)** | Tepe Noktası Dizi Nesnesi | VBO bağlantılarını ve köşe niteliklerinin (*vertex attributes*) biçim yapılandırmasını tek çatı altında saklayan durum nesnesi. |
| **EBO / IBO** | Eleman / İndeks Tampon Nesnesi | Tekrarlayan tepe noktalarını bellekte çoğaltmadan indeksler üzerinden çizim yapmayı sağlayan tampon bellek (`glDrawElements`). |
| **Uniform** | Evrensel / Sabit Değişken | CPU'dan GPU'daki tüm shader evrelerine gönderilen, çizim çağrısı boyunca değeri sabit kalan global GLSL değişkeni. |
| **Texture** | Doku | Nesnelerin yüzeyine sarılarak onlara gerçekçi detay ve renk yanılsaması kazandıran 2B/3B dijital resim verisi. |
| **Texture Wrapping** | Doku Sarma Modu | Doku koordinatları $[0.0, 1.0]$ aralığının dışına taştığında OpenGL'in dokuyu nasıl tekrarlayacağını veya kırpacağını belirleyen kural (`REPEAT`, `CLAMP_TO_EDGE`). |
| **Texture Filtering** | Doku Filtreleme Modu | Doku çözünürlüğü ile ekrandaki piksel boyutu uyuşmadığında (büyütme/küçültme) texel renklerinin nasıl örnekleneceğini belirleyen yöntem (`NEAREST`, `LINEAR`). |
| **Mipmaps** | Doku Seviye Piramidi | Kameraya olan uzaklığa göre GPU tarafından otomatik seçilen, orijinal dokunun gittikçe küçülen önceden hesaplanmış çözünürlük zinciri. |
| **stb_image** | Resim Yükleme Kütüphanesi | PNG, JPEG gibi popüler resim formatlarını piksel bayt dizisine dönüştüren hafif C başlık kütüphanesi. |
| **Texture Units** | Doku Birimleri | Tek bir shader içerisinde birden fazla dokunun eşzamanlı kullanılmasına imkan veren donanımsal doku yuvaları (`GL_TEXTURE0`..`GL_TEXTURE15`). |
| **Vector** | Vektör | Uzayda hem yönü hem de büyüklüğü (uzunluğu) olan matematiksel nicelik. |
| **Matrix** | Matris | Sayılardan oluşan, öteleme, döndürme ve ölçekleme gibi geometrik dönüşümleri gerçekleştiren 2B tablo. |
| **GLM** | OpenGL Matematik Kütüphanesi | GLSL dilinin matematiksel fonksiyonlarını ve vektör/matris tiplerini C++ tarafında sunan endüstri standardı kütüphane. |
| **Local Space** | Yerel Uzay | Bir 3B modelin kendi merkezine göre tanımlandığı, modellendiği orijinal koordinat uzayı. |
| **World Space** | Dünya Uzayı | Sahnedeki tüm nesnelerin ortak bir global orijin noktasına göre konumlandırıldığı evrensel uzay. |
| **View Space** | Görünüm / Kamera Uzayı | Sahnedeki tüm koordinatların kameranın konumuna ve bakış açısına göre dönüştürüldüğü gözlemci uzayı. |
| **Clip Space** | Kırpma Uzayı | İzdüşüm matrisinin uygulandığı, görüş alanı dışındaki köşelerin kırpıldığı koordinat uzayı. |
| **Screen Space** | Ekran Uzayı | Kırpma uzayındaki koordinatların ekran çözünürlüğündeki piksel adreslerine ($[0, W], [0, H]$) haritalandığı nihai 2B uzay. |
| **LookAt** | Hedefe Bakış Matrisi | Verilen bir kamera konumu, bakış hedefi ve yukarı vektöründen Görünüm Matrisi oluşturan özel dönüşüm matrisi. |
| **Euler Angles** | Euler Açıları | 3B uzayda herhangi bir yönü temsil edebilen üç temel açı: Sapma (*Yaw*), Eğim (*Pitch*) ve Yuvarlanma (*Roll*). |
| **Z-Buffer** | Derinlik Tamponu | Her bir pikselin kameraya olan mesafesini saklayarak öndeki nesnelerin arkadakileri kapatmasını sağlayan donanımsal derinlik belleği (`GL_DEPTH_TEST`). |

---

## Grafik Programlamada Hayat Kurtaran Hata Ayıklama Kontrol Listesi 🔍

OpenGL ile çalışırken siyah bir ekranla karşılaşmak her geliştiricinin başına gelir. Bir şeyler ters gittiğinde şu 7 maddeyi mutlaka sırayla kontrol edin:

1. **Shader Derleme Hatalarını Kontrol Edin:**
   Shader derleme (`glGetShaderiv`) ve program bağlama (`glGetProgramiv`) adımlarında info log'ları konsola yazdırıyor musunuz? Sözdizimi hatası olan bir shader sessizce başarısız olur ve siyah ekran verir.

2. **VAO Bağlantısını Unutmayın:**
   `glDrawArrays` veya `glDrawElements` çağırmadan hemen önce ilgili nesnenin `glBindVertexArray(VAO)` çağrısını yaptınız mı?

3. **Shader Programını Aktif Ettiniz mi?**
   Çizim çağrısından önce `glUseProgram(shaderProgram)` fonksiyonunu çağırdınız mı?

4. **Kamera Nereye Bakıyor?**
   Kameranız sahnenin içine mi gömülü kaldı? Ya da kameranız nesnelerin tam tersi yönüne mi bakıyor? Nesnelerinizin $Z$ koordinatı negatifte iken kameranız pozitif $Z$'de olmalıdır.

5. **Derinlik Tamponunu Temizlediniz mi?**
   `glEnable(GL_DEPTH_TEST)` açtıysanız, her karenin başında sadece renk tamponunu değil, derinlik tamponunu da temizlemelisiniz:
   ```cpp
   glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
   ```

6. **Kırpma Düzlemlerini (Near / Far Plane) Aştınız mı?**
   `glm::perspective` içindeki yakın düzlem `0.1f`, uzak düzlem `100.0f` ise ve nesneniz $Z = -150.0f$ konumundaysa ekranda hiçbir şey görünmez!

7. **OpenGL Hata Kodunu Sorgulayın:**
   Şüphelendiğiniz fonksiyonların ardından `glGetError()` çağırarak `GL_INVALID_ENUM`, `GL_INVALID_VALUE` veya `GL_INVALID_OPERATION` gibi donanım hatalarını yakalayın.

---

## Sıradaki Macera: 2. Modül — Aydınlatma (Lighting)! 💡

Şu ana kadar dünyamızdaki nesneler dokularının ham renkleriyle aydınlanıyordu. Ancak gerçek dünyayı inandırıcı kılan şey ışıktır: Gölgeler, parlak yüzeyler, ortam ışığı ve metalik yansımalar...

2. Modülde bilgisayar grafiklerinin en büyüleyici konularına dalıyoruz:
* **Phong Aydınlatma Modeli:** Ortam Işığı (*Ambient*), Yaygın Işık (*Diffuse*) ve Aynasal Parlama (*Specular*).
* **Malzemeler (Materials):** Ahşap, plastik, altın veya zümrüt gibi yüzeylerin ışığa verdiği farklı tepkileri matematiksel olarak simüle etme.
* **Işık Haritaları (Lighting Maps):** Diffuse haritaları ve Specular haritaları ile dokulara metalik parlaklık efektleri verme.
* **Işık Kaynakları (Light Casters):** Güneş gibi Yönlü Işıklar (*Directional Light*), Ampul gibi Noktasal Işıklar (*Point Light*) ve El Feneri gibi Spot Işıklar (*Spotlight*).
* **Çoklu Işık Kaynakları:** Sahneye onlarca farklı renk ve türde ışık kaynağı yerleştirip hepsini tek bir Fragment Shader'da birleştirme!

Hazırsanız, ışığı açıyoruz!

👉 **[2. Modüle Geç: Renkler ve Temel Aydınlatma](../02-aydinlatma/01-renkler.md)**
