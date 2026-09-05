# Işık Haritaları (Lighting Maps) 🗺️

Önceki bölümde, sahnemizdeki nesnelere `Material` yapısı üzerinden ortam, yaygın ve aynasal renkler atamayı öğrendik. Ancak fark ettiğiniz gibi, bu yöntem tüm nesneyi **tek bir tekdüze malzeme** haline getiriyordu.

Gerçek bir dünyada nesneler nadiren tek bir saf malzemeden oluşur. Örneğin bir nakliye sandığını düşünün:
* Gövdesi **mat ahşap** tahtalardan yapılmıştır (ışığı yayar, hiç parlamaz).
* Köşelerinde ise sandığı bir arada tutan **çelik metal şeritler ve paslanmaz vidalar** bulunur (ayna gibi parlar).

Bütün bu ahşap ve metal ayrımını tek bir 3D model üzerinde nasıl sağlarız? Cevap: **Işık Haritaları (Lighting Maps)**!

Işık haritaları, nesnenin her bir pikselinin (*texel*) farklı diffuse ve specular tepkiler vermesini sağlayan özel 2B dokulardır.

---

## 1. Yaygın Doku Haritası (Diffuse Map) 📦

Yaygın harita, aslında Bölüm 6'da öğrendiğimiz standart dokunun ta kendisidir! Nesnenin üzerindeki ahşap desenlerini ve temel renkleri içerir:

![Konteyner Yaygın Dokusu](../img/textures/container2.png)

Shader'ımızda `vec3 diffuse` yerine bir `sampler2D` kullanırız:

```glsl
struct Material {
    sampler2D diffuse;  // Renk dokusu
    sampler2D specular; // Parlama dokusu
    float shininess;
};

uniform Material material;
```

Fragment Shader içerisinde ortam ve yaygın bileşenleri doğrudan bu dokudan örnekleriz:

```glsl
// Ortam Işığı
vec3 ambient = light.ambient * vec3(texture(material.diffuse, TexCoords));

// Yaygın Işık
vec3 norm = normalize(Normal);
vec3 lightDir = normalize(light.position - FragPos);
float diff = max(dot(norm, lightDir), 0.0);
vec3 diffuse = light.diffuse * diff * vec3(texture(material.diffuse, TexCoords));
```

Sonuç olarak sandığımız gerçekçi bir ahşap gövdeye kavuşur:

![Diffuse Map Uygulanmış Küp](../img/lighting/materials_diffuse_map.png)

---

## 2. Aynasal Parlama Haritası (Specular Map) 🪙

Şu anda sandığımızın ahşap gövdesi de ışık geldiğinde parlar; oysa ahşap parlamamalı, sadece metal köşeler ve vidalar parlamalıdır!

Bunu çözmek için **Specular Map** kullanırız. Specular map, yüzeyin nerelerinin ne kadar parlayacağını piksel piksel belirleyen siyah-beyaz bir maskedir:

![Specular Map Dokusu](../img/textures/container2_specular.png)

* **Siyah Bölgeler ($0.0$):** Ahşap yüzeylerdir. Aynasal yansıma $0$ ile çarpılacağı için **hiç parlamaz**!
* **Beyaz ve Gri Bölgeler ($1.0$):** Çelik şeritler ve vidalardır. Işık vurduğunda **ayna gibi parlar**!

Fragment Shader'da aynasal parlama hesaplaması:

```glsl
vec3 viewDir = normalize(viewPos - FragPos);
vec3 reflectDir = reflect(-lightDir, norm);  
float spec = pow(max(dot(viewDir, reflectDir), 0.0), material.shininess);

// Specular haritasından örnekleme yapıyoruz:
vec3 specular = light.specular * spec * vec3(texture(material.specular, TexCoords));
```

Sonuç grafik dünyasının en tatmin edici manzaralarından biridir: Ahşap tamamen mat kalırken, ışık hareket ettikçe sadece metal vidalar ve çerçeveler göz alıcı şekilde parıldar!

![Specular Map ile Kusursuz Parlama](../img/lighting/materials_specular_map.png)

---

## C++ Kodunda Dokuları Bağlama 🔌

Her iki dokuyu da `stb_image` ile yükleyip farklı doku birimlerine (`GL_TEXTURE0` ve `GL_TEXTURE1`) bağlarız:

```cpp
// Shader uniform'larına doku yuva numaralarını bildir
lightingShader.use();
lightingShader.setInt("material.diffuse", 0);
lightingShader.setInt("material.specular", 1);

// Render döngüsü içinde:
glActiveTexture(GL_TEXTURE0);
glBindTexture(GL_TEXTURE_2D, diffuseMap);

glActiveTexture(GL_TEXTURE1);
glBindTexture(GL_TEXTURE_2D, specularMap);

glBindVertexArray(cubeVAO);
glDrawArrays(GL_TRIANGLES, 0, 36);
```

---

## Alıştırmalar 🏋️

1. Specular haritasının renklerini ters çevirin (inverting: `vec3(1.0) - vec3(texture(...))`) ve ahşabın parlayıp vidaların mat kalışının yarattığı tuhaf etkiyi görün!
2. **Işıma Haritası (Emission Map):** Sandığın çelik şeritlerinin içine neon ışıklar yerleştirmek için 3. bir doku yuvası olan `material.emission` ekleyin ve ortam ışığından bağımsız olarak kendi kendine parlayan floresan bir sandık yapın!

---

Sandığımız artık malzeme ve ışık haritalarıyla adeta bir AAA oyun varlığı gibi görünüyor. Ancak şu ana kadar sahnemizde tek bir sabit noktasal ışık kullandık.

Gerçek dünyada ışıklar sadece küçük ampullerden ibaret değildir; gökyüzündeki devasa **Güneş (Yönlü Işık)** ya da bir madencinin kaskındaki **El Feneri (Spot Işık)** bambaşka kurallarla çalışır!

Sırada bu 3 farklı ışık türünü kodlayacağımız **Bölüm 5: "Işık Kaynakları (Light Casters)"** var!

👉 **[Sonraki Bölüm: Işık Kaynakları (Light Casters)](05-isik-kaynaklari.md)**
