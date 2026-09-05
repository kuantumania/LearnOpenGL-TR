# İleri Düzey Veri (Advanced Data) 💾

Şimdiye kadar bir tampon belleği doldurmak için neredeyse sadece `glBufferData` fonksiyonunu kullandık. Bu fonksiyon GPU belleğinde yeni bir alan tahsis eder ve veriyi oraya kopyalar.

Ancak bir oyun çalışırken her karede devasa bir tamponu sıfırdan oluşturup silmek korkunç bir performans kaybıdır. Bunun yerine var olan bir tamponun sadece **belirli bir bölgesini güncellemek** veya tampon belleğini **doğrudan CPU işaretçisine haritalamak** isteriz.

Bu bölümde OpenGL'in gelişmiş tampon yönetimi fonksiyonlarını inceleyeceğiz.

---

## 1. Parçalı Güncelleme: `glBufferSubData` 🔄

Tüm tamponu yeniden tahsis etmek yerine sadece belirli bir bayt ofsetinden başlayarak veriyi güncellemek için:

```cpp
glBufferSubData(GL_ARRAY_BUFFER, 24, sizeof(data), &data);
```

Bu sayede tamponun boyutunu değiştirmeden saniyede yüzlerce kez küçük veri parçacıklarını güncelleyebilirsiniz.

---

## 2. Bellek Eşleme: `glMapBuffer` 🗺️

Bazen C++ tarafındaki bir veriyi `memcpy` gibi fonksiyonlarla ara belleğe almadan, **doğrudan GPU'nun VRAM belleğine** yazmak istersiniz. `glMapBuffer` fonksiyonu GPU belleğinin o anki adresini bir C++ işaretçisi (`void*`) olarak döndürür:

```cpp
glBindBuffer(GL_ARRAY_BUFFER, VBO);
void *ptr = glMapBuffer(GL_ARRAY_BUFFER, GL_WRITE_ONLY);

// Doğrudan GPU belleğine yaz:
memcpy(ptr, data, sizeof(data));

// İşimiz bittiğinde eşlemeyi kaldır (ZORUNLUDUR!):
glUnmapBuffer(GL_ARRAY_BUFFER);
```

---

## 3. Tamponlar Arası Kopyalama: `glCopyBufferSubData` 📋

OpenGL 3.1 ile gelen bu fonksiyon, veriyi CPU belleğine hiç çekmeden **doğrudan bir GPU tamponundan diğer GPU tamponuna** aktarır:

```cpp
glBindBuffer(GL_COPY_READ_BUFFER, vbo1);
glBindBuffer(GL_COPY_WRITE_BUFFER, vbo2);
glCopyBufferSubData(GL_COPY_READ_BUFFER, GL_COPY_WRITE_BUFFER, 0, 0, sizeof(data));
```

---

Sırada GLSL dilinin gücünü artıran ve ortak bellek blokları kuracağımız **Bölüm 8: "İleri Düzey GLSL"** var!

👉 **[Sonraki Bölüm: İleri Düzey GLSL](08-ileri-duzey-glsl.md)**
