---
title: Aynasal IBL (Specular IBL)
description: Epic Games'in Ayrık-Toplam (Split-Sum) yaklaşımı, Ön-Filtrelenmiş Çevre Haritası ve 2B BRDF Entegrasyon Dokusu (LUT).
---

# Görüntü Tabanlı Aydınlatma (IBL) - Aynasal IBL (Specular IBL)

Yaygın ışınım (Diffuse IBL) ile mat yüzeylerin ortam aydınlatmasını çözdük. Peki ya altın, gümüş, krom veya cilalı mermer gibi **ayna yansımasına (Specular)** sahip yüzeyler çevrelerindeki dünyayı nasıl yansıtacak?

Kusursuz bir ayna için doğrudan çevre haritasını (`environmentMap`) örneklemek yeterlidir. Ancak pürüzlü (Roughness > 0) yüzeylerde yansıma bulanıklaşır!

---

## Epic Games ve Ayrık-Toplam (Split-Sum) Yaklaşımı

PBR speküler integralini gerçek zamanlı çözmek için Epic Games (Unreal Engine) tarafından geliştirilen **Ayrık-Toplam (Split-Sum Approximation)** tekniği kullanılır:

$$\int_{\Omega} f_r \cdot L_i \cdot (n \cdot \omega_i) d\omega_i \approx \underbrace{\left( \int_{\Omega} L_i d\omega_i \right)}_{\text{1. Ön-Filtrelenmiş Harita}} \cdot \underbrace{\left( \int_{\Omega} f_r \cdot (n \cdot \omega_i) d\omega_i \right)}_{\text{2. BRDF Entegrasyon LUT}}$$

İntegral iki bağımsız parçaya ayrılır ve ikisi de önceden hesaplanır:

1. **Ön-Filtrelenmiş Çevre Haritası (Prefiltered Environment Map):**
   Farklı pürüzlülük seviyeleri için çevre haritası GGX önem örneklemesi (Importance Sampling) ile bulanıklaştırılır ve küp haritasının **Mipmap katmanlarına** kaydedilir:
   - Mip 0: Mükemmel pürüzsüz ayna (Roughness = 0.0),
   - Mip 4: Tamamen bulanık yansıma (Roughness = 1.0).

2. **2B BRDF Entegrasyon LUT (Look-Up Table):**
   $n \cdot v$ açısı ile Roughness parametrelerine göre $F_0$ çarpanı ve ofseti $256 \times 256$ boyutunda 2 boyutlu kırmızı-yeşil bir dokuya önceden fırınlanır.

![Split-Sum Bileşenleri](../img/pbr/ibl_specular_result.png)

---

## Tam PBR + IBL Birleşimi

PBR Fragment shader'ında nihai ortam ışığı:

```glsl linenums="1" title="Tam IBL Ambient Hesabı"
// Diffuse IBL
vec3 F = fresnelSchlickRoughness(max(dot(N, V), 0.0), F0, roughness);
vec3 kS = F;
vec3 kD = 1.0 - kS;
kD *= 1.0 - metallic;	  

vec3 irradiance = texture(irradianceMap, N).rgb;
vec3 diffuse    = irradiance * albedo;

// Specular IBL
const float MAX_REFLECTION_LOD = 4.0;
vec3 R = reflect(-V, N);
vec3 prefilteredColor = textureLod(prefilterMap, R, roughness * MAX_REFLECTION_LOD).rgb;   
vec2 envBRDF  = texture(brdfLUT, vec2(max(dot(N, V), 0.0), roughness)).rg;
vec3 specular = prefilteredColor * (F * envBRDF.x + envBRDF.y);

// Nihai Ortam Işığı
vec3 ambient = (kD * diffuse + specular) * ao;
```

Sonuç: Metal bir küre üzerinde gökyüzünün kristal netliğindeki yansımasını, mat küre üzerinde ise çevre renklerinin yumuşacık saçılmasını görürsünüz. AAA kalitesinde modern fiziksel render motorunuz tamamlandı!
