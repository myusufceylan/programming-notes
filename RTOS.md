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


# FPGA Speed Grade Nedir? (-1, -2, -3)

## Tanım

- FPGA’lerin **işleme hızı ve zamanlama performansını** belirten sınıflamasıdır  
- Daha **düşük sayı**, daha **hızlı FPGA** anlamına gelir  
  - Örnek: `-3` → en hızlı, `-1` → en yavaş  

---

## Ne Etkiler?

- **Maksimum Clock Frekansı (Fmax)**  
- **Setup / Hold Timing Margin**  
- **Timing Closure** başarısını  
- **Veri Yolu Gecikmesi (Data Path Delay)**  
- **Zamanlama ihlalleri (timing violation)** çözüm yollarını  

---

## Speed Grade Karşılaştırma

| Speed Grade | Hız         | Güç Tüketimi | Fiyat      | Kullanım Tipi              |
|-------------|-------------|--------------|------------|-----------------------------|
| `-3`        | ✅ En hızlı | ❌ Yüksek     | ❌ Pahalı  | Zaman kritik tasarımlar     |
| `-2`        | Orta        | Orta         | Orta       | Genel amaçlı tasarımlar     |
| `-1`        | ❌ En yavaş | ✅ Düşük      | ✅ Ucuz    | Düşük frekanslı uygulamalar |

---

## Neden Her Zaman En Hızlıyı Kullanmayız?

- Daha hızlı FPGA’ler:
  - Daha fazla **güç tüketir**
  - Daha **pahalıdır**
  - **Soğutma ihtiyacı** doğurur
- Gerçek tasarımlarda genellikle **Fmax’in %80–90’ı** kullanılır  
- Bu nedenle çoğu uygulama için `-2` sınıfı **yeterli ve dengeli bir tercih** olur  

---

## Vivado’da Kullanımı

- `Timing Report (Report Timing Summary)` dosyasında Fmax ve Slack bilgisi yer alır  
- Speed grade’e göre:
  - `Slack` artabilir veya azalabilir  
  - `Worst Negative Slack (WNS)` değişebilir  

### Timing Violation varsa:

- **Pipelining** ekle  
- Daha hızlı **Speed Grade** seç  
- **Placement/Route** optimizasyonu yap  

---

## Koddan Örnek Speed Grade Ayarı

```tcl
# Vivado'da device seçiminde örnek:
set_property part xc7z020clg484-1 [current_project]
```

- Buradaki `-1`, speed grade bilgisidir  
- `-2`, `-3` versiyonları da aynı chip için farklı hızlarda mevcuttur  

---

## İpuçları

- Sadece timing violation çözmek için speed grade yükseltmek **maliyetlidir**  
- `-2` sınıfı, çoğu tasarım için **dengeli çözümdür**  
- **Isı, maliyet ve güç tüketimi birlikte** değerlendirilmelidir  


# Industrial Temperature ve Sıcaklık Sınıfları

## Neden Önemlidir?

- FPGA ve diğer entegre devreler, belirli sıcaklık aralıklarında **kararlı** çalışmak üzere tasarlanır  
- Özellikle **savunma**, **uzay**, **otomotiv** ve **endüstriyel otomasyon** gibi kritik sistemlerde **ısı dayanımı** hayati öneme sahiptir  

---

## Sıcaklık Sınıfları

| Sınıf       | Aralık (°C)           | Kullanım Alanı                  |
|-------------|------------------------|----------------------------------|
| Commercial  | 0°C to +85°C           | PC, tüketici elektroniği         |
| Industrial  | -40°C to +100/+105°C   | Endüstriyel otomasyon, robotik   |
| Automotive  | -40°C to +125°C        | Otomotiv sistemleri              |
| Military    | -55°C to +125/+135°C   | Askeri, uzay, savunma sistemleri |

---

## FPGA Datasheet’lerinde Nasıl Belirtilir?

- Parça numaralarında **son karakter** genellikle sıcaklık sınıfını belirtir:

| Sonek | Sınıf        |
|-------|--------------|
| `-C`  | Commercial   |
| `-I`  | Industrial   |
| `-M`  | Military     |

### Örnek

```text
XC7Z020-1CLG484I → Industrial grade
```

---

## FPGA Sıcaklık Performans Etkileri

- **Yüksek sıcaklıkta**:
  - Delay artar, **Fmax düşer**
  - Timing violation riski yükselir  
- **Aşırı soğukta**:
  - Delay azalabilir ancak malzeme **gerilimi** riski artar  
- **Sıcaklık dalgalanması**, zamanlama tutarlılığını olumsuz etkileyebilir  

---

## Termal Yönetim Neden Kritiktir?

- **Fan**, **heatsink**, **termal pad** gibi çözümler kullanılabilir  
- **Xilinx Power Estimator (XPE)** ve **Vivado Thermal Analysis** araçları ile analiz yapılmalıdır  

---

## Termal Performans Takibi

- FPGA içinde **dahili sıcaklık sensörü** bulunabilir  
- Zynq gibi platformlarda **XADC** ile sıcaklık izlenebilir  

```c
float temp = read_xadc_temperature(); // Örnek pseudo kod
```

---

## İpuçları

- Projenin **çalışma ortam sıcaklığına göre** doğru sınıf seçilmelidir  
- Sıcaklık aralığı uygun olsa bile **soğutma tasarımı** yapılmalıdır  
- **Gerçek zamanlı sıcaklık takibi** ve **watchdog** entegrasyonu ile **FDIR** güçlendirilebilir  
- Military ve automotive-grade FPGA’ler **daha pahalı** ve **uzun tedarik sürelidir**  


# Golden Image: Güvenli Boot ve Geri Dönüş Yapısı

## Tanım

- **Golden Image**, FPGA ya da gömülü sistemin **her zaman çalışacağı garanti edilen yedek firmware’idir**  
- Sistem güncellenirken veya sorun oluştuğunda geri dönülebilecek **emniyetli yedek** olarak kullanılır  

---

## Neden Golden Image?

- Yeni firmware **hatalı olabilir** (boot başarısız, CRC hatası, donma...)  
- Boot sırasında sistemin **brick** olmasını önler  
- Güvenilir bir **geri dönüş yolu** sağlar  
- Özellikle uçuş kontrol sistemleri, savunma elektroniği gibi **kritik uygulamalarda zorunludur**  

---

## Golden Image Yapısı (Zynq Örneği)

| Boot Aşaması              | Açıklama                                      |
|---------------------------|-----------------------------------------------|
| FSBL (First Stage Bootloader) | Her zaman **Golden Image** ile başlar        |
| SD/eMMC kontrolü          | Güncel image kontrol edilir                   |
| CRC / Signature Check     | Uygulama doğruluğu test edilir                |
| Başarısızsa               | Otomatik olarak **Golden Image** çalıştırılır |

---

## Golden Image Nerede Tutulur?

### Zynq İçin

- **QSPI Flash**’in ilk 1 MB’lık bölümü → `BOOT.BIN` (golden image)  
- Diğer bölümler → Güncel firmware (fallback için)

### Diğer Sistemler

- STM32, LPC vb. sistemlerde:
  - **Internal Flash** içinde özel alan ayrılır  
  - Bootloader, **flash bank switching** ile çalışır  

---

## Kontrollü Geçiş Mekanizması

- Firmware versiyonu kontrolü (`version.txt`, header, metadata)  
- **CRC / Hash** doğrulaması  
- **Boot flag** kontrolü (EEPROM / FRAM üzerinde tutulabilir)  
- Hata varsa `golden_image.bin` çalıştırılır  

---

## FreeRTOS / Bare-Metal Sistemlerde Kullanım

```c
if (check_flag_valid_app())
    jump_to_application();
else
    jump_to_golden_image();
```

- Temel seviye uygulamalarda basit **flag + CRC kontrolü** yeterlidir  

---

## İpuçları

- **Golden Image asla üzerine yazılmaz**, sadece güncel image güncellenir  
- Bootloader, golden ve güncel image arasında **karar verecek mantık** içermelidir  
- JTAG ile brick olan sistem kurtarılabilir, ancak **saha ortamında bu mümkün olmayabilir**  
- Golden Image, **watchdog**, **FDIR**, **safe mode** mekanizmalarıyla birlikte çalışmalıdır  


# Bit Scrambling & Data Encoding Yaklaşımları

## Bit Scrambling Nedir?

- Verinin **şifrelenmeden**, fiziksel hatalara karşı **yeniden düzenlenmesidir**  
- Hedef: Uzun süreli 0/1 dizilerini kırarak **sinyal bütünlüğünü korumak**  
- Genellikle **seri iletişim** protokollerinde (UART, SPI, PCIe, SATA) kullanılır  

---

## Neden Kullanılır?

- Uzun süreli aynı bit dizileri (ör. sürekli 0) → PLL senkron kaybına neden olabilir  
- **DC dengesizlik** → alıcıda referans kaymasına yol açar  
- **EMI (Elektromanyetik Girişim)** artar  
- CRC gibi hata yakalama mekanizmalarının etkinliği artar (daha tutarlı veri)  

---

## Scrambling vs Encryption

| Özellik        | Scrambling                         | Encryption                        |
|----------------|-------------------------------------|------------------------------------|
| Amaç           | Sinyal düzeyinde karışıklık        | Veri güvenliği ve gizlilik         |
| Geri çözme     | Donanımsal olarak mümkündür        | Anahtar ile çözülür                |
| Performans     | ✅ Çok hızlı (donanım tabanlı)     | ❌ Daha yavaş (karmaşık algoritmalar) |
| Kullanım Alanı | PHY seviyesi, veri yolu            | Uygulama katmanı                   |

---

## Yaygın Scrambling / Encoding Yöntemleri

### 1. LFSR (Linear Feedback Shift Register)

- En yaygın donanımsal scrambling tekniğidir  
- Her yeni bit, önceki bazı bitlerin XOR’lanması ile oluşur  
- Tekrarsız, dengeli ve geçiş yoğunluğu yüksek veri üretir  

```verilog
reg [6:0] lfsr = 7'b1001101;
always @(posedge clk) begin
    lfsr <= {lfsr[5:0], lfsr[6] ^ lfsr[5]};
end
```

---

### 2. 8b/10b Encoding

- 8-bit veriyi 10-bit kodla gönderir  
- **DC denge** ve **maksimum geçiş sıklığı** sağlanır  
- Kullanım: **PCIe**, **USB**, **SATA**, **DisplayPort**

| 8-bit Giriş | 10-bit Kod       |
|-------------|------------------|
| `0xC5`      | `1101010100`     |

---

### 3. Manchester Encoding

- Her bit 2 sinyale dönüştürülür:  
  - `1` → `10`, `0` → `01`  
- Saat sinyali verinin içine gömülür  
- Kullanım: **Ethernet PHY**, RFID, IR iletişim  

---

## Hata Yakalama & Maskeleme

- **Scrambling hata düzeltmez**, sadece iletim güvenliğini artırır  
- Scrambling sonrası mutlaka **CRC, ECC** gibi doğrulama yöntemleri uygulanmalıdır  

---

## FPGA Uygulaması

- LFSR tabanlı **scrambler** modülü basitçe implemente edilebilir  
- Scrambler ve **descrambler simetrik** çalışır  

```verilog
assign data_out = data_in ^ lfsr_value;
```

---

## İpuçları

- Bit scrambling **güvenlik** sağlamaz → şifreleme değildir  
- Donanım sistemlerinde **LFSR tercih edilir**, düşük kaynak kullanır  
- Scrambling varsa, **descrambling** aynı algoritmayla yapılmalıdır  
- UART gibi düşük hızlı protokollerde gerekmez, ancak **yüksek hızlı veri yollarında zorunludur**  


# ECC (Error Correction Code) ile Bellek Güvenliği

## ECC Nedir?

- **Bellek üzerindeki bit hatalarını algılayan ve düzelten** sistemdir  
- Genellikle **RAM, cache, register file** gibi kritik bellek bloklarında kullanılır  
- Hata kaynakları:
  - **SEU (Single Event Upset)** → kozmik ışın, EMI  
  - **Bit-flip** → sıcaklık, voltaj dalgalanması, zamanla yıpranma  

---

## ECC Türleri

| Tür         | Açıklama                                               |
|-------------|--------------------------------------------------------|
| SEC         | Single Error Correction                                |
| SEC-DED     | Single Error Correction, Double Error Detection        |
| Chipkill    | Çoklu bellek çipi üzerinden hata düzeltme              |
| Reed-Solomon| Flash belleklerde yaygın, çok bitli hata düzeltme için |

---

## ECC Nasıl Çalışır?

- **Yazma sırasında**: Veriyle birlikte **kontrol bitleri** hesaplanır  
- **Okuma sırasında**: Kontrol bitleri ile karşılaştırma yapılır  
- Hata varsa:
  - **1 bit**: Düzeltilebilir  
  - **2+ bit**: Tespit edilir ama düzeltilemez → uyarı verilir  

### Örnek (SEC-DED)

| Veri (8 bit) | ECC (5 bit) |
|--------------|-------------|
| `11010011`   | `10101`     |

---

## ECC Bellek Donanımı

- **ECC destekli RAM** gerekir (ek parity pinleri içerir)  
- **ECC destekli işlemci/mikrodenetleyici** (örn. ARM Cortex-R5, bazı R7)  
- **ECC destekli FPGA bellek blokları** (BRAM, URAM)  

### Örnek (Verilog - BRAM ECC)

```verilog
// Vivado IP: Block Memory Generator
// ECC destek opsiyonu: "Enable Error Correction"
parameter C_USE_ECC = 1;
```

---

## ECC ve Zynq

### Zynq-7000

- **PL (Programmable Logic)** kısmındaki BRAM için ECC vardır  
- **PS (Processing System)** kısmındaki OCM ECC desteklemez  

### Zynq UltraScale+

- **DDR** ECC destekler  
- **OCM** ECC destekler (kontrol register’ları üzerinden yapılandırılır)  

---

## Yazılımsal ECC Kullanımı

- Bazı sistemlerde ECC **yazılımla yapılabilir**, fakat yavaştır  
- Genelde **donanım tabanlı ECC** tercih edilir  

---

## ECC ile İlgili Terminoloji

| Terim      | Anlamı                                            |
|------------|---------------------------------------------------|
| Hard error | Kalıcı donanım hatası                             |
| Soft error | Geçici SEU / EMI kaynaklı hata                    |
| Scrubbing  | Arka planda ECC ile sürekli veri doğrulama işlemi |
| Syndrome   | ECC sonucu hesaplanan hata durumu / kodu         |

---

## İpuçları

- ECC, **yüksek güvenilirlik** gerektiren sistemlerde tercih edilir  
- ECC aktifken **performans kaybı** olabilir → sistem gereksinimlerine göre değerlendirilmelidir  
- **ECC logları izlenmeli**, FDIR ve watchdog ile entegre edilmelidir  
- **ECC tek başına yeterli değildir** → yedeklilik (redundancy) ve izleme mekanizmalarıyla birlikte kullanılmalıdır  


# System Hardening: Fiziksel ve Elektronik Sertlik

## Tanım

- **System Hardening**, bir gömülü sistemin çevresel, fiziksel ve elektromanyetik (EMI/EMC) gibi zorlu koşullara dayanıklı hale getirilmesidir.
- Sadece yazılım değil, **donanım altyapısı da güçlendirilir**.
- Amaç: Kararlılık, güvenlik, uzun süreli çalışma.

---

## Neden Gerekli?

- Savunma, uzay, medikal sistemler gibi **kritik alanlarda**:
  - Radyasyon
  - Sıcaklık/soğuk
  - Titreşim
  - Elektromanyetik girişim
  - Fiziksel sabotaj

---

## Sertlik Türleri

| Sertlik Tipi             | Açıklama                                              |
|--------------------------|--------------------------------------------------------|
| Fiziksel Hardening       | PCB kaplama, konnektör koruma, mekanik sağlamlık      |
| Termal Hardening         | Soğutma, ısıya dayanıklı malzeme, fan, heatsink       |
| Radyasyon Sertliği       | SEU dayanımlı FPGA (Radiation-Hardened, RHBD)         |
| EMI/EMC Hardening        | RF ekranlama, filtreleme, metal kasa, ferrit boncuk    |
| Elektriksel Sertlik      | ESD, overvoltage, brown-out koruması                  |

---

## Donanımsal Teknikler

- **Conformal Coating**: PCB’yi koruyucu şeffaf katmanla kaplamak
- **Ground Plane**: EMI’yi bastırmak için çok katmanlı PCB’lerde zemin düzlemi
- **TVS Diyotları**: Gerilim ani sıçramalarına karşı koruma (ESD)
- **Shielded Cable**: RF girişimini önlemek için metal kaplı kablolar
- **Power Filter**: Güç hattındaki bozulmaları temizlemek için LC filtre

---

## Radyasyon Sert FPGA Örnekleri

| Marka   | Model                      | Özellik                     |
|---------|----------------------------|-----------------------------|
| Xilinx  | Virtex-5QV                 | Radiation-tolerant (military) |
| Microsemi | RTG4, RTAX                | Rad-Hard FPGA               |

---

## Sistemik Önlemler

- Watchdog + FDIR + ECC üçlüsü entegre çalışmalı
- Sistem hatasında safe mode veya reset mekanizması aktif olmalı
- Harici voltaj koruma entegreleri (eFuse, overvoltage protection)

---

## Yazılımda Sertlik Destekleri

- Stack overflow kontrolü
- Heap sınır kontrolü
- Critical section yönetimi
- Bellek sızıntısı denetimi (valgrind, unit test)

---

## İpuçları

- Sistem sertliği sadece donanımla değil, **bütünleşik sistem mühendisliğiyle** sağlanır  
- Fiziksel hardening, yazılım sertliğiyle birlikte düşünülmelidir  
- Geliştirilen ürün için kullanılacak ortam (hava, deniz, askeri) **baştan tanımlanmalı**  
- Gereksiz koruma → maliyet ve güç tüketimini artırır → denge gerekir


# Yazılım Taraflı Hardness & Watchdog Entegrasyonu

## Yazılımsal Hardness Nedir?

- Sistem yazılımının **beklenmedik durumlara karşı dayanıklı** hale getirilmesidir  
- Donanım tabanlı güvenliği **tamamlayan bir yazılım savunma katmanıdır**  
- Hedef: 
  - Görev kilitlenmesi  
  - Sonsuz döngü  
  - Stack taşması  
  - Hatalı pointer kullanımı gibi sorunları **erken yakalamak ve müdahale etmektir**  

---

## Temel Teknikler

### 1. Watchdog Timer (WDT)

- Sistem periyodik olarak **watchdog beslemesi yapar**  
- Eğer görev düzgün çalışmazsa ve **beslenmezse → sistem otomatik reset alır**  

```c
// FreeRTOS'ta watchdog örneği (pseudo-code)
for (;;)
{
    refresh_watchdog(); // Her görev döngüsünde çağrılır
    vTaskDelay(pdMS_TO_TICKS(100));
}
```

---

### 2. Stack Overflow Protection

- FreeRTOS’ta `configCHECK_FOR_STACK_OVERFLOW` makrosu ile etkinleştirilir  
- Her görev için ayrılan stack alanı izlenir  
- Taşma varsa özel **hook fonksiyonu** devreye girer  

```c
void vApplicationStackOverflowHook(TaskHandle_t xTask, char *pcTaskName)
{
    // Logla, LED yak, sistem resetle vs.
}
```

---

### 3. Memory Corruption Kontrolü

- `malloc` / `free` işlemleri **izlenmeli**  
- Valgrind / AddressSanitizer gibi araçlarla test yapılmalı  
- **Sentinel değeri (magic number)** kullanılarak veri alanları korunmalı  

---

### 4. Assert ve Hata Yönetimi

- `configASSERT` ile runtime kontroller yapılır  
- Null pointer, beklenmedik durumlar erken fark edilir  

```c
configASSERT(xSemaphore != NULL); // Null kontrolü
```

- Assertion hatalarında sistem **güvenli moda** alınabilir  

---

### 5. Fail-Safe Mode

- Kritik hata algılandığında:
  - Geri bildirim sağlanır (UART log, LED blink)  
  - Sistem minimum işlevle devam eder  
  - Watchdog resetine **bilinçli izin verilir**  

---

## Watchdog Entegrasyonu

| Seviye       | İzlenen Durumlar                         |
|--------------|-------------------------------------------|
| Görev        | Tetiklenmeyen görev, kilitlenme           |
| ISR          | ISR süresinin limit dışı olması           |
| Zamanlayıcı  | Cron task, zaman aşımı, timeout           |
| Donanım      | UART, ADC, GPIO tepkisizliği              |

---

### Donanım Bazlı Watchdog (Zynq Örneği)

- Zynq-7000 üzerinde **System Watchdog Timer (SWDT)** mevcuttur  
- PS (Processing System) tarafında konfigüre edilir  
- PL (Programmable Logic) tarafı da tetikleyici olabilir  

```c
XWdtPs_SetControlValue(&WdtInstance, XWDTPS_CRR, 0x76); // Watchdog beslemesi
```

---

## İpuçları

- Watchdog süresi çok kısa ayarlanırsa → **yanlış resetler** oluşabilir  
- Stack/heap taşması gibi yazılımsal hatalar **donanımı etkilemeden önce** yakalanmalıdır  
- **Watchdog**, FDIR sisteminin **çekirdeğidir**  
- Hatalı bir watchdog tasarımı, sistemi **gereksiz yere resetleyebilir** → test kritik önem taşır  


# Power-on Reset, Brown-out Detection, Startup Logic

## Power-on Reset (POR)

- Sistem ilk kez enerji verildiğinde devreye giren **otomatik sıfırlama (reset) mekanizmasıdır**  
- Tüm bileşenlerin **deterministik ve kararlı bir başlangıç** yapmasını sağlar  
- Mikrodenetleyici ve FPGA’lerde genellikle **donanım tabanlı olarak entegredir**  

---

## POR Devresi Nasıl Çalışır?

- **Vcc voltajı** yeterli seviyeye ulaşana kadar sistemi reset durumunda tutar  
- Voltaj **stabil hale gelince** → reset bırakılır ve sistem başlar  
- Genellikle bir **RC zamanlayıcı** veya dahili analog devre ile tetiklenir  

```
|----|                POR aktif (reset)
     |----------------> Normal çalışma
   0V       3.3V (Vcc)
```

---

## Brown-out Detection (BOD)

- Sistem çalışırken **voltaj belirli bir eşik altına düşerse** bunu algılar  
- **Bellek bozulması** veya **boot hatalarını** önlemek için reset tetikleyebilir  
- Bazı sistemlerde sadece **flag üretir**, bazıları otomatik **reset** atar  

---

### BOD Kullanım Senaryosu

- Güç kaynağına ani yük → voltaj düşüşü  
- Bu sırada **flash yazımı** ya da **boot** oluyorsa sistem zarar görebilir  
- BOD bunu algılayıp reset atarak sistemi korur  

---

## Startup Logic (Başlangıç Mantığı)

- Sistem açıldığında bileşenlerin hangi sırayla devreye gireceğini belirler  
- Tipik sıralama:

  1. **Power-on Reset**
  2. **Clock stabilizasyonu**
  3. **Bootloader / FSBL yüklenmesi**
  4. **Firmware doğrulaması**
  5. **Ana uygulamanın başlatılması**

```c
if (check_power_good() && check_clk_lock()) {
    boot_main_firmware();
} else {
    enter_safe_mode();
}
```

---

## Zynq Sistemlerinde Uygulama

- PS (Processing System) açıldığında otomatik olarak **POR + BOD logic** çalışır  
- BootROM → eFuse → FSBL → Uygulama sırası uygulanır  
- Voltaj izleme ve müdahale, **PMU (Power Management Unit)** üzerinden yapılabilir  

---

## İpuçları

- **POR süresi** doğru ayarlanmazsa sistem **sonsuz reset döngüsüne** girebilir  
- **BOD eşikleri** donanımda ayarlanabilir olmalı ve test edilmelidir  
- **Startup logic**, FDIR ve watchdog ile **entegre bir yapı** olarak düşünülmelidir  
- BOD olayları yazılımda **loglanmalı** ve gerekirse **uygulama davranışı değiştirilmelidir**  


# Bootloader Temelleri ve Çoklu Firmware Yönetimi

## Bootloader Nedir?

- Sistem açıldığında ilk çalışan yazılımdır  
- Görevleri:
  - Donanım hazırlığı (clock, bellek, çevre birimleri)  
  - Uygun firmware'i seçmek ve yüklemek  
  - Gerekirse **güncelleme kontrolü** yapmak  
- **Bootloader → firmware ayrımı**, sistemin kararlılığını ve güvenliğini artırır  

---

## Bootloader Türleri

| Tür                | Açıklama                                                |
|--------------------|----------------------------------------------------------|
| FSBL               | Donanım üreticisinin sunduğu temel ilk açılış kodu       |
| Custom Bootloader  | Projeye özel yazılmış yükleme ve kontrol mantığı         |
| OTA Bootloader     | Uzaktan güncelleme (over-the-air) destekleyen yapılar    |

---

## Bootloader Adımları (Genel)

1. Power-on Reset  
2. Donanım başlatma (PLL, DRAM, IO ayarları)  
3. Flash / SD / eMMC üzerinden firmware kontrolü  
4. **Hash / CRC** kontrolü  
5. Firmware’in RAM’e kopyalanması  
6. **Ana uygulamaya sıçrama (jump to application)**  

---

## Çoklu Firmware Yapısı

- Aşağıdaki gibi birden fazla imaj tutulur:
  - `firmware_current.bin`
  - `firmware_backup.bin`
  - `golden_image.bin`  
- Seçim kriterleri:
  - **Versiyon kontrolü**
  - **Hata sayacı** (watchdog reset sonrası)
  - **CRC başarısızlığı**
  - Önceki boot başarısızlığı

```c
if (is_firmware_valid(current)) {
    jump_to(current);
} else if (is_firmware_valid(backup)) {
    jump_to(backup);
} else {
    jump_to(golden);
}
```

---

## FreeRTOS ile Bootloader

- Bootloader, FreeRTOS uygulamasından **ayrı bir bare-metal yazılım** olabilir  
- .bin dosyası RAM’e kopyalanır, ardından **function pointer** ile çalıştırılır  

```c
typedef void (*app_entry_t)(void);
app_entry_t app_entry = (app_entry_t) 0x8000000;
app_entry(); // Uygulama başlatılır
```

---

## Boot Flags & Versiyonlama

- EEPROM / FRAM içinde küçük bir alan ayrılır:  
  - Son çalıştırılan firmware versiyonu  
  - Boot durumu (başarılı / başarısız)  
  - Güncelleme zamanı  
  - Safe mode sayacı gibi durum bilgileri  

---

## Bootloader ve Güvenlik

- **Firmware imza doğrulama** (RSA, SHA256 gibi)  
- **CRC16/32** ile bütünlük kontrolü  
- Flash üzerindeki alanların sınırlandırılması  
- Read-only bölge tanımlanması (yazma koruması)  

---

## İpuçları

- Bootloader, sistemin **en güvenilir yazılım katmanıdır** → test kritik önemdedir  
- **Rollback desteği** olmadan güncelleme mekanizması eksiktir  
- Watchdog ile **boot döngüsü tespiti (loop detection)** entegre edilmelidir  
- Geliştirme sırasında boot süresi ve firmware geçişleri **loglanmalı ve analiz edilmelidir**  


# Memory Map, Flash Partitions, Linker Script Okuma

## Memory Map (Bellek Haritası)

- Mikrodenetleyici veya işlemcinin **adresleme düzenini** tanımlar  
- Kod, veri ve çevre birimlerinin **bellek üzerindeki yerlerini** belirtir  

### Tipik Bellek Bölmeleri

| Bölge        | Açıklama                            |
|--------------|-------------------------------------|
| ROM / Flash  | Kod ve sabit veriler                |
| RAM          | Değişkenler, stack, heap alanı      |
| Periferikler | UART, GPIO, SPI gibi donanımlar     |
| Özel Alanlar | Bootloader, EEPROM, log alanları    |

```text
0x0000_0000 → Boot ROM  
0x0800_0000 → Flash Başlangıcı  
0x2000_0000 → RAM Başlangıcı  
0x4000_0000 → Periferik Kayıtlar  
```

---

## Flash Partitions (Bölümlendirme)

- Flash belleğin farklı bölgelere **amaç odaklı ayrılmasıdır**  
- Amaç: Firmware, yedek, ayarlar ve loglar gibi bölümleri **birbirinden izole etmek**

### Örnek Bölümleme

| Bölüm        | Adres        | Açıklama                    |
|--------------|--------------|-----------------------------|
| Bootloader   | 0x08000000   | Sabit, güvenli başlatıcı    |
| Firmware A   | 0x08010000   | Güncel yazılım              |
| Firmware B   | 0x08030000   | Geri dönüş (fallback)       |
| Config Data  | 0x08070000   | Kalıcı ayar bilgileri       |
| User Logs    | 0x08078000   | Hata / debug logları        |

---

## Linker Script Nedir?

- Derleyicinin hangi kodun **hangi bellek bölgesine** yerleştirileceğini belirleyen dosyadır  
- `.ld` veya `.lds` uzantılıdır (GNU toolchain ile birlikte)  
- **MEMORY** ve **SECTIONS** bloklarından oluşur  

### Örnek Linker Script

```ld
MEMORY
{
  FLASH (rx)  : ORIGIN = 0x08000000, LENGTH = 512K
  RAM   (rwx) : ORIGIN = 0x20000000, LENGTH = 128K
}

SECTIONS
{
  .text : { *(.text*) } > FLASH
  .data : { *(.data*) } > RAM
  .bss  : { *(.bss*)  } > RAM
}
```

---

## Linker Script ile Neler Kontrol Edilir?

- Kod (.text), veri (.data), boş alan (.bss) konumları  
- Bootloader ve uygulama farklı alanlara yerleştirilebilir  
- Belirli değişkenlerin sabit adrese konulması (`__attribute__((section))`)  
- Flash’tan RAM’e kopyalanacak veri adresleri  

```ld
__firmware_start__ = 0x08010000;
__firmware_end__   = 0x0802FFFF;
```

---

## FreeRTOS ile Linker Script

- Kernel + uygulama ayrı bölgelere yerleştirilebilir  
- ISR’ler ve **interrupt vector table**, RAM başına yerleştirilebilir  
- **Heap ve stack sınırları**, linker script üzerinden kontrol edilebilir  

---

## İpuçları

- Linker script hatası → firmware çalışmaz, debug edilmesi zor olur  
- Güvenlik için bootloader ve uygulama **ayrı bölgelerde** olmalı  
- Güncelleme sistemlerinde partition'ların üzerine yazma **engellenmelidir**  
- Bellek yapısı, watchdog, CRC, bootloader ile **uyumlu ve entegre tasarlanmalıdır**  
