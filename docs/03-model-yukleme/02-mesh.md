# Mesh Sınıfı (Mesh Class) 🕸️

Bir 3B model genellikle tek bir yekpare parçadan oluşmaz. Örneğin bir robot modeli; baş, gövde, kollar, bacaklar ve silahlar olmak üzere bağımsız parçalardan meydana gelir. 

İşte kendi tepe noktası verilerine, indislerine ve malzemelerine sahip olan bu bağımsız parçaların her birine grafik programlamada **Mesh (Ağ / Yüzey Parçası)** denir.

Bu bölümde, her bir parçayı bağımsız olarak saklayabilen, kendi VAO/VBO tamponlarını yöneten ve tek bir `Draw()` çağrısıyla ekrana çizebilen modüler bir C++ `Mesh` sınıfı tasarlayacağız!

---

## Veri Yapılarının Tasarımı 📐

Öncelikle bir tepe noktasını (*Vertex*) ve bir dokuyu (*Texture*) temsil eden C++ yapılarını kuralım:

```cpp
#include <glm/glm.hpp>
#include <string>
#include <vector>

struct Vertex {
    glm::vec3 Position;  // Köşe konumu (location = 0)
    glm::vec3 Normal;    // Normal vektörü (location = 1)
    glm::vec2 TexCoords; // UV Doku koordinatı (location = 2)
};

struct Texture {
    unsigned int id;
    std::string type;    // "texture_diffuse" ya da "texture_specular"
    std::string path;    // Yinelenen yüklemeleri engellemek için dosya yolu
};
```

---

## Mesh Sınıfının İskeleti 🏛️

```cpp
class Mesh {
public:
    // Mesh Verileri
    std::vector<Vertex>       vertices;
    std::vector<unsigned int> indices;
    std::vector<Texture>      textures;
    unsigned int VAO;

    // Yapıcı Fonksiyon
    Mesh(std::vector<Vertex> vertices, std::vector<unsigned int> indices, std::vector<Texture> textures)
    {
        this->vertices = vertices;
        this->indices = indices;
        this->textures = textures;

        // OpenGL tamponlarını yapılandır
        setupMesh();
    }

    // Çizim Çağrısı
    void Draw(Shader &shader);

private:
    // Donanım Tamponları
    unsigned int VBO, EBO;

    void setupMesh();
};
```

---

## Tamponların Yapılandırılması: `setupMesh()` 🛠️

`setupMesh` fonksiyonu, C++ `std::vector` bellek bloklarını doğrudan GPU VRAM'ine aktarır. C++ vektörlerinin ardışık bellek garantisi sayesinde `&vertices[0]` ifadesi doğrudan ham bellek işaretçisidir:

```cpp
void Mesh::setupMesh()
{
    glGenVertexArrays(1, &VAO);
    glGenBuffers(1, &VBO);
    glGenBuffers(1, &EBO);
  
    glBindVertexArray(VAO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);

    // Tüm vertex verilerini VRAM'e aktar
    glBufferData(GL_ARRAY_BUFFER, vertices.size() * sizeof(Vertex), &vertices[0], GL_STATIC_DRAW);  

    // İndisleri EBO'ya aktar
    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
    glBufferData(GL_ELEMENT_ARRAY_BUFFER, indices.size() * sizeof(unsigned int), 
                 &indices[0], GL_STATIC_DRAW);

    // 1. Köşe Pozisyonları (location = 0)
    glEnableVertexAttribArray(0);	
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), (void*)0);
    
    // 2. Normaller (location = 1)
    glEnableVertexAttribArray(1);	
    glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), (void*)offsetof(Vertex, Normal));
    
    // 3. Doku Koordinatları (location = 2)
    glEnableVertexAttribArray(2);	
    glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, sizeof(Vertex), (void*)offsetof(Vertex, TexCoords));

    glBindVertexArray(0);
}
```

!!! tip "C++ offsetof Makrosu"
    `offsetof(Vertex, Normal)` ifadesi, `Normal` değişkeninin `Vertex` yapısının başlangıcından kaç bayt uzakta olduğunu derleme anında tam olarak hesaplar. Böylece elle bayt hesabı yapma derdinden kurtuluruz!

---

## Mesh Çizimi: `Draw()` 🎨

Bir mesh çizilmeden önce kendisine ait tüm diffuse ve specular dokularını ilgili doku birimlerine (`GL_TEXTURE0`, `GL_TEXTURE1`...) bağlamalı ve shader uniform'larını adlandırmalıdır:

```cpp
void Mesh::Draw(Shader &shader) 
{
    unsigned int diffuseNr = 1;
    unsigned int specularNr = 1;
    
    for(unsigned int i = 0; i < textures.size(); i++)
    {
        glActiveTexture(GL_TEXTURE0 + i); // Uygun doku birimini aktif et
        
        std::string number;
        std::string name = textures[i].type;
        if(name == "texture_diffuse")
            number = std::to_string(diffuseNr++);
        else if(name == "texture_specular")
            number = std::to_string(specularNr++);

        shader.setInt(("material." + name + number).c_str(), i);
        glBindTexture(GL_TEXTURE_2D, textures[i].id);
    }
    glActiveTexture(GL_TEXTURE0);

    // Mesh'i çiz
    glBindVertexArray(VAO);
    glDrawElements(GL_TRIANGLES, indices.size(), GL_UNSIGNED_INT, 0);
    glBindVertexArray(0);
}
```

Harika! Artık tek bir nesne parçasını temsil eden kusursuz bir `Mesh` sınıfımız var.

Sırada tüm bu mesh parçalarını Assimp yardımıyla bir dosyadan okuyup birleştiren **Bölüm 3: "Model Sınıfı"** var!

👉 **[Sonraki Bölüm: Model Sınıfı (Model Class)](03-model.md)**
