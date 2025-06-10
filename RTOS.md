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
