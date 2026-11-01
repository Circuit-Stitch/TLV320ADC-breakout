# Audio Oscillator Selection

This file tracks the 24.576 MHz oscillators considered for the audio reference clock. The clock drives MCLK on the TLV320ADC6140. Prices and stock were checked on 2026-11-01.

## Decision

| Board              | Part                              | Why                                                                                     |
|--------------------|-----------------------------------|-----------------------------------------------------------------------------------------|
| ADC breakout rev 1 | Abracon ASTX-H11-24.576MHZ-T      | Already in the pending JLCPCB order. JLCPCB stocks it. Rev 1 tests the audio path, not drift |
| Recorder Rev A     | Connor-Winfield TL602-024.576M    | ±0.14 ppm over temperature. Needed for timecode sync. Not yet in the Rev A design            |

### Breakout rev 1 assembly quotes

JLCPCB quoted assembly for 2 boards on 2026-11-01:

| Y1 part                       | JLCPCB part | Assembly cost, 2 boards | Result                                 |
|-------------------------------|-------------|-------------------------|----------------------------------------|
| ASTX-H11-24.576MHZ-T          | C1987701    | $58.48                  | Chosen                                 |
| SJK 3N24576G33YC              | C252340     | $47.75                  | Not used. Saving not worth re-ordering |
| ECS-TXO-3225MV-245.7-TR       | C7183330    | —                       | Out of stock at JLCPCB                 |

All three parts share the same pinout: pad 1 enable, 2 GND, 3 output, 4 VDD. The SJK footprint was checked against the Y1 pads in the JLCPCB placement preview. Pin 1 sits at the bottom right of the board view. An ECS or SJK can be hand-swapped onto a rev 1 board later.

The firmware should store a per-unit frequency correction. A one-time measurement against GPS or a frequency counter removes the initial tolerance. A yearly re-check removes aging.

## Requirements

The recorder is used with video and timecode sync. Jam-syncing timecode aligns only the start of a take. After that, the recorder's timecode runs from the audio clock. Clock error therefore becomes timecode drift over the length of a take.

One frame at 24 fps is 41.7 ms.

| Clock error | Drift per hour | Time to 1 frame of drift |
|-------------|----------------|--------------------------|
| ±25 ppm     | 90 ms          | ~28 min                  |
| ±20 ppm     | 72 ms          | ~35 min                  |
| ±5.6 ppm    | 20 ms          | ~2.1 hr                  |
| ±2.5 ppm    | 9 ms           | ~4.6 hr                  |
| ±1 ppm      | 3.6 ms         | ~11.6 hr                 |
| ±0.2 ppm    | 0.7 ms         | ~58 hr                   |

Jitter does not separate these parts. Every candidate is near 1 ps RMS or better. A 20 kHz full-scale tone with 1 ps of jitter has a jitter-limited SNR of 138 dB. The TLV320ADC6140 has about 106 dB of dynamic range. Its internal PLL also re-times MCLK and filters most reference jitter.

Close-in phase noise matters more for audio than the integrated jitter figure. Integrated jitter is measured from 12 kHz offset upward. Audio is affected by phase noise below 20 kHz offset.

Required:

- 24.576 MHz, CMOS output, 3.3 V supply.
- Total error small enough for timecode work. Target ±1 ppm or better after per-unit calibration.

Preferred:

- 3.2 × 2.5 mm, 4-pad, to fit the breakout's Y1 footprint.
- Stocked at Digi-Key or LCSC.

No stocked part met both the ±0.5 ppm target and the 3.2 × 2.5 mm package. Precision TCXOs at 24.576 MHz come in 5 × 3.2 mm or 7 × 5 mm 10-pad packages.

## Candidates

### Summary

| Part                                   | Type          | Package             | Temp. stability | Worst-case total, year 1 | Price at 1 | Stock                    | Status                        |
|----------------------------------------|---------------|---------------------|-----------------|--------------------------|------------|--------------------------|-------------------------------|
| ECS ECS-TXO-3225MV-245.7-TR            | TCXO          | 3.2 × 2.5 mm, 4-pad | ±2.5 ppm        | ±5.6 ppm                 | $2.38      | 2,725 Digi-Key           | Backup. Out of stock at JLCPCB |
| Connor-Winfield TL602-024.576M         | TCXO          | 7 × 5 mm, 10-pad    | ±0.14 ppm       | ±4.6 ppm total tolerance | $34.26     | 352 Digi-Key             | **Chosen, Recorder Rev A**    |
| Abracon ASTX-H11-24.576MHZ-T           | TCXO          | 3.2 × 2.5 mm, 4-pad | ±2.5 ppm        | Not checked              | $4.37      | 556 Digi-Key             | **Chosen, breakout rev 1**    |
| SJK 3N24576G33YC                       | Crystal XO    | 3.2 × 2.5 mm, 4-pad | ±20 ppm         | ±23 ppm                  | $0.66      | 895 LCSC                 | Rejected: drift               |
| TI CDC6CE024576ADLXR                   | BAW XO        | 2.0 × 1.6 mm, 4-pad | —               | ±25 ppm                  | $2.76      | 2,998 Digi-Key           | Rejected: drift, footprint    |
| Fox FT3HN, ±1 ppm option               | TCXO          | 3.2 × 2.5 mm, 4-pad | ±1 ppm          | ±4.6 ppm                 | —          | Not listed               | Rejected: no real gain        |
| CTS 536L24CX2GT5                       | TCXO          | 5 × 3.2 mm, 10-pad  | ±0.28 ppm       | ±4.6 ppm over 20 years   | —          | 0 Digi-Key               | Alternate for Rev A           |
| TXC 7N-24.576MBP-T                     | TCXO          | 10-pad              | ±0.14–0.28 ppm  | Not checked              | —          | 21-week lead time        | Alternate for Rev A           |
| SiTime SiT5156AI-FK-33E0-24.576000     | MEMS TCXO     | 5 × 3.2 mm, 10-pad  | ±0.5 ppm        | Not checked              | $65        | SiTime direct            | Rejected: cost                |

Worst-case totals add the datasheet's initial tolerance, temperature, supply, load, and first-year aging terms. TCXOs over a 10–35 °C shooting range usually stay well inside their full-range temperature limit. The datasheets do not give a curve to confirm that.

### ECS ECS-TXO-3225MV-245.7-TR

- Datasheet: `datasheets/ECS-TXO-3225MV.pdf`. Digi-Key: 50-ECS-TXO-3225MV-245.7-TRCT-ND.
- "245.7" in the part number means 24.576 MHz.
- Error terms: ±1.5 ppm initial at 25 °C, ±2.5 ppm over −30 to 85 °C, ±0.3 ppm supply, ±0.3 ppm load, ±1.0 ppm per year aging.
- Phase noise: −145 dBc/Hz at 10 kHz offset.
- Supply 1.7–3.6 V. 6 mA maximum. Start-up 10 ms. Rise and fall 5 ns maximum.
- Pads: 1 tri-state, 2 GND, 3 output, 4 VDD. Pad 1 high or open enables the output.
- Operating range is −30 to 85 °C. The `-N` option extends it to −40 °C as a custom order.
- $2.38 at 1, $1.80 at 100, $1.57 at 1,000. 25-week standard lead time.
- JLCPCB lists it as C7183330, with no stock on 2026-11-01.
- Same pinout as the ASTX-H11. It can be hand-swapped onto a rev 1 board.

### Connor-Winfield TL602-024.576M

- Datasheet: `datasheets/tx414.pdf`. Digi-Key: CW857CT-ND.
- Error terms: ±1.0 ppm calibration at 25 °C, ±0.14 ppm over −40 to 85 °C, ±0.05 ppm load, ±0.05 ppm supply, ±0.40 ppm temperature hysteresis, ±1.0 ppm first-year aging. Total tolerance is ±4.6 ppm.
- After per-unit calibration, the remaining error is about ±0.2 ppm plus hysteresis and aging.
- Phase noise at 20 MHz: −100 dBc/Hz at 10 Hz, −130 at 100 Hz, −150 at 1 kHz, −158 at 10 kHz, −160 at 100 kHz.
- Jitter: 0.5 ps RMS typical, 1.0 ps maximum, integrated from 12 kHz to Fo/2.
- Supply 3.3 V ±5%, 3.135–3.465 V. 6 mA maximum. Start-up 1 ms. Rise and fall 4 ns typical.
- Pads: 4 GND, 5 output, 8 enable, 9 VCC, 10 N/C. Pads 1, 2, 3, 6, and 7 are do-not-connect. Pad 8 open enables the output.
- Keep traces out of the area under the package. The next layer down under that area should be ground plane.
- $34.26 at 1, $31.21 at 10, $25.50 at 250. 352 in stock. 17-week lead time.
- The TVL602 is the voltage-controlled version. It is the path to locking the clock to an external reference later.

### Abracon ASTX-H11-24.576MHZ-T

- The Ensor-384 board uses this part. It is Y1 on breakout rev 1.
- ±2.5 ppm TCXO. $4.37 at 1, $3.30 at 100. 556 in stock at Digi-Key, 16-week lead time. JLCPCB lists it as C1987701 and has stock.
- Pad 1 is standby. It is tied to 3.3 V.

### SJK 3N24576G33YC

- Datasheet: `datasheets/SJK 3N24576G33YC.pdf`. LCSC: C252340.
- Plain crystal oscillator, not a TCXO. ±10 ppm at 25 °C, ±20 ppm over −40 to 85 °C, ±3 ppm first-year aging.
- Jitter 0.5 ps typical, 1.0 ps maximum, 12 kHz–20 MHz.
- Fits the Y1 pads and JLCPCB can place it. $0.66 at 1. Checked in the JLCPCB placement preview.
- Using it on rev 1 would save $10.73 on 2 boards. Not used, because the rev 1 order already carries the ASTX-H11.
- Rejected for Rev A because it drifts one frame in about 35 minutes.

### TI CDC6CE024576ADLXR

- Datasheet: `datasheets/cdc6c.pdf`.
- BAW oscillator. ±25 ppm total, including 10 years of aging.
- Jitter 0.4 ps typical, 1.0 ps maximum, 12 kHz–5 MHz. Spot phase noise at 100 MHz is −86 dBc/Hz at 1 kHz offset. That is weaker close-in than a crystal TCXO.
- DLX package is 2.0 × 1.6 mm and does not fit Y1. The 3.2 × 2.5 mm variant would be CDC6CE024576ADLER.
- Rejected because of drift.

### Fox FT3HN

- Datasheet: <https://abracon.com/datasheets/Fox/FT3HN.pdf>.
- 3.2 × 2.5 mm HCMOS TCXO, 8–40 MHz. ±1 ppm is available over −30 to 85 °C.
- ±2.0 ppm initial tolerance after reflow and ±1.0 ppm per year aging. Worst case is about ±4.6 ppm, close to the ECS.
- 3.3 V ±5% only. No 24.576 MHz listing found at Digi-Key or Mouser.
- Rejected because the tighter temperature spec does not reduce total error much.

### CTS 536L24CX2GT5

- Datasheet: <https://www.ctscorp.com/Files/DataSheets/Passives/FCP/TCXO/TCXO-536-datasheet.pdf>.
- ±0.28 ppm over temperature. ±1.0 ppm initial, ±1.0 ppm first-year aging. ±4.6 ppm overall over 20 years.
- 5 × 3.2 mm, 10-pad. 3.3 V. 10 mA maximum.
- 0 in stock at Digi-Key. 28-week lead time. LCSC lists it as C6821397.

### TXC 7N-24.576MBP-T

- Datasheet: <https://www.mouser.com/datasheet/2/417/7n-541594.pdf>.
- 7N series lists ±0.14–0.28 ppm over −40 to 85 °C. The exact figure for this part number was not confirmed.
- 10-pad package. 21-week lead time at Digi-Key. No price found.

### SiTime SiT5156AI-FK-33E0-24.576000

- Product page: <https://www.sitime.com/parts/sit5156ai-fk-33e0-24576000>.
- MEMS Super-TCXO. ±0.5 ppm over −40 to 85 °C. 5 × 3.2 mm, 10-pad. LVCMOS, 3.3 V.
- $65 at 1 from SiTime direct. SiTime is moving its clock products to Renesas.
- Rejected because it costs twice the TL602 for worse stability.

## Open items

- Add timecode input and output to `PROJECT_OUTLINE.md`.
- Add the TL602 footprint and its keep-out area to the Rev A layout.
- Confirm the Rev A 3.3 V rail stays inside the TL602's 3.135–3.465 V range.
- Define the per-unit calibration procedure and where firmware stores the correction.
