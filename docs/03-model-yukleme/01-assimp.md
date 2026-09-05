# Assimp ve Model Yüklemeye Giriş 🗿

Şimdiye kadar sahnemizdeki tüm nesneleri (küpler ve zeminler) C++ kodumuzun içine elle yazdığımız tepe noktası (*vertex*) dizileriyle oluşturduk. Ancak bir video oyununda veya simülasyonda bir uzay gemisini, bir ejderhayı ya da detaylı bir insan karakterini binlerce satır dizi tanımlayarak elle yazmak **imkansızdır**!

3B modelleme sanatçıları Blender, Maya, 3ds Max veya ZBrush gibi gelişmiş yazılımlar kullanarak milyonlarca poligon ve kemik hiyerarşisine sahip harikulade modeller üretirler. Bu modeller daha sonra `.OBJ`, `.FBX`, `.GLTF` veya `.DAE` gibi dosya formatlarına aktarılır (*export*).

Bizim bir grafik programcısı olarak görevimiz, bu dosya formatlarını okumak, içindeki köşe noktalarını, normalleri, doku koordinatlarını ve malzeme bilgilerini ayıklayıp OpenGL'in anlayacağı **VBO ve VAO** tamponlarına dönüştürmektir.

Bu bölümde piyasadaki onlarca farklı 3B formatını tek bir çatı altında çözen sektör standardı **Assimp (Open Asset Import Library)** kütüphanesini tanıyacağız!

---

## Assimp Mimarisi Nasıl Çalışır? 🏗️

Her 3B model formatı veriyi farklı bir yapıda saklar: Bazıları XML tabanlıdır, bazıları saf metin (OBJ), bazıları ise sıkıştırılmış ikili veridir (FBX). 

**Assimp**, bütün bu farklı dosya formatlarını okur ve kendi içinde standart, hiyerarşik bir veri yapısına dönüştürür:

![Assimp Veri Yapısı](../img/model_loading/assimp_structure.png)

Assimp bir dosyayı yüklediğinde şu hiyerarşiyi oluşturur:
1. **aiScene (Sahne Nesnesi):** Yüklenen modelin en tepe kök nesnesidir. Sahnedeki tüm modelleri, malzemeleri, ışıkları ve animasyonları içerir.
2. **Kök Düğüm (Root Node):** Sahnedeki nesnelerin uzaysal hiyerarşisini ve birbirlerine göre dönüşüm matrislerini saklayan düğümler ağacıdır.
3. **Meshes (Ağlar / Yüzeyler):** Gerçek geometri verilerini (köşe noktaları, normaller, doku koordinatları ve yüzey üçgenleri) saklayan dizidir (`aiMesh`). Bir model tek bir parça olmak zorunda değildir; örneğin bir araba modeli; kaporta, 4 tekerlek ve camlar olmak üzere birden fazla alt-mesh'ten oluşabilir.
4. **Materials (Malzemeler):** Her bir mesh'in hangi diffuse ve specular dokularını kullandığını saklayan malzeme listesidir.

---

## Assimp'i Projeye Dahil Etme ⚙️

Assimp'i projenizde kullanmanın en kolay yolu hazır derlenmiş kütüphane ikililerini indirmek ya da CMake ile derlemektir:

1. [Assimp Resmi Sitesinden](https://www.assimp.org/) veya GitHub deposundan güncel sürümü indirin.
2. `include` klasörünü projenizin include dizinine ekleyin.
3. `assimp.lib` dosyasını bağlayıcıya (*linker*) ekleyin ve `assimp.dll` dosyasını çalıştırılabilir dosyanızın (`.exe`) yanına yerleştirin.

Kodunuzda şu başlıkları eklemeniz yeterlidir:

```cpp
#include <assimp/Importer.hpp>
#include <assimp/scene.h>
#include <assimp/postprocess.h>
```

---

## Assimp'in Güçlü İşlem Bayrakları (Post-processing Flags) 🚩

Assimp sadece dosyayı okumakla kalmaz, yükleme anında geometriyi OpenGL için optimize eden harika bayraklar sunar:

* `aiProcess_Triangulate`: Model dörtgen (*quad*) ya da çokgen poligonlardan oluşuyorsa, bunları otomatik olarak üçgenlere (*triangles*) böler. OpenGL sadece üçgenlerle çalıştığı için bu bayrak **zorunludur**!
* `aiProcess_FlipUVs`: Blender ve OBJ formatında doku Y koordinatları yukarıdan başlarken OpenGL'de aşağıdan başlar. Bu bayrak yükleme anında doku koordinatlarının Y eksenini otomatik olarak ters çevirir ($1.0 - y$).
* `aiProcess_GenNormals`: Eğer yüklenen 3D modelde normal vektörleri tanımlanmamışsa, Assimp yüzey geometrisinden otomatik olarak normalleri hesaplar.
* `aiProcess_OptimizeMeshes`: Çizim çağrılarını (*draw calls*) azaltmak için küçük alt mesh'leri tek bir mesh altında birleştirir.

Bir modeli yüklemek tek satırdır:

```cpp
Assimp::Importer importer;
const aiScene *scene = importer.ReadFile("model.obj", 
    aiProcess_Triangulate | aiProcess_FlipUVs | aiProcess_GenSmoothNormals);

if(!scene || scene->mFlags & AI_SCENE_FLAGS_INCOMPLETE || !scene->mRootNode) 
{
    std::cout << "HATA::ASSIMP::" << importer.GetErrorString() << std::endl;
    return;
}
```

---

Sırada bu verileri OpenGL tarafında temiz ve profesyonel bir şekilde saklayacağımız **Bölüm 2: "Mesh Sınıfı"** var!

👉 **[Sonraki Bölüm: Mesh Sınıfı (Mesh Class)](02-mesh.md)**
