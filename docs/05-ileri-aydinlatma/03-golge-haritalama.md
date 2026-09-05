---
title: Gölge Haritalama (Shadow Mapping)
description: Yönsel ışıklar için iki aşamalı gölge haritası (Shadow Map), derinlik FBO'su, gölge aknesi ve PCF yumuşak gölgeler.
---

# Gölge Haritalama (Shadow Mapping)

Gölgeler, 3 boyutlu bir sahneye gerçekçilik ve derinlik kazandıran en önemli görsel unsurdur. Gölgeler olmadan nesnelerin havada mı uçtuğu yoksa zemine mi bastığı anlaşılamaz.

Gerçek zamanlı grafiklerde gölge üretmenin en popüler ve standart algoritması **Gölge Haritalama (Shadow Mapping)** tekniğidir.

---

## Gölge Haritalama Algoritmasının Mantığı

Algoritma çok temel bir sezgiye dayanır: **Eğer bir nokta ışık kaynağının görüş alanındaysa aydınlıktır; eğer ışık ile o nokta arasında başka bir nesne varsa gölgededir!**

Bunu tespit etmek için render işlemini **iki geçişte (two-pass)** yaparız:

![Gölge Haritalama Mantığı](../img/advanced-lighting/shadow_mapping_theory.png)

1. **Geçiş 1 (Derinlik Haritası / Depth Map):**
   - Kamerayı **ışık kaynağının yerine** koyarız.
   - Sahneyi yalnızca derinlik tamponuna (Z-buffer) çizeriz. Renk çıktısına ihtiyaç yoktur.
   - Oluşan dokuya **Gölge Haritası (Shadow Map)** denir. Bu harita, ışıktan görünen en yakın yüzeylerin mesafesini saklar.

2. **Geçiş 2 (Sahne Renderı):**
   - Sahneyi normal oyuncu kamerasından çizeriz.
   - Her bir piksel için, o pikselin dünya koordinatını ışık uzayına projekte ederiz.
   - Pikselin ışığa olan mesafesi ($z_{\text{current}}$), gölge haritasındaki en yakın mesafeden ($z_{\text{closest}}$) büyükse, araya başka bir nesne girmiş demektir: **Piksel gölgededir!**

---

## 1. Geçiş: Derinlik FBO'su Kurulumu

Yalnızca derinlik bilgisi tutan bir Framebuffer (FBO) oluşturalım:

```cpp linenums="1" title="Derinlik FBO Kurulumu"
const unsigned int SHADOW_WIDTH = 1024, SHADOW_HEIGHT = 1024;
unsigned int depthMapFBO;
glGenFramebuffers(1, &depthMapFBO);

unsigned int depthMap;
glGenTextures(1, &depthMap);
glBindTexture(GL_TEXTURE_2D, depthMap);
glTexImage2D(GL_TEXTURE_2D, 0, GL_DEPTH_COMPONENT, SHADOW_WIDTH, SHADOW_HEIGHT, 0, GL_DEPTH_COMPONENT, GL_FLOAT, NULL);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_BORDER);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_BORDER);
float borderColor[] = { 1.0f, 1.0f, 1.0f, 1.0f };
glTexParameterfv(GL_TEXTURE_2D, GL_TEXTURE_BORDER_COLOR, borderColor);

glBindFramebuffer(GL_FRAMEBUFFER, depthMapFBO);
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_DEPTH_ATTACHMENT, GL_TEXTURE_2D, depthMap, 0);
glDrawBuffer(GL_NONE);
glReadBuffer(GL_NONE);
glBindFramebuffer(GL_FRAMEBUFFER, 0);
```

---

## 2. Geçiş: Fragment Shader'da Gölge Testi ve Gölge Aknesi Çözümü

Işık uzayındaki koordinatları $[-1, 1]$ aralığından $[0, 1]$ doku aralığına çevirip derinlik testi yaparız:

```glsl linenums="1" title="shadow_mapping.frag"
float ShadowCalculation(vec4 fragPosLightSpace, vec3 normal, vec3 lightDir)
{
    // Perspektif bölme
    vec3 projCoords = fragPosLightSpace.xyz / fragPosLightSpace.w;
    // [0, 1] aralığına dönüştür
    projCoords = projCoords * 0.5 + 0.5;

    if(projCoords.z > 1.0)
        return 0.0;

    // Gölge Aknesi Çözümü (Eğim tabanlı Bias)
    float bias = max(0.05 * (1.0 - dot(normal, lightDir)), 0.005);

    // PCF (Percentage-Closer Filtering) ile Yumuşak Gölgeler
    float shadow = 0.0;
    vec2 texelSize = 1.0 / textureSize(shadowMap, 0);
    for(int x = -1; x <= 1; ++x)
    {
        for(int y = -1; y <= 1; ++y)
        {
            float pcfDepth = texture(shadowMap, projCoords.xy + vec2(x, y) * texelSize).r; 
            shadow += projCoords.z - bias > pcfDepth ? 1.0 : 0.0;        
        }    
    }
    shadow /= 9.0;
    
    return shadow;
}
```

> [!TIP]
> **PCF (Percentage Closer Filtering):**
> Gölge haritasından tek bir piksel okumak kenarlarda tırtıklı, pikselli gölgelere yol açar. Komşu 9 pikseli ($3 \times 3$) örnekleyip ortalamasını aldığımızda harika, yumuşak gölge sınırları elde ederiz!
