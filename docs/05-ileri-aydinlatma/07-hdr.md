---
title: Yüksek Dinamik Aralık (HDR)
description: Kayan noktalı Framebuffer'lar (GL_RGBA16F), Reinhard ve Pozlama Ton Haritalama (Tone Mapping).
---

# Yüksek Dinamik Aralık (HDR)

Standart bir Framebuffer'da her bir RGB kanalı 8 bit ile saklanır ($[0, 255]$ veya $[0.0, 1.0]$). Buna **Düşük Dinamik Aralık (LDR - Low Dynamic Range)** denir.

Gerçek dünyada ise bir mum alevi ile bir stadyum projektörü veya Güneş aynı parlaklıkta değildir! Güneş'in parlaklığı $100.000$ iken iç mekan lambası $1.0$ olabilir. Eğer tüm değerleri $[0.0, 1.0]$ aralığına zorlarsanız, tüm parlak alanlar saf beyaza ($1.0$) doyar ve detaylar kaybolur.

**Yüksek Dinamik Aralık (HDR - High Dynamic Range)**, parlaklık değerlerini sınırsız kayan noktalı sayılar olarak saklayıp sahneyi çizme ve ardından ekrana uygun şekilde sıkıştırma sanatıdır.

---

## Kayan Noktalı Framebuffer (`GL_RGBA16F`)

Renk tamponumuzun $1.0$'dan büyük değerleri kırpmaması (clamp etmemesi) için kayan noktalı bir FBO oluştururuz:

```cpp linenums="1" title="HDR FBO Kurulumu"
unsigned int hdrFBO;
glGenFramebuffers(1, &hdrFBO);

unsigned int colorBuffer;
glGenTextures(1, &colorBuffer);
glBindTexture(GL_TEXTURE_2D, colorBuffer);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA16F, SCR_WIDTH, SCR_HEIGHT, 0, GL_RGBA, GL_FLOAT, NULL);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);

glBindFramebuffer(GL_FRAMEBUFFER, hdrFBO);
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, colorBuffer, 0);
```

Artık ışık şiddetlerimizi `vec3(100.0, 100.0, 100.0)` gibi devasa fiziksel değerlerle tanımlayabiliriz!

---

## Ton Haritalama (Tone Mapping)

HDR tamponundaki sınırsız değerleri monitörün gösterebileceği $[0.0, 1.0]$ LDR aralığına sıkıştırma işlemine **Ton Haritalama (Tone Mapping)** denir.

İki popüler yöntem:

### 1. Reinhard Ton Haritalaması
Erik Reinhard'ın klasik formülü; yüksek parlaklıkları yumuşak bir eğriyle $1.0$'a yaklaştırır:

$$\text{Renk}_{\text{LDR}} = \frac{\text{Renk}_{\text{HDR}}}{\text{Renk}_{\text{HDR}} + 1.0}$$

### 2. Pozlama (Exposure) Ton Haritalaması
Fotoğraf makinelerindeki diyafram ve enstantane gibi çalışır:

$$\text{Renk}_{\text{LDR}} = 1.0 - e^{-\text{Renk}_{\text{HDR}} \times \text{Exposure}}$$

```glsl linenums="1" title="hdr_tone_mapping.frag"
#version 330 core
out vec4 FragColor;
in vec2 TexCoords;

uniform sampler2D hdrBuffer;
uniform float exposure;

void main()
{             
    const float gamma = 2.2;
    vec3 hdrColor = texture(hdrBuffer, TexCoords).rgb;
  
    // Pozlama Ton Haritalama
    vec3 mapped = vec3(1.0) - exp(-hdrColor * exposure);

    // Gama düzeltmesi
    mapped = pow(mapped, vec3(1.0 / gamma));
  
    FragColor = vec4(mapped, 1.0);
}
```

Sonuç: Karanlık bir tünelden parlak güneşli bir güne çıktığınızda gözün veya kameranın ışığa alışması gibi dinamik pozlama efektleri uygulayabilirsiniz!
