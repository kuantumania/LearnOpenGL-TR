# Gama Düzeltmesi (Gamma Correction) 📺

Bilgisayar grafiklerinde aydınlatma hesaplaması yaparken tüm formüller (Phong, Blinn-Phong, mesafe zayıflaması) renklerin **doğrusal (Linear Space)** olduğunu varsayar: yani ışığın şiddeti iki katına çıktığında piksel parlaklığının da iki katına çıkması gerekir.

Ancak bilgisayar monitörleri (tarihsel CRT tüplerinden miras kalan bir özellikle) pikselleri doğrusal göstermez; parlaklığı yaklaşık **2.2'lik bir üs kuvvetine (Gama $\gamma = 2.2$)** yükselterek karartır:

$$Çıkış = Girdi^{2.2}$$

![Gama Eğrileri](../img/advanced-lighting/gamma_correction_gamma_curves.png)

Monitör her şeyi kararttığı için, grafik sanatçıları dokuları çizerken onları önceden aydınlatılmış (**sRGB uzayında**) kaydederler. Eğer shader'larınızda bu durumu hesaba katmazsanız:
1. Sahneniz aşırı karanlık görünür,
2. Işığın mesafeyle zayıflaması çok dik ve yapay olur,
3. Aynasal parlamalar çirkin halkalar oluşturur!

![Gama Düzeltmesi Farkı](../img/advanced-lighting/gamma_correction_example.png)

---

## Gama Düzeltmesi Nasıl Uygulanır? 🛠️

Monitörün renkleri $\gamma = 2.2$ ile karartmasını telafi etmek için, nihai rengi monitöre göndermeden hemen önce tersi olan **$1.0 / 2.2$ kuvvetine** yükseltiriz:

```glsl
void main()
{
    // ... aydınlatma hesaplamaları (Linear Space) ...
    
    // Gama Düzeltmesi:
    float gamma = 2.2;
    FragColor.rgb = pow(FragColor.rgb, vec3(1.0 / gamma));
}
```

### sRGB Dokularını Düzeltme
Eğer dokularınız Photoshop veya Blender'dan sRGB olarak geldiyse, onları VRAM'e yüklerken `GL_SRGB` veya `GL_SRGB_ALPHA` olarak yüklemelisiniz:

```cpp
glTexImage2D(GL_TEXTURE_2D, 0, GL_SRGB_ALPHA, width, height, 0, GL_RGBA, GL_UNSIGNED_BYTE, data);
```

Böylece OpenGL dokuyu okurken otomatik olarak doğrusal uzaya çevirir, shader tüm matematiği kusursuz hesaplar ve en sonda monitör için gama düzeltmesi uygulanır!

---

Sırada grafik dünyasının en dramatik unsuru olan **Bölüm 3: "Gölge Haritalama (Shadow Mapping)"** var!

👉 **[Sonraki Bölüm: Gölge Haritalama](03-golge-haritalama.md)**
