# Örnekleme (Instancing / 100.000 Nesne) 🪐

Bir uzay simülasyonunda bir gezegenin etrafındaki **astroit kuşağını** veya bir ormandaki binlerce ağacı çizmek istediğinizi hayal edin.

Eğer her bir asteroit için bir `for` döngüsü kurup `glDrawElements` çağırırsanız, CPU ile GPU arasındaki iletişim bant genişliği (*draw call overhead*) kilitlenir ve saniyede 5 kareye (5 FPS) düşersiniz!

Çözüm: **Örnekleme (Instancing)**.

Örnekleme, aynı geometri verisini paylaşan binlerce nesneyi **tek bir çizim çağrısıyla (Single Draw Call)** GPU'ya gönderme tekniğidir!

---

## `glDrawElementsInstanced` 🚀

Standart çizim çağrısı yerine instanced sürümünü çağırır ve kaç kopya istediğimizi belirtiriz:

```cpp
glDrawElementsInstanced(GL_TRIANGLES, indices.size(), GL_UNSIGNED_INT, 0, 100000);
```

GLSL Vertex Shader içerisinde her kopyaya özel `gl_InstanceID` yerleşik değişkeni verilir.

---

## Örneklenmiş Diziler: `glVertexAttribDivisor` ⚙️

100.000 adet asteroitin her birinin dünya uzayındaki `model` matrisini tek bir VBO tamponuna depolarız ve OpenGL'e bu niteliğin her köşe noktasında değil, **her yeni örnekte (instance)** bir sonraki matrise geçmesini söyleriz:

```cpp
// Matris 4 adet vec4 sütunundan oluşur (location 3, 4, 5, 6):
glEnableVertexAttribArray(3);
glVertexAttribPointer(3, 4, GL_FLOAT, GL_FALSE, sizeof(glm::mat4), (void*)0);
glVertexAttribDivisor(3, 1); // 1 = Her yeni örnekte 1 ilerle!

glEnableVertexAttribArray(4);
glVertexAttribPointer(4, 4, GL_FLOAT, GL_FALSE, sizeof(glm::mat4), (void*)(sizeof(glm::vec4)));
glVertexAttribDivisor(4, 1);

glEnableVertexAttribArray(5);
glVertexAttribPointer(5, 4, GL_FLOAT, GL_FALSE, sizeof(glm::mat4), (void*)(2 * sizeof(glm::vec4)));
glVertexAttribDivisor(5, 1);

glEnableVertexAttribArray(6);
glVertexAttribPointer(6, 4, GL_FLOAT, GL_FALSE, sizeof(glm::mat4), (void*)(3 * sizeof(glm::vec4)));
glVertexAttribDivisor(6, 1);
```

---

## Sonuç: 100.000 Asteroitli Gezegen Kuşağı! 🌌

Bu teknik sayesinde ekran kartınız zorlanmadan, akıcı 60+ FPS ile uzayda dönen yüz binlerce asteroiti aynı anda render edebilir!

---

Sırada kenarlardaki tırtıklanmaları ve piksellenmeleri yok edeceğimiz **Bölüm 11: "Kenar Yumuşatma (Anti-Aliasing)"** var!

👉 **[Sonraki Bölüm: Kenar Yumuşatma (Anti-Aliasing)](11-kenar-yumusatma.md)**
