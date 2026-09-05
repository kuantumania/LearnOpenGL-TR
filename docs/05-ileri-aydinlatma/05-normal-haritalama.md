---
title: Normal Haritalama (Normal Mapping)
description: Düz bir yüzeye milyonlarca poligon harcamadan detay kazandırmak, Tanjant Uzayı ve TBN matrisi matematiği.
---

# Normal Haritalama (Normal Mapping)

3 boyutlu bir tuğla duvara veya taş zemine baktığınızda aralardaki çatlakları, çıkıntıları ve taşların eğimini net bir şekilde görürsünüz. Bu detayları gerçek 3B poligonlarla modellemek milyonlarca üçgen gerektirir ve GPU'yu felç eder.

**Normal Haritalama (Normal Mapping / Bump Mapping)**, tek bir düz dikdörtgen (2 üçgen) üzerine ışığın yönüne göre sahte yüzey normalleri uygulayarak gözü aldatma sanatıdır!

---

## Normal Haritası Nedir?

Normal haritası, RGB renk kanallarını 3B yüzey normali bileşenleri olarak saklayan özel bir dokudur:
- **Kırmızı (R):** $X$ ekseni yönelimi ($[-1, 1]$),
- **Yeşil (G):** $Y$ ekseni yönelimi ($[-1, 1]$),
- **Mavi (B):** $Z$ ekseni yönelimi (yüzeyden dışarı doğru dik olan $+Z$ ekseni).

Normaller çoğunlukla yüzeyden dışarıya ($+Z$) doğru baktığı için normal haritaları daima **baskın bir mavi/mor renge** sahiptir!

$$\mathbf{N}_{\text{dünya}} = \text{texture}(\text{normalMap}, \text{TexCoords}).rgb \times 2.0 - 1.0$$

![Tanjant Uzayı ve Normal Haritası](../img/advanced-lighting/normal_mapping_tbn_vectors.png)

---

## Tanjant Uzayı ve TBN Matrisi

Eğer bir normal haritasını doğrudan dünya uzayında uygularsanız, küpü veya modeli döndürdüğünüzde normaller yanlış yöne bakar!

Normaller daima **üçgenin kendi yerel yüzeyine göre (Tanjant Uzayı / Tangent Space)** tanımlanır. Tanjant uzayını oluşturan üç eksen:
1. **$\mathbf{T}$ (Tangent):** Doku koordinatlarındaki $U$ doğrultusuna paralel eksen.
2. **$\mathbf{B}$ (Bitangent):** Doku koordinatlarındaki $V$ doğrultusuna paralel eksen.
3. **$\mathbf{N}$ (Normal):** Yüzeye dik olan orijinal normal vektörü.

Bu üç vektörden **TBN Matrisi** kurulur:

$$\mathbf{TBN} = \begin{bmatrix} T_x & B_x & N_x \\ T_y & B_y & N_y \\ T_z & B_z & N_z \end{bmatrix}$$

---

## Tanjant ve Bitanjant Hesabı (C++)

Üçgenin tepe noktalarından ($P_1, P_2, P_3$) ve doku koordinatlarından ($UV_1, UV_2, UV_3$) Tanjant vektörleri şöyle hesaplanır:

```cpp linenums="1" title="Tanjant Hesabı"
vec3 edge1 = v2 - v1;
vec3 edge2 = v3 - v1;
vec2 deltaUV1 = uv2 - uv1;
vec2 deltaUV2 = uv3 - uv1;

float f = 1.0f / (deltaUV1.x * deltaUV2.y - deltaUV2.x * deltaUV1.y);

vec3 tangent;
tangent.x = f * (deltaUV2.y * edge1.x - deltaUV1.y * edge2.x);
tangent.y = f * (deltaUV2.y * edge1.y - deltaUV1.y * edge2.y);
tangent.z = f * (deltaUV2.y * edge1.z - deltaUV1.y * edge2.z);
tangent = normalize(tangent);
```

---

## GLSL Fragment Shader Kullanımı

```glsl linenums="1" title="normal_mapping.frag"
in vec2 TexCoords;
in mat3 TBN; // Vertex shader'dan gelen TBN matrisi

uniform sampler2D normalMap;

void main()
{
    // Normal haritasından oku ve [-1, 1] aralığına aç
    vec3 normal = texture(normalMap, TexCoords).rgb;
    normal = normalize(normal * 2.0 - 1.0);   

    // Tanjant uzayından dünya uzayına dönüştür!
    normal = normalize(TBN * normal);

    // ... normal ile aydınlatma hesapları ...
}
```

Sonuç: Düz bir yüzey, ışık hareket ettikçe sanki gerçek oyuklara ve taş çıkıntılarına sahipmiş gibi büyüleyici bir şekilde parlar!
