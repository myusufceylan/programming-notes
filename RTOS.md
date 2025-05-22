# Gerçek-Zamanlı Sistemlere Giriş

## 1. Gerçek-Zamanlılık Nedir?

- Gerçek-zamanlılık, sistemin bir görevi belirli bir **maksimum süre içinde** tamamlayacağına dair garanti vermesidir.
- **Hızlı olmak ≠ Gerçek-zamanlı olmak**
- Ortalama değil, **en kötü durum** önemlidir.

### Örnek:
> Fren sisteminin hızlı olması değil, her durumda **belirlenen süre içinde tepki vermesi** önemlidir.

---

## 2. RTOS vs Genel Amaçlı İşletim Sistemleri

| Özellik           | Genel Amaçlı OS | RTOS                |
|------------------|------------------|---------------------|
| Öncelik          | Verim, konfor    | Zaman garantisi     |
| Scheduler        | Karmaşık/adil    | Basit ve deterministik |
| Gecikme          | Değişken         | Kontrollü           |
| Jitter           | Belirsiz         | Hesaplanabilir      |
| Kaynak yönetimi  | Dinamik          | Statik ve öngörülebilir |
| Hata toleransı   | Genelde tolere edilir | Kritik sonuçlar doğurabilir |

---

## 3. RTOS Temel Kavramlar

### Task (Görev)
- Bağımsız çalışan kod parçalarıdır.
- Her biri kendi **fonksiyonuna**, **stack** belleğine ve **önceliğine** sahiptir.

### Scheduler
- Hangi görevin ne zaman çalışacağını belirler.
- Genellikle **öncelik tabanlı** çalışır.

### Preemption (Öncelik Kesmesi)
- Daha yüksek öncelikli görev geldiğinde çalışan görev durdurulur.
- CPU yeni göreve geçer.

### Context Switch
- CPU'nun bir görevden diğerine geçerken yaptığı veri kaydetme ve yükleme işlemidir.
- Kaydedilenler: Program counter, stack pointer, register’lar vs.

### Görev Durumları
- **Running**: Aktif olarak CPU’yu kullanıyor.
- **Ready**: Çalışmaya hazır ama sırada bekliyor.
- **Blocked**: Zaman/sinyal/bekleme nedeniyle çalışamıyor.

---

## 4. RTOS Zaman Tabanı (Tick)

- RTOS, sistem saatinden gelen düzenli kesmelere (ticks) göre çalışır.
- Örnek: 1 kHz tick → her 1 ms’de bir zaman ilerletilir.
- `vTaskDelay()` gibi fonksiyonlar bu tick’lerle çalışır.

### Tickless Mode
- CPU boşta kaldığında gereksiz tick’leri atlayarak enerji tasarrufu sağlar.

---

## 5. Idle Görev

- Sistemde başka hiçbir görev çalışmadığında çalışan özel görevdir.
- Gelişmiş sistemlerde:
  - CPU uyku moduna alınabilir
  - Watchdog beslenebilir
  - İşsizlik yüzdesi hesaplanabilir

---

## 6. RTOS Kullanmanın Artıları ve Eksileri

### Avantajlar
- Gerçek zaman garantisi
- Modülerlik ve görev ayrımı
- Öncelik kontrolü
- Watchdog ve hata toleransı kolaylığı

### Dezavantajlar
- Karmaşık yapı büyüdükçe hata yapma riski artar
- Yanlış öncelik → görev starvation
- Stack yönetimi hassastır
- Debugging daha zordur

---

## 7. Linux’ta Jitter Ölçümü (Uygulama)

### Amaç:
- `clock_nanosleep()` ile 5 ms aralıkla çalışan programın **jitter’ını ölçmek**

### Komutlar:

```bash
cat > periodic.c <<'EOF'
#define _GNU_SOURCE
#include <stdio.h>
#include <time.h>
#include <stdlib.h>

static void ns_sleep(long ns)
{
    struct timespec req = { .tv_sec = ns / 1000000000L,
                            .tv_nsec = ns % 1000000000L };
    clock_nanosleep(CLOCK_MONOTONIC, 0, &req, NULL);
}

static long now_ns(void)
{
    struct timespec t; clock_gettime(CLOCK_MONOTONIC, &t);
    return t.tv_sec * 1000000000L + t.tv_nsec;
}

int main(int argc, char **argv)
{
    if (argc < 3) {
        fprintf(stderr,"kullanim: %s <periyot_ms> <tekrar>\n", argv[0]);
        return 1;
    }
    long period_ns = atol(argv[1]) * 1000000L;
    int loops = atoi(argv[2]);

    long next = now_ns();
    for (int i = 0; i < loops; ++i) {
        long t0 = now_ns();
        printf("%03d  Δt = %ld µs\n", i, (t0 - (next - period_ns))/1000);
        next += period_ns;
        ns_sleep(next - now_ns());
    }
}
EOF

gcc -O2 -Wall periodic.c -o periodic -lrt
./periodic 5 50
```

### Örnek Çıktı:

```
000  Δt = 5000 µs
001  Δt = 5111 µs
002  Δt = 5082 µs
...
025  Δt = 5063 µs
...
049  Δt = 5142 µs
```

### Gözlem:

- İdeal: 5000 µs  
- Gerçek: 5060–5230 µs arası → **~200 µs jitter**
- Bu, RTOS’un neden gerekli olduğunu gösterir.

---

## 8. Kritik Not: `vTaskDelayUntil()` ile Sarkma Önlemi

- `vTaskDelay()` her seferinde kaldığı yerden başlar → sarkma oluşabilir
- `vTaskDelayUntil()` ise **geçmiş referansa göre** gecikme verir → sabit aralık korunur

```c
vTaskDelayUntil(&lastWakeTime, xDelay);
```

# FreeRTOS – Görevler (Tasks)

## 1. FreeRTOS Nedir?

- FreeRTOS, küçük sistemlerde görevleri yönetmek için kullanılan açık kaynaklı bir gerçek-zamanlı çekirdek yazılımıdır.
- Masaüstü işletim sistemleri gibi dosya sistemi, pencere yönetimi, sürücüler içermez.
- Sadece görev planlaması, bekleme, sıralama, öncelik gibi temel sistem yönetimini sağlar.

---

## 2. Görev (Task) Nedir?

- Görev, FreeRTOS’un çalıştırıp yönettiği bir kod bloğudur.
- Her görev:
  - Kendi fonksiyonuna sahiptir
  - Kendi stack (yığın) belleğini kullanır
  - Bir öncelik değeriyle FreeRTOS tarafından sıralanır
  - Sistem tarafından “beklemede”, “hazır”, “çalışıyor” gibi durumlarda olabilir

### Görev Formatı:

```c
void MyTask(void *pvParameters)
{
    for (;;)
    {
        // Görev burada sürekli çalışır
    }
}
```

- `pvParameters`: Göreve dışarıdan veri geçmek için kullanılır  
- `for (;;)` → Görev bitmemelidir, FreeRTOS’un kontrolünden çıkmamalıdır

---

## 3. Görev Oluşturma (`xTaskCreate`)

### Kullanım:

```c
xTaskCreate(
    MyTask,             // Görev fonksiyonu
    "TaskName",         // Görev adı (debug için)
    128,                // Stack boyutu (word cinsinden)
    NULL,               // Parametre (isteğe bağlı)
    2,                  // Öncelik
    NULL                // Görev tanıtıcısı (isteğe bağlı)
);
```

- Bu işlem genellikle `main()` fonksiyonu içinde yapılır

---

## 4. Scheduler Başlatma (`vTaskStartScheduler`)

- Görevler tanımlandıktan sonra scheduler başlatılmalıdır:

```c
vTaskStartScheduler();
```

- Bu fonksiyon çağrıldıktan sonra kontrol tamamen FreeRTOS’a geçer.  
- `main()` fonksiyonunun altındaki kodlar artık çalışmaz.

---

## 5. Görev Önceliği

- Öncelik değeri: Daha yüksek sayı = daha öncelikli  
- Aynı önceliğe sahip görevler round-robin (sıralı) şekilde çalışır  
- Daha yüksek öncelikli görev aktif olursa **diğer görev durdurulur**

---

## 6. Görev Zamanlaması (`vTaskDelay`)

### Kullanım:

```c
vTaskDelay(pdMS_TO_TICKS(500));  // 500 ms bekle
```

- Görevi belirtilen süre boyunca **Blocked** durumuna geçirir  
- Diğer görevlerin çalışmasına izin verir  
- `pdMS_TO_TICKS()` makrosu, sistemin tick süresinden bağımsız olarak **gerçek zamanlı bekleme sağlar**

---

## 7. Örnek Kod

```c
void LEDTask(void *pvParameters)
{
    for (;;)
    {
        gpio_toggle(LED_PIN);                   // LED’i yak/söndür
        vTaskDelay(pdMS_TO_TICKS(500));         // 500 ms bekle
    }
}

int main(void)
{
    board_init();
    xTaskCreate(LEDTask, "LED", 128, NULL, 1, NULL);
    vTaskStartScheduler();

    // Buraya asla gelinmez
    for (;;);
}
```
