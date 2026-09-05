---
title: Gama Düzeltmesi (Gamma Correction)
description: Monitörlerin doğrusal olmayan Gama 2.2 tepkisini düzeltmek, sRGB renk uzayı ve fiziksel doğrusal aydınlatma iş akışı.
---

# Gama Düzeltmesi (Gamma Correction)

Bilgisayar grafiklerinde aydınlatma hesapları yaparken örtük bir varsayımda bulunuruz: Matematiksel olarak iki ışığın toplamı $0.5 + 0.5 = 1.0$ ise, ekrandaki parlaklığın da iki katına çıkacağını düşünürüz. 

Ancak fiziksel dünyada ve monitörlerimizde durum böyle değildir!

---

## Monitörlerin Gama Eğrisi (CRTs & LCDs)

Eski katot ışın tüplü (CRT) monitörlerin fiziksel yapısı gereği, ekrana verilen voltaj ile üretilen ışık miktarı doğrusal değildi. Günümüz modern LCD ve OLED monitörleri de geriye dönük uyumluluk ve insan gözünün biyolojik yapısı nedeniyle aynı eğriyi taklit eder:

$$\text{Görünen Parlaklık} = \text{Giriş Sinyali}^{2.2}$$

![Gama Eğrisi Karşılaştırması](../img/advanced-lighting/gamma_correction_gamma_curves.png)

İnsan gözü karanlık tonlardaki parlaklık değişimlerine çok daha duyarlıdır. Monitörlerin bu **$2.2$ üstel eğrisi (Gamma 2.2)**, görüntülenen rengi olması gerekenden çok daha karanlık hale getirir.

Eğer biz shader'larımızda fiziksel aydınlatma hesaplarını doğrusal (linear) uzayda yapıp doğrudan ekrana basarsak:
1. Renkler gereğinden çok daha karanlık görünür,
2. Işık mesafesi zayıflaması (attenuation) aşırı dik ve yapay hale gelir,
3. Speküler parlamalar daralıp plastiğimsi bir his verir.

---

## Gama Düzeltmesi Nasıl Uygulanır?

Çözüm son derece basittir: Monitör değerlerimizi $2.2$ üssü ile çarparak karartıyorsa, biz de pikselleri ekrana göndermeden önce değerlerin **ters üssünü ($1 / 2.2$)** alırız!

$$\text{Düzeltilmiş Renk} = \text{Doğrusal Renk}^{1.0 / 2.2}$$

Böylece monitör bu rengi aldığında:

$$(\text{Doğrusal Renk}^{1.0 / 2.2})^{2.2} = \text{Doğrusal Renk}^{1.0}$$

Ekrandaki çıktı tam olarak hesapladığımız fiziksel parlaklığa eşit olur!

---

## İki Farklı Uygulama Yöntemi

### 1. Fragment Shader İçinde Manuel Düzeltme
Shader'ın en sonunda renk çıktısını gama düzeltmesine tabi tutabilirsiniz:

```glsl linenums="1" title="fragment_shader.frag"
void main()
{
    // ... aydınlatma hesapları (doğrusal uzayda) ...
    vec3 result = ambient + diffuse + specular;

    // Gama 2.2 Düzeltmesi
    float gamma = 2.2;
    FragColor.rgb = pow(result, vec3(1.0 / gamma));
    FragColor.a = 1.0;
}
```

### 2. OpenGL Donanımsal sRGB Framebuffer (`GL_FRAMEBUFFER_SRGB`)
OpenGL'in modern sürümleri bu işlemi ekran kartı donanımında tek satırda ücretsiz olarak yapabilir:

```cpp
glEnable(GL_FRAMEBUFFER_SRGB);
```

Bu bayrak açıldığında OpenGL, ekrana yazılan her piksele donanımsal olarak otomatik $1 / 2.2$ gama dönüşümü uygular!

---

## sRGB Dokuları (sRGB Textures)

Sanatçıların Photoshop veya internetten indirdiği PNG/JPEG dokuları zaten sRGB (gama uygulanmış) uzayındadır. Eğer siz bu dokuları olduğu gibi okuyup bir de üzerine aydınlatma eklerseniz, renkleri **iki kez gama işlemine** sokmuş olursunuz (renkler solar ve patlar)!

OpenGL'e dokunun sRGB formatında olduğunu bildirirsek, OpenGL dokuyu okurken donanımsal olarak doğrusal uzaya çevirir:

```cpp
glTexImage2D(GL_TEXTURE_2D, 0, GL_SRGB_ALPHA, width, height, 0, GL_RGBA, GL_UNSIGNED_BYTE, data);
```

> [!IMPORTANT]
> **Kritik Kural:** Difüz ve Albedo dokuları için `GL_SRGB` veya `GL_SRGB_ALPHA` kullanın; ancak Normal haritaları ve Speküler haritalar gibi matematiksel veri tutan dokular için daima `GL_RGBA` (doğrusal) kullanın!
