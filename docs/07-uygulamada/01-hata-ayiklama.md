---
title: OpenGL Hata Ayıklama (Debugging)
description: glGetError(), modern OpenGL Hata Ayıklama Çıktısı (Debug Output Callback) ve RenderDoc / Nsight grafik hata ayıklayıcıları.
---

# OpenGL Hata Ayıklama (Debugging)

Grafik programlama yaparken ekranınızda sadece simsiyah bir pencere görmek (ünlü **"Black Screen of Death"**) her OpenGL geliştiricisinin en sık yaşadığı kabustur. 

Klasik C++ programlarında olduğu gibi satır satır `printf` koyamazsınız; çünkü kodlarınız GPU'nun içinde paralel çalışan yüz binlerce çekirdekte icra edilir.

Bu bölümde OpenGL hatalarını yakalamanın modern, profesyonel yöntemlerini öğreneceğiz.

---

## 1. Klasik Yöntem: `glGetError()`

En eski yöntem her şüpheli OpenGL çağrısından sonra `glGetError()` fonksiyonunu çağırmaktır:

```cpp linenums="1" title="glGetError Kontrolü"
GLenum err;
while((err = glGetError()) != GL_NO_ERROR)
{
    std::cout << "OpenGL Hatası: " << err << std::endl;
}
```

Yaygın hata kodları:
- `GL_INVALID_ENUM (1280)`: Geçersiz enum sabiti gönderildi.
- `GL_INVALID_VALUE (1281)`: Sınırların dışında bir sayısal değer verildi.
- `GL_INVALID_OPERATION (1282)`: O anki durum için yasaklanmış bir komut çağrıldı (örneğin VAO bağlı değilken çizim yapmak).
- `GL_OUT_OF_MEMORY (1285)`: GPU belleği tükendi.

Ancak bu yöntem her satırın arasına kod yazmayı gerektirir ve hatanın **hangi satırda** oluştuğunu doğrudan söylemez.

---

## 2. Modern Çözüm: OpenGL Hata Ayıklama Çıktısı (`glDebugMessageCallback`)

OpenGL 4.3 sürümüyle (veya `GL_KHR_debug` uzantısıyla) birlikte gelen **Debug Output**, grafik dünyasındaki en büyük devrimlerden biridir. Bir hata oluştuğu anda OpenGL doğrudan sizin belirlediğiniz bir C++ fonksiyonunu (callback) çağırır ve hatanın türünü, şiddetini ve açıklama metnini verir!

Pencere oluştururken debug bayrağını açalım:

```cpp
glfwWindowHint(GLFW_OPENGL_DEBUG_CONTEXT, true);
```

Callback fonksiyonumuzu tanımlayalım:

```cpp linenums="1" title="glDebugOutput Callback Fonksiyonu"
void APIENTRY glDebugOutput(GLenum source, 
                            GLenum type, 
                            unsigned int id, 
                            GLenum severity, 
                            GLsizei length, 
                            const char *message, 
                            const void *userParam)
{
    // Önemsiz bildirimleri yoksay
    if(id == 131169 || id == 131185 || id == 131218 || id == 131204) return; 

    std::cout << "---------------" << std::endl;
    std::cout << "Hata Mesajı (" << id << "): " <<  message << std::endl;

    switch (type)
    {
        case GL_DEBUG_TYPE_ERROR:               std::cout << "Tür: Hata"; break;
        case GL_DEBUG_TYPE_DEPRECATED_BEHAVIOR: std::cout << "Tür: Eski Kullanım"; break;
        case GL_DEBUG_TYPE_UNDEFINED_BEHAVIOR:  std::cout << "Tür: Tanımsız Davranış"; break; 
        case GL_DEBUG_TYPE_PERFORMANCE:         std::cout << "Tür: Performans"; break;
        default:                                std::cout << "Tür: Diğer"; break;
    } std::cout << std::endl;

    switch (severity)
    {
        case GL_DEBUG_SEVERITY_HIGH:         std::cout << "Şiddet: YÜKSEK"; break;
        case GL_DEBUG_SEVERITY_MEDIUM:       std::cout << "Şiddet: Orta"; break;
        case GL_DEBUG_SEVERITY_LOW:          std::cout << "Şiddet: Düşük"; break;
        case GL_DEBUG_SEVERITY_NOTIFICATION: std::cout << "Şiddet: Bildirim"; break;
    } std::cout << std::endl;
}
```

Fonksiyonu kaydedip aktif edelim:

```cpp
int flags; glGetIntegerv(GL_CONTEXT_FLAGS, &flags);
if (flags & GL_CONTEXT_FLAG_DEBUG_BIT)
{
    glEnable(GL_DEBUG_OUTPUT);
    glEnable(GL_DEBUG_OUTPUT_SYNCHRONOUS); 
    glDebugMessageCallback(glDebugOutput, nullptr);
    glDebugMessageControl(GL_DONT_CARE, GL_DONT_CARE, GL_DONT_CARE, 0, nullptr, GL_TRUE);
}
```

Artık yanlış bir parametre verdiğiniz an, konsolunuzda dosya ve satır detayına kadar kusursuz bir hata raporu belirecektir!

---

## 3. Profesyonel Grafik Hata Ayıklayıcılar: RenderDoc & Nsight

Karmaşık sahnelerde shader matematik hatalarını, Framebuffer dokularının içini veya hangi üçgenin nereye çizildiğini görmek için kod dışı profesyonel araçlar kullanılır:

1. **RenderDoc:** Tamamen ücretsiz, açık kaynak ve oyun sektörünün standart aracıdır. Tek bir kareyi (frame) dondurur; o karedeki tüm çizim çağrılarını (draw calls), VBO tamponlarını, dokuları ve shader değişkenlerini tek tek incelemenize imkan tanır.
2. **NVIDIA Nsight Graphics:** Ekran kartı çekirdeklerinin doluluk oranını, bellek bant genişliği darboğazlarını ve GPU profil analizini gösterir.
