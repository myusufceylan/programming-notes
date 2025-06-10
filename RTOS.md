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


# EEPROM, FRAM, Boot Flag, Safe Mode

## EEPROM Nedir?

- **Electrically Erasable Programmable Read-Only Memory**  
- Kalıcı bellektir (güç kesilse de veri korunur)  
- Genellikle birkaç KB – yüzlerce KB kapasite  
- Yazma işlemi yavaştır (~ms mertebesi)  
- Sınırlı yazma ömrü vardır (~1 milyon yazma)  

### Kullanım Örnekleri

- Konfigürasyon ayarları  
- Seri numaralar  
- Kayıt defteri (log)  

---

## FRAM Nedir?

- **Ferroelectric RAM**  
- EEPROM'dan çok daha hızlıdır (~ns – µs)  
- Enerji kesilse bile veri korunur  
- Sınırsıza yakın yazma ömrü  
- Genellikle I2C veya SPI ile haberleşir  

### FRAM vs EEPROM

| Özellik       | EEPROM        | FRAM            |
|---------------|---------------|-----------------|
| Hız           | Yavaş         | ✅ Hızlı         |
| Yazma Ömrü    | Sınırlı       | ✅ Sonsuz        |
| Dayanıklılık  | Orta          | ✅ Yüksek        |
| Fiyat         | ✅ Ucuz       | Daha pahalı     |

---

## Boot Flag Nedir?

- Sistem yeniden başladığında **hangi modu çalıştıracağını belirlemek için kullanılan bayraktır**  
- Genellikle **EEPROM**, **FRAM** veya **RTC backup register** içine yazılır  

### Kullanım Senaryosu

```c
// Safe mode'a geçmek için flag set edilir
eeprom_write(BOOT_FLAG_ADDR, SAFE_MODE_FLAG);
NVIC_SystemReset();

// Bootloader başlangıcında:
if (eeprom_read(BOOT_FLAG_ADDR) == SAFE_MODE_FLAG) {
    jump_to_safe_mode();
}
```

---

## Safe Mode Nedir?

- Sistem, hata sonrası **kritik görevlerle sınırlı şekilde çalışmaya devam eder**  
- Genellikle:
  - Minimum sayıda görev  
  - LED/GPIO ile durum bildirimi  
  - Sensörlerin devre dışı bırakılması  
  - Haberleşme ile hata bildirimi  

### Tipik Safe Mode Özellikleri

| Özellik         | Normal Mod        | Safe Mode            |
|------------------|-------------------|------------------------|
| Görev sayısı     | Tüm görevler      | Yalnızca kritik        |
| I/O              | Açık              | Kısıtlı                |
| Veri kaydı       | Açık              | Devre dışı             |
| Watchdog         | Etkin             | ✅ Daha kısa sürede     |

---

## İpuçları

- Boot flag işlemleri **atomik** olmalıdır (yarım kalırsa sistem brick olabilir)  
- Safe mode sadece geçici bir çözüm değil, **denetimli geri yükleme** sağlayabilmelidir  
- EEPROM / FRAM erişimleri, **mutex** ile korunmalıdır (özellikle FreeRTOS altında)  


# AMP: Asymmetric Multiprocessing (Linux + RTOS)

## AMP Nedir?

- **Asimetrik Çoklu İşlem** yaklaşımıdır  
- Farklı çekirdeklerde farklı işletim sistemleri çalıştırılır  
- Örneğin: `Core 0 → Linux`, `Core 1 → FreeRTOS`  

> Zynq-7000 gibi SoC'lerde (örneğin çift çekirdekli Cortex-A9) yaygın olarak kullanılır

---

## AMP ile SMP Farkı

| Özellik           | AMP                                | SMP                         |
|-------------------|-------------------------------------|------------------------------|
| Çekirdekler       | Farklı işler yapar                  | Ortak işletim sistemi        |
| İşletim Sistemleri| Farklı (Linux + RTOS)               | Aynı OS tüm çekirdeklerde    |
| Bellek Kullanımı  | Kontrollü paylaşım / ayrılmış       | Ortak bellek                 |
| Kullanım Amacı    | Gerçek zamanlı + genel amaçlı       | Genel amaçlı                 |

---

## Neden AMP?

- **Gerçek zamanlı işlemleri Linux’ten izole etmek**  
- **Güvenlik**: Bir OS çökse diğer etkilenmez  
- **Performans**: FreeRTOS zaman kritik işleri, Linux kullanıcı arayüzü veya ağ yönetimi gibi işleri üstlenir  

---

## AMP Yapı Taşları

- **Device Tree**  
  - Hangi çekirdeğin Linux tarafından kullanıldığını belirtir  
  - `cpu@1` gibi düğümler devre dışı bırakılabilir (`status = "disabled"`)  

- **OpenAMP / RemoteProc / RPMsg**  
  - **RemoteProc**: RTOS uygulamasını başlatır (ELF dosyasını yükler)  
  - **RPMsg**: Linux ↔ RTOS arasında veri alışverişi sağlar (virtio tabanlı)  

- **Shared Memory**  
  - Linux ve RTOS’un birlikte kullandığı bellek bölgesi  
  - Genelde OCM (On-Chip Memory) tercih edilir  

---

## Haberleşme: RPMsg ve Mailbox

- **RPMsg (Remote Processor Messaging)**  
  - Queue-benzeri yapı  
  - İki çekirdek arasında yüksek seviyeli mesajlaşma

- **Mailbox IP Core**  
  - Donanım tetikleyici / interrupt mekanizması  

### FreeRTOS Tarafında RPMsg Callback

```c
void rpmsg_recv_callback(void *payload, size_t len, void *priv, u32 src) {
    process_message(payload);
}
```

---

## Boot Süreci (Zynq Örneği)

```
FSBL → U-Boot → Linux başlar → RemoteProc ile FreeRTOS (Core 1) başlatılır
```

---

## İpuçları

- RTOS çekirdeği için bellek tahsisinde **OCM (On-Chip Memory)** tercih edilmelidir  
- Device Tree'de kullanılmayan CPU `status = "disabled"` olarak işaretlenmelidir  
- **IRQ paylaşımı** ve interrupt yönetimi dikkatle tasarlanmalıdır  
- Shared memory yapısı mutex/semaphore ile korunmalıdır (özellikle RPMsg üzerinde)  


# ARM Cortex-R5 Mimarisi

## Genel Özellikler

- ARMv7-R mimarisine dayanır (R: Real-time)
- Gerçek zamanlı sistemler için optimize edilmiştir
- Zynq-7000 SoC gibi platformlarda Linux dışı çekirdek olarak kullanılır

## Temel Özellikler

| Özellik                  | Açıklama                             |
|---------------------------|--------------------------------------|
| Çekirdek Yapısı          | 32-bit RISC                         |
| Clock Speed              | ~300 MHz – 600 MHz (SoC'e bağlı)   |
| Gerçek Zamanlı Destek    | ✅ Var (deterministik)               |
| Cache                    | ✅ I-Cache, D-Cache ayrı             |
| TCM (Tightly Coupled Mem)| ✅ Düşük gecikmeli özel RAM alanı    |
| Vectored Interrupt       | ✅ Hızlı ISR geçişi sağlar           |
| MPU (Memory Protection)  | ✅ Bellek alanı koruması             |

---

## R5 vs A9 (Karşılaştırma)

| Özellik         | Cortex-A9                  | Cortex-R5                  |
|------------------|----------------------------|-----------------------------|
| OS Desteği       | Linux, Android             | RTOS (FreeRTOS, baremetal) |
| Cache            | Var                         | Var + TCM opsiyonu         |
| Real-time        | ❌ Sınırlı                  | ✅ Deterministik            |
| Kullanım Alanı   | UI, ağ, dosya sistemleri    | Kontrol, motor sürme, safety |

---

## Bellek Yapısı

- **TCM (Tightly Coupled Memory)**: Gecikmesiz erişim, predictability yüksek
- **OCM (On-Chip Memory)**: Paylaşımlı RAM, genelde 256 KB
- **DDR**: Harici RAM (R5 tarafında kullanılabilir ama deterministik değil)

---

## Interrupt Sistemi

- NVIC benzeri: Vectored interrupt controller
- Interrupt latency çok düşüktür (~10–20 cycle)
- ISR’lar `__attribute__((interrupt))` ile tanımlanabilir

---

## Yazılım Geliştirme

- **Toolchain**: `arm-none-eabi-gcc`
- **Geliştirme Ortamı**: Vitis, SDK, baremetal veya RTOS
- **Debug**: Xilinx XSCT, OpenOCD, JTAG debugger

---

## İpuçları

- R5 gerçek zamanlı görevler için idealdir (örneğin motor sürme)
- TCM’e kod veya data atanması için linker script düzenlenmelidir
- I-Cache ve D-Cache etkili ama predictability dikkat ister
- R5’in bellek alanı Linux’ten izole edilmelidir (AMP senaryolarında)


# GDB ve Valgrind ile Debug Süreci

## GDB Nedir?

- **GNU Debugger**  
- C/C++ gibi diller için hata ayıklama aracıdır  
- Bellek içerikleri, değişken değerleri, call stack izlenebilir  
- Zynq gibi platformlarda JTAG, UART veya semihosting üzerinden bağlanabilir  

---

### Temel GDB Komutları

```bash
gdb ./my_program.elf
(gdb) break main        # main fonksiyonunda dur
(gdb) run               # programı çalıştır
(gdb) next              # satır atla
(gdb) step              # fonksiyona gir
(gdb) print var         # değişken değeri göster
(gdb) backtrace         # çağrı yığını göster
(gdb) info locals       # yerel değişkenleri listele
```

---

### GDB ile Uzak Debug (Remote Debug)

```bash
(gdb) target remote :3333
```

- `OpenOCD`, `XSDB`, `J-Link GDB Server` gibi araçlarla bağlantı sağlanabilir  
- Zynq R5 için örnek XSCT akışı:

```tcl
xsct
connect
targets -set -filter {name =~ "Cortex-R5#0"}
rst -processor
dow my_r5_app.elf
con
```

---

## Valgrind Nedir?

- Bellek sızıntılarını ve bellek erişim hatalarını analiz eder  
- **Sadece Linux kullanıcı alanı** uygulamaları için geçerlidir (RTOS üzerinde çalışmaz)  
- En çok kullanılan aracı: **memcheck**

---

### Valgrind Kullanımı

```bash
valgrind --leak-check=full ./my_app
```

#### Örnek Uyarı

```
==1234== Invalid write of size 4
==1234==    at 0x4005D4: main (example.c:10)
```

---

## GDB + Valgrind Farkı

| Özellik         | GDB                                 | Valgrind                         |
|------------------|--------------------------------------|-----------------------------------|
| Hedef            | Her platform                         | Sadece Linux kullanıcı alanı      |
| Sağladığı şey    | Adım adım izleme, değişken okuma     | Bellek hataları, leak tespiti     |
| Gerçek Zamanlı   | ✅ Evet                              | ❌ Hayır, uygulamayı yavaşlatır   |

---

## İpuçları

- GDB ile ISR fonksiyonları da **breakpoint** ile izlenebilir  
- Valgrind sadece **geliştirme/simülasyon** ortamında kullanılır  
- FreeRTOS gibi sistemlerde GDB kullanımı için `-g` derleme flag’i zorunludur  
- **Memory leak** şüphesinde Valgrind ile test yapılması önerilir  


# CMake / Make: Derleme Süreci

## Make Nedir?

- Derleme sürecini tanımlayan basit otomasyon aracıdır  
- `Makefile` ile hedefler (**targets**), kurallar (**rules**), bağımlılıklar (**dependencies**) tanımlanır  

---

### Basit Makefile Örneği

```makefile
main: main.o utils.o
	$(CC) main.o utils.o -o main

main.o: main.c
	$(CC) -c main.c

utils.o: utils.c
	$(CC) -c utils.c
```

### Komutlar

```bash
make         # Varsayılan hedefi derler
make clean   # Genellikle temizleme hedefi tanımlıdır
```

---

## CMake Nedir?

- Platformdan bağımsız, modern bir **derleme sistemi üreticisidir**  
- `CMakeLists.txt` dosyası üzerinden **Makefile** veya **Ninja** gibi sistemler üretilir  

---

### Temel CMake Akışı

```bash
mkdir build && cd build
cmake ..
make
```

---

### Basit CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.10)
project(MyApp C)

set(SOURCES main.c utils.c)

add_executable(my_app ${SOURCES})
```

---

## Make vs CMake

| Özellik          | Make                | CMake                          |
|------------------|---------------------|---------------------------------|
| Kullanım         | Doğrudan            | ✅ Üst katman (Make üretir)     |
| Taşınabilirlik   | Düşük               | ✅ Platform bağımsız            |
| Derleme Tanımı   | Elle                | ✅ Deklaratif, modüler          |
| Büyük Proje Desteği | Karmaşık         | ✅ Kolay yönetilir              |

---

## Gömülü Sistemlerde CMake

- Gömülü projelerde **toolchain file** ile çapraz derleme yapılır  

```cmake
set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_C_COMPILER arm-none-eabi-gcc)
```

- FreeRTOS, STM32, Zynq gibi sistemlerde yapı yönetimini kolaylaştırır  

---

## İpuçları

- Küçük projelerde `Makefile` yeterlidir, büyük projelerde **CMake** tercih edilmelidir  
- CMake ile **test**, **konfigürasyon**, **versiyon yönetimi** kolay entegre edilir  
- **Out-of-source build** (build dizini ayrı) temiz derleme sağlar  
- Derleme çıktılarında `-Wall`, `-O2`, `-g` gibi bayraklar kritik rol oynar  


# CI / CD: Gömülü Sistemlerde DevOps Yaklaşımı

## Tanımlar

- **CI (Continuous Integration)**:  
  Her kod değişikliğinde otomatik **test** ve **derleme** sürecidir  

- **CD (Continuous Delivery / Deployment)**:  
  - **Delivery**: Testlerden geçmiş sürümün yayınlamaya hazır hale getirilmesi  
  - **Deployment**: Yazılımın otomatik olarak hedef cihaza yüklenmesi  

---

## Neden Gömülü Sistemlerde CI/CD?

- Firmware değişiklikleri sistem kararlılığını bozabilir  
- Donanıma özel test ve simülasyon gereklidir  
- CI/CD ile insan hatası azaltılır, sürüm yönetimi kolaylaşır  
- Süreçler otomatikleştirilerek **verimlilik ve kalite** artırılır  

---

## Tipik CI/CD Adımları

1. **Kod Push** (`git push` ile tetiklenir)  
2. **Otomatik Derleme** (`make`, `cmake`, `arm-none-eabi-gcc`)  
3. **Statik Kod Analizi** (Cppcheck, SonarQube, Clang-Tidy)  
4. **Birim Testleri** (Unity, Ceedling, GoogleTest)  
5. **Firmware Paketleme** (hex, bin, elf üretimi)  
6. **Deploy / Programlama** (JTAG, OTA, UART üzerinden yükleme)  

---

## Araçlar

| Amaç              | Araçlar                            |
|-------------------|-------------------------------------|
| Kaynak Kontrolü   | Git, GitHub, GitLab                |
| CI Motoru         | GitHub Actions, GitLab CI, Jenkins |
| Derleme           | Make, CMake, GCC, arm-none-eabi    |
| Test              | Unity, Ceedling, GoogleTest        |
| Statik Analiz     | Cppcheck, Clang-Tidy               |
| Otomasyon         | Python, Bash, Docker               |

---

## Gömülü CI/CD Pipeline Örneği (GitHub Actions)

```yaml
name: Build and Test

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: Install toolchain
        run: sudo apt-get install gcc-arm-none-eabi

      - name: Build firmware
        run: make all

      - name: Run tests
        run: make test
```

---

## Zorluklar ve Çözümler

| Zorluk                   | Çözüm                                   |
|--------------------------|------------------------------------------|
| Donanım gereksinimi      | Donanım simülasyonu / emulator           |
| Çapraz derleyici kurulumu| Docker konteynerleri ile çözüm          |
| Sürüm takibi ve rollback | Git tag / release yönetimi              |
| Programlama (flash/UART) | CLI tabanlı otomasyon araçları          |

---

## İpuçları

- **Docker** kullanımı, build ortamlarının tutarlılığını garanti eder  
- **Mock GPIO** gibi tekniklerle testler simülasyon ortamında yapılabilir  
- **Versiyon numaraları** otomatik üretilebilir (`git describe`, `build ID`)  
- Donanım testleri için **hardware-in-the-loop** entegrasyonu planlanabilir  


# Unit Test: Görevler, ISR ve Mocklama

## Unit Test Nedir?

- Küçük kod birimlerinin (**fonksiyon**, **görev** vb.) bağımsız olarak test edilmesidir  
- Kodun doğruluğunu **erkenden ve izole şekilde** kontrol etmeye olanak sağlar  
- Gömülü sistemlerde hata maliyeti yüksek olduğundan **kritik öneme sahiptir**  

---

## Gömülü Sistemlerde Test Zorlukları

- Donanım bağımlılığı (GPIO, UART, SPI vs.)  
- **ISR**, **timer**, **FreeRTOS task** gibi zaman veya donanım tabanlı yapıların soyutlanması  
- Görevlerin zamanlamaya bağlı çalışması  
- Çevresel bileşenlerin **mocklanması** gerekmesi  

---

## Test Framework’leri

| Framework    | Özellik                                      |
|--------------|-----------------------------------------------|
| Unity        | Hafif, C diline uygun, gömülü sistem dostu     |
| Ceedling     | Unity + otomasyon + CMock                     |
| CMock        | Donanım fonksiyonları için **mock** üretir     |
| GoogleTest   | C++ projeler için zengin API ve assert seçenekleri |

---

## Görev Testi Örneği

```c
void task_toggle_led(void *pvParameters) {
    while (1) {
        toggle_led();
        vTaskDelay(pdMS_TO_TICKS(1000));
    }
}
```

### Test Edilebilir Hale Getirmek İçin

- `toggle_led()` fonksiyonu ayrı tanımlanmalı ve mocklanabilir olmalı  
- `vTaskDelay()` etkisi testte kaldırılmalı (yinelemeli simülasyon tercih edilir)  

---

## ISR Testi

ISR’ler doğrudan test edilmez. Ancak:

- ISR içinde çağrılan yardımcı fonksiyonlar **bağımsız test edilebilir**  
- `xQueueSendFromISR()`, `xSemaphoreGiveFromISR()` gibi çağrılar mocklanabilir  

```c
void EXTI0_IRQHandler(void) {
    BaseType_t xHigherPriorityTaskWoken = pdFALSE;
    xSemaphoreGiveFromISR(xSemaphore, &xHigherPriorityTaskWoken);
    portYIELD_FROM_ISR(xHigherPriorityTaskWoken);
}
```

- Bu örnekte ISR fonksiyonu değil, içindeki **FreeRTOS çağrıları** test kapsamına alınabilir  

---

## Mocklama Nedir?

- Gerçek donanım fonksiyonlarının **taklit edilmesi**dir  
- Giriş/çıkış etkisi olmadan yazılımın sadece mantığı test edilir  

### Örnek

#### Gerçek Fonksiyon

```c
void uart_send(char *msg) {
    HAL_UART_Transmit(...);
}
```

#### Mock Versiyon

```c
void uart_send(char *msg) {
    mock_uart_log(msg);  // Sadece kaydet, donanım yok
}
```

---

## Ceedling ile Test Süreci

```bash
ceedling new my_test_project
cd my_test_project
ceedling test:all
```

### Dosya Yapısı

- `test/test_my_module.c` → Test senaryoları  
- `src/my_module.c` → Gerçek uygulama kodu  
- `mock_my_module.c` → Otomatik mock dosyası (CMock üretir)  

---

## İpuçları

- ISR içinde minimum işlem yap → test kolaylaşır  
- **Delay** veya zaman bağlı fonksiyonlar izole edilmelidir  
- Donanım arayüzleri (GPIO, UART) **soyutlanmalı**  
- Her görev/fonksiyon için **code coverage** takibi yapılmalıdır  
- **Mocklama ≠ Simülasyon** → yalnızca dış etkilerin taklididir, zaman davranışı içermez  


# FPGA Sınıfları: Artix, Kintex, Virtex, Zynq

## Neden Sınıflar Var?

- Xilinx (AMD) FPGA’leri farklı uygulama ihtiyaçlarına göre sınıflandırmıştır:
  - Performans
  - Güç tüketimi
  - Entegre özellikler (örneğin: işlemci çekirdeği, transceiver, DDR interface)

---

## Artix-7

- **Düşük güç – orta performans**
- Genellikle küçük/orta ölçekli gömülü sistemler için
- IOT, endüstriyel kontrol, görüntü işleme uygulamalarında kullanılır

| Özellik               | Artix-7                       |
|------------------------|-------------------------------|
| Güç Tüketimi           | ✅ Düşük                      |
| Transceiver Desteği    | ❌ Yok                        |
| DDR Desteği            | ❌ Sınırlı                    |
| Hedef Kullanım         | Kamera arayüzü, küçük FPGA uygulamaları |

---

## Kintex-7

- **Yüksek bant genişliği – orta güç tüketimi**
- DSP ağırlıklı uygulamalar ve yüksek hızlı veri iletimi gereken yerler
- Radar, iletişim, video kodlama

| Özellik               | Kintex-7                     |
|------------------------|-------------------------------|
| Transceiver Desteği    | ✅ GTH / GTX                 |
| DDR Desteği            | ✅ Var                       |
| Güç Tüketimi           | Orta                         |
| Hedef Kullanım         | Haberleşme, sinyal işleme    |

---

## Virtex-7

- **En yüksek performans**
- HPC, yüksek hızlı iletişim, veri merkezi çözümleri
- Çok sayıda transceiver, büyük bellek ve dev logic alanı

| Özellik               | Virtex-7                     |
|------------------------|-------------------------------|
| Transceiver Desteği    | ✅ En gelişmiş               |
| Logic Kapasitesi       | ✅ En yüksek                 |
| Güç Tüketimi           | Yüksek                       |
| Hedef Kullanım         | Savunma, HPC, data center    |

---

## Zynq-7000 / Zynq UltraScale+

- **FPGA + ARM SoC birleşimi**
- FPGA kısmı (PL) + ARM işlemci (PS)
- Heterojen sistemler: Linux + FPGA + RTOS aynı sistemde

| Özellik                   | Zynq-7000                      |
|----------------------------|--------------------------------|
| ARM İşlemci                | ✅ Cortex-A9 (PS)              |
| FPGA Bölümü                | ✅ Artix-7 sınıfı (PL)         |
| DDR / DMA / UART           | ✅ ARM üzerinden kolay         |
| Hedef Kullanım             | Gömülü Linux, kontrol sistemleri, video işleme |

---

## Sınıflar Arası Kıyaslama

| Özellik       | Artix     | Kintex     | Virtex     | Zynq       |
|---------------|-----------|------------|------------|------------|
| Güç Tüketimi  | ✅ Düşük  | Orta       | ❌ Yüksek  | Orta       |
| Performans    | Orta      | ✅ Yüksek  | ✅ En yüksek | Orta       |
| Fiyat         | ✅ Ucuz   | Orta       | ❌ Pahalı  | Orta       |
| ARM Çekirdek  | ❌        | ❌         | ❌         | ✅ Var     |

---

## İpuçları

- Küçük projeler için **Artix**, gerçek zamanlı kontrol için **Zynq**, DSP için **Kintex**, HPC için **Virtex** uygundur  
- Zynq sistemleri yazılım + donanım birlikte çalışmayı öğrenmek için idealdir  
- Board seçiminde **power budget** ve **I/O ihtiyaçları** kritik önemdedir
