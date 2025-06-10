# 🚀 Gömülü Sistemler Öğrenme Serisi – Yazılım & Donanım Temelli

---

## ⏱️ Bölüm 1: Gerçek Zamanlı İşletim Sistemleri (RTOS) ve Gömülü Yazılım

### 🧠 Gün 1 – RTOS Nedir? Avantajları ve Temel Yapısı  
### 🧠 Gün 2 – Görevler, Zamanlayıcılar, Durumlar  
### 🧠 Gün 3 – FreeRTOS'a Giriş: Görevler ve Delay  
### 🧠 Gün 4 – Semaphore, Mutex, Queue, Event Group  
### 🧠 Gün 5 – Yazılım Zamanlayıcıları ve ISR Haberleşmesi  
### 🧠 Gün 6 – FDIR (Fault Detection, Isolation & Recovery)  
### 🧠 Gün 7 – EEPROM, FRAM, Boot Flag, Safe Mode  
### 🧠 Gün 8 – AMP: Asymmetric Multiprocessing (Linux + RTOS)  
### 🧠 Gün 9 – ARM Cortex-R5 Mimarisi  
### 🧠 Gün 10 – GDB ve Valgrind ile Debug Süreci  
### 🧠 Gün 11 – CMake / Make: Derleme Süreci  
### 🧠 Gün 12 – CI / CD: Gömülü Sistemlerde DevOps Yaklaşımı  
### 🧠 Gün 13 – Unit Test: Görevler, ISR ve Mocklama

---

## 🔩 Bölüm 2: Donanım Bilinci – Gelişmiş Gömülü Sistem Bileşenleri

### 🧠 Gün 14 – FPGA Sınıfları: Artix, Kintex, Virtex, Zynq  
### 🧠 Gün 15 – FPGA Speed Grade Nedir? (-1, -2, -3)  
### 🧠 Gün 16 – Industrial Temperature ve Sıcaklık Sınıfları  
### 🧠 Gün 17 – Golden Image: Güvenli Boot ve Geri Dönüş Yapısı  
### 🧠 Gün 18 – Bit Scrambling & Data Encoding Yaklaşımları  
### 🧠 Gün 19 – ECC (Error Correction Code) ile Bellek Güvenliği  
### 🧠 Gün 20 – System Hardening: Fiziksel ve Elektronik Sertlik  
### 🧠 Gün 21 – Yazılım Taraflı Hardness & Watchdog Entegrasyonu  
### 🧠 Gün 22 – Power-on Reset, Brown-out Detection, Startup Logic  
### 🧠 Gün 23 – Bootloader Temelleri ve Çoklu Firmware Yönetimi  
### 🧠 Gün 24 – Memory Map, Flash Partitions, Linker Script Okuma  
### 🧠 Gün 25 – Peripheral’lerle Düşük Seviye Haberleşme (SPI/I2C/UART)  
### 🧠 Gün 26 – Donanım Tabanlı Zamanlama: Timer, Counter, PWM  
### 🧠 Gün 27 – GPIO, Interrupt, DMA Kullanımı ve Donanım Etkileşimi  


# RTOS Nedir? Avantajları ve Temel Yapısı

## RTOS (Real-Time Operating System) Nedir?

- Gerçek zamanlı işletim sistemidir
- Belirli zaman kısıtları içinde işlemlerin deterministik olarak tamamlanmasını sağlar
- Genellikle gömülü sistemlerde kullanılır

## Avantajları

- ✅ Deterministik tepki süresi
- ✅ Çoklu görev (multi-tasking) yönetimi
- ✅ Kaynak paylaşımı (semaphore, mutex, queue)
- ✅ Donanım soyutlama ve modülerlik
- ✅ Zamanlayıcı ve kesme yönetimi

## RTOS ile Bare-metal Farkı

| Özellik          | Bare-metal      | RTOS              |
|------------------|------------------|--------------------|
| Görev yönetimi   | Yok              | Var                |
| ISR karmaşıklığı | Yüksek           | Azalır             |
| Zamanlama        | Manuel           | Otomatik (scheduler) |
| Modülerlik       | Zayıf            | Yüksek             |

## RTOS Bileşenleri

- Görevler (Tasks)
- Zamanlayıcı (Tick)
- Scheduler (Zamanlayıcı çekirdek)
- IPC yapıları: Semaphore, Mutex, Queue
- ISR mekanizması
- Bellek yönetimi


# Görevler, Zamanlayıcılar, Durumlar

## Görev (Task) Nedir?

- RTOS içerisinde bağımsız çalışan bir fonksiyondur  
- Her görev bir **öncelik** değeriyle scheduler’a kaydedilir  
- Görevler stack alanı, durumu ve fonksiyon işaretçisiyle temsil edilir  

## Görev Durumları

| Durum     | Açıklama                                          |
|-----------|---------------------------------------------------|
| Ready     | Çalışmaya hazır, scheduler seçmesini bekliyor     |
| Running   | Aktif olarak CPU’da çalışıyor                     |
| Blocked   | Bir olayı/semaphore/queue’yu bekliyor             |
| Suspended | Bilinçli olarak duraklatılmış                     |
| Delayed   | `vTaskDelay()` gibi zaman gecikmesinde            |

## Görev Önceliği

- Sayısal değerle ifade edilir (örneğin 0–5)  
- Yüksek öncelikli görev, düşük öncelikliden önce çalışır  
- Aynı öncelikte round-robin çalışabilir (`configTICK_RATE_HZ` etkili)  

## Zamanlayıcı (Scheduler)

- Görevler arasındaki CPU kullanım hakkını yönetir  
- Tipik scheduler türleri:
  - **Preemptive**: Yüksek öncelikli görev her zaman önceliklidir  
  - **Cooperative**: Görev kendisi işlem hakkını bırakmalıdır  

## Görev Oluşturma

```c
xTaskCreate(vTaskFunction, "TaskName", STACK_SIZE, NULL, PRIORITY, &xHandle);
```

## Delay Fonksiyonları

```c
vTaskDelay(tick);           // Görevi belirli süre uyutur

vTaskDelayUntil(&lastWakeTime, tick);  // Periyodik görevler için daha kararlıdır
```

## İpuçları

- Her görev **stack overflow**’a karşı izlenmelidir (`configCHECK_FOR_STACK_OVERFLOW`)  
- **Öncelik hataları**: *Priority Inversion* riski → `Mutex` ile çözülür  


# FreeRTOS'a Giriş: Görevler ve Delay

## FreeRTOS Nedir?

- Hafif ve açık kaynaklı gerçek zamanlı işletim sistemi çekirdeğidir  
- Mikrodenetleyiciler ve gömülü sistemler için tasarlanmıştır  
- Kütüphane gibi projeye eklenir, kernel ayrı derlenir  

## Temel Kavramlar

- **Tick**: Her bir RTOS zaman birimi (`configTICK_RATE_HZ` ile belirlenir)  
- **Task**: Bağımsız çalışan fonksiyonlar  
- **Idle Task**: Sistem boşta olduğunda otomatik çalışan görev  
- **Hook Functions**: Sistem olayları için özel fonksiyon tanımları  
  - Örn: `vApplicationIdleHook`, `vApplicationTickHook`  

## Görev Oluşturma (xTaskCreate)

```c
xTaskCreate(
    vTaskFunction,    // Görev fonksiyonu
    "TaskName",       // Görev ismi (debug için)
    STACK_SIZE,       // Stack boyutu (word cinsinden)
    NULL,             // Parametre
    PRIORITY,         // Görev önceliği
    &xHandle          // Görev tanıtıcısı
);
```

## Görev Geciktirme (vTaskDelay)

```c
vTaskDelay(pdMS_TO_TICKS(1000)); // 1 saniye uyur
```

- Görev delay süresince `Blocked` durumundadır  
- CPU, başka görevleri çalıştırmakta serbesttir  
- Non-busy wait sağlar (verimli bekleme)  

## Periyodik Görev Örneği

```c
TickType_t lastWakeTime = xTaskGetTickCount();
while(1) {
    do_work();
    vTaskDelayUntil(&lastWakeTime, pdMS_TO_TICKS(100));
}
```

- `vTaskDelayUntil` zaman tabanlı periyodik işler için daha tutarlıdır  
- İlk parametre olarak "son uyanma zamanı" bilgisi verilir  
- `do_work()` işlemi tamamlandığında kalan süre kadar uyur  

## İpuçları

- `vTaskDelay()` ve `vTaskDelayUntil()` ISR (Interrupt Service Routine) içinde kullanılmaz  
- Delay sırasında görev CPU’dan tamamen çekilir (low-power dostu)  
- Periyodik görevlerde `vTaskDelayUntil()` kullanımı zamanlamayı sabit tutar  


# Yazılım Zamanlayıcıları ve ISR Haberleşmesi

## Yazılım Zamanlayıcısı (Software Timer) Nedir?

- Belirli zaman aralıklarında çalışan **geri çağırım (callback)** fonksiyonudur  
- RTOS tick’i ile çalışır, **donanım timer'ından bağımsızdır**  
- Görev değildir; kendi içinde uzun işlem yapılmamalı, **tetikleyici rol** oynamalıdır  

### Zamanlayıcı Oluşturma

```c
TimerHandle_t xTimer = xTimerCreate(
    "MyTimer",
    pdMS_TO_TICKS(1000),  // 1 saniyelik periyot
    pdTRUE,               // pdTRUE = periyodik, pdFALSE = tek seferlik
    NULL,
    vTimerCallback        // Callback fonksiyonu
);

xTimerStart(xTimer, 0);
```

### Callback Fonksiyonu

```c
void vTimerCallback(TimerHandle_t xTimer) {
    // Timer tetiklendiğinde yapılacak işlem
}
```

---

## ISR (Interrupt Service Routine) Nedir?

- Donanımdan gelen sinyallerle tetiklenen özel fonksiyonlardır  
- ISR içinde **kısa ve kesintisiz işlemler** yapılmalıdır  
- **Görev başlatma/senkronizasyon işlemleri** yapılabilir, ama **bloklayıcı işlemler yapılmaz**  

---

## ISR'de Ne Yapılır?

✅ **Uygun** İşlemler:
- `xSemaphoreGiveFromISR()`
- `xQueueSendFromISR()`
- `xEventGroupSetBitsFromISR()`
- `portYIELD_FROM_ISR()` ile context switch

❌ **Uygun Olmayanlar:**
- `vTaskDelay()`, `malloc()`, `printf()`, görev oluşturma (`xTaskCreate`)
- Blocking çağrılar

---

### ISR'den Görev Tetikleme Örneği

```c
void button_isr_handler() {
    BaseType_t xHigherPriorityTaskWoken = pdFALSE;
    xSemaphoreGiveFromISR(xButtonSemaphore, &xHigherPriorityTaskWoken);
    portYIELD_FROM_ISR(xHigherPriorityTaskWoken);
}
```

---

## Yazılım Timer mı ISR mı?

| İhtiyaç                             | Önerilen Yapı                 |
|-------------------------------------|-------------------------------|
| Kesin zamanlı tekrar eden olaylar   | **Software Timer (RTOS)**     |
| Donanımsal tetikleme (buton vs.)    | **ISR + Semaphore**           |
| Donanım dışı zaman tetikleyicisi    | `vTaskDelay` / `DelayUntil`   |

---

## İpuçları

- ISR içindeki tetikleme `FromISR` fonksiyonlarıyla yapılmalıdır  
- Software timer callback fonksiyonları yalnızca **tetikleyici** olmalı, uzun işlem yapılmamalı  
- ISR sonrasında `portYIELD_FROM_ISR()` ile gerekli durumlarda context switch tetiklenir  



# FDIR (Fault Detection, Isolation & Recovery)

## FDIR Nedir?

- **Fault Detection**: Hatanın tespiti  
- **Isolation**: Hatayı sınırlama ve etkisizleştirme  
- **Recovery**: Sistemin kendini toparlaması veya güvenli moda geçmesi  

> Özellikle uzay, savunma, medikal gibi **kritik sistemlerde** hayati öneme sahiptir.

---

## 1. Fault Detection (Hata Tespiti)

- **Watchdog Timer**  
- **CRC**, **Checksum**  
- **Sensor değerlerinin sınır kontrolü**  
- **Memory pattern testi**  
- **ISR çağrı frekansının izlenmesi (anomaliler)**  

---

## 2. Fault Isolation (Yalıtım)

- Hatalı görevi `vTaskSuspend()` ile durdurmak  
- Hatalı modülü sistemden ayırmak  
- Sadece etkilenmiş bölümü resetlemek (örneğin yalnızca R5 core)  
- **Event Group** ile sadece ilgili görevleri tetiklememek  

---

## 3. Fault Recovery (Kurtarma)

- Görevi yeniden başlatmak (`xTaskCreate`, `vTaskResume`)  
- **Yazılım sıfırlama** (`NVIC_SystemReset()`, özel `system_reset()` fonksiyonu)  
- **Safe Mode**: Minimum işlevlerle devam etmek  
- **Boot Flag** ile farklı boot davranışı başlatmak (ör. recovery image)

---

## Watchdog Nedir?

- Sistem belirli sürede tepki vermezse reset atan zamanlayıcıdır  
- **Donanımsal (HW WDT)** ve **yazılımsal (SW WDT)** versiyonları bulunur  

### Basit Watchdog Kullanımı (Pseudocode)

```c
while(1) {
    refresh_watchdog();
    do_work();
}
```

---

## Tipik FDIR Yapısı

```
[Sensör] → [Hata Tespiti] → [Hatalı Görev?] → [İzolasyon] → [Safe Mode?] → [Reset veya Kurtarma]
```

---

## İpuçları

- FDIR mekanizması **her görevde izleme (monitoring)** yapmalıdır  
- FreeRTOS ile periyodik **denetim görevleri** oluşturulabilir  
- Sorun tekrar ediyorsa **system reset** gerekebilir  
- **Critical section**’lar hatayı maskeleyebilir: dikkatli tasarlanmalıdır  

