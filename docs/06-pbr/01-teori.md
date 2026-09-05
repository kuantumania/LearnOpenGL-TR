---
title: PBR Teori ve İlkeleri
description: Fiziksel Tabanlı İşleme (PBR) nedir? Mikroyüzey modeli, Enerji Korunumu ve Cook-Torrance BRDF matematiği.
---

# Fiziksel Tabanlı İşleme (PBR) - Teori

**Fiziksel Tabanlı İşleme (Physically Based Rendering - PBR)**, grafik programlamada ışığın gerçek dünyadaki fiziksel optik yasalarını taklit eden bir dizi render prensibi ve matematiksel formül bütünüdür.

Geleneksel Phong veya Blinn-Phong modellerinde sanatçılar her nesne için "göze hoş gelen" speküler ve difüz renkler uydurmak zorundaydı; sahnedeki ışık değiştiğinde nesneler plastik veya yapay görünmeye başlardı. 

PBR sayesinde nesneler:
- Gerçek dünya malzemelerinin fiziksel ölçüm parametreleriyle (**Roughness**, **Metallic**, **Albedo**) tanımlanır,
- Herhangi bir ışıklandırma koşulunda (güneş altında, karanlık bir odada, neon ışıklarında) **daima fotogerçekçi** görünür!

---

## PBR'ın 3 Temel İlkesi

Bir aydınlatma modelinin PBR sayılabilmesi için 3 temel fizik kuralını sağlaması gerekir:

1. **Mikroyüzey Modeli (Microfacet Theory):** 
   En pürüzsüz görünen yüzey bile mikroskobik düzeyde incelendiğinde binlerce küçük aynadan (mikroyüzeyden) oluşur. Bu mikroyüzeylerin yönelim dağılımı yüzeyin **Pürüzlülüğünü (Roughness)** belirler.
2. **Enerji Korunumu (Energy Conservation):** 
   Yüzeyden yansıyan ve saçılan toplam ışık enerjisi, yüzeye çarpan gelen ışık enerjisinden **asla daha fazla olamaz**!
3. **Fiziksel Tabanlı BRDF:** 
   Işığın nasıl yansıyacağını hesaplamak için standart fiziksel **Cook-Torrance BRDF** denklemi kullanılır.

---

## Yansıma Denkliği ve Cook-Torrance BRDF

Fiziksel renderın kalbi olan yansıma denklemi (Reflectance Equation):

$$L_o(p, \omega_o) = \int_{\Omega} f_r(p, \omega_i, \omega_o) L_i(p, \omega_i) (n \cdot \omega_i) d\omega_i$$

Burada $f_r$ terimi **BRDF (Bidirectional Reflectance Distribution Function)** fonksiyonudur. Cook-Torrance modelinde BRDF iki bileşene ayrılır:

$$f_r = k_d f_{\text{lambert}} + k_s f_{\text{cook-torrance}}$$

- **$k_d$:** Yüzeyin içine girip emilen ve saçılan ışık oranı (**Yayılımcı / Diffuse**).
- **$k_s$:** Yüzeyden ayna gibi yansıyan ışık oranı (**Parlama / Specular**).
- Enerji korunumu gereği: $k_d + k_s = 1.0$ olmalıdır!

Yayılımcı kısım basitçe Lambertian dağılımıdır: $f_{\text{lambert}} = \frac{\text{albedo}}{\pi}$.

Speküler kısım ise Cook-Torrance'ın meşhur üçlü formülüdür:

$$f_{\text{cook-torrance}} = \frac{D \cdot F \cdot G}{4 (\omega_o \cdot n)(\omega_i \cdot n)}$$

---

## D, F ve G Fonksiyonları Nedir?

![PBR D, F, G Fonksiyonları](../img/pbr/microfacets.png)

### 1. Normal Dağılım Fonksiyonu ($D$ - Trowbridge-Reitz GGX)
Mikroyüzeylerin ne kadarının yarıvektör $\mathbf{H}$ ile aynı hizada olduğunu hesaplar. Parlamanın keskinliğini veya yayvanlığını belirler:

$$D_{\text{GGX}}(n, h, \alpha) = \frac{\alpha^2}{\pi ((n \cdot h)^2 (\alpha^2 - 1) + 1)^2}$$

### 2. Geometri Fonksiyonu ($G$ - Smith GGX)
Mikroyüzeylerin birbirini gölgelemesini (Shadowing) ve önünü kapatmasını (Masking) simüle eder. Pürüzlü yüzeylerde ışığın mikro oyuklar arasında kaybolmasını açıklar:

$$G_{\text{SchlickGGX}}(n, v, k) = \frac{n \cdot v}{(n \cdot v)(1 - k) + k}$$

$$G(n, v, l, k) = G_{\text{SchlickGGX}}(n, v, k) \cdot G_{\text{SchlickGGX}}(n, l, k)$$

### 3. Fresnel Fonksiyonu ($F$ - Fresnel-Schlick)
Bakış açısına göre yüzeyden yansıyan ışığın oranını verir (dik açıda $F_0$, teğet açıda %100 yansıma):

$$F_{\text{Schlick}}(h, v, F_0) = F_0 + (1.0 - F_0)(1.0 - (h \cdot v))^5$$

---

## Metalik İş Akışı (Metallic Workflow)

PBR'da malzemeler iki ana sınıfa ayrılır:
1. **Yalıtkanlar (Dielectrics - Taş, tahta, plastik, kumaş):** Düşük baz yansımaya ($F_0 \approx 0.04$) ve zengin difüz renge sahiptirler.
2. **İletkenler / Metaller (Conductors - Demir, altın, bakır):** Sıfır difüz renge sahiptirler ($k_d = 0$). Işığı kırmazlar, çarpan tüm ışığı kendi karakteristik metalik renkleriyle yansıtırlar ($F_0 = \text{Albedo}$).

Bu sayede sadece 3 parametre ile (**Albedo**, **Metallic**, **Roughness**) dünyadaki hemen hemen tüm maddeler kusursuzca modellenebilir!
