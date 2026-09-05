---
title: Blinn-Phong Aydınlatma Modeli
description: Klasik Phong aydınlatmasındaki 90 derece üzerindeki parlama kopmalarını çözen Blinn-Phong yarıvektör (halfway vector) tekniği.
---

# Blinn-Phong Aydınlatma Modeli

Temel aydınlatma bölümünde öğrendiğimiz klasik **Phong aydınlatma modeli**, bilgisayar grafiklerinin ilk dönemlerinde gerçekçi ışık simülasyonu için harika bir yaklaşımdı. Ancak belirli bakış açılarında, özellikle de yüzey pürüzlülüğü düşük ve bakış açısı yatık olduğunda, parlama (specular) bileşeninde **ani ve rahatsız edici ışık kopmaları (cutoff)** meydana gelir.

Bu sorunu çözmek için James F. Blinn tarafından 1977 yılında geliştirilen **Blinn-Phong modeli**, günümüzde OpenGL ve DirectX uygulamalarında standart hale gelmiştir.

---

## Phong Modelindeki Sorun Nedir?

Klasik Phong modelinde speküler parlama, yansıyan ışık vektörü $\mathbf{R}$ ile kameraya bakan görüş vektörü $\mathbf{V}$ arasındaki açı ($\theta$) üzerinden hesaplanır:

$$\text{Speküler}_{\text{Phong}} = (\mathbf{R} \cdot \mathbf{V})^{\alpha}$$

Eğer bakış vektörü ile yansıma vektörü arasındaki açı $90^\circ$'yi aşarsa, nokta çarpımı negatif olur ($\mathbf{R} \cdot \mathbf{V} < 0$) ve speküler katkı sıfıra eşitlenir (`max(dot(R, V), 0.0)`). 

Bu durum, özellikle ışık kaynağı nesneye çok yakınken veya yüzey parlaklık üssü ($\alpha$) küçükken, parlamanın bir anda bıçakla kesilmiş gibi yok olmasına neden olur!

---

## Blinn-Phong ve Yarıvektör (Halfway Vector)

Blinn-Phong modeli bu problemi, yansıyan ışın yerine **Yarıvektör (Halfway Vector - $\mathbf{H}$)** kavramını kullanarak çözer.

Yarıvektör, ışık yönü $\mathbf{L}$ ile bakış yönü $\mathbf{V}$ arasındaki açının tam ortasını gösteren birim vektördür:

$$\mathbf{H} = \frac{\mathbf{L} + \mathbf{V}}{\|\mathbf{L} + \mathbf{V}\|}$$

![Blinn-Phong Yarıvektör Geometrisi](../img/advanced-lighting/advanced_lighting_halfway_vector.png)

Yarıvektör $\mathbf{H}$ ile yüzey normali $\mathbf{N}$ birbirine ne kadar yakınsa, kameraya yansıyan ışık miktarı o kadar fazladır! Speküler denklemimiz şuna dönüşür:

$$\text{Speküler}_{\text{Blinn-Phong}} = (\mathbf{N} \cdot \mathbf{H})^{\alpha}$$

$\mathbf{N}$ ile $\mathbf{H}$ arasındaki açı hiçbir zaman $90^\circ$'yi aşmayacağı için, klasik Phong modelindeki o çirkin parlama kopmaları **tamamen yok olur**!

---

## GLSL Fragment Shader Uygulaması

Klasik Phong ile Blinn-Phong arasında geçiş yapabilen modern bir fragment shader:

```glsl linenums="1" title="blinn_phong.frag"
#version 330 core
out vec4 FragColor;

in vec3 FragPos;
in vec3 Normal;
in vec2 TexCoords;

uniform sampler2D floorTexture;
uniform vec3 lightPos;
uniform vec3 viewPos;
uniform bool blinn;

void main()
{           
    vec3 color = texture(floorTexture, TexCoords).rgb;

    // Ortam (Ambient)
    vec3 ambient = 0.05 * color;

    // Yayılımcı (Diffuse)
    vec3 lightDir = normalize(lightPos - FragPos);
    vec3 normal = normalize(Normal);
    float diff = max(dot(lightDir, normal), 0.0);
    vec3 diffuse = diff * color;

    // Parlama (Specular)
    vec3 viewDir = normalize(viewPos - FragPos);
    float spec = 0.0;

    if(blinn)
    {
        // Blinn-Phong Yarıvektör Hesabı
        vec3 halfwayDir = normalize(lightDir + viewDir);  
        spec = pow(max(dot(normal, halfwayDir), 0.0), 32.0);
    }
    else
    {
        // Klasik Phong Yansıma Hesabı
        vec3 reflectDir = reflect(-lightDir, normal);
        spec = pow(max(dot(viewDir, reflectDir), 0.0), 8.0);
    }
    vec3 specular = vec3(0.3) * spec; // Parlama rengi

    FragColor = vec4(ambient + diffuse + specular, 1.0);
}
```

> [!NOTE]
> **Parlaklık Üssü (Shininess) Farkı:**
> $\mathbf{N}$ ile $\mathbf{H}$ arasındaki açı, $\mathbf{R}$ ile $\mathbf{V}$ arasındaki açının yaklaşık yarısı kadardır. Bu yüzden klasik Phong ile aynı boyutta bir parlama elde etmek için Blinn-Phong'da üs değerini genellikle **2 ila 4 kat daha yüksek** seçeriz (örneğin Phong için 8 ise, Blinn-Phong için 32).
