# 🎮 PS2 Wireless Controller Driver for Robots (STM32F103)

![MCU](https://img.shields.io/badge/MCU-STM32F103C8T6-03234B?logo=stmicroelectronics&logoColor=white)
![IDE](https://img.shields.io/badge/IDE-STM32CubeIDE-03234B)
![Language](https://img.shields.io/badge/Language-C-555555?logo=c)

A lightweight driver that reads a **PS2 wireless controller** from an **STM32F103C8T6** using a software (bit-banged) SPI. It is a ready-to-use remote-control input for robot competitions and mobile robots.

---

## ✨ Features

- **Software SPI, LSB-first:** works on any GPIO pins, with microsecond timing from TIM1.
- **Analog mode:** automatically enters config mode, enables analog mode and exits during `PS2_Init()`.
- **All 16 buttons + 2 joysticks:** decoded into a simple `PS2Buttons` struct.
- **Validity check:** ignores frames whose mode byte is not `0x41` (digital) or `0x73` (analog).

## 🔧 Wiring

| PS2 receiver | STM32F103 pin | Label |
| :-- | :-- | :-- |
| ATT (CS) | PA4 | `SS` |
| CLK | PA5 | `SCK` |
| DAT (data out) | PA6 | `MISO` |
| CMD (command in) | PA7 | `MOSI` |
| VCC / GND | 3.3 V / GND | – |

> 💡 The DAT line usually needs a pull-up resistor (1–10 kΩ) to 3.3 V.

## 🧩 API

```c
#include "PS2.h"

PS2Buttons PS2;

PS2_Init(&htim1, &PS2);    // TIM1 = 1 MHz counter used for delay_us()

while (1) {
    PS2_Update();          // poll the controller
    if (PS2.UP)    { /* move forward */ }
    if (PS2.CROSS) { /* stop */ }
    int16_t speed = 128 - PS2.LY;   // joystick: 0..255, centre ≈ 128
    HAL_Delay(2);
}
```

`PS2Buttons` fields: `SELECT, L3, R3, START, UP, RIGHT, DOWN, LEFT, L2, R2, L1, R1, TRIANGLE, CIRCLE, CROSS, SQUARE` (1 = pressed) and `LX, LY, RX, RY` (0–255).

## 📂 Project structure

```text
PS2/
├── Core/Inc/PS2.h      # driver API
├── Core/Src/PS2.c      # software SPI + PS2 protocol
├── Core/Src/main.c     # demo: UP / DOWN toggles the PC13 LED
└── PS2.ioc             # CubeMX configuration
```

## 🚀 Getting started

1. Open the `PS2` folder in **STM32CubeIDE** and build.
2. Flash the board, turn on the controller and press **UP** or **DOWN**: the PC13 LED toggles.
3. Copy `PS2.c` / `PS2.h` into your own project and call `PS2_Init()` + `PS2_Update()`.

---

<p align="center">Made by <a href="https://github.com/TuanLinh05">Vu Tuan Linh</a> · HCMUT</p>
