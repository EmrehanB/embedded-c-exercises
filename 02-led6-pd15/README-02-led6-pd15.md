# 02 — LED on (PD15 / LD6)

Turning on the on-board **blue LED (LD6, PD15)** on STM32F407G-DISC1, at register level, without HAL.

This exercise was completed independently — finding the pin, deriving every register address, and verifying each step in the debugger.

## Method

1. **Find the pin** — from the board schematic, LD6 is wired to **PD15**.
2. **Base address** — GPIOD base from the reference manual: `0x40020C00`.
3. **Enable the clock** — GPIOD is on the **AHB1** bus, so set bit 3 (GPIODEN) in `RCC_AHB1ENR`.
4. **Configure mode** — in `GPIOx_MODER`, each pin uses 2 bits (`2y`, `2y+1`). PD15 -> bits 30-31, set to `01` (output). Clear-then-set to preserve other bits.
5. **Drive the pin** — set bit 15 in `GPIOx_ODR` to pull PD15 high (3.3 V) -> LED on.

## Register addresses

```
GPIOD base   : 0x40020C00
RCC_AHB1ENR  : 0x40023830   (RCC base 0x40023800 + offset 0x30)
GPIOx_MODER  : 0x40020C00
GPIOx_ODR    : 0x40020C14
```

## Debug — register tracing

Each step verified in the STM32CubeIDE debugger by single-stepping and watching registers change live:
- `RCC_AHB1ENR |= 0x08` -> bit 3 becomes `1` (GPIOD clocked)
- write to `MODER` -> bits 30-31 become `01` (PD15 = output)
- write to `ODR` -> bit 15 becomes `1`, on-board LED turns on

Confirms the software model matches the hardware state at every stage.

---

## Türkçe

STM32F407G-DISC1 üzerindeki **mavi LED'i (LD6, PD15)** HAL kullanmadan, register seviyesinde yakma alıştırması. Tamamen bağımsız yapıldı — pini bulma, tüm register adreslerini çıkarma ve her adımı debugger'da doğrulama dahil.

**Adımlar:** Şematikten pini bul (PD15) -> GPIOD base adresi (`0x40020C00`) -> AHB1 bus'ı için `RCC_AHB1ENR` bit 3 ile clock ver -> `GPIOx_MODER` bit 30-31'i `01` (çıkış) yap -> `GPIOx_ODR` bit 15'i `1` yaparak LED'i yak. Her adım debugger'da register değerleri izlenerek doğrulandı.
