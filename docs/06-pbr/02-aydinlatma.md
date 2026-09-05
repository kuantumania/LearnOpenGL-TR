---
title: Doğrudan PBR Aydınlatma (Direct Lighting)
description: Çoklu noktasal ışık kaynakları ile tam GLSL Cook-Torrance PBR aydınlatma shader'ı uygulaması.
---

# Doğrudan PBR Aydınlatma (Direct Lighting)

Teori bölümünde öğrendiğimiz Cook-Torrance formüllerini şimdi çalışan, modern ve yüksek performanslı bir GLSL fragment shader'ına dönüştürelim.

Sahnemizde farklı pürüzlülük (Roughness) ve metaliklik (Metallic) değerlerine sahip bir dizi küre ve etrafta dolaşan 4 adet parlak noktasal ışık kaynağı olacak.

---

## GLSL PBR Fonksiyonları

Matematiksel fonksiyonlarımızı GLSL koduna dökelim:

```glsl linenums="1" title="pbr_functions.glsl"
const float PI = 3.14159265359;

// 1. Trowbridge-Reitz GGX Normal Dağılımı
float DistributionGGX(vec3 N, vec3 H, float roughness)
{
    float a = roughness * roughness;
    float a2 = a * a;
    float NdotH = max(dot(N, H), 0.0);
    float NdotH2 = NdotH * NdotH;

    float nom   = a2;
    float denom = (NdotH2 * (a2 - 1.0) + 1.0);
    denom = PI * denom * denom;

    return nom / max(denom, 0.0000001); // 0'a bölmeyi engelle
}

// 2. Smith Geometri Fonksiyonu
float GeometrySchlickGGX(float NdotV, float roughness)
{
    float r = (roughness + 1.0);
    float k = (r * r) / 8.0;

    float nom   = NdotV;
    float denom = NdotV * (1.0 - k) + k;

    return nom / denom;
}

float GeometrySmith(vec3 N, vec3 V, vec3 L, float roughness)
{
    float NdotV = max(dot(N, V), 0.0);
    float NdotL = max(dot(N, L), 0.0);
    float ggx2 = GeometrySchlickGGX(NdotV, roughness);
    float ggx1 = GeometrySchlickGGX(NdotL, roughness);

    return ggx1 * ggx2;
}

// 3. Fresnel-Schlick Yaklaşımı
vec3 fresnelSchlick(float cosTheta, vec3 F0)
{
    return F0 + (1.0 - F0) * pow(clamp(1.0 - cosTheta, 0.0, 1.0), 5.0);
}
```

---

## Tam PBR Fragment Shader'ı

Her bir ışık kaynağının katkısını hesaplayıp toplayan ana shader:

```glsl linenums="1" title="pbr.frag"
#version 330 core
out vec4 FragColor;
in vec2 TexCoords;
in vec3 WorldPos;
in vec3 Normal;

// Malzeme parametreleri
uniform vec3 albedo;
uniform float metallic;
uniform float roughness;
uniform float ao;

// Işık parametreleri
uniform vec3 lightPositions[4];
uniform vec3 lightColors[4];
uniform vec3 camPos;

void main()
{
    vec3 N = normalize(Normal);
    vec3 V = normalize(camPos - WorldPos);

    // Yalıtkanlar için baz F0 0.04; metaller için albedo rengi
    vec3 F0 = vec3(0.04); 
    F0 = mix(F0, albedo, metallic);

    // Işık Toplamı
    vec3 Lo = vec3(0.0);
    for(int i = 0; i < 4; ++i) 
    {
        // Gelen ışık radyansı (Radiance)
        vec3 L = normalize(lightPositions[i] - WorldPos);
        vec3 H = normalize(V + L);
        float distance = length(lightPositions[i] - WorldPos);
        float attenuation = 1.0 / (distance * distance);
        vec3 radiance = lightColors[i] * attenuation;

        // Cook-Torrance BRDF bileşenleri
        float NDF = DistributionGGX(N, H, roughness);   
        float G   = GeometrySmith(N, V, L, roughness);      
        vec3 F    = fresnelSchlick(clamp(dot(H, V), 0.0, 1.0), F0);
           
        vec3 numerator    = NDF * G * F; 
        float denominator = 4.0 * max(dot(N, V), 0.0) * max(dot(N, L), 0.0) + 0.0001;
        vec3 specular = numerator / denominator;
        
        // Enerji korunumu
        vec3 kS = F;
        vec3 kD = vec3(1.0) - kS;
        kD *= 1.0 - metallic; // Metaller difüz saçmaz!

        float NdotL = max(dot(N, L), 0.0);        
        Lo += (kD * albedo / PI + specular) * radiance * NdotL;
    }   
    
    // Ortam Aydınlatması
    vec3 ambient = vec3(0.03) * albedo * ao;
    vec3 color = ambient + Lo;

    // HDR Ton Haritalama (Reinhard)
    color = color / (color + vec3(1.0));
    // Gama Düzeltmesi (Gamma 2.2)
    color = pow(color, vec3(1.0/2.2)); 

    FragColor = vec4(color, 1.0);
}
```

Sonuç: Mat tebeşirden parıldayan altına kadar her malzeme, ışıklar altında fiziksel olarak kusursuz bir tepki verir!
