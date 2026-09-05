# 2. Modül Özeti ve Terimler Sözlüğü (Lighting Review) 🌟

Muhteşem bir başarı! Bilgisayar grafiklerinin en büyüleyici ve temel konusu olan **Aydınlatma (Lighting)** modülünü tamamladınız.

Sadece iki modül içinde:
1. Karanlık ve renksiz bir dünyadan,
2. Güneş ışınlarının, parıldayan el fenerlerinin, neon sandıkların ve çoklu renkli lambaların aydınlattığı **sinematik bir 3B evrene** geçiş yaptınız!

---

## Neler Başardık? 🏆

* **Phong Aydınlatma Modeli:** Işığı 3 fiziksel bileşenine (Ortam, Yaygın ve Aynasal) ayırarak piksel bazında (*per-fragment*) gerçek zamanlı hesaplamayı öğrendik.
* **Yüzey Normalleri ve Normal Matrisi:** Yüzeylerin yönünü gösteren normal vektörlerini ve model matrisi ölçeklendiğinde bu normallerin bozulmasını engelleyen ters-transpoz matrisini kavradık.
* **Malzeme Sistemi:** Ahşap, metal, altın veya plastik gibi farklı fiziksel yüzeylerin ışığa verdiği farklı tepkileri GLSL yapılarıyla modelledik.
* **Işık Haritaları (Lighting Maps):** Nesnenin tek bir renge hapsolmasını engelleyip, piksel piksel neresinin ahşap (mat), neresinin çelik (parlak) olacağını belirleyen Diffuse ve Specular doku haritalarını entegre ettik.
* **Işık Kaynakları:**
  * Paralel ışınlarıyla sonsuzdaki **Yönlü Işık (Güneş)**,
  * Mesafeyle karesel azalan **Noktasal Işık (Zayıflama / Attenuation)**,
  * İç ve dış konisiyle yumuşak kenarlı **Spot Işık (El Feneri)**.
* **Çoklu Işık Birleşimi:** Tüm bu ışıkları tek bir GPU gölgelendiricisinde modüler fonksiyonlarla bir araya getirdik!

---

## Aydınlatma Terimler Sözlüğü (Lighting Glossary) 📖

| Terim (EN) | Türkçe Karşılığı | Açıklama |
| :--- | :--- | :--- |
| **Phong Lighting Model** | Phong Aydınlatma Modeli | Ortam, yaygın ve aynasal yansımayı birleştiren temel gerçek zamanlı aydınlatma algoritması. |
| **Ambient Lighting** | Ortam Işığı | Işığın diğer yüzeylerden sekerek oluşturduğu ve zifiri karanlığı önleyen sabit genel aydınlatma. |
| **Diffuse Lighting** | Yaygın Işık | Işığın yüzeye dik gelme açısına (skaler çarpım) bağlı olarak nesneye hacim ve gölge kazandıran yönlü ışık bileşeni. |
| **Specular Lighting** | Aynasal Parlama | Işık ışınının yüzeyden sekip doğrudan gözlemcinin gözüne ulaşmasıyla oluşan parlak yansıma noktası. |
| **Normal Vector** | Normal Vektörü | Bir yüzeye tam olarak dik ($90^\circ$) olan ve yüzeyin baktığı yönü temsil eden birim vektör. |
| **Normal Matrix** | Normal Matrisi | Model matrisine homojen olmayan ölçekleme uygulandığında normallerin dikliğini koruyan model matrisinin tersinin transpozu. |
| **Shininess** | Parlaklık Üssü | Aynasal parlamanın yüzeydeki yarıçapını ve keskinliğini belirleyen üs katsayısı ($2, 8, 32, 128$). |
| **Lighting Map** | Işık Haritası | Nesnenin malzeme özelliklerini tek bir değer yerine piksel bazında dokulardan okumayı sağlayan yöntem. |
| **Diffuse Map** | Yaygın Doku Haritası | Nesnenin temel yüzey rengini ve desenini belirleyen doku. |
| **Specular Map** | Aynasal Parlama Haritası | Nesnenin nerelerinin metalik parlayacağını, nerelerinin mat kalacağını belirleyen siyah-beyaz maske dokusu. |
| **Directional Light** | Yönlü Işık | Konumu olmayan, sadece yönü olan ve tüm ışınları paralel gelen ışık kaynağı (Güneş / Ay). |
| **Point Light** | Noktasal Işık | Belirli bir konumu olan ve her yöne küresel ışık saçan kaynak (Ampul / Meşale). |
| **Attenuation** | Zayıflama | Işık kaynağından uzaklaştıkça ışık şiddetinin mesafeyle azalması formülü ($1 / (K_c + K_l \cdot d + K_q \cdot d^2)$). |
| **Spotlight** | Spot Işık | Belirli bir koniden yönlü olarak ışık saçan konik kaynak (El feneri / Sahne spotu). |
| **Cutoff Angle** | Kesme Açısı | Spot ışığın koni sınırlarını belirleyen yarıçap açısı. |

---

## Sıradaki Dev Adım: 3. Modül — Model Yükleme (Model Loading)! 🗿

Şimdiye kadar sahnemize sadece ellerimizle C++ kodunda vertex dizilerini tek tek yazdığımız **KÜPLER** ekledik.

Ama gerçek bir oyun ya da grafik uygulamasında bir arabayı, bir canavarı, bir uzay gemisini ya da bir ortaçağ kalesini elle kodlamak mümkün değildir! 3B modelleme sanatçıları Blender, Maya veya 3ds Max kullanarak milyonlarca üçgenden oluşan büyüleyici modeller üretirler.

3. Modülde grafik dünyasının en popüler model yükleme kütüphanesi olan **Assimp**'i projemize entegre edecek, `.obj` ve `.fbx` dosyalarını okuyacak ve sahnemizde profesyonel 3B modeller render edeceğiz!

👉 **[3. Modüle Geç: Assimp ve Model Yükleme](../03-model-yukleme/01-assimp.md)**
