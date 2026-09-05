---
title: Noktasal Gölgeler (Omnidirectional Shadow Mapping)
description: Her yöne ışık yayan noktasal ışıklar için Küp Haritası (Cube Map) tabanlı derinlik haritası ve Geometri Shader ile tek geçişli render.
---

# Noktasal Gölgeler (Omnidirectional Shadow Mapping)

Yönsel ışıklar (Güneş gibi) tek bir yöne bakar ve 2B bir derinlik dokusuyla çözülebilir. Ancak bir ampul, meşale veya el bombası patlaması gibi **noktasal ışıklar (Point Lights)** uzayın **her yönüne ($360^\circ$)** ışık yayar!

Tüm yönlere gölge düşürmek için 2B bir doku yerine **Küp Haritası (Cubemap)** kullanırız.

---

## Derinlik Küp Haritası (Depth Cubemap)

Bir küp haritası 6 adet 2B yüzeyden oluşur ($+X, -X, +Y, -Y, +Z, -Z$). Işık kaynağının merkezine bir kamera koyup 6 yöne birden derinlik renderı alırız:

![Küp Haritası Gölgeleri](../img/advanced-lighting/point_shadows_diagram.png)

```cpp linenums="1" title="Derinlik Küp Haritası Kurulumu"
unsigned int depthCubemap;
glGenTextures(1, &depthCubemap);
glBindTexture(GL_TEXTURE_CUBE_MAP, depthCubemap);
for (unsigned int i = 0; i < 6; ++i)
    glTexImage2D(GL_TEXTURE_CUBE_MAP_POSITIVE_X + i, 0, GL_DEPTH_COMPONENT, 
                 SHADOW_WIDTH, SHADOW_HEIGHT, 0, GL_DEPTH_COMPONENT, GL_FLOAT, NULL);

glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_R, GL_CLAMP_TO_EDGE);
```

---

## Geometri Shader ile Tek Geçişte 6 Yüzü Çizmek

Sahneyi CPU'dan 6 kez çizdirmek yerine, modern OpenGL'de **Geometri Shader (Geometry Shader)** kullanarak tek bir çizim çağrısında (draw call) üçgenleri küp haritasının 6 yüzüne birden kopyalayabiliriz (`gl_Layer = face`):

```glsl linenums="1" title="point_shadows_depth.geom"
#version 330 core
layout (triangles) in;
layout (triangle_strip, max_vertices=18) out;

uniform mat4 shadowMatrices[6];
out vec4 FragPos;

void main()
{
    for(int face = 0; face < 6; ++face)
    {
        gl_Layer = face; // Hangi küp yüzüne yazılacağını belirler
        for(int i = 0; i < 3; ++i)
        {
            FragPos = gl_in[i].gl_Position;
            gl_Position = shadowMatrices[face] * FragPos;
            EmitVertex();
        }
        EndPrimitive();
    }
}
```

---

## Fragment Shader Gölge Sorgusu

Fragment shader içinde 3B bir yön vektörü ile doğrudan küp haritasını örnekleriz:

```glsl linenums="1" title="point_shadows.frag"
float VectorizedShadowCalculation(vec3 fragPos)
{
    vec3 fragToLight = fragPos - lightPos;
    float currentDepth = length(fragToLight);

    float closestDepth = texture(depthCubemap, fragToLight).r;
    closestDepth *= far_plane; // [0, 1] aralığından dünya birimine çevir

    float bias = 0.05;
    float shadow = currentDepth - bias > closestDepth ? 1.0 : 0.0;

    return shadow;
}
```
