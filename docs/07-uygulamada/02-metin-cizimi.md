---
title: Metin Çizimi (Text Rendering - FreeType)
description: TrueType (.ttf) yazı tiplerini FreeType kütüphanesi ile yüklemek, Glif metrikleri ve dinamik VBO ile ekrana yazı yazdırma.
---

# Metin Çizimi (Text Rendering - FreeType)

Oyunlarda can göstergesi (HUD), menü yazıları, diyaloglar veya debug FPS sayaçları için ekrana metin yazdırmak zorundasınız.

OpenGL'in içinde yerleşik bir `printText("Merhaba")` fonksiyonu yoktur; çünkü OpenGL yalnızca üçgenler, çizgiler ve noktalar çizmeyi bilir. Metin çizmek için her bir harfi (glif / glyph) küçük bir doku dörtgeni olarak ekrana basmamız gerekir.

Bunu yapmak için endüstri standardı olan **FreeType** kütüphanesini kullanırız.

---

## FreeType Kurulumu ve Glif Metrikleri

FreeType; TTF, OTF ve Type1 yazı tiplerini açıp her bir harfin piksellerini çıkaran açık kaynaklı bir kütüphanedir.

Bir harfin ekrandaki konumu şu metriklerle belirlenir:

![Glif Metrikleri](../img/in-practice/glyph.png)

- **Size (Boyut):** Glifin genişliği ve yüksekliği,
- **Bearing (Yataklama):** Taban çizgisine (baseline) göre harfin sol ve üst ofseti (örneğin 'g' veya 'y' harfi tabanın altına inerken, 'A' tabanın üstündedir),
- **Advance (İlerleme):** Bir sonraki harfe geçmek için yatayda ne kadar piksel ilerleneceği.

Bu verileri saklamak için bir C++ yapısı kuralım:

```cpp linenums="1" title="Character Yapısı"
struct Character {
    unsigned int TextureID; // Glif dokusu ID'si
    glm::ivec2   Size;      // Glif boyutları
    glm::ivec2   Bearing;   // Glif ofseti
    unsigned int Advance;   // Sonraki harfe uzaklık (1/64 piksel cinsinden)
};

std::map<char, Character> Characters;
```

---

## Yazı Tipini Yükleme ve Doku Karakter Haritası (Atlas)

ASCII tablosundaki ilk 128 karakteri FreeType ile yükleyip tek kanallı (`GL_RED`) dokulara aktaralım:

```cpp linenums="1" title="FreeType Yükleme Kodu"
FT_Library ft;
if (FT_Init_FreeType(&ft))
    std::cout << "HATA: FreeType başlatılamadı!" << std::endl;

FT_Face face;
if (FT_New_Face(ft, "fonts/arial.ttf", 0, &face))
    std::cout << "HATA: Yazı tipi dosyası yüklenemedi!" << std::endl;

FT_Set_Pixel_Sizes(face, 0, 48); // 48 piksel boyut

glPixelStorei(GL_UNPACK_ALIGNMENT, 1); // 1 bayt hizalama

for (unsigned char c = 0; c < 128; c++)
{
    if (FT_Load_Char(face, c, FT_LOAD_RENDER))
        continue;

    unsigned int texture;
    glGenTextures(1, &texture);
    glBindTexture(GL_TEXTURE_2D, texture);
    glTexImage2D(
        GL_TEXTURE_2D,
        0,
        GL_RED,
        face->glyph->bitmap.width,
        face->glyph->bitmap.rows,
        0,
        GL_RED,
        GL_UNSIGNED_BYTE,
        face->glyph->bitmap.buffer
    );
    
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);

    Character character = {
        texture, 
        glm::ivec2(face->glyph->bitmap.width, face->glyph->bitmap.rows),
        glm::ivec2(face->glyph->bitmap_left, face->glyph->bitmap_top),
        face->glyph->advance.x
    };
    Characters.insert(std::pair<char, Character>(c, character));
}
FT_Done_Face(face);
FT_Done_FreeType(ft);
```

---

## Metin Çizim Fonksiyonu (`RenderText`)

Dinamik bir VBO kullanarak her bir karakter için bir dörtgen (quad) üretir ve ekrana çizeriz:

```cpp linenums="1" title="RenderText Fonksiyonu"
void RenderText(Shader &s, std::string text, float x, float y, float scale, glm::vec3 color)
{
    s.use();
    glUniform3f(glGetUniformLocation(s.ID, "textColor"), color.x, color.y, color.z);
    glActiveTexture(GL_TEXTURE0);
    glBindVertexArray(VAO);

    for (auto c = text.begin(); c != text.end(); c++)
    {
        Character ch = Characters[*c];

        float xpos = x + ch.Bearing.x * scale;
        float ypos = y - (ch.Size.y - ch.Bearing.y) * scale;

        float w = ch.Size.x * scale;
        float h = ch.Size.y * scale;

        float vertices[6][4] = {
            { xpos,     ypos + h,   0.0f, 0.0f },            
            { xpos,     ypos,       0.0f, 1.0f },
            { xpos + w, ypos,       1.0f, 1.0f },

            { xpos,     ypos + h,   0.0f, 0.0f },
            { xpos + w, ypos,       1.0f, 1.0f },
            { xpos + w, ypos + h,   1.0f, 0.0f }           
        };

        glBindTexture(GL_TEXTURE_2D, ch.TextureID);
        glBindBuffer(GL_ARRAY_BUFFER, VBO);
        glBufferSubData(GL_ARRAY_BUFFER, 0, sizeof(vertices), vertices); 
        glBindBuffer(GL_ARRAY_BUFFER, 0);

        glDrawArrays(GL_TRIANGLES, 0, 6);
        // Advance 1/64 piksel cinsindedir, bu yüzden 64'e böleriz (>> 6)
        x += (ch.Advance >> 6) * scale; 
    }
    glBindVertexArray(0);
    glBindTexture(GL_TEXTURE_2D, 0);
}
```

Sonuç: Ekranınızda dilediğiniz renk, boyut ve konumda pürüzsüz yazı tipleri çizebilirsiniz!
