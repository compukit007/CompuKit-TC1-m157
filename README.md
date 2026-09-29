# m-firmware 1.57m for the LCR-TC1 (LGT8F328P) – test versions

Two ports of the newest **m-firmware v1.57m by Markus Reschke** to the
**LCR-TC1 / "Multi-function Tester TC1"** (blue board) with its original
**LGT8F328P** microcontroller and 1.8" ST7735 color display (128×160).

No hardware changes are needed: no jumpers, no extra capacitors, no ATmega.
The firmware runs on the stock LGT8F328P. If your LGT8F328P is dead, you can
simply solder in a new LGT8F328P (1:1) and flash one of these files.

> ⚠️ These are **test versions**. They were tested on a real TC1, but not every
> language file was checked on the display (see *Test status*).

---

## The two folders

| Folder | What it is |
|---|---|
| `kaal (originele 1.57m)` | **Bare port.** The original m-firmware 1.57m with the original user interface. Only changed so that it works on the TC1 board and the LGT8F328P. |
| `v0.2 met TC1-schermen` | **TC1 interface.** The same 1.57m port, plus the original-TC1 style interface of my v19.x firmware (based on 1.42m). |

Both folders contain four hex files (Dutch, English, Russian, Russian with
the alternative font) and a zip with the complete source code.

Next to this README:
- `m-firmware_1.57m_TC1_LGT8F328P_broncode.zip` – the source code of both
  versions in one zip (plus this README and the license)
- `LICENSE` – EUPL v1.2 license text

---

## Changes in both versions (needed for the TC1 / LGT8F328P)

**TC1 board (pins and hardware)**
- Display ST7735: RES = PD0, D/C = PD1, SCL = PD2, SDA = PD4, no /CS,
  horizontal flip, offset x 2 / y 1
- IR receiver on PD3 (`HW_IR_RECEIVER`)
- Zener check via the K/A holes: voltage divider 53.3 kΩ / 10 kΩ on PC3,
  28 V boost converter runs all the time (`HW_ZENER`, `ZENER_UNSWITCHED`)
- Battery: 3.7 V Li-ion measured directly on PC5 (`BAT_DIRECT`,
  weak 3.5 V, low 3.2 V), no power-off when powered by the programmer
  (`BAT_EXT_UNMONITORED`)
- Adjustment values are stored in flash (`DATA_FLASH`)

**LGT8F328P** (partly based on the 1.42m LGT8F328P port by Arnaud Durand)
- Switch to the external 16 MHz crystal at startup (PMCR/CLKPR)
- 1 KB EEPROM emulation in the last 2 KB of flash (ECCR), so the program
  must stay below **30 720 bytes**
- 12 bit ADC (0–4095) instead of 10 bit
- Internal reference is 1.024 V (VCAL1), it can't be measured by the ADC
- `wait.S`: extra nops, because `rcall`/`ret` are faster on the LGT8F328P
  (without this all µs delays are too short, e.g. IR decoding fails)
- Cold start: longer LCD reset and busy-wait delays instead of Timer2
- Watchdog is disabled early in `.init3`
- Sleep mode (`SAVE_POWER`) is off, it isn't used on the LGT8F328P

**Languages**
- The m-firmware 1.57m has no Dutch translation anymore, a new
  `var_dutch.h` was made
- Language and font are selected with `make` (see *Building*)

---

## Bare version (`kaal (originele 1.57m)`)

Everything else is the original m-firmware 1.57m: original screens,
original menu and original key handling.

To fit into 30 720 bytes these options are **disabled**:
- UJT detection (`SW_UJT`)
- Schottky transistor detection (`SW_SCHOTTKY_BJT`)
- Base-emitter capacitance of BJTs (`SW_C_BE`)

**Keys (original m-firmware)**
- Power on with a short press: continuous mode (measures again every 3 s,
  switches off after 5 times "no component")
- Power on and hold the key a bit longer (> 0.3 s): auto-hold mode (result
  stays until you press the key)
- Short press: next measurement
- 2× short: main menu (short = next item, long = select)
- Long press: power off

**Menu:** PWM, Square Wave, Zener, IR detector, Opto Coupler, Test
(self-test), Adjustment, Save, Load, Show Values, Exit.

---

## TC1 interface version (`v0.2 met TC1-schermen`)

The 1.57m port with the interface of my v19.x firmware:
- Yellow "M-Tester" title bar and large 10×16 font (8×16 Cyrillic for Russian)
- Graphic result screens for diode, capacitor (with ESR), resistor, inductor
  and two resistors, with colored probe blocks (1 = red, 2 = blue,
  3 = yellow)
- 3-pin semiconductors with symbol and a pin legend like `[1]=C [2]=B [3]=E`
- "Testing" screen with ZIF socket drawing and battery voltage + charge in %
- Startup screen "Compukit v0.2 / m-firmware 1.57" (10 s, key skips)
- IR decoder always on: a NEC or Samsung frame on the result screen shows
  the IR decoder page (address/command + waveforms), IR lamp in the title bar
- Large menu: Calibration, Zener test, ESR meter (with safety warning and
  discharge check), Opto coupler test, Power off, Back
- A new test starts automatically after leaving the menu

**Keys**
- Short press: new measurement
- Long press (about 0.5 s) on a result: menu (short = next, long = select)
- Tools (Zener, ESR, opto coupler): 1× short = measure/start, 2× short = back
- Automatic power off after about 3 minutes

Left out to fit into 30 720 bytes: PUT detection and the PUT/UJT symbols.
The Dutch version uses 30 716 bytes, so there is almost no space left.

---

## Files

| File | Language | Font | Size |
|---|---|---|---|
| `kaal_m-firmware_1.57m_nl.hex` | Dutch | 10×16 | 30 456 |
| `kaal_m-firmware_1.57m_en.hex` | English | 10×16 | 30 434 |
| `kaal_m-firmware_1.57m_ru.hex` | Russian | 8×16 Windows-1251 | 29 476 |
| `kaal_m-firmware_1.57m_ru_alt.hex` | Russian | 8×16alt Windows-1251 | 29 476 |
| `TC1_LGT8F328P_AUTO_MEASURE_v0.2_nl.hex` | Dutch | 10×16 | 30 716 |
| `TC1_LGT8F328P_AUTO_MEASURE_v0.2_en.hex` | English | 10×16 | 30 710 |
| `TC1_LGT8F328P_AUTO_MEASURE_v0.2_ru.hex` | Russian | 8×16 Windows-1251 | 29 722 |
| `TC1_LGT8F328P_AUTO_MEASURE_v0.2_ru_alt.hex` | Russian | 8×16alt Windows-1251 | 29 722 |

Sizes in bytes, limit 30 720 bytes. All files are flash-only.

---

## Flashing

Tested with an **Arduino Nano running the LGTISP sketch** (stk500v1
protocol) and avrdude 8.3. A normal "ArduinoISP" sketch can't program the
LGT8F328P.

```bash
avrdude -p m328p -c stk500v1 -P COM3 -b 115200 -U flash:w:kaal_m-firmware_1.57m_en.hex:i
```

Change `COM3` to the port of your programmer.

**After flashing, calibrate once:** flashing also erases the emulated EEPROM.
At the first start a checksum message is shown and default values are used.
- Bare version: 2× short → *Adjustment*
- TC1 interface version: long press → *Kalibratie / Calibration*

Then follow the instructions on the display (connect probes 1, 2 and 3,
then remove the connection).

> While the tester is connected to the programmer, the test button doesn't
> work. Disconnect it and run it on the battery.

---

## Building

Requirements: `avr-gcc` (tested with 7.3.0 from the Arduino IDE) and GNU make.
Unzip the source code, then:

```bash
make TC1_LANG=nl                   # nl, en or ru
make TC1_LANG=ru TC1_FONT=alt      # Russian with the 8x16alt font
```

Delete the `*.o` files and the `dep` folder when you switch languages.
Both versions are built with LTO (`-flto -mrelax`), otherwise they don't fit.

---

## Test status

| | Tested on a real TC1 |
|---|---|
| v0.2 Dutch | yes: components, capacitor + ESR, menu, IR decoder |
| Bare version English | yes: measuring, menu, IR detector (before the `wait.S` fix) |
| Other languages, bare version after the `wait.S` fix | only compiled, not tested on the display |

---

## Credits and license

- **m-firmware** (Component Tester) – © Markus Reschke,
  based on the work of Markus Frejek and Karl-Heinz Kübbeler
- **LGT8F328P port of m-firmware 1.42m** – © 2021 Arnaud Durand
  (clock start, EEPROM emulation, `wait.S` timing)
- **Russian translation** – indman@EEVblog
- **TC1 / LGT8F328P port of 1.57m, Dutch translation and TC1 interface** – Compukit

**This is a modified version (Derivative Work) of the m-firmware v1.57m.
Modified by Compukit, September 2026 (TC1 / LGT8F328P port, test versions).**
All original copyright notices in the source files are kept intact.

The m-firmware 1.57m is licensed under the **EUPL v1.2**
(European Union Public Licence), so these versions are too. See `LICENSE`;
the license text (`EUPL-v1.2.txt`) is also included in the source zips.

---

## Русский (кратко)

Две тестовые версии новой **m-firmware 1.57m** для тестера **LCR-TC1**
(синяя плата) на штатном **LGT8F328P**, без переделок платы:

- **`kaal (originele 1.57m)`** – «чистая» 1.57m: оригинальный интерфейс,
  изменены только пины и то, что нужно для LGT8F328P (кварц 16 МГц,
  12-битный АЦП, опорное 1.024 В, хранение во флеше, тайминги `wait.S`).
  Чтобы влезло во флеш, отключены UJT, Schottky-транзисторы и C_BE.
  Меню – двойное нажатие кнопки.
- **`v0.2 met TC1-schermen`** – та же 1.57m с интерфейсом в стиле
  оригинального TC1 (как в моей v19.15): крупный шрифт, ИК-декодер,
  стабилитроны, ESR, оптопары, заряд батареи. Меню – долгое нажатие.

Языки: nl, en, ru и ru_alt (шрифт 8x16alt). Прошивка через Arduino Nano с
LGTISP, после прошивки **один раз выполнить калибровку**.

---

## Nederlands (kort)

Twee testversies van de nieuwste **m-firmware 1.57m** voor de **LCR-TC1**
(blauwe print) met de originele **LGT8F328P**, zonder de print om te bouwen:

- **`kaal (originele 1.57m)`** – de originele 1.57m met de originele
  schermen en het originele menu. Alleen aangepast zodat hij op de TC1-print
  en de LGT8F328P werkt (pinnen, 16 MHz kristal, 12-bit ADC, 1,024 V
  referentie, opslag in flash, timing in `wait.S`). Om te passen staan UJT,
  Schottky-transistors en C_BE uit. Menu: 2× kort drukken.
- **`v0.2 met TC1-schermen`** – dezelfde 1.57m met de TC1-interface van
  mijn eerste versie (v19.x): grote letters, grafische schermen, IR-decoder,
  zenertest, ESR-meting, optocouplertest en accu-percentage.
  Menu: lang drukken.

Talen: nl, en, ru en ru_alt (8x16alt-lettertype). Flashen met een Arduino
Nano met de **LGTISP**-sketch, daarna **één keer kalibreren**.
